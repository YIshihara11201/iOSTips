# Asynchronous Programming
Before async/await, asynchronous programming was achieved using `EventLoopFurure`.  
EventLoopFutures are an alternative to callback-based asynchronous APIs.  

## Event Loop
When your application boots, it will usually create one event loop for each core in the CPU it is running on.  
`Each event loop has exactly one thread`. If you are familiar with event loops from Node.js, the ones in Vapor are similar.  
The main difference is that `Vapor can run multiple event loops in one process` since Swift supports multi-threading.  

Each time a client connects to your server, it will be assigned to one of the event loops. From that point on, all communication between the server and that client will happen on that same event loop (and by association, that event loop's thread).

The event loop is responsible for keeping track of each connected client's state. If there is a request from the client waiting to be read, the event loop triggers a read notification, causing the data to be read. Once the entire request is read, any futures waiting for that request's data will be completed.


## EventLoopFuture APIs

### map
map method allows you to transform the future's value to another value.  
Because the future's value may not be available yet (it may be the result of an asynchronous task) we must provide a closure to accept the value.  

```swift
// Assume we get a future string back from some API
let futureString: EventLoopFuture<String> = ...

// Map the future string to an integer
let futureInt = futureString.map { string in
    print(string) // The actual String
    return Int(string) ?? 0
}

// We now have a future integer
print(futureInt) // EventLoopFuture<Int>
```

### flatMap
flatMap method allows you to transform the future's value to another future value.  
It gets the name "flat" map because it is what allows you to avoid creating nested futures (e.g., EventLoopFuture<EventLoopFuture<T>>).  
In other words, it helps you keep your generics flat.  
```swift
// Assume we get a future string back from some API
let futureString: EventLoopFuture<String> = ...

// Assume we have created an HTTP client
let client: Client = ... 

// flatMap the future string to a future response
let futureResponse = futureString.flatMap { string in
    client.get(string) // EventLoopFuture<ClientResponse>
}
  
// We now have a future response
print(futureResponse) // EventLoopFuture<ClientResponse>
```  
  
### transform
transform method allows you to modify a future's value, ignoring the existing value.  
This is especially useful for transforming the results of EventLoopFuture<Void> where the actual value of the future is not important.  
```swift
// Assume we get a void future back from some API
let userDidSave: EventLoopFuture<Void> = ...

// Transform the void future to an HTTP status
let futureStatus = userDidSave.transform(to: HTTPStatus.ok)
print(futureStatus) // EventLoopFuture<HTTPStatus>
```

### makeFuture
You can use an event loop to create pre-completed future with either the value or an error.
```swift
// Create a pre-succeeded future.
let futureString: EventLoopFuture<String> = eventLoop.makeSucceededFuture("hello")

// Create a pre-failed future.
let futureString: EventLoopFuture<String> = eventLoop.makeFailedFuture(error)
```
