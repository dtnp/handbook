<!-- TOC -->
## Contents
[](#top)
- [Publishing Messages](#publishing-messages)
  - [Common events to publish](#common-events-to-publish)
  - [Handling out-of-order delivery](#handling-out--of--order-delivery)
  - [Message contents](#message-contents)
- [Subscribing to topics](#subcribing-to-topics)
- [Example code](#example-code)
  - [Publishing to a topic](#publishing-to-a-topic)
  - [Subscribing to a topic pattern](#subscribing-to-a-topic-pattern)
  - [Subscribing to a topic pattern with single-delivery among consumers](#subscribing-to-a-topic-pattern-with-single--delivery-among-consumers)
- [HTTP Topic API](#http-topic-api)

## Overview

Our standard queueing will be using a [topic exchange](https://www.rabbitmq.com/tutorials/tutorial-five-python.html), which allows for the following use cases:
1. Full publish and subscribe (pubsub) semantics, where all subscribers receive a copy of every message published to a matching topic
2. Pubsub but with single-delivery of events for  replicas of a given consumer

## Publishing messages

All messages should be published to a topic (routing key, in RabbitMQ) of the form `ENTITY.ACTION.(EXTRA.)*?`.
* `ENTITY` is the data model, say `target`
    * These should be singular, so `target` rather than `targets`, unless it only makes sense to operate upon a collection of the entity at once
* `ACTION` is the event type, such as `created` or `deleted`
* `EXTRA` are period-separated fields reserved for entity-specific specifiers, like the target's scheme or the lead's engine. This will allow us to use our queueing system to filter out unwanted inputs for us.

For example:
* `target.created.https` (entity.action.scheme)
    * This would allow activations like analyzer to subscribe to `target.(reachable|created).https?` rather than needing to filter out the full target set locally
* `subdomain.created.`
* `screenshot.created.`
* `scanLevel.updated.`
* `lead.created.analyzer.1027` (entity.action.engine.source)

All entity and action names must be camel cased, so:
* `portProbe.new` would be valid
* `named_target.reachable` would not be

### Common events to publish

A non-exhaustive list of events you should consider publishing is:
* The discovery of an entity, such as `subdomain.new` signifying a subdomain that is found by a system that is _not_ the data owner.
* Data model creation: `$entity.created.(extra.)*?`
* Data model updates
    * This is difficult to do well in the presence of faults; see [Handling out-of-order delivery](#handling-out--of--order-delivery) below for a discussion of this
* Data model deletion: `$entity.deleted.(extra.)*?`
* "Significant" status changes---which are defined or a per-entity basis---including:
    * A target becoming reachable: `target.reachable.$scheme`

### Message contents

All messages must be wrapped in our [`queue.Message`](https://github.com/BishopFox/queue/blob/v0.1.0/message.go#L36) envelope, which will allow for scan level checking and metric collection.

As for the `"data"` payload: :essages---events---should strive to be [idempotent](https://queue.acm.org/detail.cfm?id=2187821). A subscriber should be able to act on an event based on the event's message body alone (barring unforeseen circumstances).

For example:
* All `new` events _must_ include the full import model as JSON
* All `created` events _must_ include the full model as JSON
* All `deleted` events _must_ include the full model as JSON

Updates should have the ability to be serviced out of order.

### Handling out-of-order delivery

From the [RabbitMQ documentation](https://www.rabbitmq.com/semantics.html#ordering):
>  Section 4.7 of the AMQP 0-9-1 core specification explains the conditions under which ordering is guaranteed: messages published in one channel, passing through one exchange and one queue and one outgoing channel will be received in the same order that they were sent. RabbitMQ offers stronger guarantees since release 2.7.0.
>
> Messages can be returned to the queue using AMQP methods that feature a requeue parameter (basic.recover, basic.reject and basic.nack), or due to a channel closing while holding unacknowledged messages. Any of these scenarios caused messages to be requeued at the back of the queue for RabbitMQ releases earlier than 2.7.0. From RabbitMQ release 2.7.0, messages are always held in the queue in publication order, even in the presence of requeueing or channel closure.
>
> With release 2.7.0 and later it is still possible for individual consumers to observe messages out of order if the queue has multiple subscribers. This is due to the actions of other subscribers who may requeue messages. From the perspective of the queue the messages are always held in the publication order.

We do not have a general mechanism for applying updates out of order, however tracking versions of a data model (or of its fields) would allow some guarantees around update ordering.
[Numerous](https://en.wikipedia.org/wiki/Lamport_timestamp) [techniques](https://en.wikipedia.org/wiki/Version_vector) exist; a simple per-field or per-model version could be used to implement [optimistic concurrency control](https://en.wikipedia.org/wiki/Optimistic_concurrency_control).

## Subscribing to topics

All golang services should use the [RabbitMQ](https://github.com/BishopFox/fury/tree/master/queue/rabbit) implementations of our general [queueing library](https://github.com/BishopFox/fury/tree/master/queue), rather than the HTTP queueing API for performance reasons. Using our queueing library has the extra benefit of allowing us to swap out queueing implementations in case rabbit does not suffice (which we've done with redis already).

Non-go services, when and if they exist, should use an appropriate AMQP library.

### Processing messages

Message handling should strive for [idempotence](https://queue.acm.org/detail.cfm?id=2187821) so that the system is resilient to multiple-delivery.

#### Handling failure

System designers should take care to consider the failure modes of their processing. What should be done if we fail to import a new subdomain from `subdomain.new`? What about instead if we're unable to execute an automation task?

You as the designer should reason about these tradeoffs for your particular use case. For example, we may wish to siphon models we fail to instantiate into a [dead letter exchange](https://www.rabbitmq.com/dlx.html) for secondary (or manual) processing, whereas if we fail to execute an automation task we would like wish to log it and move on.

## Example code

We maintain a queueing library at https://github.com/BishopFox/queue that handles most use cases. For more details see its documentation and the examples folder.

## HTTP Topic API

Our lambdas aren't allowed to publish to RabbitMQ directly, and must (barring changes to our security model) publish through an AWS API Gateway.

As these are publish-only, the following Topic API is proposed as an extension to our queueing service. If subscriptions are needed they can be added.

### Publishing to a topic: `POST /topics/{topic}`

Publish an array of JSON messages to the specified topic. This will send a copy of each message to _each_ subscription that matches the provided topic.

#### Parameters

**Path parameters:**

* `{topic}`: topic string. Must not include the wildcard character `*`

**Body**:

* A JSON array of messages (objects) to publish

#### Responses

| Code                      | Situation                                                                                                                         | Response body              |
| ------------------------- | ------------------------------------------------------------                                                                      | -------------------------- |
| 200 OK                    | Messages successfully published.                                                                                                  | `{"success": true}`        |
| 400 Bad Request           | The API will respond with a 400 in two cases: 1) the `{topic}` string is invalid, or 2) the body is not an array of JSON objects. | `{"error": "description"}` |
| 429 Too Many Requests     | The queuing server is overloaded and the client should back off and retry after sleeping the instructed number of ns.             | `{"retryAfter": int64}`    |
| 500 Internal Server Error | Server error encountered that the client cannot address.                                                                          | `{"error": golangError}`   |
