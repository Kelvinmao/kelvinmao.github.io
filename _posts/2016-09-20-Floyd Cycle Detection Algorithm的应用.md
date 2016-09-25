---
layout: post
title: Floyd Cycle Detection Algorithm的应用
description: ""
modified: 2016-06-01T15:27:45-04:00
tags: [Leetcode Review]
image:
  feature: abstract-10.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

```
https://leetcode.com/problems/find-the-duplicate-number/
https://leetcode.com/problems/linked-list-cycle/

```

这篇文章通过实际刷题过程中遇到的两个问题对```Floyd Cycle Detection Algorithm```进行复习总结。
-
# Floyd Cycle Detection Algorithm

这个算法的核心思想就是用快慢指针在链表上移动，如果有环，快指针就必然会追上慢指针。不仅在探测链表是否存在环问题上有应用，还有不少的延伸应用。

Python代码如下(via Wikipedia):

```python
def brent(f, x0):
    # main phase: search successive powers of two
    power = lam = 1
    tortoise = x0
    hare = f(x0)  # f(x0) is the element/node next to x0.
    while tortoise != hare:
        if power == lam:  # time to start a new power of two?
            tortoise = hare
            power *= 2
            lam = 0
        hare = f(hare)
        lam += 1

    # Find the position of the first repetition of length λ
    mu = 0
    tortoise = hare = x0
    for i in range(lam):
    # range(lam) produces a list with the values 0, 1, ... , lam-1
        hare = f(hare)
    # The distance between the hare and tortoise is now λ.

    # Next, the hare and tortoise move at same speed until they agree
    while tortoise != hare:
        tortoise = f(tortoise)
        hare = f(hare)
        mu += 1
 
    return lam, mu
```


# 141. Linked List Cycle

描述:

Given a linked list, determine if it has a cycle in it.

Follow up:

Can you solve it without using extra space?

分析:

这道题想用恒定空间解决，就必须用FLoyd算法，思路比较简单。


```c++
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
    bool hasCycle(ListNode *head) {
        if(!head)
            return false;
        struct ListNode * pmove=head,* qmove=head;
        while(pmove&&qmove&&qmove->next){
            pmove=pmove->next;
            qmove=qmove->next->next;
            if(pmove==qmove)
                return true;
        }
        return false;
    }
};
```

# 142. Linked List Cycle II

描述:

Given a linked list, return the node where the cycle begins. If there is no cycle, return null.

Note: Do not modify the linked list.

Follow up:

Can you solve it without using extra space?

分析:

这道题的要求高一些，要求找出环的入口，那么我们知道当相遇时，快指针一定比慢指针多跑了环长的整数倍。所以我们可以在相遇时从起点释放一个慢指针，当其与原来的慢指针相遇时，即为环的入口。

这个证明在以前的文章中写过，不再赘述。

代码如下:


```c++
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
    ListNode *detectCycle(ListNode *head) {
        if(!head||!head->next)
            return NULL;
        struct ListNode * fast=head,*slow=head,*entry=head;
        while(fast&&fast->next){
            fast=fast->next->next;
            slow=slow->next;
            if(fast==slow){
                while(entry!=slow){
                    entry=entry->next;
                    slow=slow->next;
                }
                return entry;
            }
        }
        return NULL;
    }
};
```





# 287. Find the Duplicate Number

描述:

Given an array nums containing n + 1 integers where each integer is between 1 and n (inclusive), prove that at least one duplicate number must exist. Assume that there is only one duplicate number, find the duplicate one.

Note:

1.You must not modify the array (assume the array is read only).

2.You must use only constant, O(1) extra space.

3.Your runtime complexity should be less than O(n2).

4.There is only one duplicate number in the array, but it could be repeated more than once.

分析：

这道题需要借鉴上面的思想。

一个数组中有且只有一个重复数字，数组索引和元素存在映射关系，所以也可以用FLoyd算法，与快慢指针不同的是，此处的快指针是做两次映射，而慢指针只做一次映射。最后让快指针从0开始找环的入口即为重复元素。代码如下:

```c++
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        if(nums.empty())
            return 0;
        int slow=nums[0],fast=nums[nums[0]];
        while(slow!=fast){
            slow=nums[slow];
            fast=nums[nums[fast]];
        }
        fast=0;
        while(fast!=slow){
            slow=nums[slow];
            fast=nums[fast];
        }
        return fast;
    }
};

```
