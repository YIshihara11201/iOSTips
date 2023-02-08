# PushNotifications

Vapor's Apple Push Notification Service (APNS) API makes it easy to authenticate and send push notifications to Apple devices.  
It's built on top of APNSwift.

## Get authenticate configuration
```swift
enum APNSError: Error {
  case invalidToken
  case invalidAppStoreKey
  case invalidTeamIdentifier
}

func getAuthentication() throws -> APNSClientConfiguration.AuthenticationMethod {
  guard let token = Environment.get("APNS_TOKEN") else { throw APNSError.invalidToken  }
  guard let appStoreKey = Environment.get("APPSTORE_KEY") else { throw APNSError.invalidAppStoreKey }
  guard let teamId = Environment.get("TEAM_IDENTIFIER") else { throw APNSError.invalidTeamIdentifier }
  do {
    return APNSClientConfiguration.AuthenticationMethod.jwt(
      privateKey: try .init(pemRepresentation: token),
      keyIdentifier: appStoreKey,
      teamIdentifier: teamId)
  } catch {
    fatalError("\(error)")
  }
}
```

## Get client
```swift
func getClient() throws -> APNSClient<JSONDecoder, JSONEncoder> {
  var environment: APNSClientConfiguration.Environment = .production
#if DEBUG
  environment = .sandbox
#endif
  let client = APNSClient(
    configuration: .init(
      authenticationMethod: try APNSManager.getAuthentication(),
      environment: environment),
    eventLoopGroupProvider: .createNew,
    responseDecoder: JSONDecoder(),
    requestEncoder: JSONEncoder()
  )
  
  return client
}
```

## Send notification
```swift
struct Reminder: Codable {
  ...
}

func sendNotification(token: String, client: APNSClient<JSONDecoder, JSONEncoder>, reminder: Reminder) async throws {
  defer {
    do {
      try client.syncShutdown()
    } catch {
      fatalError("\(error)")
    }
  }
  
  try await client.sendAlertNotification(
    .init(
      alert: .init(
        title: .raw("Notification title"),
        subtitle: .raw("Notification subtitle!"),
        launchImage: nil
      ),
      expiration: .none,
      priority: .immediately,
      topic: "com.yusuke.App",
      payload: reminder
    ),
    deviceToken: "dummyToken",
    deadline: .distantFuture
  )
}
```
