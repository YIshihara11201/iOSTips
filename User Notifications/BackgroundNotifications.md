# Background Notifications

## Enable the Remote Notifications Capability
To receive background notifications, you must add the remote notifications background mode to your app.  
In the Signing & Capability tab, add the Background Modes capability, then select the Remote notification checkbox. 

<img width="500" src="https://user-images.githubusercontent.com/98417271/216240833-48498784-a9ed-4ae2-b1b2-dc178705421d.png">

<br>

## Example implementation

*inside AppDelegate*
```swift

class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {

  func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any]) async -> UIBackgroundFetchResult {
    // receive background notifications and handle userInfo object here.
  }

}

```
