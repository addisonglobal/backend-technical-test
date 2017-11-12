# Addison Global Backend Technical Assesement

## Introduction

Welcome to Addison Global Backend technical test.

The main goal of this exercise is to assess your approach to problem solving, as well as your ability to write clean, well tested and reusable code. There's no hard rules or tricky questions.

**Note:** Despite some snippets are written in Scala, the exercise can be developed in Java.
## Glossary
* Credentials - A tuple of _username_ and _password_ that are used to authenticate a customer.
* User - Identifies a given customer within the system. For simplicity, it just contains _userId_ which will match the _username_ of the given customer.
* UserToken - Token granted to a user in order to perform further operations in the system. It is the concatenation of the _userId_ and the current time. For example: *user123_2017-01-01T10:00:00.000*

Implementation example:
```scala
case class Credentials(username: String, password: String)
case class User(userId: String)
case class UserToken(token: String)
```
## Brief
The goal of the exercise is to improve the definition of a backend service/module and provide an implementation for it. Once this is finished, you'll create a microservice that offers a REST API to consume the service/module functionality.

### 1. Service Trait / Interface
Given these two synchronous and asynchronous definitions of the TokenService
```scala
trait SyncTokenService {
  protected def authenticate(credentials: Credentials): User
  protected def issueToken(user: User): UserToken

  def requestToken(credentials: Credentials): UserToken = ???
}
```
```scala
trait AsyncTokenService {
  protected def authenticate(credentials: Credentials): Future[User]
  protected def issueToken(user: User): Future[UserToken]

  def requestToken(credentials: Credentials): Future[UserToken] = ???
}
```
**Task:** Provide both implementations of _requestToken_ in terms of _authenticate_ and _issueToken_. By doing that, whoever implements the service will only need to implement _authenticate_ and _issueToken_.


### 2. Service Implementation

Provide an implementation for the following API, which is **different from** the one designed in the previous section:
```scala
 trait SimpleAsyncTokenService {
   def requestToken(credentials: Credentials): Future[UserToken]
 }
```
We prefer you to use an Actor Model implementation such as [Akka](https://akka.io/), but it's not mandatory. You can use other frameworks like Spring.

**Task** guidelines:
1. Implement an Actor/Service/Module that:
   * Validates the *Credentials* and return an instance of a *User*.
   * If the password matches the username in uppercase, the validation is a success, otherwise is a failure. Examples:
       * username: house , password: HOUSE => Valid credentials.
       * username: house , password: House => Invalid credentials.
   * The *userId* of the returned user will be the provided username.
   * This logic has to be encapsulated in a separate Actor/Service/Module.
   * The result will always be returned with a random delay between 0 and 5000 milliseconds. 
2. Implement another Actor/Service/Module that:
   * Returns a *UserToken* for a given *User*. If the *userId* starts with **A**, the call will fail. 
   * The *token* will be the concatenation of the *userId* and the current date time in UTC: *yyyy-MM-dd'T'HH:mm:ssZ*. Example:
       * username: house => house_2017-01-01T10:00:00Z
   * This logic has to be encapsulated in a separate Actor/Service/Module.
   * The result will always be returned with a random delay between 0 and 5000 milliseconds.
3. Implement the *requestToken* function/method from the **SimpleAsyncTokenService** trait/interface in a way that:
   * Its logic in encapsulated in an Actor/Service/Module.
   * It makes use of the previously defined actors/services/modules for authenticating users and granting tokens.
   * It will first use the validation of the *Credentials* to obtain a *User*.
   * After that it will then use the actor/service/module to obtain a *UserToken*.
   * Returns the *UserToken* to the original caller.

We're particularly interested on how the actor system (or the service orchestration) is designed and tested, paying special attention to the following two aspects:
* Threading model.
* Fault tolerance.

**Keep in mind:**
* As the implementation is intended to serve multiple concurrent requests!!! The fact that a computation might take 5 seconds should not prevent other computations to happen during that time.
* How to model/handle the failures.

### 3. REST API

**Task:** Define a simple REST API to offer the implementation of the **SimpleAsyncTokenService** developed previously. For its implementation we prefer you to use Akka HTTP, however, it's not mandatory and you can use other frameworks such as Play or Spring Boot.

We're interested on the structure and completeness of the API, so as how it is tested. Providing a [Swagger](https://swagger.io/) definition for the API is a plus.

## Deliverable
* A bundled/archived repository showing your commit history or a link to an accessible private repository with your work in (Github can host private repositories at a cost; there is no charge for doing so with Bitbucket). Git example for sending us a standalone bundle:

        git bundle create <yourname>.bundle --all --branches

* A Readme.md file explaining the decisions you've made solving this task including technology and library choices.
* Any instructions required to run your solution and tests in a Linux environment.

