# Subarray

An example of subarraying

```swift
let nums = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
let idx = 3
let substringIdx = nums.index(nums.startIndex, offsetBy: idx)
let substr = nums[substringIdx...]
```
