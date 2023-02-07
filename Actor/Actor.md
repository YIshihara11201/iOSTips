# Actor
This is keypoint note for 
"iOS 15 Programming Fundamentals with Swift, by Matt Neuburg, Released October 2021"

With Actor, you can specify whether code should run on the main thread or on a background thread.  
`By default, an actor’s code runs on a background thread`.  
It helps your multithreaded code run safely.  

*Note*  
Threads exist at a far lower level than the structured concurrency features built into the Swift language.  
Structured concurrency is generally free to assign your code to any thread it chooses.  
Indeed, structured concurrency is free to change what thread your code runs on.  
In particular, when your code says await, the thread on which your code runs after waiting and resuming might not be the same thread on which it was running before waiting. The decision about what thread to use belongs to a task; a task releases the current thread when it pauses at await, freeing it up for other purposes, and can use a different thread when it resumes afterward.  

## Actor Serialization  
While an actor’s code is running, no other code belonging to the same actor can start.  
This solves the problem of simultaneous access: with an actor, there is no simultaneous access.  
```swift
actor MyActor {
  let id = UUID().uuidString // random unique identifier
  var myProperty: String
  init(_ myProperty: String) {
    self.myProperty = myProperty
  }
  
  func changeMyProperty(to newValue: String) {
    self.myProperty = newValue
  }
}

func test() async {
  let act = MyActor("howdy")
  let id = act.id // legal
  let prop = await act.myProperty // legal but requires await
  await act.changeMyProperty(to: "hello") // legal but requires await
  await act.myProperty = "hello" // compile error: illegal
}
```

## Main Actor
If you want to ensure that code runs on the main thread, you isolate it to the main actor.  
The primary way to do that is with the @MainActor attribute.  
You can attach the @MainActor attribute:  
```swift
actor MyActor {
  let id = UUID().uuidString // random unique identifier
  
  @MainActor var myProperty: String
  init(_ myProperty: String) {
    self.myProperty = myProperty
  }
  
  @MainActor func changeMyProperty(to newValue: String) {
    self.myProperty = newValue
  }
}
```

## Context Switching
A change in thread context: code that has been running on the main thread proceeds to run on a background thread, or vice versa.  
`When you call the Task initializer, the task inherits the characteristics of the surrounding context.`

### Implicit context switching
Suppose that you want a particular method to run on a background thread.  
To make a method run on a background thread, we can isolate it to an actor. 
```swift
class ViewController: UIViewController {
  actor MyDownloader {
    func download(url: URL) async throws -> Data {
      // ...
    }  
    func fetchManyURLs() async throws -> [URL:Data] {
      // ...
    }
  }
  
  let downloader = MyDownloader()
  override func viewDidLoad() {
    super.viewDidLoad()
    Task {
      // runs on the main thread
      // but fetchManyURLs runs on a background thread
      let result = try? await self.downloader.fetchManyURLs()
      // ...
    }
  }
  
}
```

### Explicit Context Switching

  - main to background  
  Task.detached cuts off any relationship between the resulting Task object and the surrounding context, so the operation: function runs on its own background thread:
```swift
class ViewController: UIViewController { // marked @MainActor
  override func viewDidLoad() {
    super.viewDidLoad() // runs on the main thread
      Task.detached {
      // runs on a background thread
      let result = try? await self.fetchManyURLs()
      // ...
    }
  }
}
```

  - background to main (Example of UIImageView)  
  UIImageView is an interface class; it is isolated to the main actor. So we can’t mutate its image property from a background thread, and the compiler stops us with an error: “Property image isolated to global actor MainActor can not be mutated from a non-isolated context.”

```swift
class ViewController: UIViewController {
  var imageView : UIImageView?
  actor MyDownloader {
    // ...
  }
  
  let downloader = MyDownloader()
  override func viewDidLoad() {
    super.viewDidLoad()
    Task.detached {
    // runs on a background thread
    let url = URL(string: "https://www.apeth.com/pep/manny.jpg")!
    if let data = try? await self.downloader.download(url: url) {
      if let image = UIImage(data: data) {
        self.imageView?.image = image // compile error
      }
    }
  }
  
}
```  
  You can call the special MainActor run static method to switch context like the following.  
```swift
await MainActor.run { 
  self.imageView?.image = image
}
```  
  warning: that sort of deliberate context switching to the main thread can be expensive. 


