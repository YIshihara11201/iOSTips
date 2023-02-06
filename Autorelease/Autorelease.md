# Autorelease
autorelease is like “automatic variable” in the C language

## Automatic variable in C language
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

## Autorelease lifecycle
<img width="445" alt="autopool_lifetime" src="https://user-images.githubusercontent.com/98417271/217064184-dc804826-0e8a-4f24-98a5-fe1b1287edb8.png">  

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
