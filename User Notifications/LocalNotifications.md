# Local Notifications

## Example implementation
*Local Notifications managing class*
```swift

import UserNotifications

class LocalNotification {

  let sampleNotificationIdentifier = "SAMPLE_IDENTIFIER"
  var center: UNUserNotificationCenter = UNUserNotificationCenter.current()
  
  func authorize() {
    center.requestAuthorization(options: [.badge, .alert, .sound]) { (_: Bool, _: Error?) in }
  }
  
  func sendScheduleNotification(triggerTime: DateComponents) {
    let content = UNMutableNotificationContent()
    content.title = "notification title"
    content.body = "notification message"
    
    let dateComponents = triggerTime
    let trigger = UNCalendarNotificationTrigger(dateMatching: dateComponents, repeats: true)
    let request = UNNotificationRequest(identifier: sampleNotificationIdentifier, content: content, trigger: trigger)
    
    center.add(request) {( _ ) in }
  }
  
  func setLocalNotification(date: Date) {
    let triggerDateComps = Calendar.current.dateComponents([.hour, .minute], from: date)
    
    center.getNotificationSettings(completionHandler: { settings in
      if settings.authorizationStatus == .authorized {
        self.sendScheduleNotification(triggerTime: triggerDateComps)
      }
    })
  }
  
}

```

<br>

*delegate Notification handling to appDelegate by implementing UNUserNotificationCenterDelegate*
```swift
class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {
  
  //This method is called after your app has been launched and its main storyboard or nib file has been loaded, 
  //but before your app’s state has been restored. 
  //At the time this method is called, your app is in the inactive state
  func application(_ application: UIApplication, willFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]? = nil) -> Bool {

   ...
    UNUserNotificationCenter.current().delegate = self // delegate AppDelegate to UNUserNotificationCenterDelegate
    UNUserNotificationCenter.current().requestAuthorization(options: [.badge, .alert, .sound]) { (_: Bool, _: Error?) in } // enable user notifications
   ...
  
    return true
  }

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
