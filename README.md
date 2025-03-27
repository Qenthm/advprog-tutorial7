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
1. In the Observer pattern, traditionally the Subscriber is defined as an interface. In the BambangShop case, we could use a trait to define the behavior of Subscribers, but a single Model struct is sufficient because:
   - We have a single, consistent implementation of Subscribers
   - All subscribers interact with the system in the same way (receiving HTTP notifications)
   - The behavior is simple enough that we don't need multiple implementations of the Subscriber concept
   - Using a struct directly simplifies the code and aligns well with Rust's preference for concrete types when abstractions aren't needed

2. Using DashMap (map/dictionary) instead of Vec (list) is necessary in this case because:
   - We need to ensure uniqueness of IDs and URLs efficiently
   - Looking up subscribers by ID is a common operation that should be fast
   - DashMap provides O(1) lookup time compared to O(n) for Vec when searching for items
   - We need to perform frequent additions and removals which are more efficient with DashMap
   - DashMap provides thread-safety guarantees which are important in a web server context

3. DashMap is preferred over implementing a Singleton pattern because:
   - Rust's ownership system makes traditional Singleton implementation complex
   - DashMap already provides thread-safety guarantees that we would need to implement manually
   - DashMap handles concurrent access efficiently, which is crucial for a web application
   - Using a battle-tested library reduces the chance of subtle concurrency bugs
   - Rust encourages using its type system and ownership model rather than traditional OOP patterns when possible

#### Reflection Publisher-2
1. We need to separate "Service" and "Repository" from the Model for several important design principles:
   - **Single Responsibility Principle**: By separating repositories (data access) from services (business logic), each component has one clear responsibility. Repositories handle data persistence operations, while services handle business rules and operations.
   - **Separation of Concerns**: This separation creates clearer boundaries between data storage, business logic, and presentation, making the system more modular and easier to understand.
   - **Testability**: With separate layers, we can more easily create unit tests with mocks for each component, rather than needing to test everything together.
   - **Maintainability**: Changes to database implementation won't affect business logic, and vice versa, allowing teams to work in parallel and reducing the impact of changes.
   - **Scalability**: These layers can be scaled independently based on their specific needs (e.g., database connections vs. business processing).

2. If we only used the Model to handle everything (data storage, business logic, etc.):
   - **Coupling**: Program, Subscriber, and Notification models would become tightly coupled, with each needing to know too much about the others' implementations.
   - **Code Complexity**: Each model would grow unmanageably large, handling persistence, business rules, notifications, and more.
   - **Decreased Cohesion**: Models would lose their focused purpose, becoming catch-all entities responsible for too many different functions.
   - **Difficult Testing**: Testing would require setting up the entire ecosystem rather than isolating components.
   - **Example**: The Program model would need to manage subscriber lists, send notifications, handle HTTP calls, and maintain its own data, violating multiple design principles and creating a maintenance nightmare.

3. Exploring Postman has been valuable for testing this system:
   - **Request Collections**: Organizing related API calls together helps maintain structure during testing different components (subscribers, products, notifications).
   - **Environment Variables**: Setting up different environments (development, testing) makes it easy to switch between them without changing request URLs.
   - **Automated Testing**: The ability to write test scripts for responses ensures endpoints behave as expected consistently.
   - **Request Chaining**: Using data from previous responses in subsequent requests lets me test workflows, like subscribing and then testing notification delivery.
   - **Mock Servers**: For future projects, Postman's mock server capability could help front-end development progress independently of backend completion.
   - **Team Collaboration**: Sharing collections makes it easier to collaborate with team members who need to understand and test the API.

#### Reflection Publisher-3
