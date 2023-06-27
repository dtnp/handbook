# Rate Limiting - Outgoing

In order for to scale our automation load, without causing operational
incidents for our customers we need to restrict the rate at which we interact
with their infrastructure.

## Goals

1. Respect configurable per-IP and per-Org rate limits for analyzers
2. Design the system in such a way that it can be easily added to other
   automations
3. The system should be cost-effective: redis is not
4. The systems should scale as the number of IPs we need to scan grows

## Considerations

1. The MVP need only work for Analyzers, which are the vast majority of our
   network traffic
2. All proposals below only discuss rate limiting by IP as rate limiting by org
   is a trivial extension <sup name="r1.1">[1](#1.1)</sup>
3. The system should fail closed: if the rate limiting system is inaccessible,
   no requests should be made to customer infrastructure _unless_ they opt-in to
   unlimited requests
4. All proposals below require something to durably store rate limits. I'm
   assuming that there is a Postgres-backed API somewhere that seeds and
   configures limits for _all_ of these proposals, as that will change rarely
   and can be handled outside of the critical path

### Terminology

- **Resource identifier** a unique identifier for a rate-limited resource. Here:
  an IP address or an organization UID. Also _resource id_.
- **RTT** network round-trip time

## Proposals

### Centralized rate limit server

This is by far the simplest solution: we implement a simple HTTP server that
receives requests like `PUT /{resourceId}` and either:

1. Looks up and caches the rate limit for that identifier
2. Sleeps until the requester is allowed to proceed
3. Returns the amount of time the requester must sleep before making its request

A basic improvement to 2 is to subtract the RTT from the sleep amount returned
and then sleep that amount, if greater than 0.

#### Benefits

This approach trades resiliency and throughput for simplicity. It is by far the
simplest as there is no coordination on the server side or implementation on the
client side other than a simple HTTP request.

It would be fast as well: the majority of the overhead would be the RTT to and
from the server. All rate limit operations could be done efficiently using
[atomic operations](https://github.com/uber-go/ratelimit/blob/master/ratelimit.go#L142)
as durability is unnecessary.

If the service restarts, it restarts and we're OK with that.

#### Trade-offs

Under load this approach may suffer: with 200 parallel requests per analyze
container, 250 containers per org and ~13 orgs in parallel we could see an
instantaneous load of 650,000 concurrent requests to a single server. As the
server will fail, clients will retry which will cause a failure loop as they
crush the service under their
[thundering hooves](https://en.wikipedia.org/wiki/Thundering_herd_problem).

This can be worked around by dropping all HTTP requests among some maximum
number of concurrently serviceable requests and sending them
`HTTP 429 Too many requests`: "try again later".
[`golang.org/x/netutil.LimitListener`](https://godoc.org/golang.org/x/net/netutil#LimitListener)
does this at the TCP level.

The [`fasthttp.Server`](https://pkg.go.dev/github.com/valyala/fasthttp#Server)
also allows setting the maximum number of concurrent requests

#### Off-the-shelf implementations

Wrap a thin HTTP server around https://github.com/uber-go/ratelimit and we're
done. Minus load-testing it'd take a few hours.

### Partitioned rate-limit server

This extends the centralized server above by partitioning the keyspace across a
number of rate-limit servers. The approach would use a consistent hashing
algorithm to deterministically assign a resource identifier to a single peer in
a cluster , allowing us to use efficient atomic operations to enforce limits as
there is no coordination between nodes.

#### Benefits

We get the ease of implementation of the central server with a lower likelihood
of crushing numbers of HTTP requests.

#### Tradeoffs

The worst-case of the previous proposal is still the worst-case here: if all
possible tasks request the same IP instantaneously, requests will be dropped.

#### Off-the-shelf implementations

Mailgun's [Guberator](https://github.com/mailgun/gubernator) implements nearly
the exact approach I describe above, but with an unfortunate caveat: each
request must include the rate limit configuration, which would require all of
our jobs to have some way of knowing the rate limit per IP. If that value is the
same across all IPs then its trivial (and we can inject it into our containers
via an environment variable), but as soon as it's configurable that approach
falls apart. We could extend gubernator (or implement our own variant) that is
backed by a configuration system (postgres or another service) to eliminate this
issue.

YouTube's [Doorman](https://github.com/youtube/doorman) would also work, but it
has not been updated in 5 years and seems to have no community support. The
benefit of doorman is that it's adaptive, but since our workloads are unstable
(as we don't know what IPs are going to what node) our wants could vary wildly
from one scheduling epoch to the next.

### Local rate limiting (simple)

The simplest local rate limiter that solves this problem is one in which each
EC2 instance is granted
<img src="http://www.sciweavers.org/tex2img.php?eq=limit%5Cover%7B%7CASG%7C%7D&bc=White&fc=Black&im=png&fs=12&ff=arev&edit=0" align="center" border="0" alt="limit\over{|ASG|}" width="49" height="46" />
rate limit tokens per IP per second.

#### Benefits

This avoids the problem of a central server, as we've reduced the maximum load
per local server to
<img src="http://www.sciweavers.org/tex2img.php?eq=%7Ccontainers%7C%2A%7Cconcurrent%5Cspace%20requests%7C&bc=White&fc=Black&im=png&fs=12&ff=arev&edit=0" align="center" border="0" alt="|containers|*|concurrent\space requests|" width="311" height="19" />,
which will be on the order of thousands of concurrent requests rather than
hundreds of thousands.

#### Tradeoffs

While simple it comes with a major caveat: we know that jobs are not evenly
distributed across EC2 instances---they're packed tightly by tool and org
uid---so our EC2 instances will not have the same needs.

### Local rate limiting (adaptive)

This is the most complicated but efficient approach: each EC2 node would need to
identify what its resource _wants_ are and collaborate with either a central
limit server or other EC2 nodes to divvy-up the rate limit per resource.

This is unlikely to work for us at the moment as without our cluster
coordinator's cooperation (Akbar) we do not have a best-effort guess at what
each node's resource wants are.

This is essentially YouTube's [Doorman](https://github.com/youtube/doorman) but
with each EC2 instance being its own server.

## Implementing rate limiting into Go's HTTP client

Project Discovery's [httpx](https://github.com/projectdiscovery/httpx) makes use
of a custom
[`net.Dialer`](https://github.com/projectdiscovery/httpx/blob/master/common/cache/dialer.go#L29)
that caches DNS responses. Not only could we do the same, allowing us to use the
IPs passed as part of our Target models while not mucking up TLS, but we could
implement per-IP rate limiting at the same time
[like I did on Friday](https://github.com/chronoslynx/httpx/commit/af9e658c2bb6cfd15051581155808686427c022c)

## Recommendation

I recommend that we begin with either a centralized rate limiting server, or a
partitioned set of servers using
[Gubernator as a library](https://github.com/mailgun/gubernator#gubernator-as-a-library).
By using gubernator as a library we take advantage of their partitioning logic
while being able to define our limits in the server so our clients need not be
aware.

Gubernator is used in production by Mailgun and is actively maintained, letting
us take advantage of their work on reliability and performance.

## Future Work

This document does not include discussions of rate limiting that require
changing how we batch, assign, or enqueue work as they require changing much
larger swaths of our infrastructure and are unlikely to be completeable in a
single sprint as MVP.

If we batch, enqueue, and assign work by IP (or set of IPs), then we can handle
rate limiting as a part of our standard bin-packing as our coordinator (Akbar)
would know:

1. How many EC2 nodes are assigned containers for a given limited resource
2. What proportion of work per resource is assigned to each node

Given those it could assign a local rate limit per resource per node and enforce
our global rate limits without the need for a secondary central server. No
coordination would be necessary between EC2 nodes, and we could use a
highly-efficient local rate limiting server on each to minimize the RTT.

## Notes

<a name="1.1">[1]</a> The simplest scenario involves treating the org uid as
another IP address, and checking its rate at before (or instead of) the actual
IP's [&#x2934;](#r1.1)
