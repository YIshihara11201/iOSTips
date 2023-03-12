# Sendable
This is keypoint note for "iOS 15 Programming Fundamentals with Swift, by Matt Neuburg, Released October 2021". 

Sendable is a protocol that ensures types conforms to it are safe to share across threads.

*example of unsafe sharing of an object*
```swift
class Dog {
  var name = "Rover"
}
actor MyActor {
  let dog = Dog()
  func getMyDog() -> Dog? { self.dog }
}

func test() async {
  let act = MyActor()
  let dog1 = await act.dog // should be illegal
  let dog2 = await act.getMyDog() // should be illegal
   // ...
}
```
In above code, if multiple tasks call tests function, they will have the reference to the dog object, which can lead to data racing.
