# Techniques to isolate difficult dependencies from test code

<br>

## Add Backdoor (use conditional compilation to replace singleton)

*Before Adding Backdoor*
```swift

class Singleton {
  static let shared = Singleton()
  func doSomething() {}	
}

func main() {
  Singleton.shared.doSomething()
}

```

<br>

*Backdoor applied*
```swift

class MySingleton {
  static var shared: MySingleton {
#if DEBUG
    if let stubbedInstance = stubbedInstance {
      return stubbedInstance
    }
#endif
    return instance
  }
	
#if DEBUG
  static var stubbedInstance: MySingleton?
#endif
  
  static let instance = MySingleton()
  func doSomething() {}
}

func main() {
  MySingleton.stubbedInstance = MySingleton()
  MySingleton.shared.doSomething()
}

```


*Note*  
`Use this technique when you own singletons.`  
But in general, you should avoid mixing test code into production code.  
Conditional compilation makes code hard to read, reason about, and maintain.  
Dependency Injection Principles, Practices, and Patterns [^1] describes the singleton backdoor as an anti-pattern called Ambient Context.  
It’s far preferable to use other means of injection, especially constructor injection.  

<br>

## Subclass and Override (add a layer of indirection around singleton)

*Before Subclassing and Overriding*
```swift
class Act {
  static let shared = Act()
  func doSomething() {}
}

class ExampleVC: UIViewController {

  override func viewDidAppear(_ animated: Bool) {
    super.viewDidAppear(animated)
    Act.doSomething()
  }
  
}

class ExampleVCTests: XCTestCase {
  
  func test_viewDidAppear() {
    let sut = ExampleVC() // this contains singleton, violates isolation principle
      sut.loadViewIfNeeded()
      sut.viewDidAppear(false)
   }
    
}
```
<br>

*Subclassing and Overriding applied*
```swift

class Act {
  static let shared = Act()
  func doSomething() {}
}


class TestableOverrideVC: ExampleVC {
  
  override func act() -> Act {
    Act()
  }
	
}

class OverrideVCTests: XCTestCase {
  
  func test_viewDidAppear() {
    let sut = TestableOverrideVC() // singleton is isolated now
    sut.loadViewIfNeeded()
    sut.viewDidAppear(false)
  }
	
}

```

*Note*  
`Use this technique when do not you own singletons.`  

The idea is to create a subclass of production code that lives only in test code, or a test-specific subclass.  
It gives us a way to override methods that are problematic for testing.

Subclass and Override Method can only be applied to a class that permits subclassing:  
• Swift doesn’t allow subclassing of structs.  
• The final modifier prevents classes from having subclasses. Remove it to apply this technique.  
• Storyboard-based view controllers can’t be subclassed because the storyboard stores an instance of a predetermined type.  

<br>

## Inject Instances Through Properties

*Dependency injection(through property) applied*
```swift

class InstancePropertyVC: UIViewController {
  
  lazy var act = Act.shared
  
  override func viewDidAppear(_ animated: Bool) {
    super.viewDidAppear(animated)
    act.doSomething()
  }
	
}

class InstancePropertyVCTests: XCTestCase {
  
  func test_viewDidAppear() {
    let sut = InstancePropertyVC()
    sut.act = Act() // singleton is isolated now
    sut.loadViewIfNeeded()
    sut.viewDidAppear(false)
  }
	
}

```

<br>

## Extract protocol and inject Fake object

*Extract behaviour into protocol for a dependency(persistence, etc...)*
```swift

protocol UserDefaultsProtocol {
  func object(forKey defaultName: String) -> Any?
  func set(_ value: Any?, forKey defaultName: String)
  func value(forKey key: String) -> Any?
}

```

*Define Fake-class implementing the protocol*
```swift

class FakeUserDefaults: UserDefaultsProtocol {
  var store: [String: Any] = [:]
  
  func object(forKey defaultName: String) -> Any? {
    if let obj = store[defaultName] {
      return obj
    }
    
    return nil
  }
  
  func set(_ value: Any?, forKey defaultName: String) {
    store[defaultName] = value
  }
  
  func value(forKey key: String) -> Any? {
    if let val = store[key] {
      return val
    }
    
    return nil
  }
	
}

```

*Declare the dependency as a Protocol-type variable inside main Class*
```swift

class ExampleViewModel: ObservableObject {

  let userDefaults: UserDefaultsProtocol = UserDefaults.standard

}

```

*Replace dependency with Fake inside test code*
```swift

final class ExampleViewModelTests: XCTesst {

  private var sut: AppStateViewModel!
  
  override func setUpWithError() throws {
    try super.setUpWithError()
    sut = AppStateViewModel()
    sut.userDefaults = FakeUserDefaults()
  }
  
}

```

<br>

To be added  
explanation for utilizing Wrapper class (Adapter pattern) to replace built in method in Data class -> write method
explanation for asynchronous method tests (dataTask, completion handler)

[^1]: Steven van Deursen and Mark Seemann. Dependency Injection Principles, Practices, and Patterns. Manning Publications Co., Greenwich, CT, 2019.
