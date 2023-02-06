## Structured Concurrency


Structured Concurrency is Swift language feature to manage concurrency.

Asynchronous Code. 
Code that might be called at some unknown future time  

## Task
Async function can be called only from within some task.  
Tasks are also the atoms of structured concurrency — meaning that a task itself has no concurrency.  
While a task is running on one thread, that same task cannot be running simultaneously on another thread. 
But two different tasks can run concurrently; and if a task doesn’t run on the main thread, it can run concurrently with the main thread.  



Multiple Concurrent Tasks
Suppose we want to download two images and return them both together. We could call our download(url:) method twice in succession:
```swift
func fetchTwoURLs() async throws -> (Data, Data) {
  let url1 = URL(string:"https://www.apeth.com/pep/manny.jpg")!
  let url2 = URL(string:"https://www.apeth.com/pep/moe.jpg")!
  let data1 = try await self.download(url: url1)
  let data2 = try await self.download(url: url2)
  return (data1, data2)
}
```

That works, but it might not be what we want to say. Each successive await represents a pause until the async method we’re calling has finished.  
So we are downloading one image and waiting for it to arrive; then, when the first download has finished, we start downloading the second image and waiting for it to arrive. That seems unnecessarily time-consuming.

async let
When we have a known finite number of subtasks to perform, Swift structured con‐ currency provides a simple, elegant syntax: async let. Here’s a rewrite of fetchTwo- URLs that uses async let:
```swift
func fetchTwoURLs() async throws -> (Data, Data) {
  let url1 = URL(string:"https://www.apeth.com/pep/manny.jpg")!
  let url2 = URL(string:"https://www.apeth.com/pep/moe.jpg")!
  async let data1 = self.download(url: url1)
  async let data2 = self.download(url: url2)
  return (try await data1, try await data2)
}
```

The expression async let acknowledges that we are calling an async function that returns a value; but it doesn’t wait for that value. 
Behind the scenes, each async let creates a subtask of the current task. That subtask is launched immediately, and runs concurrently with the current task and any other subtasks. When you say async let and assign the result of an async function, you do not say await; you’re going to say it later, when you first access the returned value. Similarly, if the async function also throws, you do not say try; you’re going to say it later, when you say await.


Task Group
perform an indeterminate number of downloads simultaneously
This approach starts with a call to a global function; it will be one of these:
- `withTaskGroup(of:returning:body:)`
- `withThrowingTaskGroup(of:returning:body:)`
```swift
func fetchManyURLs() async throws -> [URL:Data] {
  let urls: [URL] = [url1, ...urln]
  
  return try await withThrowingTaskGroup(of: [URL:Data].self, returning: [URL:Data].self) { group in
    var result = [URL:Data]()
    
    for url in urls {
      group.addTask { // ask task group to create sub‐ tasks
        return [url: try await self.download(url: url)]
      }
    }
    
    for try await d in group {
      result.merge(d) { cur,_ in cur }
    }
    
    return result
  } 
}
```


