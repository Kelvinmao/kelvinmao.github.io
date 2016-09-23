---
layout: post
title: Majority Voting Algorithm的实际应用
description: ""
modified: 2016-06-01T15:27:45-04:00
tags: [Leetcode Review]
image:
  feature: abstract-10.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

对于找出一个序列中的Majority element，有很多种方法，可以Brute force，可以借助hashmap。

但我们要想做到O(n)的时间复杂度和O(1)的空间复杂度，就必须要使用Majority Voting Algorithm。

# Majority Voting Algorithm

描述:

The algorithm is carried out in two steps:

1.Eliminate all elements except one.

Iterating through the array of numbers, maintain a current candidate and a counter initialized to 0. With the current element x in iteration, update the counter and (possibly) the candidate: if the counter is 0, set the current candidate to x and the counter to 1. If the counter is not 0, increment or decrement the counter based on whether x is the current candidate.

2.Determine if the remaining element is a valid majority element.

With the candidate acquired in step 1, iterate through the array of numbers and count its occurrences. Determine if the result is more than half of the sequence's length. If so, the candidate is the majority. Otherwise, the sequence doesn't contain a majority.

简单的说，就是维护一个curIndex，用来记录当前的候选元素，count用来记录得票数，具体规则如下:
1.如果当前元素与候选元素相同，得票;不相同，减去一票
2.如果票数为0，则候选元素切换为当前元素，票数从1重新计算
3.检查候选元素是否过半

JAVA实现如下:

```java
public int majorityElement(int[] num) {
        int n = num.length;
        int candidate = num[0], counter = 0;
        for (int i : num) {
            if (counter == 0) {
                candidate = i;
                counter = 1;
            } else if (candidate == i) {
                counter++;
            } else {
                counter--;
            }
        }

        counter = 0;
        for (int i : num) {
            if (i == candidate) counter++;
        }
        if (counter <= n / 2) return -1;
        return candidate;

    }
```

那么我们以例题来认识Majority Voting Algorithm的实际应用。

# 169. Majority Element

Given an array of size n, find the majority element. The majority element is the element that appears more than ⌊ n/2 ⌋ times.

You may assume that the array is non-empty and the majority element always exist in the array.

那么这道题就是典型的多数投票的应用，我们直接上代码:

```c++
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        int curIndex=0,count=0,i=0;
        for(i=0;i<nums.size();i++){
            nums[curIndex]==nums[i]?count++:count--;
            if(!count){
                curIndex=i;
                count=1;
            }
        }
        return nums[curIndex];
    }
};
```

# 229. Majority Element II

Given an integer array of size n, find all elements that appear more than ⌊ n/3 ⌋ times. The algorithm should run in linear time and in O(1) space.

Hint:

How many majority elements could it possibly have?

Do you have a better hint? 

这道题和上面的题唯一的区别在于这个题目中要求找出的是出现次数超过 n/3 的元素。应用多数投票算法前，我们首先要想一轮投票过后能够选出最多多少个元素。

这道题很明显有可能会出现两个多数元素。所以我们需要设置两个候选元素，其余规则不变，代码如下:


```
class Solution {
public:
    vector<int> majorityElement(vector<int>& nums) {
        int curIndex1=0,curIndex2=0,count1=0,count2=0;
        int size=nums.size(),i=0,tri=size/3;
        vector<int>res;
        for(i=0;i<nums.size();i++){
            if(nums[i]==nums[curIndex1])
                count1++;
            else if(nums[i]==nums[curIndex2])
                count2++;
            else if(!count1){
                count1=1;
                curIndex1=i;
            }
            else if(!count2){
                count2=1;
                curIndex2=i;
            }
            else{
                count1--;
                count2--;
            }
        }
        count1=0;
        count2=0;
        for(i=0;i<nums.size();i++){
            if(nums[i]==nums[curIndex1])
                count1++;
            else if(nums[i]==nums[curIndex2])
                count2++;
        }
        if(count1>tri)
            res.push_back(nums[curIndex1]);
        if(count2>tri)
            res.push_back(nums[curIndex2]);
        return res;
    }
};
```

