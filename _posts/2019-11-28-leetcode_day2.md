---
layout: post
title:  "leetcode_day2"
date:   2019-11-28 21:44:01 +0800
categories: leetcode
tag: leetcode
---

* content
{:toc}


[数字反转]

        class Solution(object):
        def reverse(self, x):
        """
        :type x: int
        :rtype: int
        """
        
        if x>=0:
            x = int(str(x)[::-1])
        else:
            x = -int(str(-x)[::-1])
        return x if x < 2147483648 and x >= -2147483648 else 0

负数的表示：

补码=原码取反+1,一个字节有8位 可以表示的数值范围在 -128到+127,用二进制表示也就是 10000000 - 01111111（注意：最高位表示符号）,最高位是1的都是负数,最高位是0的都是正数。如-7原码是 10000111 然后取反（最高位是符号位不用取反）得11111000，加１得11111001，那么-7的二进制数就是 11111001．
