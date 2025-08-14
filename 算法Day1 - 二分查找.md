# 算法Day1 - 二分查找
- 适用于有序数组，每次搜索区间的中间元素与目标值比大小，决定继续在左半区间还是右半区间比大小。

## 要点一 right是length-1还是length？
| right = length | right = length - 1 |
|---|---|
| left == right时[left, right)无意义 | left == right时[left, right]有意义 |
| 循环条件 left<right | 循环条件 left<=right |

## 要点二 mid的定义？
#### 为什么是left+(right-left)/2，而不是(right+left)/2？
因为后者会导致整型溢出，比如left和right均为2^30。
#### 为什么前者就可以？
增大可运算范围，防止整型溢出。

## 复杂度
#### 时间复杂度 O(log n)
#### 空间复杂度 O(1) -- 查找本身

# 代码示例
## Java
### 左闭右开
```JAVA
public class Solution {
    public int search(int[] nums, int target){
        int left = 0, right = nums.length;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) {
                return mid;
            }
            if (nums[mid] > target) {
               right = mid;
            }
            if (nums[mid] < target) {
               left = mid +1;
            }
        }
        return -1;
    }
}
```

## Python
### 左闭右闭
```PYTHON
from typing import List

class Solution:
    def BinarySearch(self, nums: List[int], target: int) -> int:
        left: int = 0
        right: int = len(nums) - 1
        while(left <= right):
            mid: int = left + (right - left)//2
            if nums[mid] == target:
                return mid
            if nums[mid] < target:
                left = mid + 1
            else:
                right = mid - 1
        return -1
```