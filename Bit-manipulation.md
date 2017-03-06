##Bit manipulation
####342. Power of Four
学习一条位运算基础知识：如果n是2^m，那么n-1是m-1个1的重复而n是1后面跟着m-1个0，这样的话n & (n-1) == 0

接着这道题要用到的就是等比数列求和，Sn = a1(q^n - 1)/(q - 1)，赋值q=4, a1 = 1，得到(4^n - 1)必然为3的倍数
~~~~
return num > 0 and num & (num - 1) == 0 and (num - 1) % 3 == 0
~~~~
