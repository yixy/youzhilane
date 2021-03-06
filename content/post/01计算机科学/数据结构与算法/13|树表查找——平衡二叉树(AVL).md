# 树表查找——平衡二叉树(AVL) #

为了追求使二叉排序树总是保持最佳情况 O(log­2n)，某些情况需要对二叉排序树进行“平衡化”。

* 注意，BST树最坏查找次数是树的深度，其平均查找长度与树的形态有关，最佳情况是 O(log­n)，而最坏情况是 O(n)。
* AVL在查找、插入和删除在平均和最坏情况下都是O（log n）。空间复杂度是O(n)，因为有指针及平衡因子。

## 1. 平衡二叉树的定义 ##

平衡二叉树（balanced binary tree），又被称为AVL树（Adelson-Velskii and Landis），一棵平衡二叉树或者是空树，或者是具有下列性质的二叉排序树：

* 左子树与右子树的高度之差的绝对值小于等于1
* 左子树和右子树也是平衡二叉树

平衡因子：为方便起见，给每个结点附加一个数字，给出该结点左子树与右子树的高度差。平衡二叉树上所有结点的平衡因子只能是-1，0，1。

![AVL定义](http://sweeat.me/AVL定义.png)

## 2. 失衡处理 ##

失衡二叉排序树的“平衡化”原则：1）降低树的高度；2）保持二叉树的性质

* A：失衡结点。不止一个失衡结点时，取最小失衡子树（其根结点为失衡结点）进行调整。
* B：失衡结点的孩子
* C：插入新结点的子树

![AVL平衡化](http://sweeat.me/AVL平衡化.png)

一个例子。

![AVL平衡化例子](http://sweeat.me/AVL平衡化例子.png)

avl的旋转次数是常数，这个有证明的。但是很多算法书上的伪代码是错误的，它们会一直旋转到root。其实不用的，你可以自己去搜正确的算法，youtube上也有国外大学教授的证明视频