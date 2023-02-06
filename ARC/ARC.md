# Automatic Reference Counting
This article is a key-points note for  

"Pro Multithreading and Memory Management for iOS and OS X: with ARC, Grand Central Dispatch, and Blocks,   
by Kazuki Sakamoto, Tomohiko Furumoto, Apress, April 25 2012."  

## What is ARC?
- ARC makes memory management the job of the compiler rather than the programmer, which quite often increases performance significantly.  
- Memory management means that a programmer allocates a memory area when the program needs it and frees it when the program no longer needs it.  
- *`Reference counting`*, invented by George E. Collins in 1960, is used to make memory management simple.  

  <img width="652" alt="MemoryManagementWithReferenceCounting" src="https://user-images.githubusercontent.com/98417271/216911009-7122a1b4-b3e7-44f2-91fd-9496ebc7d193.png">
  
  *Memory management with reference counting*
  
  Before moving on to ARC, get yourself familiar with [reference counting](https://github.com/YIshihara11201/iOSTips/blob/main/Reference%20Counting/ReferenceCounting.md)

<br>

## Reference Counting with ARC
Reference counting is still used as the basis of ARC and you will follow 4 rules.  
1. You have ownership of any objects you create.
2. You can take ownership of an object using retain.
3. When you no longer need it, you must relinquish ownership of an object of which you have ownership.
4. You must not relinquish ownership of an object of which you don’t have ownership.

With ARC, compiler manages all counting works on our behalf, which is achieved by utilizing `ownership qualifiers`.

<br>

## Ownership qualifiers

With ARC, ‘id’ and object type variables must have one of the following four ownership qualifiers:  
- __strong
- __weak
- __unsafe_unretained 
- __autoreleasing

### __strong
The __strong ownership qualifier is used as the default for ‘id’ and object types. 
__strong ownership qualifier indicates a strong reference for the object.  
When the control flow leaves the variable scope, the strong reference disappears and the assigned object is released.  
```objc  
{
  /* You create an object and have ownership. */
   id __strong obj = [[NSObject alloc] init];
  
  /* The variable obj is qualified with __strong, which means, it has ownership of the object */
}
/* Leaving the scope of variable obj, its strong reference disappears.
 * The object is released automatically.
 * Because no one has ownership, the object is disposed of. */
```
  
### __weak
When two objects refere to each other as a strong qualified property, they never free each other and memory leak occurs.  
You use __weak qualifier to avoid this circular reference.  
```objc
{
  /*
   * You create an object and have ownership. */
   id __strong obj0 = [[NSObject alloc] init];
   
  /* The variable obj0 is qualified with __strong.
   * Which means, it has ownership of the object. */
   id __weak obj1 = obj0;
   
  /* variable obj1 has a weak reference of the created object */
} 

/* Leaving the scope of variable obj0, its strong reference disappears.
 * The object is released automatically.
 * Because no one has ownership, the object is discarded. */
```

**Note**  
When a variable has a reference to an object and the object is discarded, the weak reference also disappears automatically, which means that the variable is assigned to nil.
```objc
id __weak obj1 = nil; 
{
  id __strong obj0 = [[NSObject alloc] init];
  obj1 = obj0;
  NSLog(@"A: %@", obj1); 
}

NSLog(@"B: %@", obj1);
```
The result is:  
A: <NSObject: 0x753e180> 
B: (null)  

### __unsafe_unretained 
Variables qualified with __unsafe_unretained are excluded from ARC mechanism. And you have to take care of the variables manually.  
__unsafe_unretained variables don’t have ownership of an object, the same as __weak, and it doesn't manage reference count automaticaly.  
Supposing your __unsafe_unretained qualified variable is assined with __strong qaualified object. After the object disappears, the __unsafe_unretained variable still points to the object, which leads to dangling pointer.
```objc
id __unsafe_unretained obj1 = nil;
{
  id __strong obj0 = [[NSObject alloc] init];
  obj1 = obj0;
  NSLog(@"A: %@", obj1);
}
NSLog(@"B: %@", obj1);
```
The result will be:  
A: <NSObject: 0x753e180>   
B: <NSObject: 0x753e180> /* This is a dangling pointer! */ 

### __autoreleasing
This qualfier is used to manage [autorelease](https://github.com/YIshihara11201/iOSTips/blob/main/Autorelease/Autorelease.md).  
Assigning the object to the __autoreleasing qualified variable is equivalent to calling autorelease on a non-ARC environment.  
with ARC enabled, you just have to use @autoreleasepool instead of NSAutoreleasePool class and use the __autoreleasing qualified variable instead of the autorelease method.   
But in reality, it is very rare to type __autoreleasing explicitly as compiler cares __autoreleasing automatically.  
Compiler manages autoreleasepool so that reference count is consistent when pool is drained.

<img width="433" alt="autorelease_with_ARC" src="https://user-images.githubusercontent.com/98417271/217078967-0d6b8af9-990a-4f3f-a6ed-735b4756ac70.png">

<br>

## Property
With ARC, new modifiers are introduced for the Objective-C class property as follows.  
Assigning to a property is the same as assigning to a variable with a corresponding ownership qualifier. 
```objc
@property (nonatomic, strong) NSString *name;
```

*Property modifier - Ownership qualifier*  
<img width="630" alt="Property" src="https://user-images.githubusercontent.com/98417271/217081844-dd026abd-54a2-4422-a9d5-6df41df67d1c.png">
