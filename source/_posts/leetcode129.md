---
title: 129. 求根到叶子节点数字之和
date: 2020-10-29 18:40:46
tags: algorithm
---

leetcode每日一题疑惑

今天的leetcode每日一题[129. 求根到叶子节点数字之和](https://leetcode-cn.com/problems/sum-root-to-leaf-numbers/)

看完题以后想了种解法（比较繁琐，层序遍历，记录到根节点的所有数字，最后再求和），不要在意细节，看完题解发现自己写的太复杂了，此文不讨论算法，记录下其中的一个疑惑，希望有大佬给解答下。

先上代码

<!-- more -->

```go
func sumNumbers(root *TreeNode) int {
	if root == nil {
		return 0
	}
	numbers := [][]int{}
	// 遍历所有到叶子节点的路径，存到numbers中
	queue := make([]*TreeNode, 0)
	queue = append(queue, root)
	values := make([][]int, 0)
	values = append(values, []int{root.Val})
	for len(queue) > 0 {
		q := queue[0]
		v := values[0]
		queue = queue[1:]
		values = values[1:]
		if q.Left == nil && q.Right == nil {
			numbers = append(numbers, v)
		}
		if q.Left != nil {
			queue = append(queue, q.Left)
			// tempV := make([]int, len(v))
			// copy(tempV, v)
			values = append(values, append(v, q.Left.Val))
		}
		if q.Right != nil {
			queue = append(queue, q.Right)
			// tempV := make([]int, len(v))
			// copy(tempV, v)
			values = append(values, append(v, q.Right.Val))
		}
	}
	ans := 0
	for _, number := range numbers {
		sum := 0
		for i := 0; i < len(number); i++ {
			sum = sum*10 + number[i]
		}
		ans += sum
	}
	return ans
}
```

给的用例通过了，于是提交，发现卡在这个用例上

![](./images/leetcode129.png)

debug以后，发现在进行到其中某一步的时候values中第一个切片最后一个元素本来是9，后来变成5了，不明白咋回事

原来values [[8,3,9,9]]，append一个切片[8,3,9,5]以后应该是[[8,3,9,9], [8,3,9,5]]，可是变成了[[8,3,9,5], [8,3,9,5]]，导致结果出错，应该是切片的问题，不太明白，谁能解释一下其中的原因，为啥原有的数据会被改变，感谢大佬。

注释掉的是后来把切片copy了下，问题解决了，知道是切片问题，没想明白为啥。

提供下出错用例的树形结构供debug

```go
type TreeNode struct {
	Val int
	Left *TreeNode
	Right *TreeNode
}
//      8
//     / \
//    3   5
//     \  
//      9 
// 	   /  \      
//    9    5     
func main() {
	node5 := &TreeNode{Val: 5}
	node52 := &TreeNode{Val: 5}
	node92 := &TreeNode{Val: 9}
	node9 := &TreeNode{Val: 9, Left: node92, Right: node52}
	node3 := &TreeNode{Val: 3, Left: nil, Right: node9}
	node8 := &TreeNode{Val: 8, Left: node3, Right: node5}
	fmt.Println(sumNumbers(node8))
    // 输出结果：16875
    // 正确结果：16879
}
```

