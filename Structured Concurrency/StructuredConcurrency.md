# Structured Concurrency
This is keypoint note for 
"iOS 15 Programming Fundamentals with Swift, by Matt Neuburg, Released October 2021"  
<br>
Structured Concurrency is Swift language feature to manage concurrency.
<br>

## Relationship between "Concurrency" and "Asynchronous" 
In Structured Concurrency, using await keyword, function invocation waits until the function returns, and this is what asynchronos is.  
While the function is running, another functions can also be invoked without waiting for the completion of prior method.  
This means multiple tasks can run simultaneously, which is the concurrency.

## Task
Async function can be called only from within Tasks.  
Tasks are one of the atoms of structured concurrency — meaning that a task itself has no concurrency.  
While a task is running on one thread, that same task cannot be running simultaneously on another thread.  
But two different tasks can run concurrently; and if a task doesn’t run on the main thread, it can run concurrently with the main thread.  

## Multiple Concurrent Tasks
Suppose you want to download two images and return them both together. We could call our download(url:) method twice in succession:
```swift
func fetchTwoURLs() async throws -> (Data, Data) {
  let url1 = URL(string:"https://www.apeth.com/pep/manny.jpg")!
  let url2 = URL(string:"https://www.apeth.com/pep/moe.jpg")!
  let data1 = try await self.download(url: url1)
  let data2 = try await self.download(url: url2)
  return (data1, data2)
}
```
Each successive await represents a pause until the async method we’re calling has finished.  
So we are downloading one image and waiting for it to arrive; then, when the first download has finished, we start downloading the second image and waiting for it to arrive. That seems unnecessarily time-consuming.

### async let
When we have a known finite number of subtasks to perform, Swift structured concurrency provides a simple, elegant syntax: async let. Here’s a rewrite of fetchTwo- URLs that uses async let:
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

### Task Group
Perform an indeterminate number of downloads simultaneously.  
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
