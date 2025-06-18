# Introduction 
TODO: Give a short introduction of your project. Let this section explain the objectives or the motivation behind this project. 

# Welcome to the Guest Survey API!

This document is your guide to understanding our project's structure. We use a powerful architectural pattern called **Domain-Driven Design (DDD)** with **Clean/Onion Architecture**.

The main goal is simple: **to keep our code organized, easy to test, and easy to change.**

### The Big Idea: The House Analogy 🏠

Think of our API as a house built in layers:

*   **The Foundation (`Domain`):** The most important part. It holds all the business rules and logic. It's solid and doesn't care what the rest of the house looks like.
*   **The Walls & Structure (`Application`):** This defines the "rooms" or use cases of our house. It connects the foundation to the other parts.
*   **The Plumbing & Wiring (`Infrastructure`):** The technical details that make the house functional—the database, external APIs (like POEditor), etc.
*   **The Paint & Decor (`Api`):** The part the outside world sees. It's the "look and feel" (our HTTP endpoints) but it's just a thin layer on top.

---

### The Golden Rule: Dependencies Point Inwards

This is the most important rule of our architecture. The layers can only depend on layers closer to the center.

```
+-------------------------------------------------------------+
|  Survey.Api (The "Front Door")                              |
|   - Depends on Application & Infrastructure                 |
|   +-------------------------------------------------------+ |
|   |  Survey.Infrastructure (The "Toolbox")                | |
|   |   - Implements things Application needs               | |
|   |   - Knows about the database, POEditor, etc.          | |
|   |   - Depends on Application & Domain                   | |
|   +-------------------------------------------------------+ |
|   +-------------------------------------------------------+ |
|   |  Survey.Application (The "Brain")                     | |
|   |   - Orchestrates everything                           | |
|   |   - Contains the use cases (Commands & Queries)       | |
|   |   - Depends on Domain                                 | |
|   |   +-------------------------------------------------+ | |
|   |   |   Survey.Domain (The "Heart & Soul")            | | |
|   |   |    - Contains business rules and entities.      | | |
|   |   |    - Has ZERO dependencies on other projects.   | | |
|   |   +-------------------------------------------------+ | |
|   +-------------------------------------------------------+ |
+-------------------------------------------------------------+
```

**Why?** This protects our core business logic (`Domain`) from any changes in technology. We can swap our database or change a web framework without ever touching the core rules of our application.

---

## A Tour of the Projects (Layers)

### `Survey.Domain` - The Rulebook

This is the heart of our application. It contains the pure business logic and knows nothing about databases, the internet, or any other technical detail.

*   **`Aggregates/`**: A cluster of business objects that we treat as a single unit. For example, a `Response` and all its `SurveyAnswers` form an aggregate. The main entity is the "Aggregate Root" (`Response.cs`).
*   **`Contracts/Persistence/`**: Defines *what* we need to do with data (e.g., `IResponseRepository` says "we need a way to save a Response"), but not *how*.
*   **`DomainEvents/`**: A way to announce that something important happened in our system (e.g., `SurveyCompletedEvent`).

### `Survey.Application` - The Brain 🧠

This project orchestrates the work. It takes requests from the outside world and uses the `Domain` to fulfill them.

*   **`Features/`**: This is where most of the work happens! We organize our code by feature (like `Responses`, `Translations`).
*   **`Features/Commands/`**: A "Command" is a request to **change** something (e.g., `StartSurveyCommand`, `CompleteSurveyCommand`).
*   **`Features/Queries/`**: A "Query" is a request to **read** something (e.g., `GetSurveyStructureQuery`).
*   **`DTOs` (Data Transfer Objects)**: These are simple classes used to send data to and from the API layer. We don't want to expose our internal `Domain` models to the outside world.

### `Survey.Infrastructure` - The Toolbox 🛠️

This project contains all the implementation details—the "how." It's where we talk to the outside world.

*   **`Persistence/`**: All the database code lives here. This is where we use **Entity Framework Core**.
*   **`Persistence/Repositories/`**: The actual implementation of the interfaces we defined in the `Domain` layer (e.g., `ResponseRepository.cs` contains the code to save a `Response` to our PostgreSQL database).
*   **`ExternalServices/PoEditor/`**: The code that knows how to talk to the POEditor API.
*   **`BackgroundJobs/`**: Code for tasks that run on a schedule, like our job to sync translations from POEditor.

### `Survey.Api` - The Front Door

This is the thinnest layer. Its only job is to handle HTTP requests and responses. It acts as the entry point to our application.

*   **`Controllers/`**: Receives incoming web requests. A controller's job is to be as "dumb" as possible. It should immediately create a `Command` or `Query` and pass it to the `Application` layer to handle.
*   **`Middleware/`**: Code that runs on every single request, like for handling errors globally.
*   **`Program.cs`**: The starting point of the whole application. This is where we configure everything and tell the system how to "wire up" all our services (this is called Dependency Injection).

---

## "Where do I put my code?" - A Quick Guide

Here’s a cheat sheet for where to add new code:

| If you need to...                                       | Add it in...                                      |
| ------------------------------------------------------- | ------------------------------------------------- |
| Add a **new business rule** (e.g., a survey can't be empty) | a method inside a `Domain` entity (`Response.cs`). |
| Create a **new page or use case** (e.g., "Archive a Survey") | a new `Command` or `Query` in the `Application` layer. |
| Talk to a **new external service** (e.g., sending an SMS)   | a new service in the `Infrastructure` layer.       |
| Change **how data is saved** to the database            | a `Repository` in the `Infrastructure` layer.      |
| Add a **new HTTP endpoint** (e.g., `GET /api/v2/surveys`)   | a new method in a `Controller` in the `Api` layer. |

<br>

We hope this guide helps you get started. Don't hesitate to ask questions. Welcome aboard, and happy coding

# Getting Started
TODO: Guide users through getting your code up and running on their own system. In this section you can talk about:
1.	Installation process
2.	Software dependencies
3.	Latest releases
4.	API references

# Build and Test
TODO: Describe and show how to build your code and run the tests. 

# Contribute
TODO: Explain how other users and developers can contribute to make your code better. 

If you want to learn more about creating good readme files then refer the following [guidelines](https://docs.microsoft.com/en-us/azure/devops/repos/git/create-a-readme?view=azure-devops). You can also seek inspiration from the below readme files:
- [ASP.NET Core](https://github.com/aspnet/Home)
- [Visual Studio Code](https://github.com/Microsoft/vscode)
- [Chakra Core](https://github.com/Microsoft/ChakraCore)