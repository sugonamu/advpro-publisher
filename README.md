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

## 1. Do we need an interface (trait) in this case?

Yes, using a trait (Rust’s equivalent of an interface) is still recommended in the BambangShop case. The main reason is to stay aligned with the intent of the Observer design pattern, which emphasizes decoupling the Publisher from the concrete implementations of its Subscribers.

By using a trait, we ensure that the Publisher only depends on the trait abstraction, not on specific subscriber types. This makes the system easier to extend in the future if we want to support different kinds of subscriber behavior (e.g., logging, database storage, or various notification methods). Even if the current implementation only uses one type of subscriber, applying the trait still follows good design principles and prepares the system for future flexibility.

## 2. Is using Vec sufficient or is DashMap necessary?

Using a simple Vec would be sufficient if the application were guaranteed to be single-threaded. However, since the application may involve concurrent HTTP requests (especially when using Rocket), there could be multiple threads accessing the list of subscribers at the same time.

In this case, using a DashMap is more appropriate because it is a concurrent map that allows safe and efficient read and write access across multiple threads. It also allows for fast lookup using unique identifiers like `url` or `id`. Therefore, DashMap is necessary to maintain thread safety and performance in a concurrent environment.

## 3. Do we still need DashMap or can we use the Singleton pattern instead?

The Singleton pattern and DashMap serve different purposes. Singleton provides a single shared instance of an object, which is useful for global access. However, it does not guarantee thread safety on its own.

DashMap, on the other hand, is specifically designed to allow thread-safe access to a shared collection. In this case, the best approach is to combine both patterns: use a Singleton to create a globally accessible subscriber registry, and use DashMap within that Singleton to handle concurrent access.

Thus, The Singleton pattern does not replace DashMap. We still need DashMap to ensure thread safety, and Singleton helps manage global access to that DashMap instance.


#### Reflection Publisher-2


### Why do we need to separate "Service" and "Repository" from a Model?

Separating the Service and Repository layers from the Model aligns with the Single Responsibility Principle. In this design, each component has a specific role: the Model represents the data structure, the Repository handles data access (e.g., querying or persisting data to a database), and the Service layer contains business logic.

By separating these layers, we make the system more modular, easier to maintain, and easier to test. Changes to business logic won't affect data access code, and vice versa. This separation also improves flexibility, allowing us to replace or mock components independently in testing or during refactoring.

### What happens if we only use the Model?

If we only use the Model and put both data access and business logic inside it, the code becomes harder to manage and violates the Single Responsibility Principle. Each Model becomes tightly coupled with data operations and logic, leading to poor separation of concerns.

As the application grows and more interactions occur between Program, Subscriber, and Notification models, the complexity increases. Each model would need to be aware of how to interact with other models directly, which leads to code duplication, less maintainability, and potential circular dependencies.

For example, if the Program model manages subscribers and also sends notifications, it might end up depending on all other models. This makes it difficult to change one part of the application without affecting others.

### How does Postman help with testing?

Postman is very helpful in testing REST APIs. It allows me to send HTTP requests to specific endpoints and immediately view the response. This is useful for checking whether the application works as expected without having to build a front-end interface.

Some helpful features I’ve used:
- Organizing endpoints into collections for easy access and grouping by functionality
- Reusing environment variables (e.g., `{{host}}`) for switching between development and production
- Viewing detailed response bodies, status codes, and headers
- Exporting and sharing collections with teammates

Postman is especially helpful for collaborative projects or testing APIs that integrate with external systems. I can see how this tool would continue to be useful in future projects, especially for debugging and verifying back-end logic.

#### Reflection Publisher-3


### Which variation of Observer Pattern is used in this tutorial case?

In this tutorial, we use the **Push model** of the Observer Pattern. The Publisher (main application) pushes product data directly to all registered Subscribers by sending HTTP POST requests to their specified URLs when a new product is added.

### What are the advantages and disadvantages of using the other variation (Pull model)?

If we used the **Pull model**, the Subscribers would be responsible for contacting the Publisher to fetch new data, such as periodically requesting the latest product updates.

**Advantages of Pull model:**
- Subscribers have more control over when and how often they retrieve data.
- The Publisher becomes simpler since it doesn't need to notify each Subscriber.
- Can reduce load on the Publisher if Subscribers poll infrequently.

**Disadvantages of Pull model:**
- Data may be outdated on the Subscriber side due to delayed polling.
- More complex scheduling logic is required on each Subscriber.
- Increases network traffic if many Subscribers poll frequently.
- Does not support real-time updates effectively, unlike Push.

In this case, using Push is more suitable because we want Subscribers to get real-time notifications immediately when a new product is published.

### What happens if we do not use multi-threading in the notification process?

If we do not use multi-threading or asynchronous execution during the notification process, the Publisher will notify each Subscriber one by one, blocking the main thread until all HTTP requests are completed. This introduces several issues:
- The product creation endpoint becomes slow and unresponsive if there are many Subscribers or if any of them are slow to respond.
- A single slow or failed Subscriber can delay the entire process.
- The system becomes less scalable and harder to maintain real-time
