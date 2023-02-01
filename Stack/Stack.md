# Stack implementation

A stack is a collection that is based on the last-in-first-out (LIFO) policy. '
By tradition, we name the stack insert method push() and the stack remove operation pop(). We also include a method to test whether the stack is empty


```swift

struct ArrayStack<T>: Sequence {
  fileprivate var items: [T] = []
  
  public var isEmpty: Bool {
    return items.isEmpty
  }
  
  public var size: Int {
    return items.count
  }
  
  public mutating func push(item: T) {
    items.append(item)
  }
  
  public mutating func pop() -> T? {
    return items.popLast()
  }
  
  public func makeIterator() -> ReverseIndexIterator<T> {
    return ReverseIndexIterator<T>(self)
  }
  
}

struct ReverseIndexIterator<T>: IteratorProtocol {
  let stack: ArrayStack<T>
  var times = 0
  
  init(_ stack: ArrayStack<T>) {
    self.stack = stack
  }
  
  mutating func next() -> Int? {
    let nextIndex = stack.items.count - 1 - times
    guard nextIndex >= 0 else { return nil }
    times += 1
    
    return nextIndex
  }
  
}


```

https://introcs.cs.princeton.edu/java/43stack/
