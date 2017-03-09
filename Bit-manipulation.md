##Bit manipulation
###1. 与操作
####342. Power of Four
学习一条位运算基础知识：如果n是2^m，那么n-1是m-1个1的重复而n是1后面跟着m-1个0，这样的话n & (n-1) == 0

接着这道题要用到的就是等比数列求和，Sn = a1(q^n - 1)/(q - 1)，赋值q=4, a1 = 1，得到(4^n - 1)必然为3的倍数
~~~~
return num > 0 and num & (num - 1) == 0 and (num - 1) % 3 == 0
~~~~

####191. Number of 1 Bits
继续刚才的那条奇技淫巧，n & (n - 1)能够消掉二进制数字最末尾的1，为什么呢？因为最末尾的1以两种形式出现，1后面n个0和1，前者的话，1后面n个0减去1=n个1，两者与操作一下就变成n+1个0，后者的话1-1=0，两者与操作还是0；这题了解这项奇技淫巧之后就反复消掉末尾0即可，我就不上代码了

###2. 掩码操作
####190. Reverse Bits
位操作即使是很基础的技巧也会被认为是魔法的……

这个题的知识点是掩码操作，比如下面例程里的第3行代码，原数字比如是0xabcdefgh，两个掩码操作分别得到0xa0c0e0f0和0x0b0d0f0h，然后一个右移4格（16进制里的1位），一个左移四格，加一起就是0xbadcfehg，连续进行五次即可
~~~~
class Solution:
    # @param n, an integer
    # @return an integer
    def reverseBits(self, n):
        n = (n >> 16) | (n << 16)
        n = ((n & 0xff00ff00) >> 8) | ((n & 0x00ff00ff) << 8)
        n = ((n & 0xf0f0f0f0) >> 4) | ((n & 0x0f0f0f0f) << 4)
        n = ((n & 0xcccccccc) >> 2) | ((n & 0x33333333) << 2)
        n = ((n & 0xaaaaaaaa) >> 1) | ((n & 0x55555555) << 1)
        return n
~~~~
###3. 异或操作

####136. Single Number
基础题，0^x = x，x^x = 0，不上代码了，不难但很基础
####268. Missing Number
题目要求说n个数字里包含[0,n+1]每个数字，只是漏掉一个，那么就从0开始每一位异或两次，一个异或角标i，另一个异或值nums[i]，最后再异或一个n，这样我们一共做了2n+1次异或，相当于[0,n+1]每个数字异或了两遍，除了那个漏掉的数字只异或了一遍

###4. K-map
终于到了通用方法，本科一年级数字电路
####137. Single Number II
这个题目要求在一群出现过3次的数字里找唯一一个只出现过1次的，可以理解成一个状态机，3个状态的话，00->01->10->00，很容易画出来K-map，求出来两个状态机方程，a = (a^b) & (a^c); b = (b^c) & ~a，后面就不用写了。。。

###5. 面向对象编程
####179. Largest Number
这题的逻辑很浅显，然而限于我python力太弱，python3取消cmp之后的语法我也不会写……

这题的逻辑就是按照两个字串连接之后的数字大小来排序，不难
~~~~
class Solution:
    # @param num, a list of integers
    # @return a string
    def largestNumber(self, num):
        num = [str(x) for x in num]
        num.sort(cmp=lambda x, y: cmp(y+x, x+y))
        return ''.join(num).lstrip('0') or '0'
~~~~
