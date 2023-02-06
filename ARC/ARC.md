# Automatic Reference Counting
This article is a key-points note for  

"Pro Multithreading and Memory Management for iOS and OS X: with ARC, Grand Central Dispatch, and Blocks,   
by Kazuki Sakamoto, Tomohiko Furumoto, Apress, April 25 2012."  


## What is ARC?
- ARC makes memory management the job of the compiler rather than the programmer, which quite often increases performance significantly.  
- Memory management means that a programmer allocates a memory area when the program needs it and frees it when the program no longer needs it.  
- *`Reference counting`*, invented by George E. Collins in 1960, is used to make memory management simple.  

  <img width="652" alt="MemoryManagementWithReferenceCounting" src="https://user-images.githubusercontent.com/98417271/216911009-7122a1b4-b3e7-44f2-91fd-9496ebc7d193.png">
  
  Memory management with reference counting

<br>

## Reference Counting (before moving on to ARC)
You should think about reference counting as on the following rules.  

1. You have ownership of any objects you `create`.
2. You can take ownership of an object using `retain`. 
3. When no longer needed, you must `relinquish` of ownership of an object you own.
4. You must not relinquish ownership of an object you don’t own.     

Above are all the rules about reference counting. All you have to do is just follow the rules.  
And `create`, `retain` `relinquish`, and `dispose` are keywords in reference counting.

<br>

*Comparison of 4 actions for Objective-C object and methods*
<img width="802" alt="ARC_rules_methods" src="https://user-images.githubusercontent.com/98417271/216911716-4533668d-5885-479b-ab92-e37bae609090.png">

<br>

## 4 Rules for reference counting (without ARC)
  ### 1. You Have Ownership of Any Objects You Create
  When you use a method whose name begins with one of the following, you are creating an object and have ownership of it
  - alloc
  - new
  - copy
  - mutableCopy
  
  *example*
  ```objc
  /* You create an object and have ownership. */
   id obj = [[NSObject alloc] init];
  /* Now, you have ownership of the object. */

  /* You create an object and have ownership. */
   id obj = [NSObject new];
  /* Now you have ownership of the object. */
  ```

  ### 2. You Can Take Ownership of an Object Using retain
  Sometimes methods that are not in the `alloc/new/copy/mutableCopy` method group return an object. In this case, you haven’t created it, so you don’t have ownership of it (like the following).  
  ```objc
  /* Obtain an object without creating it yourself or having ownership */
   id obj = [NSMutableArray array];
  /* The obtained object exists and you don’t have ownership of it. */
  ```

  The variable obj has a reference to the NSMutableArray object, but you don’t have ownership of it. To take ownership, you have to use the retain method.  
  ```objc
  /* Obtain an object without creating it yourself or having ownership */
   id obj = [NSMutableArray array];
  /* The obtained object exists and you don’t have ownership of it. */

   [obj retain];
  /* Now you have ownership of it. */
  ```

  ### 3. When No Longer Needed, You Must Relinquish Ownership of an Object You Own
  When you own an object, but don’t need it anymore, you must relinquish ownership by calling the release method.  
  ```objc
  /* Obtain an object without creating it yourself or having ownership */
   id obj = [NSMutableArray array];
  /* The obtained object exists and you don’t have ownership of it. */
  [obj retain];
  /* Now you have ownership of the object. */

  [obj release];
  /*
   * The object is relinquished.
   * You can’t access the object anymore. */
  ```

  ### 4. You Must Not Relinquish Ownership of an Object You Don’t Own

  you must not call the release method when you’ve obtained the object in some other way.  
  If you do, the application will crash.

  <br>

## Return Object with Ownership
If a method returns an object of which the method has ownership, ownership is passed to the caller.  
Methods other than `alloc/new/copy/mutableCopy` group, like [NSMutableArray array] method returns a new object without ownership taken by the caller.  
Such methods are implemented by using the `autorelease` method. Autorelease offers a mechanism to relinquish objects properly when the lifetime of the objects has ended.  

<img width="356" alt="autorelease" src="https://user-images.githubusercontent.com/98417271/217060535-a30e93ed-1381-4372-93f1-c7dcfb8e48c4.png">  
*autorelease*

<br>

## Autorelease
autorelease is like “automatic variable” in the C language

### Automatic variable in C language
An automatic variable is a lexically scoped variable, which is disposed of automatically when the execution leaves the scope.
```c
{
 int a;
}
/*
 * Because the variable scope is left,
 * auto variable 'int a' is disposed of and can't be accessed anymore. */
```
With autorelease, you can use objects in the same manner as automatic variables, meaning that when execution leaves a code block, the “release” method is called on the object automatically

### Autorelease lifecycle
<img width="445" alt="autopool_lifetime" src="https://user-images.githubusercontent.com/98417271/217064184-dc804826-0e8a-4f24-98a5-fe1b1287edb8.png">
<br>

1. Create an NSAutoreleasePool object.
2. Call “autorelease” to allocated objects.
3. Discard the NSAutoreleasePool object.  

A code block between the creation and disposal of the NSAutoreleasePool object is equivalent to the variable scope in C. When an NSAutoreleasePool object is disposed of, the release method is automatically called for all the autoreleased objects. Some example source code is as follows.
```objc
NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init]; 
id obj = [[NSObject alloc] init];
[obj autorelease];
[pool drain];
```

When there are too many autoreleased objects, application memory becomes short.  
In this case, you should create and discard an NSAutoreleasePool object by yourself
explicitly at the appropriate time (Figure 1–15).
```objc
for (int i = 0; i < numberOfImages; ++i) {
  NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];
  /* Loading images, etc.
   * Too many autoreleased objects exist. */
  
  [pool drain];
  /* All the autoreleased objects are released by [pool drain]. */
}
```
