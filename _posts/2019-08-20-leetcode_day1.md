---
layout: post
title:  "leetcode_day1"
date:   2019-08-20 22:00:01 +0800
categories: leetcode
tag: leetcode
---

* content
{:toc}


[两数之和]

        class Solution(object):
            def twoSum(self, nums, target):
                """
                :type nums: List[int]
                :type target: int
                :rtype: List[int]
                """
                dict_buff = {}
                for i in xrange(len(nums))
                    x = num[i]
                    if (target - x) in dict_buff:
                        return [dict_buff[target - x], i]
                    dict_buff[x] = i

第一次刷leetcode，这体验酸爽，需要做的还是很多的，加油！