# Misc

## 1. 正则表达式和各种String

### 8. String to Integer (atoi)

1. 用trim()函数删除左右的空格，如果剩余字符串为空则返回0；
2. 处理首位的正负号；
3. 处理数字，一旦遇到非数字字符则退出循环，处理溢出。

### 65. Valid Number

这题目纠结各种情况就是自讨苦吃，本质仅仅是一道正则表达式练习题。还有人用自动机做，我只想说真的是吃饱了撑的……

```py
"[‐+]?(([0‐9]+(.[0‐9]*)?)|.[0‐9]+)(e[‐+]?[0‐9]+)?"
```

### 179. Largest Number

这题的逻辑很浅显，然而限于我python力太弱，python3取消cmp之后的语法我也不会写……

这题的逻辑就是按照两个字串连接之后的数字大小来排序，不难

```py
def largestNumber(self, num):
    num = [str(x) for x in num]
    num.sort(cmp=lambda x, y: cmp(y+x, x+y))
    return ''.join(num).lstrip('0') or '0'
```

## 2. Heap

### 347. Top K Frequent Elements

这个题不难，hash map+heap queue，需要注意的就是heapq默认是最小堆，改成最大堆只需要把key变成负的就可以呀

```py
def topKFrequent(self, nums, k):
    freq, h, ret = {}, [], []
    for i in nums:
        if i in freq:
            freq[i] += 1
        else:
            freq[i] = 1
    for i in freq:
        heapq.heappush(h, (-freq[i], i))
    for i in range(0, k):
        temp = heapq.heappop(h)
        ret.append(temp[1])
    return ret
```

### Given unsorted array of int, each element is the length of a rod, return the minimum total cost of combining all rods

难度2/5，这题目最简单的办法很显然是最小堆，不证明了，每次从最小堆里提出来两根棍子接一起，然后把接起来的新棍子丢最小堆里，每次操作的复杂度是O(logn)，所以总时间复杂度就是O(nlogn)，空间复杂度很显然是O(n)

```java
int minCost(int len[], int n) {
    int cost = 0;
    struct MinHeap* minHeap = createAndBuildMinHeap(len, n);
    while (!isSizeOne(minHeap)) {
        int min     = extractMin(minHeap);
        int sec_min = extractMin(minHeap);
        cost += (min + sec_min);
        insertMinHeap(minHeap, min+sec_min);
    }
    return cost;
}
```

## 3. 取余操作

### Fast power

```py
(a * b) % p = ((a % p) * (b % p)) % p
```

## 4. Boyer-Moore 投票算法

### 169 & 229. Majority Element I & II

这题目里Majority元素必然出现超过半数次数，那么我们可以用一个逆向逻辑去考虑：

1. 一个非majority元素的话，必然有多于他出现次数的其他元素
2. 那么如果所有其他元素的出现次数都少于他，那么他就是majority元素
3. 要点是，数组中从candidate被赋值到count减到0的那一段可以被去除，余下部分的多数元素依然是原数组的多数元素。

229的逻辑和169类似，就不赘述了

```java
public int majorityElement(int[] num) {
    int major=num[0], count = 1;
    for(int i=1; i<num.length;i++) {
        if (count==0) {
            count++;
            major=num[i];
        } else if(major==num[i]) {
            count++;
        } else count--;
    }
    return major;
}
```

```py
def majorityElement(self, nums):
    if not nums:
        return []
    count1, count2, candidate1, candidate2 = 0, 0, 0, 1
    for n in nums:
        if n == candidate1:
            count1 += 1
        elif n == candidate2:
            count2 += 1
        elif count1 == 0:
            candidate1, count1 = n, 1
        elif count2 == 0:
            candidate2, count2 = n, 1
        else:
            count1, count2 = count1 - 1, count2 - 1
    return [n for n in (candidate1, candidate2)
                    if nums.count(n) > len(nums) // 3]
```

## 5. 无法归类的各种怪题

### 189. Rotate Array

这个题不难，ABCD要旋转成CDAB的话，可以先翻转ABCD到DCBA，然后分别翻转两半为CDAB即可

```java
public void rotate(int[] nums, int k) {
    k %= nums.length;
    reverse(nums, 0, nums.length - 1);
    reverse(nums, 0, k - 1);
    reverse(nums, k, nums.length - 1);
}

public void reverse(int[] nums, int start, int end) {
    while (start < end) {
        int temp = nums[start];
        nums[start] = nums[end];
        nums[end] = temp;
        start++;
        end--;
    }
}
```

### 172. Factorial Trailing Zeroes

这个题不难，首先要构成一个0的尾数就必须包含5，其次呢如果这个n的系数里包含不止一个5，需要处理完现在这个再去除以5，处理下一个

```py
def trailingZeroes(self, n):
    if n == 0:
        return 0
    else:
        a = self.trailingZeroes(n // 5)
        return n // 5 + a
```

### 208. Implement Trie (Prefix Tree)

Trie最好是不要用递归写法，太长，容易错，迭代其实不难解决

