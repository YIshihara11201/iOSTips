# Queue implementation

The Queue class represents a first-in-first-out (FIFO) queue of generic items.
It supports the usual *eunque* and *dequeue* operations, along with methods for peeking at the first item, testing if the queue is empty, and iterating through the items in FIFO order.

## Array implementation of Queue.
```swift

public struct Queue<E>: Sequence {
	private var initCapacity: Int = 8;
	
	private var q: [E]
	private var n: Int
	private var first: Int
	
	public init() {
		q = []
		n = 0				// number of elements on queue
		first = 0			// index of first element of queue
	}
	
	public var isEmpty: Bool {
		return n == 0
	}
	
	public var size: Int {
		return n
	}
	
	public mutating func enqueue(item: E) {
		q.append(item)
		n += 1
	}
	
	public mutating func dequeue() -> E? {
		if isEmpty {
			return nil
		}
		
		let res = q[first]
		n -= 1
		first += 1
		
		return res
	}
	
	public func peek() -> E? {
		if isEmpty {
			return nil
		}
		return q[first]
	}
	
	public func makeIterator() -> QueueIterator<E> {
		return QueueIterator<E>(queue: self)
	}
	
	public struct QueueIterator<E>: IteratorProtocol {
		let queue: Queue<E>
		var idx = 0
		
		public func hasNext() -> Bool {
			return idx < queue.n
		}
		
		mutating public func next() -> E? {
			if !hasNext() {
				return nil
			}
			
			let item = queue.q[idx+queue.first]
			idx += 1
			
			return item
		}
	}
	
}


```

## Linked-list implementation of Queue.
```swift
public final class Queue<E>: Sequence {
	
	private var first: Node<E>? = nil
	private var last: Node<E>? = nil
	private(set) var count: Int = 0
	
	/// helper linked list node class
	fileprivate class Node<E> {
		fileprivate var item: E
		fileprivate var next: Node<E>?
		fileprivate init(item: E, next: Node<E>? = nil) {
			self.item = item
			self.next = next
		}
	}
	
	public init() {}
	
	public func isEmpty() -> Bool {
		return first == nil
	}
	
	public func peek() -> E? {
		return first?.item
	}
	
	public func enqueue(item: E) {
		let oldLast = last
		last = Node<E>(item: item)
		if isEmpty() { first = last }
		else { oldLast?.next = last }
		count += 1
	}
	
	public func dequeue() -> E? {
		if let item = first?.item {
			first = first?.next
			count -= 1
			// to avoid loitering
			if isEmpty() { last = nil }
			return item
		}
		return nil
	}
	
	public struct QueueIterator<E>: IteratorProtocol {
		private var current: Node<E>?
		
		fileprivate init(_ first: Node<E>?) {
			self.current = first
		}
		
		public mutating func next() -> E? {
			if let item = current?.item {
				current = current?.next
				return item
			}
			return nil
		}
		
		public typealias Element = E
	}
	
	public func makeIterator() -> QueueIterator<E> {
		return QueueIterator<E>(first)
	}
}

extension Queue: CustomStringConvertible {
	public var description: String {
		return self.reduce(into: "") { $0 += "\($1) " }
	}
}

```
