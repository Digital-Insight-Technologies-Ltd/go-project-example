# Go Project Example

This repository contains the source code, Github Actions, and test setup for an example Golang project. This serves as an example and template, to ensure consistencey and aid the setup for all future Golang projects.

## Glossary

- [Go Project Example](#go-project-example)
  - [Glossary](#glossary)
  - [Requirements](#requirements)
  - [Project Structure](#project-structure)
  - [Testing \& Analysis](#testing--analysis)
    - [Unit Tests](#unit-tests)
    - [Integration Tests](#integration-tests)
      - [Running Locally](#running-locally)
      - [Build](#build)
  - [Contributions](#contributions)

## Requirements

Here is a list of tools that this repository is built with, all of which you'll probably have anyway!

- Golang
- Docker (including compose)
- [Staticcheck](https://staticcheck.dev/)

## Project Structure

A key goal of this example is to show how to setup your project's structure so that it is consistent with existing projects, and so it is easy to understand.

- `cmd` - the "cmd" folder is the home for all executables in this project.
  - `api` - "api" is an example executable of this project and contains source code that is only needed for the API, and the `main.go` file.
    - `handler` - specific to an API project, but this folder would house API handlers.
- `domain` - the "domain" folder is home of all source code for your project's domain, for example:
  - `model` - contains domain models such as `report.go`, notably not for dtos.
  - `dto` - contains common data transfer objects, notably not domain models.
  - `enum` - contains domain enums
  - `service` - contains any domain services, which centralise business logic, i.e. a report service.
  - `dao` - contains shared data access objects - this is only really needed if your domain models don't map to your data models.
  - `command/query` - if you took a CQRS approach, you should house your command and query objects here.
  - `repository` - if you take a repository approach, you should store your repository interfaces and mocks here, i.e. a report repository.
- `util` - as the name suggests, utility functions go here.
- `internal` - any code you don't want to export but share at this level.
- `integration` - contains all integration tests, separated from the main Go code with build tags.

This project tries to follow some basic Domain Driven Design concepts, highlights by the `domain` folder. There is a common misunderstanding around the difference between a domain model and a data transfer object, so here is a [link](https://stackoverflow.com/questions/6732124/difference-between-transfer-objects-and-domain-objects) to a Stack Overflow post with some detail.

## Testing & Analysis

The example project has been built with Test-Driven Development (TDD) in mind, and hence we use a combination of unit tests and integration tests in this project. In addition, we use both StaticCheck and SonarCloud to perform static analysis on our code, to ensure code quality and to ensure our code is safe and maintainable. Our test suite and analysis are run in GitHub Actions when a Pull Request is raised or when a Pull Request is merged into the main and release branches.

### Unit Tests

Unit tests are used to test isolated and small units of code to ensure they behave as expected, such as, business rules and edge cases. An example of this is `cmd/api/handler/<handler>_test.go`, where we would test what happens when send a request with an invalid body. Note that this only tests that unit of code, and not any downstream dependencies.

### Integration Tests

We use integration tests to tests the behaviour of the service when it's allow to interact with downstream services, such as Temporal or databases. These are useful to ensure that our application can integrate correctly with these services, testing things such as database connections and handling provider niches.

To ensure our integration tests are as close to how our deployed services will be, we use Docker Compose to spin up brand new instances of our API and database. This allows us to test our code how it will be run in Docker, and thus production, as well as being in an isolated environment.

#### Running Locally

To aid the development of features, the integration tests are something that can be run locally to save having to deploy a say a Redis change, to test its functionality. As we use Docker, we can quickly spin up and down a whole environment to test against.

Providing you have Docker Compose installed, you can run the following command to spin up the services, optionally adding the `--build` flag if you need to rebuild the services.

```bash
docker compose up -d --build
```

Once the services have built and been started, give it around 10 seconds to allow all of the services to start up. Once spun up, you will be able to access the API at port `8080` (http://localhost:8080).

If you're finished testing, or need to reset your environment, you can run the following to completely tear everything down to start fresh,

```bash
docker compose down -v
```

#### Build

We use Go build tags to separate our integration tests from our unit tests, as it allows us to run all of the unit tests in our project with a single command, then selectively run the integration tests.

To do this we add `//go:build integration` as the top line of each integration test file, and adding `-tags=integration` to any Go test command.

## Contributions

This is a "working" repository and almost certain to be subject to change as we develop new approaches and work with new technologies, so thoughts and suggestions are very welcome. We'd like to encourage the use of GitHub Issues on this repository, over other forums such as Slack threads, to ensure all conversations and decisions are kepts with the repository and can be used as context later on.