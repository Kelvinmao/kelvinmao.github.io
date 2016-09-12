---
layout: post
title: 26.Remove Duplicates from Sorted Array
description: ""
modified: 2016-06-01T15:27:45-04:00
tags: [Leetcode Review]
image:
  feature: abstract-10.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

# 27. Remove Element
描述:

Given an array and a value, remove all instances of that value in place and return the new length.

Do not allocate extra space for another array, you must do this in place with constant memory.

The order of elements can be changed. It doesn't matter what you leave beyond the new length.

Example:
Given input array nums = [3,2,2,3], val = 3

Your function should return length = 2, with the first two elements of nums being 2.

Hint:

1.Try two pointers.

2.Did you use the property of "the order of elements can be changed"?

3.What happens when the elements to remove are rare?

分析:

因为题目中明确要求in-place完成删除操作，所以我们不能申请额外的存储空间，那么方法就是从头到尾遍历并对数组重新赋值，代码如下:

```c++
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int new_len=0,i=0,size=nums.size();
        for(i=0;i<nums.size();i++){
            if(nums[i]!=val){
                nums[new_len++]=nums[i];
            }
        }
        return new_len;
    }
};
```
另一种one-line的解法是使用STL中的remove函数

```c++
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        return distance(nums.begin(),remove(nums.begin(),nums.end(),val));
    }
};
```

# 203. Remove Linked List Elements

描述:

Remove all elements from a linked list of integers that have value val.

Example

Given: 1 --> 2 --> 6 --> 3 --> 4 --> 5 --> 6, val = 6

Return: 1 --> 2 --> 3 --> 4 --> 5

分析:

此题要求在链表中删除指定值，那么链表与数组的区别在于链表不能随机访问，所以我们必须要遍历整个链表并且用于遍历链表的指针记录的应该是指定值的前驱元素。代码如下:

考虑到第一个结点有可能被删除，所以我们使用了dummy node这个编程技巧。

```
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
        struct ListNode dummy(0);
        dummy.next=head;
        struct ListNode *pmove=&dummy;
        while(pmove->next){
            if(pmove->next->val==val)
                pmove->next=pmove->next->next;
            else
                pmove=pmove->next;
        }
        return dummy.next;
    }
};
```

# 19. Remove Nth Node From End of List

Given a linked list, remove the nth node from the end of list and return its head.

For example,


```
 Given linked list: 1->2->3->4->5, and n = 2.

   After removing the second node from the end, the linked list becomes 1->2->3->5.
```

Note:

Given n will always be valid.

Try to do this in one pass.

这道题要求我们删除链表倒数第n个元素，快慢指针法即可，注意循环条件。


```
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        struct ListNode dummy(0);
        dummy.next=head;
        struct ListNode * fast=&dummy,*slow=&dummy,*tmp=NULL;
        for(int i=n;i>0;i--)
            fast=fast->next;
        while(fast->next){
            fast=fast->next;
            slow=slow->next;
        }
        tmp=slow->next;
        slow->next=tmp->next;
        delete tmp;
        return dummy.next;
    }
};
```
