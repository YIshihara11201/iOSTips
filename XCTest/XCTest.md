# XCTest

## FIRST principle

F for Fast 
- A unit test that takes 1/10th of a second to run is a slow unit test.  

I for Isolated 
- Neither function has any side effects that would persist beyond the test run.  

R for Repeatable 
- Calling a function with the same input will always yield the same output.  

S for self-verifying
- This means using assertions to pass or fail without human verification.  

T for timely
- This means tests have more value when written before the production code.  

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


## Bypass AppDelegate

## Example of Difficult Dependencies

singleton -> あるテストで発生したシングルトンメソッドの副作用が後続のテストにも影響を与える -> テストの条件が常に一定であることを保証できなくなる

Identify Difficult Dependencies (The ones violate FIRST rules)

F for Fast iOS programs often include code that will execute in response to some external trigger. 
In later chapters, we’ll see how to unit test delegate methods. But if there’s no way for tests to trigger the code execution immediately, that’s a slow dependency. 

Examples include the following:
• Calls to web services • Timers
I for Isolated Dependencies break the rule of isolation in two common ways: global variables and persistent storage.
Global variables come in different varieties:

• Variables defined outside of any type • Singletons
• Static properties

Globals aren’t a problem if they’re read-only, such as string constants. 
It’s when we can change the value of a global that we run into the chal- lenges of shared mutable state. 
One test can set a value that affects a following test.
Persistent storage is similar, except that we store the state in something that outlasts the app’s life cycle. 

This includes the following:

• The file system
• UserDefaults
• The keychain
• A local database
• A remote database

Recall from Chapter 2, Manage Your Test Life Cycles, on page 19 that we need each test to run in a clean room. 
Earlier test runs or manual testing should not change the outcome of automated tests. 
And automated tests should leave no trace that affect later manual testing.

R for Repeatable What dependencies are there that yield different results when called? 
We expect different results for the following:
• Current time or date
• Camera or microphone input • Face ID or Touch ID
• Core Motion sensors
• Random numbers

We can anticipate those differences. 
But there are also unpredictable differences:

• External services—they can fail.
• Writing to a log file—we can run out of disk space.
• Time zone of the machine running tests—when writing tests,

it’s easy to assume they’ll always run in your own time zone.
Hidden problems will surface if your development team grows globally.

Easy to Test? It’s not hard to test functions that return values or change properties. 
But there are also functions that cause side effects outside of the invoked type. 
Such dependencies take commands but offer no way to access the effects of those commands. Examples include the following:
• Analytics
• Playing audio or video

Analytics includes any system of logging events to a server. 
We can send events, but there’s no way for the mobile API to ask for the last batch of events you sent.


## Technique to isolate singletons from test code

### Add Backdoor (Adapter pattern)
-> when you do not own singletons
