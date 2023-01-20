Introducing MVVM Roles and Responsibilities
Before diving into refactoring, it’s essential you understand what the view model and view controller do in the MVVM pattern.

The view controller is only responsible for altering views and passing view inputs to the view model. 

In contrast, the view model is responsible for the following:

Model inputs: Taking view inputs and updating the model.
Model outputs: Passing model outputs to the view controller.
Formatting: Formatting model data for display by the view controller.




Data Binding
In MVVM, you need a way to bind view model outputs to the views. 
To do that, you need a utility that provides a simple mechanism for binding views to output values from the view model. 
There are several ways to do such bindings:

Key-Value Observing or KVO: A mechanism for using key paths to observe a property and get notifications when that property changes.
Functional Reactive Programming or FRP: A paradigm for processing events and data as streams. Apple’s new Combine framework is its approach to FRP. RxSwift and ReactiveSwift are two popular frameworks for FRP.
Delegation: Using delegate methods to pass notifications when values change.
Boxing: Using property observers to notify observers that a value has changed.
