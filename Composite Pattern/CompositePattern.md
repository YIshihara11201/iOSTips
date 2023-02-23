# Composite Pattern
This article is a key-points note for  

"Head First Design Patterns: Building Extensible and Maintainable Object-Oriented Software"
Jan. 12 2021 by Eric Freeman, Elisabeth Robson

## Summary
The Composite Pattern allows you to compose objects into tree structures to represent part-whole hierarchies.  
Composite lets clients treat individual objects and compositions of objects uniformly.  

![composite_diagram](https://user-images.githubusercontent.com/98417271/220788657-19e21858-4d1f-430e-8168-9a2962bb45f4.png)

## Example Of Restaurant Menu
![composite_example](https://user-images.githubusercontent.com/98417271/220793918-d7cbafb4-a5b6-489c-9bb9-5974d1c19708.png)

## Points
- You use this pattern when you have collections of objects with whole-part relationships and you want to be able to treat those objects uniformly.
- All components must implement the *Component* interface; however, because leaves and nodes have different roles we can’t always define a default implementation for each method that makes sense. Sometimes the best you can do is throw a runtime exception.
