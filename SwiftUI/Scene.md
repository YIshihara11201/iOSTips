# Scene

## What is Scene in SwiftUI  
A part of an app’s user interface with a life cycle managed by the system.  
You create an App by combining one or more instances that conform to the Scene protocol in the app’s body.   
A scene contains the view hierarchy of your app.  
You implement the required body computed property and provide the content for your custom scene.  

```swift
struct SomeApp: App {
  @Environment(\.scenePhase) private var scenePhase
  
  var body: some Scene { 
    WindowGroup {
      TabView {
        ContentView()
        .tabItem {
          Label("Journal", systemImage: "book")
        }
        
      SettingsView()
      .tabItem {
        Label("Settings", systemImage: "gear")
      }
    }
  }.onChange(of: scenePhase) { newScenePhase in
    // some processing
  }
  
  ...
  
}
```  

The Scene protocol provides scene modifiers, defined as protocol methods with default implementations, that you use to configure a scene
