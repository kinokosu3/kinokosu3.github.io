---
title: LeetCode-旋转数组
date: 2018-07-29 18:27:45
tags:
    - LeetCode
---
给定一个数组，将数组中的元素向右移动 k 个位置，其中 k 是非负数。

实例:
```
输入: [1,2,3,4,5,6,7] 和 k = 3
输出: [5,6,7,1,2,3,4]
解释:
向右旋转 1 步: [7,1,2,3,4,5,6]
向右旋转 2 步: [6,7,1,2,3,4,5]
向右旋转 3 步: [5,6,7,1,2,3,4]
```

使用O(n)的时间复杂度的话，可以使用数组翻转公式，`(i + k) % n = 旋转后的位置`。
要注意计算出来的位置和原来位置相同如何处理。

```cpp
class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        int length = nums.size();
        int start = 0;
        int i = 0;
        int cur = nums[i];
        int cnt = 0;
        while (cnt++ < length) {
            i = (i + k) % length;
            int t = nums[i];
            nums[i] = cur;
            if (i == start) {
                ++start;
                ++i;
                cur = nums[i];
            } else {
                cur = t;
            }
        }
    }
};

```

