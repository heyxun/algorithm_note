# 算法Day5 - 定长滑动窗口 - 盛最多水的容器

> #### 示例1：
> ![盛水容器图](https://aliyun-lc-upload.oss-cn-hangzhou.aliyuncs.com/aliyun-lc-upload/uploads/2018/07/25/question_11.jpg)
> 输入：[1,8,6,2,5,4,8,3,7]
> 输出：49 
> 解释：图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。
>
> #### 示例2：
> 输入：[1,1]
> 输出：1

## 要点：核心思路？
`双指针。`
1. 在数组的左右两端分别设置一个指针，left 和 right。
2. 计算这两个指针所指的“板”能围成的容器的面积：面积 = min(height[left], height[right]) * (right - left)。
3. 然后，将指向**较短的那个“板”的指针向内移动**。为什么呢？因为**容器的容量是由短板决定的**，移动长板不可能让面积变得更大（因为**宽度在变小**），而移动短板则有可能找到一个更高的板，从而让面积变大。
4. 重复这个过程，直到两个指针相遇。在整个过程中，记录下最大的面积即可。
### 疑点：为什么right是len(height)-1不是len(height)？
right需要指向数组的索引，不存在有效索引是len（height）且会触发越界报错。
## 要点：哪个是长版哪个是短板？
“长板”和“短板”是根据指针 left 和 right 所指向的高度来决定的。
> height[left] 是左边板子的高度。
> height[right] 是右边板子的高度。

`min(height[left], height[right])` 这个表达式计算出的就是短板的高度。容器能装多少水，是由短板决定的。
### 疑点：为什么while循环里max_area不能赋值为max(min(height[right],height[left]) * (right - left), max_area)？
 实际上可以的，是等价的。
 - 为了可读性与避免重复计算，通常会先把面积存到 `area` 变量，再用 `max_area = max(max_area, area)`。这样更清晰，也避免在同一行里重复写计算表达式（如果后续改动或表达式更复杂，也更不容易出错）。
- 性能上差别可以忽略不计，除非面积计算特别昂贵（这里是常数时间，没影响）。
### 疑点：为什么要移动短板指针，没懂，赌是否下一个比该板指针的高度更高吗？

你这个问题问到了关键点上！用“赌”这个词其实非常形象，但我们可以用更确定的逻辑来解释它，让你彻底明白为什么这并不是在赌博，而是一个必然的、正确的选择。

核心逻辑是：**我们移动短板，是因为以当前这条短板为边界的容器，我们已经找到了它的最大可能面积。**

让我们用一个具体的例子和推理来分析：

假设我们有两个指针 `left` 和 `right`，并且 `height[left] < height[right]`。
这意味着左边的板子是**短板**。

当前的面积是 `Area = (right - left) * height[left]`。

现在我们面临两个选择：

---

#### 选择 1：移动长板 (right 指针向左移)

*   **宽度 (`right - left`)：** 必然变小。
*   **高度 (`min(height[left], height[new_right])`)：** 因为 `height[left]` 本来就是短板，所以新的高度最大也只能是 `height[left]`，不可能变得更高。

**结论：** 宽度变小了，高度没有增加（甚至可能更小）。所以，移动长板（`right` 指针）得到的**新面积，绝对不可能比当前面积更大**。这是一个没有收益的选择，所以我们直接放弃。

---

#### 选择 2：移动短板 (left 指针向右移)

*   **宽度 (`right - left`)：** 同样，必然变小。
*   **高度 (`min(height[new_left], height[right])`)：** 这是关键！因为我们放弃了旧的、较短的 `height[left]`，新的 `height[new_left]` **有可能比原来的短板高**。
    *   如果 `height[new_left]` 依然很短，那新面积会变小（我们为此付出了宽度减小的代价）。
    *   但如果 `height[new_left]` 变得非常高，甚至超过了对面的 `height[right]`，那么新的高度就由 `height[right]` 决定了。这个新的、更高的 `min_height` **有可能**弥补宽度的减小，从而得到一个更大的面积。

**结论：** 移动短板（`left` 指针）是**唯一可能让面积增大的选择**。

#### 总结一下

我们可以把这个过程理解为一种 **“排除法”**。

对于当前的短板（比如 `height[left]`），它和最远处的长板 `height[right]` 组成的容器，就是**以 `height[left]` 为一边所能构成的最大容器了**。为什么？因为你如果把 `right` 指针向内移动，宽度只会更小，而高度仍然受限于 `height[left]`，面积只会更小。

所以，我们计算完 `(right - left) * height[left]` 这个面积后，就可以心安理得地认为：“`height[left]` 这块板子的潜力已经挖掘完了，它不可能再作为一边，形成更大的容器了”。

因此，我们把它丢掉，让 `left` 指针向内移动，去寻找下一个有潜力的边界。这并不是在“赌”，而是在**系统性地排除掉不可能产生最大值的组合**，从而缩小搜索范围，最终找到全局最优解。


## 复杂度
#### 时间复杂度：O(n)
左右双指针各只向中间移动，最多每个位置访问一次，循环执行至多 n−1 次。
#### 空间复杂度：O(1)
只使用了常数个变量（left、right、max_area、area），不随输入规模增长。

# 代码示例
## python
```python
def maxArea(self, height: List[int]) -> int:
  left = 0
  right = len(height) - 1
  max_area = 0
  while left < right:
    area = min(height[right],height[left]) * (right - left)
    max_area = max(area, max_area)
    if height[left] > height[right]:
      right -= 1
    else:
      left += 1
  return max_area
```