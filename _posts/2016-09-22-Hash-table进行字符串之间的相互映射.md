---
layout: post
title: Hash-table进行字符串之间的相互映射
description: ""
modified: 2016-06-01T15:27:45-04:00
tags: [Leetcode Review]
image:
  feature: abstract-10.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---


有时候我们需要在两个或多个字符串之间建立映射，以便用来解决一些匹配问题，那么我们可以用```unordered_map```来建立这个映射，同时在字符集较小的情况下，我们可以动用一些编程技巧。

先复习一道简单的题目:

# 299. Bulls and Cows

You are playing the following Bulls and Cows game with your friend: You write down a number and ask your friend to guess what the number is. Each time your friend makes a guess, you provide a hint that indicates how many digits in said guess match your secret number exactly in both digit and position (called "bulls") and how many digits match the secret number but locate in the wrong position (called "cows"). Your friend will use successive guesses and hints to eventually derive the secret number.

For example:

```
Secret number:  "1807"
Friend's guess: "7810"
```
Hint: 1 bull and 3 cows. (The bull is 8, the cows are 0, 1 and 7.)

Write a function to return a hint according to the secret number and friend's guess, use A to indicate the bulls and B to indicate the cows. In the above example, your function should return "1A3B".

Please note that both secret number and friend's guess may contain duplicate digits, for example:

```
Secret number:  "1123"
Friend's guess: "0111"
```

In this case, the 1st 1 in friend's guess is a bull, the 2nd or 3rd 1 is a cow, and your function should return "1A1B".

You may assume that the secret number and your friend's guess only contain digits, and their lengths are always equal.

这道题干比较长的题目其实并不是很难，问题的关键在于我们要建立两个字符串之间的映射，并且弄清楚元素进入这个映射的条件。

那么考虑到字符集较小，我们用数组模拟hash，这是一个很节省空间的编程技巧。

对两个字符串同时进行遍历，两个字符相同时则记为一个Bull，不同时，则让两个字符在数组中对应的元素加1，也就是用来统计出现次数。

最后，要统计cow，对两个数组进行遍历，每次只加较小值，否则会造成cow的重复计数，代码如下:

```c++
class Solution {
public:
    string getHint(string secret, string guess) {
        if(secret.empty()||guess.empty())
            return "0A0B";
        vector<int> countS(10,0);
        vector<int> countG(10,0);
        string res;
        int countA=0,countB=0;
        for(int i=0;i<secret.size();i++){
            if(secret[i]==guess[i])
                countA++;
            else{
                countS[secret[i]-'0']++;
                countG[guess[i]-'0']++;
            }
        }
        for(int i=0;i<countS.size();i++)
            countB+=min(countS[i],countG[i]);
        res=to_string(countA)+'A'+to_string(countB)+'B';
        return res;
    }
};
```

# 290. Word Pattern

Given a pattern and a string str, find if str follows the same pattern.

Here follow means a full match, such that there is a bijection between a letter in pattern and a non-empty word in str.

Examples:

pattern = "abba", str = "dog cat cat dog" should return true.

pattern = "abba", str = "dog cat cat fish" should return false.

pattern = "aaaa", str = "dog cat cat dog" should return false.

pattern = "abba", str = "dog dog dog dog" should return false.

Notes:

You may assume pattern contains only lowercase letters, and str contains lowercase letters separated by a single space.

这道题还是需要建立一个映射，但最开始需要做的并不是马上建立映射，需要用一点编程技巧将str拆分成以空格为间隔的字符串。

拆分完成后，我们需要整理一下新元素进入映射的条件
1.pattern中的字母存在于hash，那么查看以pattern为key的元素是否等于当前的word，不等，直接返回false。
2.不存在时，也不能立即将其加入hash，需要遍历整个hash，查看是否有某个之前的字母已经映射到了当前的word，如果有，直接返回false。

分析清楚后，代码就很简单了。

```c++
class Solution {
public:
    bool wordPattern(string pattern, string str) {
        vector<string> res=divideStr(str);
        unordered_map<string,string> hash;
        istringstream in(str);
        int i=0;
        for(string word;in>>word;i++){
            if(hash.find(pattern[i])!=hash.end()){
                if(hash[pattern[i]]!=word)
                    return false;
            }
            else{
                for(unordered_map<char,string>::iterator it=hash.begin();it!=hash.end();it++){
                    if(it->second==word)
                        return false;
                }
                hash[pattern[i]]=word;
            }
        }
        return i==pattern.size();
    }
};
```

再来看另一道

# 205. Isomorphic Strings

Given two strings s and t, determine if they are isomorphic.

Two strings are isomorphic if the characters in s can be replaced to get t.

All occurrences of a character must be replaced with another character while preserving the order of characters. No two characters may map to the same character but a character may map to itself.

For example,
Given "egg", "add", return true.

Given "foo", "bar", return false.

Given "paper", "title", return true.

这道题其实和上面那道题很相似，如果用hash的话代码几乎没有什么变化。因为只有字母，所以我们用一个编程技巧，即用数组模仿hash，代码如下:

```c++
class Solution {
public:
    bool isIsomorphic(string s, string t) {
        int map1[256]={0},map2[256]={0},i=0;
        for(i=0;i<s.size();i++){
            if(map1[s[i]]!=map2[t[i]])
                return false;
            map1[s[i]]=map2[t[i]]=i+1;
        }
        return i==s.size();
    }
};
```
最后看一道有难度的题目
# 49. Group Anagrams
Given an array of strings, group anagrams together.

For example, given: ```["eat", "tea", "tan", "ate", "nat", "bat"]```, 
Return:

```
[
  ["ate", "eat","tea"],
  ["nat","tan"],
  ["bat"]
]

```
Note: All inputs will be in lower-case.

这道题算是一个对变位词进行归类的题目，那么和我们讨论的用hash进行字符串之间的映射有什么关系呢？

要归类，那么必须要有一个具体且确定的标准，既然是变位词，那么互为变位词的字符串在排序过后一定是相同的，将其作为主键即可。

遍历每一个词，将其加入```unordered_map```即可。最后需要归类时，遍历```unordered_map```，将每个主键对应的```vector```压入结果即可。代码如下:


```c++
class Solution {
private:
    string strSort(string& str){
        int count[26]={0},p=0,size=str.size();
        string res(size,'a');
        for(int i=0;i<str.size();i++)
            count[str[i]-'a']++;
        for(int i=0;i<26;i++)
            for(int j=0;j<count[i];j++)
                res[p++]+=i;   
        return res;
    }
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        if(strs.empty())
            return {};
        unordered_map<string,vector<string>> hash;
        vector<vector<string>> anagrams;
        for(string s:strs){
            string t=strSort(s);
            // sort(t.begin(),t.end());
            hash[t].push_back(s);
        }
        for(unordered_map<string,vector<string>>::iterator pos=hash.begin();pos!=hash.end();pos++){
            vector<string> anagram(pos->second.begin(),pos->second.end());
            anagrams.push_back(anagram);
        }
        return anagrams;
    }
};
```
这里考虑到全部都是小写字母，用库函数中的排序太浪费时间，于是我们自己写一个计数排序可能效率会更高一些，这也是一个编程技巧。