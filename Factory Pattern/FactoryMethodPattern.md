# Factory Method Pattern
This article is a key-points note for  

"Head First Design Patterns: Building Extensible and Maintainable Object-Oriented Software"
Jan. 12 2021 by Eric Freeman, Elisabeth Robson

## Summary
The Factory Method Pattern defines an interface for creating an object, but lets `subclasses` decide which class to instantiate. Factory Method lets a class defer instantiation to subclasses.  

<img width="637" alt="factorymethod" src="https://user-images.githubusercontent.com/98417271/220463406-d793f5dd-711c-428b-8c8b-69d1681ab05d.png">

## Example Of Pizza Store
<img width="649" alt="pizza_factory" src="https://user-images.githubusercontent.com/98417271/220470068-4bcdd1ff-cb98-4150-9a5a-d4e9fd485554.png">

### Dependency Indirection Principle with Factory Method Pattern
`DIP: Depend upon abstractions. Do not depend upon concrete classes.`  
is achieved as the following.  

<img width="530" alt="factoryMethod_DIP" src="https://user-images.githubusercontent.com/98417271/220469211-89582e5f-bd88-4ed2-b83e-07933e12f309.png">

## Points
- Factory Method relies on inheritance: object creation is delegated to subclasses, which implement the factory method to create objects.
- Creator class is written without knowledge of the actual products that will be created
