# Remote Notifications

First of all, you need to register the Application to APNs.
https://developer.apple.com/documentation/usernotifications/registering_your_app_with_apns

## Example implementation

*inside AppDelegate*
```swift

class AppDelegate: UIResponder, UIApplicationDelegate {
  
  //This method is called after your app has been launched and its main storyboard or nib file has been loaded, 
  //but before your app’s state has been restored. 
  //At the time this method is called, your app is in the inactive state
  func application(_ application: UIApplication, willFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]? = nil) -> Bool {

   ...
    UNUserNotificationCenter.current().delegate = self // delegate AppDelegate to UNUserNotificationCenterDelegate
    center.requestAuthorization(options: [.badge, .alert, .sound]) { (_: Bool, _: Error?) in } // enable user notifications
    Task {
      await MainActor.run {
        application.registerForRemoteNotifications() // register to APNs
      }
    }
   ...
  
    return true
  }

  // when token is registered, this delegate method is called
  func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken token: Data) {

    let tokenRequest = Requests.shared.buildTokenRequest(pushToken: token) // "Requests" is a custom class handling URL Requests
    Task {
      try await Requests.shared.send(request: tokenRequest) // send the token to to backend server
    }
    
  }

}

extension AppDelegate: UNUserNotificationCenterDelegate {

  // when you receive notification in app foreground state
  func userNotificationCenter(_ center: UNUserNotificationCenter, willPresent notification: UNNotification, withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
    ...
    completionHandler([.list, .sound, .banner])
  }
  
  // when you receive notification in app background/inactive/terminated state
  func userNotificationCenter(_ center: UNUserNotificationCenter, didReceive response: UNNotificationResponse, withCompletionHandler completionHandler: @escaping () -> Void) {
		...
    completionHandler()
  }
  
}

```
