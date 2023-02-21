# Decorator Pattern
This article is a key-points note for  

"Head First Design Patterns: Building Extensible and Maintainable Object-Oriented Software"
Jan. 12 2021 by Eric Freeman, Elisabeth Robson

## Summary
The Decorator Pattern attaches additional responsibilities to an object dynamically.  
Decorators provide a flexible alternative to subclassing for extending functionality.

<img width="635" alt="decorator_pattern" src="https://user-images.githubusercontent.com/98417271/220439392-a6173556-4e53-46fa-9541-62c335b7ffba.png">

## Example Of Coffee Menu
<img width="636" alt="decorator_diagram" src="https://user-images.githubusercontent.com/98417271/220445095-942abaa9-ef92-469a-9cda-ebfc3c08e8a4.png">



## Points
- Decorators have the same supertype as the objects they decorate. And you can usually insert decorators transparently and the client never has to know it’s dealing with a decorator.  
- You can implement new decorators at any time to add new behavior. If you relied on inheritance, you’d have to go in and change existing code anytime you wanted new behavior.  
- Downsides of the Decorator Pattern: designs using this pattern often result in a large number of small classes. 

