# UserDefaults

You can use UserDefaults to store any basic data type for as long as the app is installed.  
When you write data to UserDefaults, it automatically gets loaded when your app runs so that you can read it back again.  
Note that if you think your saved data would take up more than say 100KB, UserDefaults is almost certainly the wrong choice.  
https://www.hackingwithswift.com/read/12/2/reading-and-writing-basics-userdefaults

<br>

```swift
let defaults = UserDefaults.standard

/*set method*/
defaults.set(25, forKey: "Age")
defaults.set(true, forKey: "UseTouchID")
defaults.set(CGFloat.pi, forKey: "Pi")
defaults.set("Paul Hudson", forKey: "Name")
defaults.set(Date(), forKey: "LastRun")
let array = ["Hello", "World"]
defaults.set(array, forKey: "SavedArray")
let dict = ["Name": "Paul", "Country": "UK"]
defaults.set(dict, forKey: "SavedDict")

/*accessing method*/
let age = defaults.integer(forKey: "Age")
let touchId = defaults.bool(forKey: "UseTouchID")
let pi = defaults.double(CGFloat.pi, forKey: "Pi")
let name = defaults.string(forKey: "Name")
let date = defaults.object(forKey: "LastRun") as! Date
let array = defaults.object(forKey: "SavedArray") as? [String] ?? [String]()
let dict = defaults.object(forKey: "SavedDict") as? [String: String] ?? [String: String]()
```
