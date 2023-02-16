# Strategy Pattern
This article is a key-points note for  

"Head First Design Patterns: Building Extensible and Maintainable Object-Oriented Software"
Jan. 12 2021 by Eric Freeman, Elisabeth Robson


## Summary
The Strategy Pattern defines a family of algorithms, encapsulates each one, and makes them interchangeable.  
Strategy lets the algorithm vary independently from clients that use it.

## Example Of Duck
**In case abstruct Duck class implements its behaviour inside the class**

Problems  
- All subclasses must implement behaviour defined in the Abstract class
- A subclass that can't perform a behaviour must implement the behaviours defined in the Abstract class

**In case you extract Duck behaviours into interface and make each concrete Duck object implement the interface**  

Problems  
- When multiple duck object behave in a same manner, for each of them you must duplicate the behaviour.


### Applying Strategy Pattern
How?    
- Extract Duck behaviours into seperate interfacte.  
- Declare the behaviour property as the interface type in Duck class, and assign a Behaviour object as its property.  
- Define setter for the behaviour in Duck class.

Merits  
When some duck objects behave in a same manner, you can reuse a behaviour object that implemented the behaviour interface.  
You can replace duck behaviour in runtime.  
