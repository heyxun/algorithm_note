# 算法Day2 - 快慢指针（双指针
- 例题：给定一个数组 nums，编写一个函数将所有 0 移动到数组的末尾，同时保持非零元素的相对顺序。要求必须在**不复制**数组的情况下原地对数组进行操作。

## 要点一：如果可以复制数组，应该如何编写？
1. 创建一个与原数组 nums 长度相同的新数组 new_nums。
2. 使用一个指针 index 来跟踪新数组中下一个非零元素的插入位置（初始为 0）。
3. 遍历原数组 nums 的每个元素：
    - 如果当前元素 nums[i] 是零，则跳过（continue）。
    - 如果当前元素 nums[i] 非零，则将其复制到 new_nums[index]，然后递增 index。
4. 将 new_nums 的内容复制到原数组 nums。（题目只检测nums的情况）

###  疑点：为什么移动万非零元素后不用再填充0？
数组刚创建时，若不主动填入元素，会默认全部填充0.

## 优化前复杂度
#### 时间复杂度 O(n)
#### 空间复杂度 O(n)

## 要点二： 若不复制，如何优化？
### 使用双指针原地修改数组，空间复杂度变为常数了。

## 优化后复杂度
#### 时间复杂度 O(n)
#### 空间复杂度 O(1) -- 原数组本身
### 疑点：那指针如何设计的？
快指针i - for循环遍历数组
慢指针index - ​​记录非零元素的写入位置​​（遍历阶段）；​确定零的填充起始位置​​（填充阶段）；​最终等于非零元素的个数​​。

# 代码示例（快慢指针）
## Java
```JAVA
class Solution{
    public void moveZeros(int[] nums){
        int index = 0;
        for(int num : nums) {
            if(num!=0){
                nums[index++] = num;
            }
        }
        for(int i=index;i<nums.length;i++)
            nums[i] = 0;
    }
}
```
## python
```python
from typing import List
def moveZeros(self, nums: List[int]) -> None:
    index = 0
    for num in nums:
        if num != 0:
            nums[index] = num
            index += 1
    for i in range(index, len(nums)):
        nums[i] = 0
```