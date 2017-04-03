## Misc
#### 8. String to Integer (atoi)
1. 用trim()函数删除左右的空格，如果剩余字符串为空则返回0；
2. 处理首位的正负号；
3. 处理数字，一旦遇到非数字字符则退出循环，处理溢出。

#### 65. Valid Number
这题目纠结各种情况就是自讨苦吃，本质仅仅是一道正则表达式练习题。还有人用自动机做，我只想说真的是吃饱了撑的……
~~~~
"[‐+]?(([0‐9]+(.[0‐9]*)?)|.[0‐9]+)(e[‐+]?[0‐9]+)?"
~~~~

#### 179. Largest Number
这题的逻辑很浅显，然而限于我python力太弱，python3取消cmp之后的语法我也不会写……

这题的逻辑就是按照两个字串连接之后的数字大小来排序，不难
~~~~
def largestNumber(self, num):
    num = [str(x) for x in num]
    num.sort(cmp=lambda x, y: cmp(y+x, x+y))
    return ''.join(num).lstrip('0') or '0'
~~~~

#### 204. Count Primes
埃拉托斯特尼筛法，不难，但这个例程可以学到不少python奇形怪状的语法
~~~~
def countPrimes(self, n):
    if n < 3: return 0
    primes = [True] * n
    primes[0] = primes[1] = False
    for i in range(2, int(n ** 0.5) + 1):
        if primes[i]:
            primes[i*i:n:i] = [False] * len(primes[i*i:n:i])
    return sum(primes)
~~~~
#### 241. Different Ways to Add Parentheses
这题目看代码就好，讲解也讲不明白……实际上是个简单的分治
~~~~
def diffWaysToCompute(self, input):
    res = []
    for i in range(0, len(input)):
        c = input[i]
        if c == '+' or c == '-' or c == '*':
            a, b = input[0:i], input[i+1:]
            al, bl = self.diffWaysToCompute(a), self.diffWaysToCompute(b)
            for x in al:
                for y in bl:
                    if c == '-':
                        res.append(x-y)
                    elif c == '+':
                        res.append(x+y)
                    elif c == '*':
                        res.append(x*y)
    if len(res) == 0:
        res.append(int(input))
    return res
~~~~
