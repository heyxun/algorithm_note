# 算法Day1 - 二分查找
- 适用于有序数组，每次搜索区间的中间元素与目标值比大小，决定继续在左半区间还是右半区间比大小。

## 要点一 right是length-1还是length？
| right = length | right = length - 1 |
|---|---|
| left == right时[left, right)无意义 | left == right时[left, right]有意义 |
| 循环条件 left<right | 循环条件 left<=right |

## 要点二 mid的定义？
#### 为什么是left+(right-left)/2，而不是(right+left)/2？
##### 因为后者会导致整型溢出，比如left和right均为2^30。
#### 为什么前者就可以？
##### 增大可运算范围，防止整型溢出。