# Golang APIs

## Restful Overview

This document lays out the current best practices around writing golang APIs. It should evolve overtime as common
patterns are found and pulled into libraries. If there is a good reason to not use one of these practices when writing
an API, that's totally fine (just be sure to check with your team).

### Example Repo

TBD

### Common Libraries

- API framework: X
- DB driver / pooling: X
- Telemetry: X
- Linter: [golangci-lint][golangci-lint]

### Patterns

TBD

#### Code Design

- Interfaces should be heavily AVOIDED until they are explicitly needed.  They are only ever explicitly needed when you are merging functionality from 2 or more things.
- Interfaces should be declared at the point of consumption.
    - If an interface is getting large, it is a sign that the unit using that interface is doing too many things (see
      Dave Cheney's article on [Solid Go Design][solid-go]).

#### DB

- All DB functions should take the context object from the initial request. This way the DB call can be canceled when
  the request is.
- There should be a reader and a writer DB connection (if the API does both).

#### Telemetry

- A healthcheck endpoint should exist at `/status` and should include the status of connections to databases.
- Metrics should be emitted for endpoint duration
- Logs should be emitted for each failed request.

TBD: K8s endpoints, liveness/readiness, opentelemetry


[ [TOP](#top) ]

