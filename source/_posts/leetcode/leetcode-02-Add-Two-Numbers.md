---
layout: post
title: "Add Two Numbers"
date: 2014-07-03 10:11
comments: true
tags: 
	- Java 
	- LeetCode 
	- 链表 
---


题目描述
-------

给定两个由个位数组成的非空链表，按位置相加，进位累加到下一个元素上，每个元素只留个位，两个链表长度可能不等。

You are given two non-empty linked lists representing two non-negative integers. The digits are stored in reverse order and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
Output: 7 -> 0 -> 8

<!-- more -->
[原题地址](https://leetcode.com/problems/add-two-numbers/)

我的做法：
------------

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public static ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode head = new ListNode(0);
        ListNode temp = head;
        int count = 0;
        head.val = l1.val + l2.val;
        if(head.val >= 10) {
            count = 1;
            head.val %= 10;
        }
        while(l1.next != null || l2.next !=null || count == 1) {
            if(l1.next == null && l2.next == null) {
               temp.next = new ListNode(count);
            }
            else if(l1.next == null) {
                temp.next = new ListNode(l2.next.val + count);
                l2 = l2.next;
            }else if(l2.next == null) {
                temp.next = new ListNode(l1.next.val + count);
                l1 = l1.next;
            }else if(l1.next != null && l2.next != null) {
                temp.next = new ListNode(l1.next.val + l2.next.val + count);
                l1 = l1.next;
                l2 = l2.next;
            }
            if(temp.next.val >= 10) {
                count = 1;
                temp.next.val %= 10;
            }else {
                count = 0;
            }
            temp = temp.next;
        }
        return head;
    }
}
```