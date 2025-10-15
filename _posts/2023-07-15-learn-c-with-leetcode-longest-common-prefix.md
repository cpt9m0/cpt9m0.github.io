---
title: "Learn C++ with LeetCode: Longest Common Prefix"
subtitle: "Finding the longest common prefix among strings using C++ and LeetCode"
date: 2023-07-15
permalink: /posts/2023-07-15/learn-c-with-leetcode-longest-common-prefix/
categories:
  - Programming
    - C++
    - LeetCode
tags:
  - Programming
    - C++
    - LeetCode
    - Algorithms
excerpt: "Solving the Longest Common Prefix problem in C++ using LeetCode as a learning platform."
---

### Learn C++ with LeetCode: Longest Common Prefix

#### I have decided to learn the C++ programming language not by watching courses or online schools, but by using LeetCode exercises. At first, I try to solve the problem in my own words, then I took a look at people’s solutions to learn C++ syntax, tips and tricks. I hope you enjoy…

![](https://cdn-images-1.medium.com/max/800/1*QVINL_nP_uaPoqJAHpd8Rw.png)Image from:<https://favtutor.com/blogs/longest-common-prefix>

**LeetCode Problem:** [Longest Common Prefix](https://leetcode.com/problems/longest-common-prefix/)

>  _Please read th_ e description and try to solve the problem on your own (in any programming _language) before reading this article!_

The problem asks us to find the longest common prefix among a set of strings. Given a vector of strings, we need to return the longest common prefix that is shared among all the strings. If there is no common prefix, we should return an empty string. Here is an example:
    
    
    Input: strs = ["flower","flow","flight"]  
    Output: "fl"

To solve this problem, we can use a simple approach. **We start by sorting the input vector of strings** in lexicographical order. By doing so, the strings with the common prefix will be adjacent to each other.

#### Sort in C++

The `std::sort()` function in C++ is a part of the Standard Template Library (STL) and is used to sort elements in a specified range. It is a generic algorithm that can be used to sort various types of containers, such as vectors, arrays, and lists.

The syntax for `std::sort()` is as follows:
    
    
    template <class RandomAccessIterator>  
    void sort(RandomAccessIterator first, RandomAccessIterator last);

The function takes two iterators as arguments: `first` and `last`, which define the range of elements to be sorted. The `first` iterator points to the beginning of the range, and the `last` iterator points to one position past the last element in the range.

The `std::sort()` function uses a comparison function or operator to determine the order of the elements. By default, it uses the less-than (`<`) operator to compare the elements. However, you can also provide a custom comparison function as a third argument to define a custom sorting order.

The `std::sort()` function rearranges the elements in the range specified by `first` and `last` in ascending order according to the specified comparison criteria.

Here’s an example that demonstrates how to use `std::sort()` to sort a vector of integers in ascending order:
    
    
    #include <iostream>  
    #include <vector>  
    #include <algorithm>  
      
    int main() {  
        std::vector<int> numbers = {5, 2, 8, 1, 6};  
      
        std::sort(numbers.begin(), numbers.end());  
      
        std::cout << "Sorted numbers: ";  
        for (std::size_t i = 0; i < numbers.size(); i++) {  
            std::cout << numbers[i] << " ";  
        }  
        std::cout << std::endl;  
      
        return 0;  
    }

**Note:** In order to get the length of a vector you can call the `size()` on the vector.

> **Try to write and execute the above code. Please, don’t use copy and paste, just type it!**

#### Let’s Continue…

We initialize an empty string called `lcp` to store the longest common prefix. Then, we take the first and last string from the sorted vector. **Since the vector is sorted, the first and last strings will be the ones with the smallest and largest lexicographical order, respectively.**

Next, we iterate through the characters of the first string. We compare each character with the corresponding character in the last string. If the characters match, we append the character to the `lcp` string. **If they don't match, we break out of the loop because we have found the longest common prefix.**

Finally, we return the `lcp` string, which contains the longest common prefix among all the strings.

#### C++ Implementation
    
    
    class Solution {  
    public:  
        string longestCommonPrefix(vector<string>& strs) {  
            string lcp = "";  
            // sort strs  
            sort(strs.begin(), strs.end());   
              
            string first_str = strs[0];  
            string last_str = strs[strs.size() - 1];  
      
            for (int i=0; i< first_str.size(); i++) {  
                if (first_str[i] == last_str[i])  
                    lcp += first_str[i];  
                else  
                    break;  
            }  
      
            return lcp;  
        }  
    };

Well Done!