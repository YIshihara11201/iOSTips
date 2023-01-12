# Factory Pattern

Factory method is a creational design pattern which solves the problem of creating product objects without specifying their concrete classes.

```swift
protocol Creator {

    func factoryMethod() -> Product

    /// Creator's primary responsibility is not creating products. 
    /// Usually, it contains some core business logic that relies on Product objects, returned by the factory method.
    /// Subclasses can indirectly change that business logic by overriding the
    /// factory method and returning a different type of product from it.
    func someOperation() -> String
}

/// This extension implements the default behavior of the Creator. This behavior can be overridden in subclasses.
extension Creator {

    func someOperation() -> String {
        let product = factoryMethod()
        return "Creator: The same creator's code has just worked with " + product.operation()
    }
}

/// Concrete Creators override the factory method in order to change the resulting product's type.
class ConcreteCreator1: Creator {

    /// Note that the signature of the method still uses the abstract product type, 
    /// even though the concrete product is actually returned from the method. 
    /// This way the Creator can stay independent of concrete product classes.
    public func factoryMethod() -> Product {
        return ConcreteProduct1()
    }
}

class ConcreteCreator2: Creator {

    public func factoryMethod() -> Product {
        return ConcreteProduct2()
    }
}

/// The Product protocol declares the operations that all concrete products must implement.
protocol Product {

    func operation() -> String
}

class ConcreteProduct1: Product {

    func operation() -> String {
        return "{Result of the ConcreteProduct1}"
    }
}

class ConcreteProduct2: Product {

    func operation() -> String {
        return "{Result of the ConcreteProduct2}"
    }
}


/// The client code works with an instance of a concrete creator, albeit through its base protocol. 
/// As long as the client keeps working with the creator via the base protocol, you can pass it any creator's subclass.
class Client {
    // ...
    static func someClientCode(creator: Creator) {
        print("Client: I'm not aware of the creator's class, but it still works.\n"
            + creator.someOperation())
    }
    // ...
}
```


https://refactoring.guru/design-patterns/factory-method/swift/example#example-0
