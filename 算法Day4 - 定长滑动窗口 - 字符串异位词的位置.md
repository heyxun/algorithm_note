# 算法Day4 - 定长滑动窗口 - 字符串异位词的位置
- 给定两个字符串 s 和 p，找到 s 中所有 p 的 异位词 的子串，返回这些子串的起始索引。不考虑答案输出的顺序。

> #### 示例 1:
>输入: s = "cbaebabacd", p = "abc"
>输出: [0,6]
>解释:
>起始索引等于 0 的子串是 "cba", 它是 "abc" 的异位词。
>起始索引等于 6 的子串是 "bac", 它是 "abc" 的异位词。
> 
> #### 示例 2:
>输入: s = "abab", p = "ab"
>输出: [0,1,2]
>解释:
>起始索引等于 0 的子串是 "ab", 它是 "ab" 的异位词。
>起始索引等于 1 的子串是 "ba", 它是 "ab" 的异位词。
>起始索引等于 2 的子串是 "ab", 它是 "ab" 的异位词。

## 要点一：这道题是不是说检索到子串中的字符都有了，无论顺序都算异位词？
是的。
**若只含26个小写字母，可用长度为26的数组做哈希表**。
若不只，则用dict。

## 要点二：核心思路？
如果两个字符串是异位词，那么它们包含的字符种类和每个字符的数量都是完全相同的。

## 要/疑点三：p_freq和window_freq谁是第一窗口？我分不清。/为什么要把第一个窗口单独处理？
p_freq可看作p子串整体本身的窗口，用来计算p子串本身字符频率，以便确认滑动窗口内是否存在与p自身属于异位词的子串。
window_freq看作滑动窗口，用来计算滑动窗口内的字符频率，**当它为s[0:k]时即为第一个窗口，或叫初始化窗口**，如果此时第一个窗口统计出来的频率刚好等于p_freq，记录当前异位词的位置：0。

### 疑点：为什么方法二不需要del？
一个键是否存在，本身就是信息的一部分。
方法一 (字典 dict)：结构是**动态**的。
 - 不存在异位字符的表示方式为当前字典中没有这个键。
 - 必须删除值为0的键，才能让字典的结构和目标字典完全一致，从而使 == 比较成功。

方法二 (数组 list)：结构是**固定**的。
 - 所有“键”（即索引 0-25）永远存在，不存在异位字符的表示方式为索引的值为0。
 - 将值减为0就自动实现了和目标数组的匹配，因为目标数组中该字符的计数也是0。

## 复杂度
#### 时间复杂度：一般O(n)，最坏O(n * k) 
- 若将字典比较视为对固定大小字符集（例如 ASCII、26 个字母）的 O(1) 操作，则整体为 O(n) —— 每个字符进入/离开窗口一次，外加每步固定时间的比较（或用长度 Σ 的频次数组比较）。
- 若字符集不是常数，而是与 n 同阶（例如任意不同字符都可能出现），则比较两个字典需 O(k)（k 为当前窗口中不同字符数），总体为 O(n·k)。

更直观的分解：
1. 初始化第一个窗口：O(np)
2. 每次滑动步骤（共 O(ns−np) 次）：更新计数是 O(1) 均摊（增、减、删除键），但字典相等比较为 O(k)。

因此总时间复杂度通常写为 O(n * k)，在字符集常数情况下简化为 O(n)。
#### 空间复杂度：O(k) 或 O(min(n, Σ))，其中 k 是 p 或窗口中不同字符的数量，Σ 是字符集大小。
最坏情况 k = O(min(np, ns)) ≤ O(n)；
对于常数大小字符集（如 ASCII/**小写字母**），视为 O(1)。

# 代码示例
## python方法一：dict存储
```python
def findAnagrams_dict(s: str, p: str) -> list[int]:
  # s主串 p子串
  ns, np = len(s), len(p)
  if ns < np:
    return []

  p_freq = {}
  window_freq = {}
  result = []

  # 初始化第一个窗口
  for i in range(np):
    char = s[i]
    p_freq[char] = p_freq.get(char, 0) + 1
    window_freq[char] = window_freq.get(char, 0) + 1
    
  if window_freq == p_freq:
    result.append(0)

  # 滑动窗口，起始位置为在s主串的len(p)位置
  for i in range(np, ns):
    # 1. 右侧新字符进入窗口
    new_char = s[i]
    window_freq[new_char] = window_freq.get(new_char, 0) + 1

    # 2. 左侧旧字符离开窗口
    old_char = s[i - np]
    window_freq[old_char] -= 1
    if window_freq[old_char] == 0:
      del window_freq[old_char] # 关键：计数为0时必须删除

    # 3. 比较两个字典
    if window_freq == p_freq:
       result.append(i - np + 1)

  return result
```

## python方法二： 数组/哈希表存储
```python
def findAnagrams_dict(s: str, p: str) -> list[int]:
  # s主串 p子串
  ns, np = len(s), len(p)
  if ns < np:
    return []

  # 创建两个长度为26的数组来存储字符频率 
  p_counts = [0]*26
  window_counts = [0]*26
  result = []

  # 填充 p 的频率数组和滑动窗口的频率数组
  for i in range(np):
    p_counts[ord(p[i]) - ord('a')] += 1
    window_counts[ord(s[i]) - ord('a')] += 1
    
  if window_counts == p_counts:
    result.append(0)

  # 滑动窗口，起始位置为在s主串的len(p)位置
  for i in range(np, ns):
    # 1. 右侧新字符进入窗口
    window_counts[ord(s[i]) - ord('a')] += 1

    # 2. 左侧旧字符离开窗口
    window_counts[ord(s[i - np]) - ord('a')] -= 1

    # 3. 比较两个字典
    if window_counts == p_counts:
       result.append(i - np + 1)

  return result
```