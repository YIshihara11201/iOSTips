# URLSession

![URLSession](https://koenig-media.raywenderlich.com/uploads/2019/05/02-URLSession-Diagram.png)  
https://www.kodeco.com/3244963-urlsession-tutorial-getting-started#toc-anchor-004

<br>

`URLSession` is the key object responsible for sending and receiving requests.  
You create it via URLSessionConfiguration, which comes in three flavors:  

- default:   
      Creates a default configuration object that uses the disk-persisted global cache, credential and cookie storage objects.  
- ephemeral:   
      Similar to the default configuration, except that you store all of the session-related data in memory. 
- background:  
      Lets the session perform upload or download tasks in the background.  
      Transfers continue even when the app itself is suspended or terminated by the system.  

URLSessionConfiguration also lets you configure session properties such as timeout values, caching policies and HTTP headers.  

`URLSessionTask` is an abstract class that denotes a task object.  

Session Task Types
There are three types of concrete session tasks:

  1. URLSessionDataTask:  
      Use this task for GET requests to retrieve data from servers to memory.
  2. URLSessionUploadTask:  
      Use this task to upload a file from disk to a web service via a POST or PUT method.
  3. URLSessionDownloadTask: 
      Use this task to download a file from a remote service to a temporary file location.

Generally, URLSession returns data in two ways:
- Via a completion handler when a task finishes, either successfully or with an error; or,
- By calling methods on a delegate that you set when you create the session.

<br>

## Example for DataTask (process data using completion handler)

a data task to query the iTunes Search API for the user’s search term
```swift
let defaultSession = URLSession(configuration: .default)
var dataTask: URLSessionDataTask?

func getSearchResults(searchTerm: String, completion: @escaping QueryResult) {
 // cancel any data task that already exists to reuse the data task object for this new query.
  dataTask?.cancel()
  
  if var urlComponents = URLComponents(string: "https://itunes.apple.com/search") {
   urlComponents.query = "media=music&entity=song&term=\(searchTerm)"
   guard let url = urlComponents.url else { return }
   
   // dataTask(wit: URL) returns URLSessionDataTask.
   // Passing completion handler, when dataTask completes, process it with the handler
   dataTask = defaultSession.dataTask(with: url) { [weak self] data, response, error in

  // clean up dataTask after this method completes
  defer {
    self?.dataTask = nil
  }
  
  if let error = error {
    self?.errorMessage += "DataTask error: " + error.localizedDescription + "\n"
  } else if
    let data = data,
    let response = response as? HTTPURLResponse,
    response.statusCode == 200 {
      self?.updateSearchResults(data)
      
      // switch to the main queue to pass tracks to the completion handler. (UI update)
      DispatchQueue.main.async {
        completion(self?.tracks, self?.errorMessage ?? "")
      }
    }
  }
  
  // All tasks start in a suspended state by default. 
  // Call resume() starts the data task.
    dataTask?.resume()
  }
}
 
```
