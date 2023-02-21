# Observer Pattern
This article is a key-points note for  

"Head First Design Patterns: Building Extensible and Maintainable Object-Oriented Software"
Jan. 12 2021 by Eric Freeman, Elisabeth Robson

## Summary
The Observer Pattern defines a one-to-many dependency between objects so that when one object changes state, all of its dependents are notified and updated automatically.

![observer_diagram](https://user-images.githubusercontent.com/98417271/220279913-7b266819-6b9f-4538-bb07-02e9b52a4598.png)

## Example Of Weather Monitoring Station
![observer_class](https://user-images.githubusercontent.com/98417271/220290143-acf65320-4749-4d90-b7c1-38e8fd11f047.png)

## Pull data implementation
![observer_pull](https://user-images.githubusercontent.com/98417271/220290629-55cfaddc-82d9-4e51-9c21-3ba98998473a.png)

## Points
- Observers are loosely coupled in that the Subject knows nothing about them, other than that they implement the Observer interface.  
- You can push or pull data from the Subject when using the pattern (pull is considered more “correct”).  

