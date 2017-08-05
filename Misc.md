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

#### 75. Sort Color
这个做法颇为鸡贼，\[0, i)部分是0，\[i, j)部分是1，\[j, n)部分是2，所以呢，2就一路向后推，遇到小于2的数字就增加j，等于0的时候就增加i
~~~~
def sortColors(self, nums):
    i = j = 0
    for k in range(0, len(nums)):
        v = nums[k]
        nums[k] = 2
        if v < 2:
            nums[j] = 1
            j += 1
        if v == 0:
            nums[i] = 0
            i += 1
~~~~
#### 347. Top K Frequent Elements
这个题不难，hash map+heap queue，需要注意的就是heapq默认是最小堆，改成最大堆只需要把key变成负的就可以呀
~~~~
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
~~~~
#### 155. min stack
这个东西的难点就是如何记录【某一时刻的最小值】，那很简单，每次最小值变得更小，就往栈里连推两次，第一次推最小值，第二次推x；每次最小值变得更大的时候呢，从栈里连弹两次，第一次弹出来的是x，第二次弹出来的是最小值
~~~~
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
~~~~
#### Ternary Expression to Binary Tree

这题目leetcode里没有，可能因为太简单？两个解法，一个是栈解法：

1. 一开始推进去一个树节点
2. 每次向右推两格
	1. 遇到问号呢，就把当前栈顶节点的左子树加上这个节点
	2. 遇到冒号的话，先往外弹一个，然后如果遇到右子树满着的节点，一路从栈往外弹，直到遇到右子树为空的节点
	3. 每次循环的最后把节点推进去

另一个是递归，问号后面的【整个字符串】是左子树，冒号后面的【整个字符串】是右子树呗

~~~~
public TreeNode convert(String expr) {
    char[] exp = expr.toCharArray();
    if (exp.length == 0)
        return null;
    TreeNode root = new TreeNode(exp[0]);
    Stack<TreeNode> stack = new Stack<>();
    stack.push(root);
    for (int i = 1; i < exp.length; i += 2) {
        TreeNode node = new TreeNode(exp[i + 1]);
        if (exp[i] == '?')
            stack.peek().left = node;
        if (exp[i] == ':') {
            stack.pop();
            while (stack.peek().right != null)
                stack.pop();
            stack.peek().right = node;
        }
        stack.push(node);
    }
    return root;
}

Node convertExpression(char[] expression, int i)
{
    if (i >= expression.length)
        return null;
    Node root = new Node(expression[i]);
    ++i;
    if (i < expression.length && expression[i]=='?')
        root.left = convertExpression(expression, i+1);
    else if (i < expression.length)
        root.right = convertExpression(expression, i+1);
    return root;
}
~~~~
#### 41. First Missing Positive

难度2/5，时间复杂度O(n)，空间复杂度O(1)

这题目不算难，首先假定n个数字里第一个漏掉的正数不可能大于n，所以咱忽略掉所有大于n和小于0的数字，剩余数字就排列到原位就可以，然后再遍历一遍已经排布过的数组即可

~~~~
def swap(self, arr, a, b):
    c = arr[a]
    arr[a] = arr[b]
    arr[b] = c

def firstMissingPositive(self, nums):
    l = len(nums)
    for i in range(0, l):
        while 0 < nums[i] <= l and nums[nums[i] - 1] != nums[i]:
            self.swap(nums, i, nums[i] - 1)
    for i in range(0, l):
        if nums[i] != i+1:
            return i+1
    return l+1
~~~~

#### Given unsorted array of int, each element is the length of a rod, return the minimum total cost of combining all rods. 

难度2/5，这题目最简单的办法很显然是最小堆，不证明了，每次从最小堆里提出来两根棍子接一起，然后把接起来的新棍子丢最小堆里，每次操作的复杂度是O(logn)，所以总时间复杂度就是O(nlogn)，空间复杂度很显然是O(n)

~~~~
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
~~~~
	
#### 493. Reverse Pairs

这题目太离谱了，居然连BST都会超时，可以算leetcode上最难最扯淡的几个题目了。数组树太麻烦了，咱用简单算法

使用合并排序的思想，学名叫分割重现关系(Partition Recurrence Relation)，用式子表示是T(i, j) = T(i, m) + T(m+1, j) + C。这里的C就是处理合并两个部分的子问题，用文字来描述就是“已知翻转对的两个数字分别在子数组nums[i, m]和nums[m+1, j]之中，求满足要求的翻转对的个数”

在合并排序的递归函数中，对于有序的两个子数组进行统计翻转对的个数，然后再逐层返回，这就实现了上述的分割重现关系的思想。

~~~~
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
#### 220. Contains Duplicate III

坐标差不能大于k，值差不能大于t

如果t=0的话其实和LC219是同一个题目，加入t的话就是利用桶排序，每个桶大小为t+1，每经历过一轮k，就删除bucket里i-k所对应的值，这样就可以避免坐标差超过k的情况出现，而值差则是利用桶排序的性质，每一轮中在m桶里的数字很显然值差小于t，两侧桶里的数字经过恰当的测试值差也会小于t

~~~~
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
~~~~
#### LC239 - Sliding Window Maximum

当我们遇到新的数时，将新的数和双向队列的末尾比较，如果末尾比新数小，则把末尾扔掉，直到该队列的末尾比新数大或者队列为空的时候才住手。这样，我们可以保证队列里的元素是从头到尾降序的，由于队列里只有窗口内的数，所以他们其实就是窗口内第一大，第二大，第三大...的数。

保持队列里只有窗口内数的方法是每来一个新的把窗口最左边的扔掉，然后把新的加进去。然而由于我们在加新数的时候，已经把很多没用的数给扔了，这样队列头部的数并不一定是窗口最左边的数。这里的技巧是，我们队列中存的是那个数在原数组中的下标，这样我们既可以直到这个数的值，也可以知道该数是不是窗口最左边的数。

为什么时间复杂度是O(N)呢？因为每个数只可能被操作最多两次，一次是加入队列的时候，一次是因为有别的更大数在后面，所以被扔掉，或者因为出了窗口而被扔掉。

~~~~
public int[] maxSlidingWindow(int[] nums, int k) {
    if(nums == null || nums.length == 0) return new int[0];
    LinkedList<Integer> deque = new LinkedList<Integer>();
    int[] res = new int[nums.length + 1 - k];
    for(int i = 0; i < nums.length; i++){
        // 每当新数进来时，如果发现队列头部的数的下标，是窗口最左边数的下标，则扔掉`
        if(!deque.isEmpty() && deque.peekFirst() == i - k) deque.poll();
        // 把队列尾部所有比新数小的都扔掉，保证队列是降序的
        while(!deque.isEmpty() && nums[deque.peekLast()] < nums[i]) deque.removeLast();
        // 加入新数
        deque.offerLast(i);
        // 队列头部就是该窗口内第一大的
        if((i + 1) >= k) res[i + 1 - k] = nums[deque.peek()];
    }
    return res;
}
~~~~