```py
class TrieNode:
    def __init__(self):
        self.word=False
        self.children={}

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node=self.root
        for i in word:
            if i not in node.children:
                node.children[i]=TrieNode()
            node=node.children[i]
        node.word=True

    def search(self, word):
        node=self.root
        for i in word:
            if i not in node.children:
                return False
            node=node.children[i]
        return node.word

    def startsWith(self, prefix):
        node=self.root
        for i in prefix:
            if i not in node.children:
                return False
            node=node.children[i]
        return True
```

### 155. min stack

这个东西的难点就是如何记录【某一时刻的最小值】，那很简单，每次最小值变得更小，就往栈里连推两次，第一次推最小值，第二次推x；每次最小值变得更大的时候呢，从栈里连弹两次，第一次弹出来的是x，第二次弹出来的是最小值

```java
class MinStack {
    int min = Integer.MAX_VALUE;
    Stack<Integer> stack = new Stack<Integer>();
    public void push(int x) {
        if(x <= min){
            stack.push(min);
            min=x;
        }
        stack.push(x);
    }
    public void pop() {
        if(stack.pop() == min)
            min=stack.pop();
    }
    public int top() { return stack.peek();}
    public int getMin() { return min;}
}
```

### 493. Reverse Pairs

这题目太离谱了，居然连BST都会超时，可以算leetcode上最难最扯淡的几个题目了。数组树太麻烦了，咱用简单算法

使用合并排序的思想，学名叫分割重现关系(Partition Recurrence Relation)，用式子表示是T(i, j) = T(i, m) + T(m+1, j) + C。这里的C就是处理合并两个部分的子问题，用文字来描述就是“已知翻转对的两个数字分别在子数组nums[i, m]和nums[m+1, j]之中，求满足要求的翻转对的个数”

在合并排序的递归函数中，对于有序的两个子数组进行统计翻转对的个数，然后再逐层返回，这就实现了上述的分割重现关系的思想。

```py
def __init__(self):
    self.cnt = 0
def reversePairs(self, nums):
    def msort(lst):
        # merge sort body
        L = len(lst)
        if L <= 1:                          # base case
            return lst
        else:                               # recursive case
            return merger(msort(lst[:int(L/2)]), msort(lst[int(L/2):]))
    def merger(left, right):
        # merger
        l, r = 0, 0                         # increase l and r iteratively
        while l < len(left) and r < len(right):
            if left[l] <= 2*right[r]:
                l += 1
            else:
                self.cnt += len(left)-l     # add here
                r += 1
        return sorted(left+right)           # I can't avoid TLE without timsort...

    msort(nums)
    return self.cnt
```

### 204. Count Primes

埃拉托斯特尼筛法，不难，但这个例程可以学到不少python奇形怪状的语法

```py
def countPrimes(self, n):
    if n < 3: return 0
    primes = [True] * n
    primes[0] = primes[1] = False
    for i in range(2, int(n ** 0.5) + 1):
        if primes[i]:
            primes[i*i:n:i] = [False] * len(primes[i*i:n:i])
    return sum(primes)
```

### 241. Different Ways to Add Parentheses

这题目看代码就好，讲解也讲不明白……实际上是个简单的分治

```py
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
```

### 220. Contains Duplicate III

坐标差不能大于k，值差不能大于t

如果t=0的话其实和LC219是同一个题目，加入t的话就是利用桶排序，每个桶大小为t+1，每经历过一轮k，就删除bucket里i-k所对应的值，这样就可以避免坐标差超过k的情况出现，而值差则是利用桶排序的性质，每一轮中在m桶里的数字很显然值差小于t，两侧桶里的数字经过恰当的测试值差也会小于t

```py
def containsNearbyAlmostDuplicate(self, nums, k, t):
    bucket = {}
    if t < 0: return False
    w, n = t + 1, len(nums)
    for i in range(0, n):
        m = nums[i] // w
        if m in bucket:
            return True
        if m - 1 in bucket and abs(nums[i] - bucket[m - 1]) < w:
            return True
        if m + 1 in bucket and abs(nums[i] - bucket[m + 1]) < w:
            return True
        bucket[m] = nums[i]
        if i >= k:
            del bucket[nums[i-k] // w]
    return False
```

### 233. Number of Digit One

这题目超级蠢，主要考点在思考每一位上1出现次数的逻辑

```java
int countDigitOne(int n) {
    int countr = 0;
    for (long long i = 1; i <= n; i *= 10) {
        long long divider = i * 10;
        countr += (n / divider) * i + min(max(n % divider - i + 1, 0LL), i);
    }
    return countr;
}
```

### 621. Task Scheduler

There is a non-negative cooling interval n that means between two same tasks, there must be at least n intervals that CPU are doing different tasks or just be idle.

这题的逻辑是这样的

1. 模块的次数为任务最大次数减1，模块的长度为n+1，
2. 最后加上的字母个数为出现次数最多的任务，可能有多个并列

```py
def leastInterval(self, tasks, N):
    task_counts = collections.Counter(tasks).values()
    M = max(task_counts)
    Mct = task_counts.count(M)
    return max(len(tasks), (M - 1) * (N + 1) + Mct)
```