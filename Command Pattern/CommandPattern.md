# Command Pattern
This article is a key-points note for  

"Head First Design Patterns: Building Extensible and Maintainable Object-Oriented Software"
Jan. 12 2021 by Eric Freeman, Elisabeth Robson

## Summary
The Command Pattern encapsulates a request as an object, thereby letting you parameterize other objects with different requests, queue or log requests, and support undoable operations.

<img width="500" alt="command_diagram" src="https://user-images.githubusercontent.com/98417271/220520999-c6589ec4-7eae-4fde-ab6e-7af7965e7bbb.png">

A command object encapsulates a request by binding together a set of actions on a specific receiver.  
To achieve this, `it packages the actions and the receiver into an object` that exposes just one method, execute().  
From the outside(including Invoker), no other objects really know what actions get performed on what receiver.  

<img width="200" alt="command_image" src="https://user-images.githubusercontent.com/98417271/220520328-e96a31a0-5477-40d3-a97f-638f388e96ac.png">

## Points
- A Command object is at the center of this decoupling and encapsulates a receiver with an action (or set of actions).  
- An invoker makes a request of a Command object by calling its execute() method, which invokes those actions on the receiver.  
- Invokers can be parameterized with Commands, even dynamically at runtime.  
