# API Standards

<!-- TOC -->
## Contents
[](#top)
- [Documentation Expectations](#documentation-expectations)
- [How are resources addressed by URL?](#how-are-resources-addressed-by-url)
  - [How should we handle "multi-resource" actions?](#how-should-we-handle-multi-resource-actions)
- [How are endpoints versioned?](#how-are-endpoints-versioned)
  - [When do we increase the version number?](#when-do-we-increase-the-version-number)
  - [When will we deprecate or sunset older versions of APIs?](#when-will-we-deprecate-or-sunset-older-versions-of-apis)
- [Allowed response codes](#allowed-response-codes)
- [Supported Verbs](#supported-verbs)
- [What must our responses look like?](#what-must-our-responses-look-like)
  - [Pagination](#pagination)
    - [Query Parameters](#query-parameters)
    - [Response envelope](#response-envelope)
    - [Counting Entities](#counting-entities)
  - [Response Headers](#response-headers)
  - [Query parameters](#query-parameters)
    - [List of Query Parameters](#list-of-query-parameters)
- [Requests](#requests)
  - [Request Bodies](#request-bodies)
  - [Request Headers](#request-headers)
- [Common Endpoints](#common-endpoints)
- [An Example API](#an-example-api)

<!-- /TOC -->

## Documentation Expectations

All APIs should produce valid OpenAPIv3 documentation (formerly known as Swagger documentation).

This documentation *must* be provided to all internal customers, and written with the expectation of being shown to external clients

Documentation *should* include all the standard expectations of OpenAPIv3 documentation, including API response codes, mandatory and optional parameters, and response bodies.

Documentation *should* also include a brief description of expectations on the user journey through the APIs and how they are expected to be used.  This may include words that link to a [Terminology/Taxonomy Page](#) to better help the reader

[ [TOP](#top) ]

## How are resources addressed by URL?

Paths must be of the form `/v{integer}/resource/{resource id}/subresource/{sub id}/action`

[ [TOP](#top) ]

### How should we handle "multi-resource" actions?

If an endpoint allows accessing all resources that the requester is authorized for, it *must* be of the form `/version/resource/all/subendpoint`, where `all` is a sentinel value in place of the normal identifier. `version` *must* be in the form of `v{integer}`.

If this does not make sense for a given entity, then the exception must be documented.

[ [TOP](#top) ]

## How are endpoints versioned?

Endpoints will be versioned individually, with the version number as the *first* entity in the path.

Paths will look like be `/v{integer}/resource/{id}`

[ [TOP](#top) ]

### When do we increase the version number?

Version numbers are increased *only* with a breaking change to the API's contract.

We do not consider the following to be breaking changes:

* Additive changes to the response
* Bug fixes (usually, see clarification and examples below)

The following are examples or breaking changes that require a version increase:

* The removal, renaming, or movement (in a nested object) of a field
* A non-additive change in the type of any inputs (parameters, etc)
  * This includes renaming any URL parameters. Additive additions of equivalent names are non-breaking, but removing an existing name or changing it in any way (even semantically what it does) is considered breaking

If a "bug" causes semantic modification of the response then it *may* require a version bump.

The key here is to look at intent and impact: was this a missed requirement? If so, bump the version because we are changing our contract intentionally.

If not, then consider keeping the version consistent unless the stakeholder impact is significant enough to warrant a version bump.

Intent overrides impact.

* Trivial example of not bumping a version: Providing a filter of "enabled=false" to the API causes a 500 response. Obviously this isn't a version bump because the API is unavailable.
* Disqualifying example to not bump a version: "filtering an API for status=all missed a status" (the definition of all is unchanged, but the response contents would be modified. The intent was to include this in the body, so it is acceptable. If this new content would not be a huge impact to stakeholders then the version should remain unchanged.
* Qualifying example to bump a version: "disabling a domain does not disable downstream subdomains, but it should" (this closely maps to a missed requirement)


[ [TOP](#top) ]

### When will we deprecate or sunset older versions of APIs?

Deprecation periods depend on the impact of the change. Broadly we break this up into external facing deprecation and internal facing deprecation.

* If the endpoint is for _internal use only_, so that no `customer` role has access to this API but the `operator` role has access (or any other non-engineering only role) then it should have a one quarter deprecation warning.
* If the endpoint has _any external use_ by the `customer` role then the API should have no less than a 2 quarter deprecation warning.

Any APIs which are only accessible from service to service or wholly within engineering will have their deprecation period handled internally, between teams if necessary. The timeline for this should be whatever is most amenable to the teams involved, as this should have no stakeholder impact.

By the same token, if engineering can demonstrate that an API is completely unused by any non-engineering stakeholders - whether that is customers or other internal Bishop Fox customers - then then API can be removed once customer and internal stakeholders approve of it.

[ [TOP](#top) ]

#### Deprecation Process
##### Deprecation Documentation and Communication
The OpenAPIv3 Documentation is the source of truth for deprecation warnings. When deprecating an API the OpenAPIv3 documentation *must* be updated with an appropriate deprecation warning.

This warning *should* include any indication of replacement APIs. It *should* also include an example of a query from the old API and how the new API (if any) can provide that same functionality.

APIs *should* only be announced as deprecated once its replacement API, if any, is available for use. Deprecation announcements *may* be made before the new API is available, but this is discouraged and the new API *must* be made available as soon as practically possible.

There are additional methods of communication which *must* take place:
* A deprecation announcement *must* be made in the #cse slack channel
  * Whenever this announcement is made, if the API has external stakeholders (customers), then this announcement *must* be duplicated in an email to our customers
  * This *should* indicate a cut off date for the old API
  * This *should* include a reference to the OpenAPI deprecation announcement
  * This announcement *should* be repeated each Quarter of the deprecation notice remaining before the cut off
  * This announcement *should* be repeated one week before the deprecation cut off
  * This announcement *should* also be made the day of the deprecation cut off

Additionally the following methods of communication *should* be put in place:
* A warning *should* be printed on the Terminus CLI whenever a user acceses the deprecated API
  * This warning *should* indicate if the CLI contract will be changing in any way, and if so, how
  * This warning *should* include a reference to the OpenAPI deprecation announcement

We would also like to begin discussions with our stakeholders about the appropriateness of including a Portal banner message for deprecation warnings. This will occur out of band from this document, and it will be updated once a conclusion has been reached.

##### Developer Expectations for Deprecated APIs
When an API is deprecated it *must* have at least one functional component test for its happy path. If one does not exist at the time of deprecation then one *must* be created as part of the deprecation feature work to produce the new API. If there are any other critical business functions for the API then component tests *should* be produced for those, depending on business impact if they were to cease functioning. The scope of automated tests to produce should be gathered by consulting with stakeholders on deprecation impact.

If there is not a ticket for a replacement API then a ticket *must* be produced to ensure that the deprecated API has appropriate automated validation.

These automated tests *should not* be all encompassing if they did not previously exist. We do not want to over-invest into a deprecated API endpoint.

This testing procedure is designed to enable engineering to adhere to our agreed upon deprecation timelines.
As deprecation timelines are _at least_ a quarter, this ensures that deprecated APIs continue to work while underlying database schemas and other changes are being made for replacement APIs.

[ [TOP](#top) ]

## Allowed response codes

| Status Code               | When it should be used                                       |
| ------------------------- | ------------------------------------------------------------ |
| 200 OK                    | When an action is successful                                 |
| 201 Created               | When a resource is successfuly created. This **must** include a `Location:PATH` header that points to the resources GET path. |
| ~204 No Conten~t          | ~When an action is successful but a response is unwarranted.~ |
| 400 Bad Request           | When a request is invalid. Response bodies **must** include details on what is invalid about the request |
| 403 Forbidden             | When an action on the specified resource is forbidden.       |
| 404 Not Found             | When an entity or path does not exist                        |
| 405 Method Not Allowed    | When a request can not be completed as the specified method is not allowed for the requested path |
| 418 I'm a Teapot          | When a request attempts to brew coffee using a teapot        |
| 422 Unprocessable Entity  | When the request was well-formed but was unable to be followed due to semantic errors |
| 429 Too Many Requests     | When the requester has been rate limited                     |
| 500 Internal Server Error | When the server has encountered an unrecoverable error handling the request |

Note that *204 No Content* is explicitly not allowed: **all** endpoints must return a body, even if its merely a count of deleted objects, or `{"success": true}`

[ [TOP](#top) ]

## Supported Verbs

The following HTTP methods are allowed

| Method | Situations                                                   | Request expectations                                         | Response expectations                                        |
| ------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| GET    | Searching, listing, or GETting a resource                    | All search or filter parameters must be specified in the querystring | Responses that return multiple values **must** be paginated. |
| PUT    | Actions on a resource, or updates to a single field of a model | All data should be passed as JSON in the request body        | *200 OK*                                                     |
| POST   | Resource creation                                            | All data should be passed as JSON in the request body        | *200 OK* if the exact value already exists (creation should be idempotent).<br />*201 Created* when successfully created. |
| PATCH  | Partial to full updates to a data model                      | The partial update must be a JSON object in the request body | *200 OK*: The new model (after the patch is applied) should be returned in the response body. |
| DELETE | Resource deletion                                            | Request body is ignored, but querystring parameters filter the delete | *200 OK*: with a json `{count: N}` of deleted objects.       |

[ [TOP](#top) ]

## What must our responses look like?

All API responses will return a JSON object. If an endpoint returns an array, it must be wrapped in an object envelope. If said API could be paginated, it should be.

All JSON keys **must** be `snake_cased`.

All responses will have *at least* the following keys:

1. `time`: which is a UTC timestamp associated with the response - useful to tell if you are returning cached data
2. `success`: boolean true or false, useful to tell if everything succeeded in the background

Individual services need not worry about these, as they will be added to your responses for you.

[ [TOP](#top) ]

### Pagination

All endpoints that return multiple entities *must* be paginated, unless there is an overriding reason not to.

[ [TOP](#top) ]

#### Query Parameters

All endpoints that support pagination **must** support the `page` and `limit` query parameters:

* `page` specifies the page number
* `limit` specifies the page size

For database calls, the query's offset corresponds to `(page - 1) * limit` while the `limit` is just the `limit`.

There is an additional special requirement for `limit=0` that APIs **should** support. When `limit=0` is provided the API **should** respond with an empty `data` field, but an accurate `total` field.

This allows for clients to determine the count of entities without first having to acquire any other data.

Additionally, APIs may have to support `id_after` as detailed below, if the page wrapper supports `last_id`.

##### Engineering guidance

A maximum limit should be enforced to protect our services, but this will vary based on the response.

Some general guidance: 1000 is a fine default limit, and 10,000 is a good maximum limit.

[ [TOP](#top) ]

#### Response envelope

Paginated responses—searches and lists—will return a response with the following metadata:

```json
{
  "page": 1,      // integer page number
  "limit": 20,    // requested page size
  "total": 1000,  // Optional value with the total number of items matching the query
  "data": [],     // the list of matching entities
  "last_id": 1234  // Optional value containing the ID of the final entity on this page
}
```

`last_id` is an optional field. It **may** not be provided by the API, or **may** be `null` if present.

If an API supports the `last_id` metadata on the page wrapper then it **must** also support the `id_after` URL parameter for that API.

The `id_after` parameter instructs the API to return the next batch of data after the ID provided. Clients **should not** adjust any parameters other than `last_id` if paging
over data in this way.

Supporting `id_after` and `last_id` permits internal clients, such as automation activations, to efficiently page over large data sets using a database primary key if they do not have any specific ordering needs. This enables us to significantly reduce the CPU consumption of our databases for common internal paginations over large, full data sets.

#### Counting entities

To improve endpoint performance the `total` field is _only_ required when the `limit` is `0`


This means that when paginating an API consumer should either:

1. Request the total count of items once before iterating
2. Iterate until an empty page is returned

[ [TOP](#top) ]

### Response Headers

Responses must include the`traceparent` and `tracestate` tracing headers. We comply with the [W3C's Tracing Recommendations](https://www.w3.org/TR/trace-context/).

#### Engineering guidance

If your service uses our [`EndpointLoggingMiddleware`](https://github.com/BishopFox/go-api/blob/master/logging/endpoint_logger.go#L85), then trace propagation is handled for you.

[ [TOP](#top) ]

### Query parameters

#### List of Query Parameters

All endpoints that support a list of query parameters **should** accept the following pattern `&param=value1&param=value2&param=value3` where:

* `param` is the query parameter name
* `value{N}` represents multiple values supplied to `param`

Note that this is the default setting for network-monitor.

[ [TOP](#top) ]

## Requests



### Request Bodies

All `PUT`, `POST`, and `PATCH` requests must have a JSON body; see [Supported Verbs](#supported-verbs) above.



All bodies must be _either_ a JSON object or an array of values, whichever makes the most sense for the behavior of that endpoint.

 For example:

* `PUT /latest/orgs/{org_id}/note` should require an HTTP body of the form `{"note": "new value"}`
* `POST /latest/subdomains/{source}` requires an array of string subdomains as it is a bulk import
* `PATCH /latest/orgs/{org_uuid}/domains/{domain}` should require a JSON object as its body that specifies the partial update, like `{"source": "new src", "registrar": "a new registrar"}`.

[ [TOP](#top) ]

### Request Headers

All requests from external users *must* include an `Authorization: Bearer {AUTH0 TOKEN}` header.

[ [TOP](#top) ]



## Common Endpoints

* `GET /status` - healthcheck endpoint; required

[ [TOP](#top) ]



## An Example API

This example is our proposed Topic and Subscription API between internal services. It demonstrates

### Topics

#### Publishing to a topic: `POST /topics/{topic}`

Publish an array of JSON messages to the specified topic. This will send a copy of each message to _each_ subscription that matches the provided topic.

##### Parameters

**Path parameters:**

* `{topic}`: topic string. Must not include the wildcard character `*`

**Body**:

* An array of JSON objects to publish as individual messages

##### Responses

| Code                      | Situation                                                    | Response body              |
| ------------------------- | ------------------------------------------------------------ | -------------------------- |
| 200 OK                    | Messages successfully published.                             | `{"success": true}`        |
| 400 Bad Request           | The API will respond with a 400 in two cases: 1) the `{topic}` string is invalid, or 2) the body is not an array of JSON objects. | `{"error": "description"}` |
| 429 Too Many Requests     | The queuing server is overloaded and the client should back off and retry after sleeping the instructed number of ns. | `{"retryAfter": int64}`    |
| 500 Internal Server Error | Server error encountered that the client cannot address.     | `{"error": golangError}`   |

[ [TOP](#top) ]

### Subscriptions

#### Create a subscription: `PUT /subscriptions/{name}`

Declare and bind a queue to the given topic pattern. Can be used to re-bind an exsting queue's subscription, and allows for shared queues in situations where a service has replicas and would like to avoid receiving messages multiple times.



For example, two Hawkeye processors could use the subscription `hawkeye-processor` to share a message stream without duplicates (barring rabbitMQ itself delivering repeatedly)

##### Parameters

**Path parameters:**

* `{name}`: the name of the subscription to create
  * Name is a unique identifier for the subscription that will be used as the queue's name in RabbitMQ.
  * Multiple consumers can reuse the same subscription to enforce single delivery of messages among them

**Body**:

The body should be a subscription definition of the form:

```json
{
  "topic": "entity.action.additional.*"
}
```

The `topic` is a string pattern that defines what topics (routing keys) this subscription cares about. This can include wildcards, e.g. a topic of  `target.*.*` would match both `target.create.http` as well as  `target.delete.smtp.`



##### Responses

| Code                      | Situation                                                    | Response body              |
| ------------------------- | ------------------------------------------------------------ | -------------------------- |
| 200 OK                    | Subscription successfully created                            | `{"success": true}`        |
| 400 Bad Request           | The API will respond with a 400 in two cases: 1) the subscription `{name}` is invalid, or 2) the topic subscription is invalid | `{"error": "description"}` |
| 429 Too Many Requests     | The queuing server is overloaded and the client should back off and retry after sleeping the instructed number of ns. | `{"retryAfter": int64}`    |
| 500 Internal Server Error | Server error encountered that the client cannot address.     | `{"error": golangError}`   |

[ [TOP](#top) ]

#### Retrieve message: `GET /subscriptions/{name}/messages?count={n}`

Retrieve at most `n` messages that this subscription has subscribed to.

##### Parameters

**Path parameters:**

* `{name}`: the name of the subscription to delete

**Query parameters**:

* `{n}`: the number of messages to retrieve

**Body**:

* No body data is expected.

##### Responses

| Code                      | Situation                                                    | Response body                                                |
| ------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 200 OK                    | Successfully retrieved messages                              | `[{message}]`. The contents of the message will depend upon what is published and cannot (yet?) be enforced at this layer |
| 400 Bad Request           | The count or subscription parameters are invalid             | `{"error": "description"}`                                   |
| 404 Not Found             | The subscription does not exist                              | `{"success": false}`                                         |
| 429 Too Many Requests     | The queuing server is overloaded and the client should back off and retry after sleeping the instructed number of ns. | `{"retryAfter": int64}`                                      |
| 500 Internal Server Error | Server error encountered that the client cannot address.     | `{"error": golangError}`                                     |

[ [TOP](#top) ]

#### Delete a subscription: `DELETE /subscriptions/{name}`

This deletes a subscription and

##### Parameters

**Path parameters:**

* `{name}`: the name of the subscription to delete

**Body**:

* No body data is expected.

##### Responses

| Code                      | Situation                                                    | Response body            |
| ------------------------- | ------------------------------------------------------------ | ------------------------ |
| 200 OK                    | Subscription deleted.                                        | `{"success": true}`      |
| 404 Not Found             | The subscription does not exist                              | `{"success": false}`     |
| 429 Too Many Requests     | The queuing server is overloaded and the client should back off and retry after sleeping the instructed number of ns. | `{"retryAfter": int64}`  |
| 500 Internal Server Error | Server error encountered that the client cannot address.     | `{"error": golangError}` |

[ [TOP](#top) ]
