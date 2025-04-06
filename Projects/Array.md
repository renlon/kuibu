## Pointer Solutions
**Key Indicators That Suggest Using Pointers:**
1. **Input Characteristics:**
    - Sorted arrays/strings
    - Linked structures
    - Need to track positions or ranges
2. **Problem Requirements:**
    - In-place modifications
    - Constant space complexity
    - Window/range tracking
    - Finding pairs/triplets
3. **Optimization Needs:**
    - Avoid extra space usage
    - Reduce time complexity
    - Single-pass solutions needed
**Real-World Example Scenarios:**

1. **Database Operations:**

```
def merge_sorted_logs(log1, log2):
    """
    Merging two sorted log files - common in database systems
    """
    p1 = p2 = 0
    merged = []
    
    while p1 < len(log1) and p2 < len(log2):
        if log1[p1].timestamp < log2[p2].timestamp:
            merged.append(log1[p1])
            p1 += 1
        else:
            merged.append(log2[p2])
            p2 += 1
    
    return merged + log1[p1:] + log2[p2:]
```

往往要非常小心考虑edge case：
- Empty sequences
- Single-element sequences
- Sequences of different lengths
- Invalid inputs
- Boundary conditions

### In-Place Operations:
```
# 1. sort() method - in-place sorting
nums = [3, 1, 4, 1, 5]
nums.sort()  # Modifies nums directly
print(nums)  # [1, 1, 3, 4, 5]

# 2. reverse() method - in-place reversal
nums = [1, 2, 3]
nums.reverse()  # Modifies nums directly
print(nums)  # [3, 2, 1]

# 3. append() - adds to end
nums = [1, 2]
nums.append(3)  # Modifies nums directly
print(nums)  # [1, 2, 3]

# 4. extend() - adds iterable to end
nums = [1, 2]
nums.extend([3, 4])  # Modifies nums directly
print(nums)  # [1, 2, 3, 4]

# 5. insert() - inserts at index
nums = [1, 3]
nums.insert(1, 2)  # Modifies nums directly
print(nums)  # [1, 2, 3]

# 6. remove() - removes first occurrence
nums = [1, 2, 2, 3]
nums.remove(2)  # Modifies nums directly
print(nums)  # [1, 2, 3]

# 7. pop() - removes at index
nums = [1, 2, 3]
nums.pop(1)  # Modifies nums directly
print(nums)  # [1, 3]

# 8. clear() - removes all elements
nums = [1, 2, 3]
nums.clear()  # Modifies nums directly
print(nums)  # []
```

### Operations That Create New Arrays:
```
# 1. sorted() function
nums = [3, 1, 4, 1, 5]
sorted_nums = sorted(nums)  # Creates new list
print(nums)        # [3, 1, 4, 1, 5] - original unchanged
print(sorted_nums) # [1, 1, 3, 4, 5] - new sorted list

# 2. Slicing
nums = [1, 2, 3, 4]
slice_nums = nums[1:3]  # Creates new list
print(nums)       # [1, 2, 3, 4] - original unchanged
print(slice_nums) # [2, 3] - new list

# 3. List concatenation with +
nums1 = [1, 2]
nums2 = [3, 4]
nums3 = nums1 + nums2  # Creates new list
print(nums1)  # [1, 2] - original unchanged
print(nums3)  # [1, 2, 3, 4] - new list

# 4. List comprehension
nums = [1, 2, 3]
doubled = [x * 2 for x in nums]  # Creates new list
print(nums)     # [1, 2, 3] - original unchanged
print(doubled)  # [2, 4, 6] - new list

# 5. map() function
nums = [1, 2, 3]
mapped = list(map(lambda x: x * 2, nums))  # Creates new list
print(nums)    # [1, 2, 3] - original unchanged
print(mapped)  # [2, 4, 6] - new list

# 6. filter() function
nums = [1, 2, 3, 4]
filtered = list(filter(lambda x: x % 2 == 0, nums))  # Creates new list
print(nums)      # [1, 2, 3, 4] - original unchanged
print(filtered)  # [2, 4] - new list

# 7. copy() or [:] - shallow copy
nums = [1, 2, 3]
nums_copy = nums.copy()  # or nums[:]
nums_copy[0] = 99
print(nums)      # [1, 2, 3] - original unchanged
print(nums_copy) # [99, 2, 3] - new list
```

## 2 Pointer 的pattern
- Fast & Slow Pointers
- Left & Right Pointers (usually for problems involving searching from both ends)
- Multiple Same-Direction Pointers

```
def removeDuplicates(nums: List[int]) -> int:
    if not nums:
        return 0
        
    k = 1  # slow pointer
    # i is the fast pointer in the loop
    for i in range(1, len(nums)):
        if nums[i] != nums[i-1]:
            nums[k] = nums[i]
            k += 1
    return k
```

**How to Identify Fast/Slow Pointer Need:**

1. Array/LinkedList problems requiring:
    - In-place modifications
    - Finding duplicates/unique elements
    - Finding patterns or cycles
    - Partitioning elements

