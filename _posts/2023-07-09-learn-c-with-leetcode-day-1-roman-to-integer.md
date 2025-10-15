---
title: "Learn C++ with LeetCode: Day 1 — Roman to Integer"
subtitle: "Roman to Integer — Learning C++ through LeetCode exercises"
date: 2023-07-09
permalink: /posts/2023-07-09/learn-c-with-leetcode-day-1-roman-to-integer/
categories:
  - Programming
    - C++
    - LeetCode
tags:
  - Programming
    - C++
    - LeetCode
    - Algorithms
excerpt: "Solving Roman to Integer problem in C++ using LeetCode as a learning platform."
canonical_url: https://medium.com/@seyyedaliayati/learn-c-with-leetcode-day-1-roman-to-integer-cd3c9e55ffdd
---

### Learn C++ with LeetCode: Roman to Integer

#### I have decided to learn the C++ programming language not by watching courses or online schools, but by using LeetCode exercises. At first, I try to solve the problem in my own words, then I took a look at people’s solutions to learn C++ syntax, tips and tricks. I hope you enjoy…

![](https://cdn-images-1.medium.com/max/800/1*Sr6Tcrlm7wQi8Bg0vLc7fg.png)Image from: <https://www.timeanddate.com/calendar/how-do-roman-numerals-work.html>

**LeetCode Problem:** [Roman to Integer](https://leetcode.com/problems/roman-to-integer/description/)

> Please read the description and try to solve the problem on your own (in any programming language) before reading this article!

In order to solve this problem, I need something like dictionary in Python (i.e. a map) to map Roman letters to integers. This specific data structure is called `unordered_map` in C++; see [here](https://www.geeksforgeeks.org/unordered_map-in-cpp-stl/).
    
    
    unordered_map<char, int> m;  
      
    m['I'] = 1;  
    m['V'] = 5;  
    m['X'] = 10;  
    m['L'] = 50;  
    m['C'] = 100;  
    m['D'] = 500;  
    m['M'] = 1000;

If you read the description very carefully, you’ll see:

> It is **guaranteed** that `s` is a valid roman numeral in the range `[1, 3999]`.

A valid roman numeral is written largest to smallest from left to right, but there are some exceptions. In those cases, we have to subtract the value instead of adding. For instance `IV` is 5–1=4 but `VI` is 5+1=6.
    
    
    // variable to store the final result  
    int ans = 0;  
      
    for (int i=0; i< s.length(); i++) {  
        if (i+1 < s.length() && m[s[i]] < m[s[i+1]]) {  
            ans -= m[s[i]];  
        }  
        else {  
            ans += m[s[i]];  
        }  
    }  
      
    return ans;

That’s it!