# Template Method Pattern
This article is a key-points note for  

"Head First Design Patterns: Building Extensible and Maintainable Object-Oriented Software"
Jan. 12 2021 by Eric Freeman, Elisabeth Robson

## Summary
The Template Method Pattern defines the skeleton of an algorithm in a method, deferring some steps to subclasses.  
Template Method lets subclasses redefine certain steps of an algorithm without changing the algorithm’s structure.  

## Example of Coffee/Tea
![template_method](https://user-images.githubusercontent.com/98417271/220715936-edcbd1ce-d69c-4824-af74-4f8124d8bc20.png)

## Use Of Hook
![template_method_hook](https://user-images.githubusercontent.com/98417271/220718771-e03fcc4a-1428-4754-acf4-820193f21bee.png)


## Points
- A template method defines the steps of an algorithm, deferring to subclasses for the implementation of those steps.  
- Hooks are methods that do nothing or default behavior in the abstract class, but may be overridden in the subclass.  
- To prevent subclasses from changing the algorithm in the template method, declare the template method as final.  