```
nums = [0,0,1,1,1,2,2,3,3,4]

# Initial state
k = 1 (slow pointer starts at 1)
i = 1 (fast pointer starts at 1)

# Step by step:
Step 1: i=1
nums[1]=0, nums[0]=0
No action (duplicate)
Array: [0,0,1,1,1,2,2,3,3,4]
        k i

Step 2: i=2
nums[2]=1, nums[1]=0
Different! Copy nums[i] to nums[k]
k++
Array: [0,1,1,1,1,2,2,3,3,4]
          k i

Step 3: i=3
nums[3]=1, nums[2]=1
No action (duplicate)
Array: [0,1,1,1,1,2,2,3,3,4]
          k   i

... and so on
```


**How to Define Loop Conditions:**

1. Start Positions:
```
# Common patterns:
k = 1  # slow pointer often starts at 1 or 0
i = 1  # fast pointer often starts same as k or one ahead
```

2. Loop Structure Options:
```
# Option 1: For loop (when you know the range)
for i in range(1, len(nums)):
    # i is your fast pointer
    # k is your slow pointer

# Option 2: While loop (when end condition is uncertain)
while fast < len(nums):
    # logic here
    fast += 1
```

3. Movement Patterns:
```
# Pattern 1: Move fast every iteration, move slow conditionally
for fast in range(len(nums)):
    if condition:
        nums[slow] = nums[fast]
        slow += 1

# Pattern 2: Move both pointers independently
while fast < len(nums):
    if condition1:
        slow += 1
    if condition2:
        fast += 2
```


| Operation              | Method/Syntax              | Time Complexity | Space Complexity | In-Place/New | Notes                                         |
| ---------------------- | -------------------------- | --------------- | ---------------- | ------------ | --------------------------------------------- |
| **Access**             | `list[i]`                  | O(1)            | O(1)             | N/A          | Direct index access                           |
| **Append**             | `list.append(x)`           | O(1)*           | O(1)             | In-Place     | *Amortized constant time                      |
| **Pop End**            | `list.pop()`               | O(1)            | O(1)             | In-Place     | Remove last element                           |
| **Pop Index**          | `list.pop(i)`              | O(n-i)          | O(1)             | In-Place     | Need to shift elements after i                |
| **Insert**             | `list.insert(i, x)`        | O(n-i)          | O(1)             | In-Place     | Need to shift elements after i                |
| **Delete**             | `del list[i]`              | O(n-i)          | O(1)             | In-Place     | Need to shift elements after i                |
| **Remove**             | `list.remove(x)`           | O(n)            | O(1)             | In-Place     | Need to find element first                    |
| **Extend**             | `list.extend(iterable)`    | O(k)            | O(1)             | In-Place     | k = len(iterable)                             |
| **Sort**               | `list.sort()`              | O(n log n)      | O(1)             | In-Place     | Uses Timsort algorithm                        |
| **Reverse**            | `list.reverse()`           | O(n)            | O(1)             | In-Place     | Reverses in-place                             |
| **Clear**              | `list.clear()`             | O(1)            | O(1)             | In-Place     | Removes all elements                          |
| **Copy**               | `list.copy()` or `list[:]` | O(n)            | O(n)             | New Copy     | Shallow copy                                  |
| **Concatenate**        | `list1 + list2`            | O(n + k)        | O(n + k)         | New Copy     | n = len(list1), k = len(list2)                |
| **Slice**              | `list[i:j]`                | O(j-i)          | O(j-i)           | New Copy     | Creates new list with elements from i to j    |
| **Sorted**             | `sorted(list)`             | O(n log n)      | O(n)             | New Copy     | Returns new sorted list                       |
| **List Comprehension** | `[expr for x in list]`     | O(n)            | O(n)             | New Copy     | Creates new list                              |
| **Map**                | `map(func, list)`          | O(n)            | O(n)             | New Copy     | Creates iterator, O(n) when converted to list |
| **Filter**             | `filter(func, list)`       | O(n)            | O(n)             | New Copy     | Creates iterator, O(n) when converted to list |
| **Index**              | `list.index(x)`            | O(n)            | O(1)             | N/A          | Finds first occurrence                        |
| **Count**              | `list.count(x)`            | O(n)            | O(1)             | N/A          | Counts occurrences                            |
| **Length**             | `len(list)`                | O(1)            | O(1)             | N/A          | Returns length                                |


**Common Fast/Slow Pointer Problems:**

1. Remove Duplicates (our current problem)
2. Find Cycle in LinkedList
3. Middle of LinkedList
4. Partition Array
Here's another similar problem using fast/slow pointers:
```
# Move all zeros to end while maintaining order of non-zero elements
def moveZeroes(nums: List[int]) -> None:
    slow = 0  # Position to place next non-zero element
    
    # fast finds non-zero elements
    for fast in range(len(nums)):
        if nums[fast] != 0:
            nums[slow] = nums[fast]
            slow += 1
    
    # Fill remaining positions with zeros
    while slow < len(nums):
        nums[slow] = 0
        slow += 1
```

**Tips for Implementing Two-Pointer Solutions:**

1. Identify what each pointer represents
2. Determine starting positions
3. Define clear movement conditions
4. Consider edge cases
5. Visualize the pointer movements on paper first

**Remember: The "fast" pointer usually explores or searches, while the "slow" pointer usually marks a position where we want to make changes or track progress.**

