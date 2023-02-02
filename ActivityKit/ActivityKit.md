# Live Activity
Quick review for Live Activities fundamentals.

## Start activity
Using `request` method defined in Activity class, you will start a live activity

```swift

import ActivityKit

let activity = try Activity.request(
  attributes: EyeDamageWidgetAttributes(name: "Sleep Timer", timePassed: Date()),
  contentState: EyeDamageWidgetAttributes.ContentState(phoneActiveTime: 0),
  pushType: .token)
  
```

*Note*  
attribute:    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; content of LiveActivity  
contentState: &nbsp; dynamic content of a Live Activity  
pushType:     &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; when nil is set, update activity locally using `update` method   
              &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; when token is setm receives updates to its dynamic content via remote push notifications  

<br>

## Update token
Using `pushTokenUpdates` variable defined in Activity class, you can observe the change of push token for an activity.
Below is an example of notifying server of updated pushToken. 

```swift

static func sendTokenRequest(for activity: Activity<EyeDamageWidgetAttributes>) async {
  for await data in activity.pushTokenUpdates { // observe changes to the push token of a Live Activity
    let request = Requests.shared.buildTokenRequest(pushToken: nil, activityToken: activityToken) // build request to inform token update
    do {
      try await Requests.shared.send(request: request) // send request (notify the update) to a server
    } catch {
      print("\(error)")
      return
    }
  }
}

```

<br>

## Update/End activity
An example of update and terminating live activity content from backend server (using *APNS* library)

```swift

static func sendLiveActivityNotification(activityToken: String, client: APNSClient<JSONDecoder, JSONEncoder>, duration: Int, type: APNSLiveActivityNotificationEvent) async throws {
  defer {
    do {
      try client.syncShutdown()
    } catch {
      fatalError("\(error)")
    }
  }
  
  try await client.sendLiveActivityNotification(
    .init(
      expiration: .none,
      priority: .immediately,
      appID: "com.yusuke.eyedamageapp", // bundle ID
      contentState: LiveActivityContentState(phoneActiveTime: duration), // dynamic content of live activity
      event: type, // update or end
      timestamp: Int(Date().timeIntervalSince1970)
    ),
     deviceToken: activityToken,
     deadline: .distantFuture
   )
 }

```
