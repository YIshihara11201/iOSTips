# Closures
This is keypoint note for "iOS 15 Programming Fundamentals with Swift, by Matt Neuburg, Released October 2021"


## Closure Setting a Captured Variable
Closure captures a reference to a variable outside itself, and if that variable is settable, then the closure can set the variable
```swift
func pass100 (_ f:(Int) -> ()) {
  f(100)
}

var x = 0
print(x) // x = 0

func setX(newX:Int) {
  x = newX
}

pass100(setX)
print(x) // x = 100!!!!
```

## Closure Preserving Captured Environment
When a closure captures its environment, it preserves that environment
```swift
func countAdder(_ f: @escaping () -> ()) -> () -> () {
  var ct = 0 // This variable is preserved as part of the environment of following "countedGreet"
  return {
    ct = ct + 1
    print("count is \(ct)")
    f()
  }
}

func greet () {
  print("howdy")
}
let countedGreet = countAdder(greet) returned closure captures "ct" variable locally defined in countAdder function!
countedGreet() // count is 1
countedGreet() // count is 2
countedGreet() // count is 1
```

## Escaping Closures
If a function passed around as a value will be preserved for later execution, rather than being called directly, it is a closure that captures and preserves its environment over time. That’s called an escaping closure.  
When you refer to a property or method of self within the function body, you must explicitly say "self"

## Capture Lists
Sometimes, you might want a function to refer to a variable outside itself, just in order to get its value, but without capturing the variable. Swift provides an ingenious syntax for doing that — but only when the function is an anonymous function. At the start of the anonymous function body, you put square brackets containing a comma- separated list of references to variables in the surrounding environment. This is called a capture list.
If you have a capture list, you must follow it with the keyword in 
```swift
var x = 0
let f : () -> () = { [x] in
  print(x)
}
f() // this prints 0
x= 1
f() // this also prints 0 as the closure preserved local variable x
```
Another use of capture lists is to work around the requirement to say self explicitly in an escaping closure.  
If you put self in the capture list, and you don’t have to write self as the implicit target in the function body. 
```swift
let f2 = funcPasser { [self] in
  print(view.bounds) // ok
}
```
