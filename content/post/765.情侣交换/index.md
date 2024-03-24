---
title: 765.情侣交换
date: 2021-10-10 14:29:10
categories: 
  - LeetCode
tags:
  - 并查集
---

题目：
N 对情侣坐在连续排列的 2N 个座位上，想要牵到对方的手。 计算最少交换座位的次数，以便每对情侣可以并肩坐在一起。 一次交换可选择任意两人，让他们站起来交换座位。

人和座位用 0 到 2N-1 的整数表示，情侣们按顺序编号，第一对是 (0, 1)，第二对是 (2, 3)，以此类推，最后一对是 (2N-2, 2N-1)。

这些情侣的初始座位  row[i] 是由最初始坐在第 i 个座位上的人决定的。

题解：

情侣是一对对的，例如0，1为一对情侣，我们将以一对情侣进行编号，例如(0,1)的编号为0，(2,3)的编号为1，由此得出(i,i+1）的编号为i/2, 对于题目的乱序坐位，比如(34,45)，34的编号为34/2，45的编号为45/2。这样就可以将同一对情侣作为同一个连通分量来看待。
这道题关键的地方在于如何将一对情侣和打乱的情侣群看待，这里会讲一对排好的情侣看作是一个连通分量，一群打乱的情侣看作是一个连通分量，如果所有的情侣都是乱序的，那么连通分量只有1，如果有一对排好的，其他的都是乱序的，那么连通分量为2(或许？)，Union操作就是将所有一对对乱序的情侣作为同一个连通分量看待。

知道了连通分量如何表示后，如何根据连通分量来解决问题呢？

如果一个连通分量里有两对情侣，他们需要交换一次来完成。
如果一个连通分量里有三对情侣，他们需要交换两次来完成。

所有最小交换次数为 情侣对数 - 连通分量数。

代码：

```golang
func minSwapsCouples(row []int) int {
    // 路径压缩
    // 平衡优化
    length := len(row)
    parent := make([]int,0)
    size := make([]int,0)
    count := 0
    for i:=0;i<length;i++{
        parent = append(parent, i)
        size = append(size,1)
    }
    Find := func (x int) int{
        for{
            if parent[x] == x{
                break
            }
            parent[x] = parent[parent[x]]
            x = parent[x]
        }
        return x
    }
    Union := func (p, q int)(){
        rootP := Find(p)
        rootQ := Find(q)
        if rootP == rootQ{
            return
        }
        if size[rootP] > size[rootQ] {
            parent[rootQ] = rootP;
            size[rootP] += size[rootQ];
        } else {
            parent[rootP] = rootQ;
            size[rootQ] += size[rootP];
        }
    }
    
    for i:=0;i<length;i+=2{
        Union(row[i] /2 ,row[i+1]/2)
    }
    for i:=0;i<length/2;i++{
        if i == Find(i){
            count++
        }
    }
    return (length/2) - count
}
```