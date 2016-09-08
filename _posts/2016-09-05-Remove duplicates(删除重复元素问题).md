---
layout: post
title: Remove duplicates(删除重复元素问题)
description: ""
modified: 2016-06-01T15:27:45-04:00
tags: [Leetcode Review]
image:
  feature: abstract-10.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

# 26.Remove Duplicates from Sorted Array

描述:

Given a sorted array, remove the duplicates in place such that each element appear only once and return the new length.

Do not allocate extra space for another array, you must do this in place with constant memory.

For example,
Given input array nums = [1,1,2],

Your function should return length = 2, with the first two elements of nums being 1 and 2 respectively. It doesn't matter what you leave beyond the new length.

分析:

遍历数组即可

代码:

使用数组
```c++
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        if(nums.empty())
            return 0;
        int i=0,new_len=0;
        for(i=1;i<nums.size();i++){
            if(nums[i]!=nums[new_len])
                nums[++new_len]=nums[i];
        }
        return new_len+1;
    }
};
```

使用STL
```c++
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        return distance(nums.begin(),unique(nums.begin(),nums.end()));
    }
};
```
看的出来使用STL可以大大地简化我们的代码。

# 80.Remove Duplicates from Sorted Array II

描述:

Follow up for "Remove Duplicates":
What if duplicates are allowed at most twice?

For example,
Given sorted array nums = [1,1,1,2,2,3],

Your function should return length = 5, with the first five elements of nums being 1, 1, 2, 2 and 3. It doesn't matter what you leave beyond the new length.

分析:这两道题的区别在于第二道题中的去除重复时不能只留一个，而是要留两个。那么我们在循环变量的初始值和逻辑判断的条件上做一些变化。

```
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        if(nums.size()<3)
            return nums.size();
        int new_len=1,size=nums.size(),i=0;
        for(i=2;i<size;i++){
            if(nums[i]!=nums[new_len-1])
                nums[++new_len]=nums[i];
        }
        return new_len+1;
    }
};
```

# 83. Remove Duplicates from Sorted List

Given a sorted linked list, delete all duplicates such that each element appear only once.

For example,

Given 1->1->2, return 1->2.

Given 1->1->2->3->3, return 1->2->3.

双指针法，让后指针指向的元素的值与前指针比较即可，注意循环终止条件

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
    ListNode* deleteDuplicates(ListNode* head) {
        if(!head||!head->next)
            return head;
        struct ListNode * pmove=head,*qmove=NULL,*tmp=NULL;
        while(pmove){
            qmove=pmove;
            while(qmove->next){
                if(pmove->val==qmove->next->val){
                        tmp=qmove->next;
                        qmove->next=tmp->next;
                        free(tmp);
                }
                else
                    qmove=qmove->next;
            }
            pmove=pmove->next;
        }
        return head;
    }
};
```

# Remove Duplicates from Sorted List II

描述:

Given a sorted linked list, delete all nodes that have duplicate numbers, leaving only distinct numbers from the original list.

For example,

Given 1->2->3->3->4->4->5, return 1->2->5.

Given 1->1->1->2->3, return 2->3.

分析:

这道题的要求比前面要高一些，不仅要删除重复元素，而且要全部删除一个不留。这就需要我们维护一个pre指针和一个布尔变量，前者指向当前元素的前驱元素，后者标记是否出现了重复，另外，还要对三种情况进行判断：

1.发现当前元素和后继元素相等:
    此时我们只需要按删除链表中某一元素的方法，删去后一元素即可，同时令标记值为```true```
    
2.标记值为```true```
    此时就需要用我们的pre指针，借助其完成删除重复元素集合的首元素，删除之后，令cur指向pre的后继元素，同时令标记值为```false```

3.其余情况
    pre，cur指针后移即可

此外，这道题应用了一个编程技巧，即哑节点(dummy node)，作用相当于链表头结点，因为这道题有可能在表头进行删除操作，所以为了统一操作，我们设置了一个哑节点。

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
    ListNode *deleteDuplicates(ListNode *head) {
        if(!head||!head->next)
            return head;
        struct ListNode dummy(0);
        struct ListNode * pre=&dummy,*cur=head,*tmp=NULL;
        bool duplicate=false;
        dummy.next=head;
        while(cur){
            if(cur->next&&cur->val==cur->next->val){
                tmp=cur->next;
                cur->next=tmp->next;
                free(tmp);
                duplicate=true;
            }
            else if(duplicate){
                pre->next=cur->next;
                free(cur);
                cur=pre->next;
                duplicate=false;
            }
            else{
                pre=cur;
                cur=cur->next;
            }
        }
        head=dummy.next;
        return head;
    }
};

```

