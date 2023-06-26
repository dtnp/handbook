# Golang Best Practices

### Related Handbooks

- [Testing][testing]
- [Golang APIs][golang-apis]
- [CI/CD][ci-handbook]

### Libraries we use

- DB driver / pooling: TBD
- Telemetry: [telemetry][telemetry]
- Testhelpers for common testing patterns: TBD
- Linter: [golangci-lint][golangci-lint]

### SOLID Go Design

Dave Cheney lays out Solid Go Design concepts in this [doc][solid-go]

In addition to the patterns defined there, we also follow Dependency Injection

##### What is dependency injection?
It's sort of a combo of ISP and Dependency Inversion. But more basically it means that our units should be isolated and our `main.go` or some other high level abstraction (`run.go`, `serve.go`) should be in charge of wiring our units together.

##### Why do we use dependency injection?

- It allows us to segregate our units which leads to less brittle and more testable code.
- It allows us to easily replace units without having to change other units.
- It allows us to separately worry about our units and the choreography of the units.

### Package structure

TBD

#### Service / non-library repos

Designed based on many open source go repos like [prometheus][prometheus]

- If the repo only has one `main.go` it should be stored in the root of the repo. If it has many, they should be stored inside the `cmd` pacakge
- Code that is not meant to be imported by other repos should be stored in `pkg`
- Code that is only used in tests (like testhelpers or common mocks) should be stored in `internal`

#### Library repos

- If the interface of the library is pretty small (like go-testhelpers), everything exported should be in the root directory.
- Otherwise, there should be one layer of nesting for exported functionality like in telemetry.

### Telemetry

- A healthcheck endpoint should exist at `/status` for all services (anything that isn't a lambda/automation).
- Times for important interactions (especially DB interactions) should be recoreded.

### DB

- All DB functions should take the context object from the initial request. This way the DB call can be canceled if the caller is cancelled.
- There should be a reader and a writer DB connection (if the project does both).
- Bulk Operations should follow the patterns laid out in attack surface api
    - Bulk Deletes and Locking
    - Bulk Inserts

[ [TOP](#top) ]
