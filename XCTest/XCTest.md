# XCTest

## Test Launch Sequence P55

1. Launch the simulator on macOS.
2. Dynamically inject the test bundle into the app.
3. Launch the app in the simulator.
4. Run the tests.
5. Terminate the app.
This gives tests the ecosystem they need to verify interactions with UIKit. As part of step 3, UIKit gives the app delegate a chance to set up anything the app needs to launch. This may include things like the following:
• Setting up core data
• Sending an app-specific key to an analytics service
• Sending a request to fetch data it needs before going to the first screen
These are things we don’t want to have happen while running unit tests. Core data should be set up and populated by test code without using or changing any stored data. We don’t want any network requests.

## Load View Controllers
