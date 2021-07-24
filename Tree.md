## 树

> 参考文档：[Tree Data Structure](https://www.programiz.com/dsa/trees)

首先我知道有**树**这个数据结构的时候我就很好奇为啥要有这个数据结构，我觉得在我的工作场景中，数组，Map都已经够用了...

引用参考文档的解释：

> Other data structures such as arrays, linked list, stack, and queue are linear data structures that store data sequentially. In order to perform any operation in a linear data structure, the time complexity increases with the increase in the data size. But, it is not acceptable in today's computational world.

翻译一下：

对于数组，链表，堆，栈这些都是线性的数据结构，想要在线性数据结构中操作数据，时间复杂度会随着数据量的增大而增大。但是对于今天的海量数据的场景就已经不够用了

**名词解释**：

1. 边(Edge): 两个节点之间的连线

![Edge](//cdn.programiz.com/sites/tutorial2program/files/nodes-edges_0.png)

2. 节点的高度：节点到最远的叶子节点的边(edges)的数量.

3. 数的高度：就是根节点的**最大**高度(如果从左子树和右子树的高度不同，则取最大)


### 二叉树

#### 计算二叉树的高度

首先我们先看一下树的高度的定义。

_在树中某一个节点的高度是指：从一个叶子节点到目标节点的最长路径的边(edge)的最大值。	如果一个目标节点没有任何链接节点，那么这个节点的高度就是0.**二叉树的高度就是根节点的高度**。换句话说二叉树的高度就是从根节点到最远子节点的最大边数。_

**有一个重要的点：当我们计算整个二叉树的深度的时候，就等于二叉树的高度**

使用递归计算树的高度/深度：

```JS
	function depth (root) {
		const fn = function (node) {
			if (node === null) {
				return 0
			}
			return Math.max(fn(root.left), fn(root.right)) + 1
		}
		fn(root)
	}
```

#### 二叉搜索树

> 参考文档：[Binary Search Tree](https://www.hackerearth.com/practice/data-structures/trees/binary-search-tree/tutorial/)

一个二叉树如果想要成为二叉搜索树需要满足：
一个节点的所有左子树的值必须要小于这个节点的值；所有右子树的值必须要大于这个节点的值。
换句话说，二叉搜索树是有序的二叉树。

下面就是来自**参考文档**中的一个二叉搜索树的例子：

![二叉搜索树](https://he-s3.s3.amazonaws.com/media/uploads/fe0eac0.png)。


#### 树的遍历

树的遍历分为三种：先序遍历、中序遍历、后序遍历。

**先序遍历(pre-order)**

先序遍历的顺序是： root -> left -> right

1. 处理根节点的data
2. 首先完全遍历左子树的所有节点
3. 然后遍历右子树

所以上图示例中的遍历结果是： 10 5 1 6 19 17

代码如下：

```JS
	function preOrderTraverse (root) {
		if (root) {
			console.log(root.data) // process root node
			preOrderTraverse(root.left)
			preOrderTraverse(root.right)
		}
	}
```

**中序遍历(in-order)**

中序遍历的顺序是： left -> root -> right

1. 在处理根节点之前先处理左子节点的值。
2. 然后处理当前的根节点
3. 然后遍历右子树

对于示例中的二叉搜索树而言，遍历的结果为： 1, 5, 6, 10, 17, 19

可以看出，对于二叉搜索树而言，中序遍历得到的就是**排序的结果**

代码如下：

```JS
function inOrderTraverse (root) {
	if (root) {
		inOrderTraverse(root.left)
		console.log(root.data)
		inOrderTraverse(root.right)
	}
}
```

在[参考文档](https://www.hackerearth.com/practice/data-structures/trees/binary-search-tree/tutorial/)中给出了中序遍历的详细的调用栈的图解，可以查看


**后序遍历(post-order)**

后序遍历的顺序是：left -> right -> root

1. 先处理左子树(跳过当前根节点)
2. 处理右子树(跳过当前根节点)
3. 处理当前根节点

对于示例的搜索二叉树，遍历结果是：1 6 5 17 19 10

对于后序遍历我的理解就是：把当前的根节点永远放在最后处理，只要还有左/右子节点就忽略当前的根节点

```JS
function inOrderTraverse (root) {
	if (root) {
		inOrderTraverse(root.left)
		inOrderTraverse(root.right)
		console.log(root.data)
	}
}
```

#### 平衡二叉树(Balanced Binary Tree)

> 参考文档: (Balanced Binary Tree)[https://www.programiz.com/dsa/balanced-binary-tree]

所谓的平衡二叉树(也就是高度平衡二叉树)，指的是满足以下条件的：
1. 任意节点的左右子树的高度差不超过1.
2. 左子树是平衡的。
3. 右子树是平衡的。




