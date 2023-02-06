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

## Exploring memory management (before moving on to ARC)
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

### *1. You Have Ownership of Any Objects You Create *

### *2. You Can Take Ownership of an Object Using retain *

### *3. When No Longer Needed, You Must Relinquish Ownership of an Object You Own *

### *4. You Must Not Relinquish Ownership of an Object You Don’t Own *



## APIs

* ### Dispatch queue creation
  *create serial dispatch queue*  
  ```objc
  dispatch_queue_t mySerialDispatchQueue = dispatch_queue_create("com.example.gcd.MySerialDispatchQueue", NULL);
  ```
