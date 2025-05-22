# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1

1. Do we still need a trait in BambangShop, or is a single Model struct enough? In typical Observer pattern setups, a trait or interface is used to define a common behavior—like an update() method—for all observers. But in BambangShop, the subscribers aren’t doing anything internally; they’re just receiving HTTP POSTs from Rocket. Since there's no shared behavior that needs to be coded, we don’t really need a trait. A simple Subscriber struct with fields like url and name is enough.

2. Is a Vec enough, or should we use DashMap to keep track of subscribers? A Vec works, but it’s not ideal—especially if you want to prevent duplicate IDs or URLs. You’d have to manually check for duplicates every time you add something, which can get slow as the list grows. DashMap is a better fit here. It gives you fast lookups and naturally enforces uniqueness through keys. On top of that, it’s thread-safe—super useful when your app handles multiple requests at once.

3. Can we just use the Singleton pattern instead of DashMap? Using a Singleton ensures you’ve got only one instance of your subscribers list, but it doesn’t solve everything. Singleton by itself doesn’t help with safe concurrent access. DashMap, on the other hand, can be used as a Singleton (with something like lazy_static!), and it’s already built to handle multiple threads safely. So in this case, DashMap is the more complete solution.

#### Reflection Publisher-2

1. Why should “Service” and “Repository” be separated from the Model in the MVC pattern?
Separating the Service and Repository layers from the Model follows the Single Responsibility Principle (SRP)—each part of the code should focus on just one job.

The Repository takes care of data operations like saving, retrieving, and updating records. It hides the complexity of how data is stored or fetched.

The Service layer handles business logic, keeping it separate from database operations.

This kind of separation keeps your code cleaner and more manageable. It also makes it easier to test and update each layer independently without breaking the rest of the system.

2. What happens if we only use the Model?
If the Model is made to handle everything—from data access to business rules—it becomes too complex and tightly coupled.

The Model would have to manage database interactions, validations, and application logic all at once, which makes it harder to read and maintain.

Common logic like validation or sending notifications might get repeated in multiple places, leading to inconsistency.

It also makes testing and scaling more difficult because everything is bundled together.

Overall, skipping the Service and Repository layers makes the system less flexible and harder to maintain.

3. How does Postman help with testing APIs?
Postman is a useful tool for testing REST APIs because it lets you send requests and check responses without needing to build a full frontend.

Key features include:

Collections: You can group related endpoints and test them easily.

Environment Variables: Switch between development and production setups without rewriting requests.

Automated Tests: You can write scripts to validate responses and catch issues early.

Mock Servers: Useful for simulating API responses when the backend isn’t ready.

These tools make Postman really helpful for both debugging and ensuring your APIs work as expected throughout development.

#### Reflection Publisher-3

1. Which version of the Observer Pattern is used in this tutorial?
This tutorial applies the Push model of the Observer Pattern. In this version, the publisher (BambangShop) actively sends updates to each subscriber using HTTP POST requests that contain relevant information, like product changes or status updates.

2. What are the benefits and drawbacks of the Pull model instead?
The Pull model flips the flow—subscribers are the ones who ask the publisher for updates when they need them.

Pros:

Subscribers can control when they fetch data, which helps avoid unnecessary traffic.

It reduces the workload on the publisher since it doesn’t have to notify everyone automatically.

Cons:

Subscribers must handle the logic to periodically check for updates, which adds complexity.

Updates aren’t immediate—there’s always a delay depending on how often polling happens.

It’s not ideal for real-time notifications, since important changes might be missed between checks.

For this tutorial’s case, where immediate alerts are important, the Pull model wouldn’t work well.

3. What’s the impact of not using multi-threading in the notification system?
If multi-threading is skipped:

Notifications go out one by one, so if one subscriber responds slowly, it holds up the others.

The main thread gets blocked while sending out updates, which can slow down the whole app.

As the number of subscribers grows, the delays stack up, making the system less efficient.

Multi-threading solves this by allowing notifications to be sent in parallel, making the system faster and more scalable, especially when handling many subscribers at once.