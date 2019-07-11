---
title: "C++ N叉树的实现"
date: 2019-07-11T10:25:43+08:00
draft: false
tags: ["算法","数据结构"]
categories: ["算法","数据结构", "C++"]
toc: false
---

# 引言

最近一个项目需要使用多叉树结构来存储数据，但是基于平时学习的都是二叉树的结构，以及网上都是二叉树为基础来进行学习，所以今天实现一个多叉树的数据结构。

# 理论基础

**树和二叉树**：

**多叉树**：多叉树，顾名思义，就是一个节点可能有若干个子节点，构造的一个较为复杂的树结构。

树的遍历：树的遍历一般认为有三种：前序遍历二叉树、中序遍历二叉树、后序遍历二叉树$^{[2]}$。

![](https://img2018.cnblogs.com/blog/1176527/201907/1176527-20190711113913965-1372436505.png)

    (1). 前序遍历二叉树。若二叉树为空，则为空操作，返回空否则访问根结点-->前序遍历左子树-->前序遍历右子树。

    (2). 中序遍历二叉树。 若二叉树为空，则为空操作，返回空否则中序遍历左子树-->访问根结点-->中序遍历右子树。

    (3). 后序遍历二叉树。若二叉树为空，则为空操作，返回空否则后序遍历左子树-->后序遍历右子树-->访问根结点。

> 本文特别强调：本文只有两种遍历方法，先根遍历和先叶遍历，先根遍历是首先遍历根节点，然后访问按顺序从左到右遍历子节点；先叶遍历指首先按照顺序从左至右遍历叶子节点，然后遍历根节点。

**C++指针：** 指针即为地址，一个指针对应一个地址，`*p = &a` $^{[3-4]}$，其中a保存的是变量值，具体数据，`*p` 或者 `&a`表示的是一个地址编号，比如：`0x80651165`，即：`a = 5` ， `p = 0x80651165`，一句话描述，变量存储的是数据，指针存放的是地址，通过指针可以找到变量存放的位置，从而找到变量的具体值;

**结构体：**结构体可以理解为一个对象(但是class 和 struct 是有不同之处的)，它是一种用户自定义的数据类型。

# 基于C++的N叉树的实现

头文件：
```cpp
#include <iostream>
#include <vector>
using namespace std;
#ifndef DBM_MTREE_H
#define DBM_MTREE_H
typedef int T;
typedef struct MNode {
    T element;
    vector<MNode*> children;
    MNode *Parent;
} MNode;

class MTree {
private:
    MNode *root;
public:
    void init(MNode *root);
    void putChild(MNode* node,MNode* parent);
    void putChildren(vector<MNode*> nodes, MNode *parent);
    void tranversal(MNode *root);
    void tranversal();
    int getMaxDepth(MNode *root,vector<MNode*> nodes);
};
```

实现：
```cpp
void MTree::init(MNode *root) { this->root = root; }

void MTree::putChild(MNode *node, MNode *parent) {
    parent->children.push_back(node);
    node->Parent = parent;
}

void MTree::putChildren(vector<MNode *> nodes, MNode *parent) {
    for (int i = 0; i < nodes.size(); ++i) {
        putChild(nodes[i], parent);
    }
}

void MTree::tranversal() {
    this->tranversal(this->root);
}

void MTree::tranversal(MNode *root) {
    vector<MNode *> nodes = root->children;
    for (int i = 0; i < nodes.size(); ++i) {
        if (nodes[i]->children.size() > 0)
            tranversal(nodes[i]);
        else
            cout << nodes[i]->element << ",";
    }
    cout << root->element << ",";
}

int MTree::getMaxDepth(MNode *root,vector<MNode*> nodes) {
    auto iResult = 0;

    return iResult;
}
```

源码：[Gitee]() | [Github]() | [CSDN]()

# 实验验证

![](https://img2018.cnblogs.com/blog/1176527/201907/1176527-20190711113853009-2034020496.png)

![](https://img2018.cnblogs.com/blog/1176527/201907/1176527-20190711113902355-1212199627.png)

# 本章小结

学习数据结构一定是从思想角度去理解算法和数据结构的意义，而不要仅仅局限于某一种数据结构和算法，将一种算法和结构扩展化、实践化才是学习数据结构的根本目的，锻炼自己对问题建模的能力。

# 参考文献

[1]. [树和二叉树的定义](https://blog.csdn.net/zhangyu4863/article/details/80467567)

[2]. [二叉树遍历算法总结](https://blog.csdn.net/wp1603710463/article/details/50937743)

[3]. [C++指针详解](https://blog.csdn.net/wjf1997/article/details/82715988)

[4]. [C/C++指针详解之基础篇（史上最全最易懂指针学习指南！！！！）](https://blog.csdn.net/weixin_39951988/article/details/87773322)