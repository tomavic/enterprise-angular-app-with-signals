# Architecting Enterprise Angular Application

Let’s accept we all have been in a situation where in we are developing the application, in the initial days the development goes really fast, as the features are simple and easy to understand, But as the app size grows, the speed of development slows down drastically.
Adding a new feature or fixing a bug with large scale enterprise application without proper design and architecture becomes a nightmare.

Now we know the importance of a good architecture, lets discuss one of the elegant and commonly used pattern for architecting enterprise angular application called “Layered Architecture”

## Layered Architecture

The idea behind this pattern is to split the app into different layers and define the rules for communicating between the layers.

> In Layered architecture there is only one main rule i.e. The layers below will talk to only the layer above and it will not communicate with any other layers

Now lets discuss the different layers in this pattern. There are 3 major layers in this pattern namely

1- Core Layer : contains application core logic. e.g. data manipulation, calling APIs to get data and other business logic.
2- Abstraction Layer : intermediate layer which handles communication between presentation and core layer.
3- Presentation Layer : this layer is used for displaying UI elements and handling user interactions.

![image](https://github.com/tomavic/angular-state-managment-with-signals/assets/17650005/0be2d520-16fa-4a61-a290-8d7d24a967d3)



Now lets take a closer look

Presentation/UI Layer :
This is the place where all our Angular components live. This layer is responsible for application’s user interface, it presents the UI and delegates user’s actions to the core layer, through the abstraction layer. Presentation layer just knows how to display the data and it does not worry about how the data is fetched.

Presentation layer should care only for the presentation and not be putting their hands into the core logic.

Presentation layer depends on the Facade layer (above layer),to get data and it should never directly interact with core layer.

By having this kind of decoupling UI layer from core layer we get following benefits
- UI components will be much easier to test because we don’t need to inject core or async services into our tests.
- Easier to split up into multiple developer tasks.

While developing UI components, we should classify and categorize UI components into 2 categories namely

### Dumb/Presentational Components 
These components does not have intelligence of their own, they depend on parent component to give them data and they pass any user interaction back to parent component.

Use @Input() to get the data from parent component and @Output() to pass events/data back to parent.

### Smart/Container Components 

Container components usually wraps one or more dumb/presentational components and is responsible for providing the data and handling the interactions from children component.
Container components have a complete idea of the current functionality and they know where to get the data from and how to handle the events from children component.

Container components interact with Facade layer to get the state data and pass them to children components.

## Abstraction Layer

The Abstraction layer decouples the presentation layer from the core layer and also has it’s own defined responsibilities. This layer exposes the state data for the components in the presentational layer, playing a role of a Facade.

We should never inject API providers(services that make http calls) and state providers directly into our components in the UI layer, instead we should inject the facade service and communicate with core layer via facade layer.

Having this kind of abstraction (middle layer) gives us lots of flexibility and allows us to change the way we manage state or API data without touching the presentation/UI layer.

**We should also remember that the abstraction layer is not the place to implement business logic. Here we just connect the presentation layer to business logic, abstracting the way it is connected.**

## Core Layer

This is the top most layer, which implements the core application logic. It is responsible for all the data manipulation and outside world communication via APIs.

It majorly consists of state management & Async service(for calling rest APIs). Along with state management and async service we could also place any validators, mappers or other more advanced use-cases that requires manipulating many slices of our UI state.

Lets take a closer look at 2 of the most important parts of the core layer.

### State Management 

A state is a JavaScript object which holds the application data structure. Here we can store the data needed to display in the presentation layer e.g. list of products, information about logged in user etc. Since data is shared and manipulated by lots of components it becomes very hard to track the changes.

Predictable application state is essential in order to avoid confusions and to manage different versions of state with different data across your application.

So it is very important to manage the state in our applications. To manage the state in our Angular application we can pick any state management library that support RxJS (like NgRx) or simply use BehaviorSubjects to model state.

One thing to remember with respect to State is that State objects are immutable and they are returned by a pure function.

We can start with BehaviorSubjects to manage the state, and later if there is a need to replace State management with some other library, thanks to layered architecture we can simply replace the state management with new library without impacting any other parts of the application.

### Async/API Service

API service have only one responsibility — that is to communicate with API(REST) end points and nothing else.

We should avoid any caching, business logic or data manipulation here.

**Don’t let the async service know about the state management logic.**

May be it comes naturally to save the response into the state right in the service, but in that case, we tightly coupled communication layer with data management layer, so we should always avoid updating state from this service.
