# Testing

### Writing testable code

- Our code should be written in composable units. These units should be composed when the application starts (so like
  in `main.go` or `run.go`).

- Interfaces should be defined at the point of consumption. In the example below, the postgres package exports a struct
  NOT and interface and the API (consumer of store) takes an interface not a struct. This allows the api and store
  packages to be decoupled from each other.

  ```
  package postgres
  
  type MyStore struct{}
  
  func (s *MyStore) DoDBThings() {}
  ```

  ```
  package api
  
  type dbDoer interface {
  	DoDBThings()
  }
  
  type Handler struct {
  	st dbDoer
  }
  ```

## Types of tests

### Unit

Unit tests are the bread and butter of testing. Here is a basic guideline to what a unit test should be

- It tests the exported functionality of your unit (package or object/struct)
- It does NOT test other units (other units should be mocked)
- They should be thorough and test all happy path and error cases of the unit. Including how the unit reacts to various
  inputs.

#### Best practices when writing unit tests

- I should be able to know exactly what your code does by looking at just your unit tests.
- If test setup is very complicated, your unit probably isn't following the single responsibility principle.

#### Mocks

- Mocks should be avoided in favor of actual testing actual systems (docker images)
- Mocks should be incredibly simple. Typically, they should just record inputs and return preset outputs.

##### Example

TBD

### Component

So we have a pile of well tested units, how do we know we've strapped them all together correctly? That is the question
that should be answered in component tests. A brief overview

- We have two types of component tests in our codebases
    1. Tests that start the application and use it as an external user would. (black box tests)
    2. Database tests
- Component tests should use a "real" (docker) database and queueing system when applicable.
- If the application calls out to external services, mock servers should be used to replicate the external services.
- Component tests should exercise each workflow in the codebase and any mission critical cases. They should not cover
  each and every error that can occur in an application (that's why we have unit tests).

#### Example

TBD
