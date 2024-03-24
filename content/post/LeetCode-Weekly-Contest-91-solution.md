---
title: LeetCode Weekly Contest 91 solution
date: 2018-07-25 17:09:12
tags:
    - LeetCode
---
## Foreword
最近的一个模拟竞赛

## 860. 柠檬水找零

[860. 柠檬水找零](https://leetcode-cn.com/problems/lemonade-change/description/)
在柠檬水摊上，每一杯柠檬水的售价为 5 美元。

顾客排队购买你的产品，（按账单 bills 支付的顺序）一次购买一杯。

每位顾客只买一杯柠檬水，然后向你付 5 美元、10 美元或 20 美元。你必须给每个顾客正确找零，也就是说净交易是每位顾客向你支付 5 美元。


注意，一开始你手头没有任何零钱。

如果你能给每位顾客正确找零，返回 true ，否则返回 false 。

```cpp
class Solution {
public:
    bool lemonadeChange(vector<int>& bills) {
        vector<int>::iterator it;
        int five = 0, ten = 0, twenty = 0;
        for(it=bills.begin();it!=bills.end();it++){
            if(*it == 5){
                five++;
                continue;
            }else if(*it == 10){
                if(five == 0) return false;
                five--;
                ten++;
                continue;
            }else if(*it == 20){
                if(ten > 0){
                    five--;
                    ten--;
                    if (five < 0) return false;
                }else {five -=3;if(five <0) return false;}
        }
    }
        return true;
}      
};
```

## 863. 二叉树中所有距离为 K 的结点

[863. 二叉树中所有距离为 K 的结点](https://leetcode-cn.com/problems/all-nodes-distance-k-in-binary-tree/description/)
给定一个二叉树（具有根结点 root）， 一个目标结点 target ，和一个整数值 K 。

返回到目标结点 target 距离为 K 的所有结点的值的列表。 答案可以以任何顺序返回。

输入：root = [3,5,1,6,2,0,8,null,null,7,4], target = 5, K = 2

输出：[7,4,1]

![示例图](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/06/28/sketch0.png)

这题的难点在如何遍历到目标节点上面的符合距离的点，所以我们使用map来映射二叉树内的每一个点，queue来处理每个点的父节点、left极点、right节点，set来保存访问过的节点。

首先遍历树，建立映射:

```cpp
 void dfs(TreeNode *root, map<int, vector<int> > &a) {
        if (root == NULL) {
            return;
        }
        if (root->left != NULL) {
            a[root->val].push_back(root->left->val);
            a[root->left->val].push_back(root->val);
            dfs(root->left, a);
        }
        if (root->right != NULL) {
            a[root->val].push_back(root->right->val);
            a[root->right->val].push_back(root->val);
            dfs(root->right, a);
        }
    }
```

主要解决方法是将树内的全部相同距离的点聚合在一起：

```cpp
vector<int> distanceK(TreeNode* root, TreeNode* target, int K) {
        map<int, vector<int> > a;
        dfs(root, a); //将每一个值前后对应
        queue<pair<int, int> > que;
        que.push(make_pair(target->val, 0)); //合并两个值
        map<int, vector<int> > res;
        set<int> st;
        st.insert(target->val);
        while (!que.empty()) { // 判断队列是否空
            auto now = que.front(); // auto是自动类型判断
            res[now.second].push_back(now.first);
            que.pop();
            for (int i = 0; i < a[now.first].size(); i++) { // 里面有父节点和left和right
                int nxt = a[now.first][i]; //下一个值，left或者right或者父节点
                if (st.find(nxt) == st.end()) { //判断nxt是否在set中
                    st.insert(nxt);
                    que.push(make_pair(nxt, now.second + 1));
                }
            }
        }
        return res[K];
    }
```

## 861. 翻转矩阵后的得分

[861. 翻转矩阵后的得分](https://leetcode-cn.com/submissions/detail/4639053/)
有一个二维矩阵 A 其中每个元素的值为 0 或 1 。

移动是指选择任一行或列，并转换该行或列中的每一个值：将所有 0 都更改为 1，将所有 1 都更改为 0。

在做出任意次数的移动后，将该矩阵的每一行都按照二进制数来解释，矩阵的得分就是这些数字的总和。

返回尽可能高的分数。

示例:

```
输入：[[0,0,1,1],[1,0,1,0],[1,1,0,0]]
输出：39
解释：
转换为 [[1,1,1,1],[1,0,0,1],[1,1,1,1]]
0b1111 + 0b1001 + 0b1111 = 15 + 9 + 15 = 39
```

这题的解法主要是先确定首位为1，因为需要二进制最大，首位的值权重很高，再进行其余位的转换，因为不是1就是0，我们每列可以做一个最大值确定，看1的数量多还是0的数量多，哪个多就使用哪个，因为可以无限转换，计算二进制的方法也非常巧妙，用每列的1值数量乘与一个与位数减1的二进制数

```cpp
class Solution {
public:
    int matrixScore(vector<vector<int>>& A) {

        int n = A.size(); //行 3
        int m = A[0].size(); //列 4
        for(int i=0;i<n;i++){
            if(A[i][0] == 0){
                for(int j=0;j<m;j++) A[i][j] = 1 - A[i][j];
            }
        }

        int ans = 0;
        for(int j=0;j<m;j++){
            int p = m - j - 1;
            int ocnt = 0, zcnt = 0;
            for(int i=0;i<n;i++){
                if(A[i][j] == 1) ocnt++;
                else zcnt++;
            }
            ans += max(ocnt, zcnt) * (1 << p);
        }
        return ans;
    }
};

```