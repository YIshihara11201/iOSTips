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

## Relationship between Reference Counting and ARC
Reference counting is still used as the basis of ARC, but ARC helps the reference counting mechanism work automatically when you follow 4 rules mentioned in [Refelence Counting](https://github.com/YIshihara11201/iOSTips/blob/main/Reference%20Counting/ReferenceCounting.md).

1. You have ownership of any objects you create.
2. You can take ownership of an object using retain.
3. When you no longer need it, you must relinquish ownership of an object of which you have ownership.
4. You must not relinquish ownership of an object of which you don’t have ownership.

These rules are still applicable. But you need some modification to source codes. 
To do so, you first need to understand `ownership qualifiers`, which are newly introduced for ARC.

<br>

## Ownership qualifiers

With ARC, ‘id’ and object type variables must have one of the following four ownership qualifiers:  
- __strong
- __weak
- __unsafe_unretained 
- __autoreleasing

  ### __strong
  The __strong ownership qualifier is used as the default for ‘id’ and object types. 
  
  
  
  ### __weak
  
  ### __unsafe_unretained 
  
  ### __autoreleasing
  
