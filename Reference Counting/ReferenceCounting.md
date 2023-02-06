# Reference Counting

Reference counting makes memory management simple.  
The concept is, while somebody is using an object, keep it, otherwise release it.

Reference counting consists of the following 4 rules.  
1. You have ownership of any objects you `create`.
2. You can take ownership of an object using `retain`. 
3. When no longer needed, you must `relinquish` of ownership of an object you own.
4. You must not relinquish ownership of an object you don’t own.     

`create`, `retain` `relinquish`, and `dispose` are keywords in reference counting.

*Comparison of 4 actions for Objective-C object and methods*
<img width="802" alt="ARC_rules_methods" src="https://user-images.githubusercontent.com/98417271/216911716-4533668d-5885-479b-ab92-e37bae609090.png">

<br>

## Four Rules for reference counting (without ARC)
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
Such methods are implemented by using the [`autorelease`](https://github.com/YIshihara11201/iOSTips/tree/main/Autorelease/Autorelease.md) method. Autorelease offers a mechanism to relinquish objects properly when the lifetime of the objects has ended.  

<img width="356" alt="autorelease" src="https://user-images.githubusercontent.com/98417271/217060535-a30e93ed-1381-4372-93f1-c7dcfb8e48c4.png"> 

<br>

