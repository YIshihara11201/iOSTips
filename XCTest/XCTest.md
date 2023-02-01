# XCTest

## FIRST principle

F for Fast:    `A unit test that takes 1/10th of a second to run is a slow unit test.`

I for Isolated:    `Neither function has any side effects that would persist beyond the test run.`

R for Repeatable:    `Calling a function with the same input will always yield the same output.`

S for self-verifying:    `This means using assertions to pass or fail without human verification.`

T for timely:    `This means tests have more value when written before the production code.`

<br>

## Test Launch Sequence

1. Launch the simulator on macOS.
2. Dynamically inject the test bundle into the app.
3. Launch the app in the simulator.
4. Run the tests.
5. Terminate the app.

This gives tests the ecosystem they need to verify interactions with UIKit.  
As part of step 3, `UIKit gives the app delegate a chance to set up anything the app needs to launch`.  
This may include things like the following:
- Setting up core data
- Sending an app-specific key to an analytics service
- Sending a request to fetch data it needs before going to the first screen

These are things we don’t want to have happen while running unit tests. 

-> appDelgate should be bypassed to prevent unintended side-effect

<br>

## Bypass AppDelegate

https://mokacoding.com/blog/prevent-swiftui-app-loading-in-unit-tests/

```swift

import SwiftUI

@main
struct AppLauncher {
  static func main() throws {
    if NSClassFromString("XCTestCase") == nil {
      EyeDamageApp.main()
    } else {
      TestAppDelegate.main()
    }
  }
}

class TestAppDelegate: NSObject, UIApplicationDelegate {
  func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
    return true
  }
}

```

<br>

## Example of Difficult Dependencies

Difficult dipendencies -> ones violate `FIRST` principle

- **F for Fast**  

	Examples include the following:  
	• Calls to web services  
	• Timers  

- **I for Isolated** 

	Globals aren’t a problem if they’re read-only, such as string constants.  
	It’s when we can change the value of a global that we run into the challenges of shared mutable state.  
	One test can set a value that affects a following test.  
	
	Examples include the following:  
	• global variables and persistent storage  
	• Variables defined outside of any type  
	• Singletons  
	• Static properties  
	• The file system  
	• UserDefaults  
	• The keychain  
	• A local database  
	• A remote database  

	we need each test to run in a clean room.  
	Earlier test runs or manual testing should not change the outcome of automated tests.  
	And automated tests should leave no trace that affect later manual testing.  

- **R for Repeatable**  

	We expect different results for the following:  
	• Current time or date  
	• Camera or microphone input  
	• Face ID or Touch ID  
	• Core Motion sensors  
	• Random numbers  
	• External services they can fail  
	• Writing to a log file, we can run out of disk space  
	• Time zone of the machine running tests when writing tests  
	• Analytics  
	• Playing audio or video  

<br>
