---
title: 1631.最小体力消耗路径
date: 2021-10-06 14:29:10
categories: 
  - LeetCode
tags:
  - 并查集
---



### 题目
[path-with-minimum-effort](https://leetcode-cn.com/problems/path-with-minimum-effort/)



你准备参加一场远足活动。给你一个二维 rows x columns 的地图 heights ，其中 heights[row][col] 表示格子 (row, col) 的高度。一开始你在最左上角的格子 (0, 0) ，且你希望去最右下角的格子 (rows-1, columns-1) （注意下标从 0 开始编号）。你每次可以往 上，下，左，右 四个方向之一移动，你想要找到耗费 体力 最小的一条路径。

一条路径耗费的 体力值 是路径上相邻格子之间 高度差绝对值 的 最大值 决定的。

请你返回从左上角走到右下角的最小 体力消耗值 。
```
输入：heights = [[1,2,2],[3,8,2],[5,3,5]]
输出：2
解释：路径 [1,3,5,3,5] 连续格子的差值绝对值最大为 2 。
这条路径比路径 [1,2,2,2,5] 更优，因为另一条路径差值最大值为 3 。

```

### 思路

很明显的最短路径问题，我一开始的思路是走单源最短路径，也就是dijkstra，准确来说是贪心。路径权值为他们的高度差。dijkstra有确定最短路径的bool数组，所以这题也能用Dp来做，保存到某点时最小高度差，状态转移为两点与该点的最小高度差。一条最短路径，当然也可以是最小生成树，将所有的边进行排序。

这里使用最小生成树与并查集。（虽然最后写出来效率巨差草）

思路是先对边排序，然后由于总是先会选择路径最短的边，在选择的过程中就将该点加入一个连通分量中，链接过程中比较出口和入口是否在同一个连通分量里，在则一颗最小生成树已经生成。


```golang
type PointLine struct{
	Start int
	End int
	Cost int
}
type PointLineList []PointLine
func(p PointLineList) Len() int {
	return len(p)
}
func(p PointLineList) Swap(i,j int){
	p[i],p[j] = p[j],p[i]
}
func(p PointLineList) Less(i, j int) bool{
	return p[i].Cost < p[j].Cost
}
func abs(n int) int {
	y := n >> 63
	return (n ^ y) - y
}
func minimumEffortPath(heights [][]int) int {
	m := len(heights)
	n := len(heights[0])
	minCost := 0
	size := make([]int, 0)
	edges := make(PointLineList,0)
	parent := make([]int, 0)
	for i:=0;i<m;i++{
		for j:=0;j<n;j++{
			if i + 1 < m {
				edge := PointLine{
					Start: i*n + j,
					End:   (i+1)*n + j,
					Cost: abs(heights[i+1][j] - heights[i][j]),
				}
				edges = append(edges, edge)
			}
            if j + 1 < n {
				edge := PointLine{
					Start: i*n + j,
					End:   i*n + j + 1,
					Cost: abs(heights[i][j+1] - heights[i][j]),
				}
				edges = append(edges, edge)
			}
		}
	}
	for i:=0;i<m*n;i++{
		parent = append(parent, i)
		size = append(size,1)
	}
	// 找根节点
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
	isSame := func(p, q int) bool{
		return Find(p) == Find(q)
	}

	sort.Sort(edges)
	for i:=0;i<edges.Len();i++{
		minCost = edges[i].Cost
		Union(edges[i].Start, edges[i].End)
		if isSame(0,m*n-1) {
			break
		}
	}
	return minCost
}
```

