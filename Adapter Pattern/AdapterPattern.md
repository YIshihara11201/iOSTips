# Adapter Pattern
This article is a key-points note for  

"Head First Design Patterns: Building Extensible and Maintainable Object-Oriented Software"
Jan. 12 2021 by Eric Freeman, Elisabeth Robson

## Summary
The Adapter Pattern `converts` the interface of a class into another interface the clients expect. Adapter lets classes work together that couldn’t otherwise because of incompatible interfaces.


## Object Adapter
![object_adapter](https://user-images.githubusercontent.com/98417271/220535680-7c2a33d3-d184-4f42-8ab9-ab6f4d5af31f.png)

## Class Adapter
![class_adapter](https://user-images.githubusercontent.com/98417271/220535689-e47fba73-d31b-46ba-9de8-6a91e2722094.png)  
Note: For Java, multiple inheritance is impossible

## Points
- When you need to use an existing class and its interface is not the one you need, use an adapter.  
- An adapter wraps an object to change its interface, a decorator wraps an object to add new behaviors and responsibilities, and a facade “wraps” a set of objects to simplify.  
- There are two forms of the Adapter Pattern: object and class adapters. Class adapters require multiple inheritance.  
