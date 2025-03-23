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
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Subscriber model struct.`
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [x] Commit: `Implement add function in Subscriber repository.`
    -   [x] Commit: `Implement list_all function in Subscriber repository.`
    -   [x] Commit: `Implement delete function in Subscriber repository.`
    -   [x] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [z] Commit: `Implement update method in Subscriber model to send notification HTTP requests.z
    -   [z] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [z] Commit: `Implement publish function in Program service and Program controller.`
    -   [z] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [z] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1

# Observer Pattern and Thread Safety in Rust

## 1. In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enough?

In the Observer pattern as explained in the Head First Design Pattern book, `Subscriber` is defined as an interface. However, for the BambangShop case, I think we don't need to use an interface (`trait`) and can manage with a single Model `struct`. 

The reason is that we currently only have one type of observer, which is the `Subscriber` class. Using a `trait` becomes valuable when we have multiple different types of observers that need to implement the same behavior pattern. 

## 2. `id` in Program and `url` in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?

Using DashMap is indeed the right choice. If Vec were used instead, it would require two separate arrays to store different id and url values, along with iterating through the array to find matching pairs. With DashMap, id and url can be stored in a single structure, making data management much more efficient. Additionally, DashMap supports concurrent access, ensuring safety if the application is deployed in a multi-threaded environment.

The BambangShop application utilizes multi-threading, which makes DashMap a better option than the Singleton pattern. DashMap is a thread-safe HashMap that supports concurrent access, allowing SUBSCRIBERS data to be accessed safely without synchronization issues. In contrast, the Singleton pattern would enforce a single instance throughout the program's execution, requiring explicit locking in a multi-threaded context. This not only increases complexity but also introduces the risk of deadlocks, making DashMap the more efficient and reliable choice.


## 3. When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?

In a multi-threaded environment, using DashMap remains the better choice over the Singleton pattern because it provides built-in thread safety without requiring explicit synchronization. While the Singleton pattern ensures a single shared instance, it does not inherently handle concurrent access, meaning we would need to manually implement synchronization using mutexes or read-write locks. This introduces additional complexity, increases the risk of deadlocks, and can lead to performance bottlenecks due to lock contention. On the other hand, DashMap is designed for concurrent access, handling race conditions efficiently while maintaining performance. Rust's strict compiler constraints already enforce thread safety, and leveraging a specialized data structure like DashMap aligns with Rust’s design philosophy by providing a safe, efficient, and maintainable solution without unnecessary manual synchronization.

#### Reflection Publisher-2

## 1. In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?

In the Model-View-Controller (MVC) compound pattern, the "Service" and "Repository" layers are not explicitly defined, as the Model is responsible for both data storage and business logic. However, separating the "Service" and "Repository" from the Model aligns with the Single Responsibility Principle (SRP), ensuring that each class has a distinct concern. By doing so, business logic and data access are decoupled, making the system more maintainable, modular, and scalable. This separation also simplifies testing and debugging, as changes to business logic or data access do not directly impact the Model.

## 2. What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?

If we only use the Model without separating the Service and Repository layers, the code will become increasingly complex and difficult to maintain. The tight coupling between different classes, such as `Program`, `Subscriber`, and `Notification`, will make modifications more challenging. Any changes to one part of the system could lead to a cascade of required changes across multiple components, increasing the risk of errors and making debugging more difficult. By not separating business logic from data access, scalability is also hindered, as the system becomes less flexible and harder to extend in the future.

## 3. Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.

With Postman, I can perform API testing. I can view the results of the requests I send and ensure that the responses I receive match my expectations. One of its supporting features is Collections, which allows requests to be grouped into folders, and Environments, which enables me to store frequently used variables.

#### Reflection Publisher-3

## 1. Observer Pattern has two variations: Push model (publisher pushes data to subscribers) and Pull model (subscribers pull data from publisher). In this tutorial case, which variation of Observer Pattern that we use?

The variation of the Observer Pattern used in this tutorial is the Push Model because the Publisher sends notifications to all its Subscribers whenever CRUD operations occur on a Product. This is done using the notify method in the NotificationService class.

## 2. What are the advantages and disadvantages of using the other variation of Observer Pattern for this tutorial case? (example: if you answer Q1 with Push, then imagine if we used Pull)

In the case of the Pull Model:

**Advantages**:

- The Observer/Subscriber has more control over the notifications it receives from the Publisher, allowing for greater flexibility.

**Disadvantages**:

- The Observer/Subscriber must actively check for updates. If it does not, it may miss important notifications since it needs to manually pull data from the Publisher.

## 3. Explain what will happen to the program if we decide to not use multi-threading in the notification process.

Without multi-threading, the notification process will execute sequentially, causing significant delays. The bottleneck occurs when the Publisher sends notifications to all Subscribers within the notify method in the NotificationService class. As a result, the notification process must complete before any other operations can proceed, leading to reduced performance and inefficiencies in handling multiple Subscribers.