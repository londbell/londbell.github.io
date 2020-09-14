---
title: 二叉树中序遍历
date: 2020-09-14 15:58:49
tags:
	- Java 
	- LeetCode 
---

题目描述
-------

给定一个二叉树，返回它的中序遍历结果。

示例:

```` shell
输入: [1,null,2,3]
   1
    \
     2
    /
   3
输出: [1,3,2]
````
<!-- more -->

[原题地址](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

思路：

中序遍历里输出的序列中，位于前面的元素必定位于后面元素的前面
相当于得先把我的左子树遍历完，才考虑自己，最后才是右子树

递归解法

-------

```` java
class Solution {

    ArrayList list = new ArrayList<Integer>();


    public List<Integer> inorderTraversal(TreeNode root) {
        if(root == null) {
            return list;
        }

        if(root.left!=null) {
            inorderTraversal(root.left);
        }

        list.add(root.val);

        if(root.right!=null) {
            inorderTraversal(root.right);

        }
        return list;
    }
}
````

非递归写法，大致思路是通过自己保存栈的状态来避免递归。

每次内循环都将剩下最左的那个元素加到栈顶，将它加入递归结果后，可以先取它的右节点，如果为空，就继续从栈顶取元素操作。所以内循环就是不停添加左子树元素到栈顶，优先处理左边，这也是中序遍历的思想。

非递归写法
----------

```` java

/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {

    public List<Integer> inorderTraversal(TreeNode root) {
        Deque<TreeNode> temp = new LinkedList();
        ArrayList<Integer> result = new ArrayList();

        while(root != null || !temp.isEmpty()) {
            while(root != null) {
                temp.push(root);
                root = root.left;
            }
            root = temp.pop();
            result.add(root.val);
            root = root.right;

        }
        return result;

    }
}
````
