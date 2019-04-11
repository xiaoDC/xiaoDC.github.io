---
layout: post
author: fri3nds
title: 树形化 Parent-Child 数组
category: 技术博文
tag: [go, tree]
description: "treeify flatten parent-child arry"
---

## 1. 前言

最近在写一些东西，需要存储一些系统的菜单到数据库。 id 标识当前菜单，pid 标识父级菜单，这样将一个系统的菜单目录存储到数据。获取到某一个系统的所有菜单，得到的数据可以简化成 Parent-Child 的一个数组，而返回给调用方，是需要做一个树形化的结构。

## 2. 模型
```javascript
[
	[1, 0],
	[2, 1],
	[3, 4],
	[4, 2],
	[5, 0],
]
```

上述一个数组中，0 是一个没有任何意义的菜单，一个虚拟的根级菜单，1 表示菜单 1，它的父级菜单是 0，那么 1 就是一个一级菜单。[2 1] 表示菜单 2 的父级菜单是菜单 1，那么 2 就是 二级菜单。其表示的树形结构如下；
![](https://pic2.zhimg.com/v2-ee8ebc46654b3e77122be158b300b27d_r.jpg)

![](https://pic4.zhimg.com/v2-aa850fcbbb7c0f9d562d9ebfdb7811c3_r.jpg)


## 3. 实现

因为知道 0 是根节点，找到 0 节点，再找出其所有子节点，然后递归改节点。如上图，如果该树形结构是一个单叉树的话，数组 length 过大的话，递归会爆栈的，且其时间复杂度 O(n^2)。那么有没有更好一点的方法呢。

首先，代码如何表示一个树形结构，大部分的编程语言都是用指针或者引用来实现。父节点持有子节点的指针或引用。那我们将定义一个 Node 节点。
```go
type  Node struct {
      Resource             *PermNode   `protobuf:"bytes,1,opt,name=resource,proto3" json:"resource,omitempty"`
      Child                []*Node `protobuf:"bytes,2,rep,name=child,proto3" json:"child,omitempty"`
}
```


我们初始化一个 map，记录遍历过的节点。

直接遍历数组，拿到数组每个元素，进而知道其 id 和 pid。

id 节点在 map 中不存在，直接添加到 map；id 节点存在的话，取出该节点。两种情况都将节点信息补全。

pid 节点在 map 中不存在的，直接添加到 map；pid 节点存在的话，取出该节点。两种情况都将 id 节点的指针 append 到 pid 节点的 child 中，关联起来。

1）id 节点添加到 map 时，没有 child 信息，没有关系，如果下次遍历到，会取出该节点，补全其信息的；如果遍历不到，说明该节点是一个叶子节点。

2）pid 节点添加到 map 中的时候，节点信息为空。没有关系，下次遍历到该节点再补全其节点信息。

3）使用 map 存储这些节点是为了方便取出该节点。

## 4. 总结
其实是用了空间换时间，这种方式是一种常规的算法优化的思路。js 的实现会更简单方便。
如有错误，请批评指正。如有更好的方法，请告知。
