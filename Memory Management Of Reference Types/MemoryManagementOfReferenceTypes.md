# Memory Management Of Reference Types

This is keypoint note for "iOS 15 Programming Fundamentals with Swift, by Matt Neuburg, Released October 2021"

When two object referes to (owns) each othre, Reference Cycle occurs.  
And to avoid this problem, There are two quealifiers `weak`, and `unowned`.

## unowned keyword
As with the weak keyword, this indicates the qualified variable won't increase retain count.
The difference between weak and unowned is that variable qualified with unowned is not optional.
You must be careful that unowned reference must have a reference (not nil) on accessing.

```swift
func testUnowned() {
  class Boy {
    var pet : Pet?
    
    deinit {
      print("farewell from Boy")
    }
  }
  
  class Dog {
    unowned let boy : Boy // Pet is an animal tamed by a human, thus you can assume a pet will always have an owner
    
    init(boy:Boy) { self.boy = boy }
    deinit {
      print("farewell from Pet")
    }
  }
  
  let b = Boy()
  let p = Pet(boy: b)
  b.pet = p
}

testUnowned() // farewell from Boy, farewell from Pet
```
