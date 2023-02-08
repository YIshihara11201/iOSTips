# Queues
Vapor Queues (vapor/queues) is a pure Swift queuing system that allows you to offload task responsibility to a side worker. 
`QueuesRedisDriver` is an officially supported driver.  
This article is an quick review for implementing Queues using QueuesRedisDriver.  

## Job
Jobs are defined by the Job or AsyncJob protocol.
```swift
struct ActivityInfo: Codable {
  let pushToken: String
  let date: Date
  let duration: Int
}

struct BackgroundLiveActivitiesUpdateJob: AsyncJob {
  typealias Payload = ActivityInfo
  
  func dequeue(_ context: Queues.QueueContext, _ payload: ActivityInfo) async throws {
    try await APNSManager.sendBackgroundNotification(token: payload.pushToken, client: APNSManager.getClient(), activityInfo: payload)
  }
}
```

## Registering a Job
After modeling a job you must add it to your configuration section like this:

```swift
// in configure method, register jobs
let bgLiveActivityJob = BackgroundLiveActivitiesUpdateJob()
app.queues.add(bgLiveActivityJob)
```

## Running Workers in-process
To run a worker in the same process as your application (as opposed to starting a whole separate server to handle it), call the convenience methods on Application:
```swift
// in configure method, run worker
try app.queues.startInProcessJobs(on: .default)
```

## Dispatching Jobs
To dispatch a queue job, you need access to an instance of Application or Request.  
You will most likely be dispatching jobs inside of a route handler:  
With `delayUntil` argument, you can specify time to trigger the job
```swift
// inside route handler function
try await req.queue.dispatch(
  BackgroundLiveActivitiesUpdateJob.self,
  activityInfo,
  delayUntil: dispatchDate
)
```
