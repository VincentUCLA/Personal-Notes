# Personal-Notes
##Stack & Queues
####Infix Expression Evaluation

中缀到后缀转换：要点在于，为什么遇到计算符时要把优先级大于等于自己的运算符全部弹出？因为在后缀表达式里他们放在前面就等于先去计算他们啊；栈在这里实际上就是起到的一个保存计算顺序的作用，而“有控制地”从栈中弹出运算符可以达到把中缀式代换成语法树（也就是后缀式）的目的。

相关leetcode题目：150. Evaluate Reverse Polish Notation, 224. Basic Calculator

~~~~
def tokenize(self, str):
    newS = str.replace("(", " ( ")
    newS = newS.replace(")", " ) ")
    newS = newS.replace("+", " + ")
    newS = newS.replace("-", " - ")
    newS = newS.replace("*", " * ")
    newS = newS.replace("/", " / ")
    return newS.split(' ')


def optr(self, char):
    if char == '*' or char == '/':
        return 2
    elif char == '+' or char == '-':
        return 1
    else:
        return 0


def in2post(self, str):
    token = self.tokenize(str)
    stk = []
    ret = []
    for i in token:
        if i.isdecimal():
            ret.append(i)
        elif i == '(':
            stk.append(i)
        elif self.optr(i) > 0:
            if len(stk) == 0:
                stk.append(i)
            else:
                top = stk[len(stk) - 1]
                while self.optr(top) >= self.optr(i):
                    ret.append(top)
                    top = stk.pop()
                stk.append(i)
        elif i == ')':
            top = stk[len(stk) - 1]
            while top != '(':
                ret.append(top)
                top = stk.pop()
    while len(stk) > 0:
        ret.append(stk.pop())
    return ret
~~~~

计算后缀式：这个不难，唯一需要注意的部分是两个操作数的弹出顺序。

~~~~
def cal(self, optr, opte1, opte2):
    opt1 = int(opte1)
    opt2 = int(opte2)
    if optr == '+':
        return opt1 + opt2
    elif optr == '-':
        return opt1 - opt2
    elif optr == '*':
        return opt1 * opt2
    elif optr == '/':
        return opt1 / opt2
    else:
        exit(1)


def eval(self, str):
    stk = []
    post = self.in2post(str)
    for i in post:
        if i.isdecimal():
            stk.append(i)
        else:
            opte2 = stk.pop()
            opte1 = stk.pop()
            stk.append(self.cal(i, opte1, opte2))
    return stk.pop()
~~~~
##Sorting
选择排序最好理解，每次迭代都从子数组里寻找最小值即可，第k次迭代抽出来的即是第k小值

~~~~
def swap(self, arr, i, j):
    temp = arr[j]
    arr[j] = arr[i]
    arr[i] = temp


def selectSort(self, arr):
    l = len(arr)
    for i in range(0, l):
        min = 2 ** 31
        m = 0
        for j in range(i, l):
            if arr[j] < min:
                min = arr[j]
                m = j
        self.swap(arr, i, m)
    return arr
~~~~

插入排序的话需要假设前面第i-1个数字是排好序的，然后把第i个数字插到已经排好序的数组里的恰当位置；既然是插入那么其实用链表比用数组要经济

~~~~
def insertSort(self, arr):
    l = len(arr)
    for i in range(1, l):
        temp = arr[i]
        j = i - 1
        while j >= 0 and arr[j] > temp:
            arr[j + 1] = arr[j]
            j -= 1
        arr[j + 1] = temp
    return arr
~~~~

合并排序很容易理解，要点是每排完一个数组就把另一个数组剩余值贴后面

~~~~
def merge(self, arr1, arr2):
    arr = []
    [l1, l2] = [len(arr1), len(arr2)]
    [i1, i2] = [0, 0]
    while i1 < l1 and i2 < l2:
        if arr1[i1] < arr2[i2]:
            arr.append(arr1[i1])
            i1 += 1
        else:
            arr.append(arr2[i2])
            i2 += 1
    if i1 == l1:
        arr += arr2[i2:]
    else:
        arr += arr1[i1:]
    return arr


def mergeSort(self, arr):
    l = len(arr)
    if l == 1:
        return arr
    elif l == 2:
        if arr[0] > arr[1]:
            self.swap(arr, 0, 1)
        return arr
    else:
        l1 = l // 2
        m1 = self.mergeSort(arr[0:l1])
        m2 = self.mergeSort(arr[l1:])
        return self.merge(m1, m2)
~~~~

快速排序没什么好说的

~~~~
def quickSort(self, arr):
    l = len(arr)
    if l <= 1:
        return arr
    pivot = arr[int(random.random() * 200) % l]
    a = []
    b = []
    c = []
    for i in arr:
        if i < pivot:
            a.append(i)
        elif i == pivot:
            b.append(i)
        else:
            c.append(i)
    return self.quickSort(a) + b + self.quickSort(c)
~~~~

###Quick-select
这个只要体会了思想，题目倒并不是很难，但如果不理解这个思想的话有的题目是完全无从措手的

相关leetcode题目：4. Median of Two Sorted Arrays，这道题因为是两个已经排好序的数列，可以略去一些选择的步骤

~~~~
def quickselect(self, arr, k):
    print(arr)
    print(k)
    if k == 0:
        return arr[0]
    else:
        l = len(arr)
        r = int(random.random() * 200) % l
        temp = arr[r]
        [a, b, c] = [[], [], []]
        for i in arr:
            if i < temp:
                a.append(i)
            elif i == temp:
                b.append(i)
            else:
                c.append(i)
        if len(a) < k <= len(a) + len(b):
            return temp
        elif k <= len(a):
            return self.quickselect(a, k)
        else:
            return self.quickselect(c, k - len(a) - len(b))
~~~~

####4. Median of Two Sorted Arrays
There are two sorted arrays nums1 and nums2 of size m and n respectively. Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).

本题就是采用的Quick-select的思想，把求两个有序数列中位数，转化成求两个有序数列中第k大的数。采取减治法：将两个数列各自分成两段，两数列前半段共有k个数字；如果两数列前半段的末尾数字相等，那即为所求；否则的话，尾数较小的一个前半段中是不可能出现这个第k大数字的，下次递归时略去即可。	

~~~~
public double findMedianSortedArrays(int[] nums1, int[] nums2) {
    int l = nums1.length + nums2.length;
    if (l % 2 == 1) return findKth(nums1, nums2, (l / 2 + 1));
    else return (findKth(nums1, nums2, l / 2) + findKth(nums1, nums2, l / 2 + 1)) / 2.0;
}

public double findKth(int[] nums1, int[] nums2, int k){
    int l = k / 2, l1 = nums1.length, l2 = nums2.length;
    if (l1 > l2) return findKth(nums2, nums1, k);
    if (l1 == 0) return nums2[k‐1];
    if (k == 1) return Math.min(nums1[0], nums2[0]);
    int pa = Math.min(l, l1), pb = k ‐ pa;
    if (nums1[pa‐1] == nums2[pb‐1]) return nums1[pa‐1];
    else if (nums1[pa‐1] < nums2[pb‐1]){
        int[] temp = Arrays.copyOfRange(nums1, pa, l1);
        return findKth(temp, nums2, k ‐ pa);
    } else {
        int[] temp = Arrays.copyOfRange(nums2, pb, l2);
        return findKth(nums1, temp, k ‐ pb);
    }
}
~~~~
##Tree
树是最简单的数据结构，主要是因为一般不会用迭代来处理树的内容，只有递归的话比较好想
####111. Minimum Depth of Binary Tree
Given a binary tree, find its minimum depth. The minimum depth is the number of nodes along the shortest path from the root node down to the nearest leaf node.

这个题目的陷阱在于如果一个节点只有一侧有儿子，你是不能返回min(left, right)的，因为此时有一侧返回值是0

~~~~
public int minDepth(TreeNode root) {
    if (root == null) return 0;
    int left = minDepth(root.left);
    int right = minDepth(root.right);
    if (left == 0 || right == 0)
        return left + right + 1;
    else 
        return Math.min(left, right) + 1;
}
~~~~
##Binary Search
二分搜索的思想是很简单的，但一次性写对也不容易：

~~~~
def binarysearch(self, arr, k):
    m = len(arr) // 2
    if k == arr[m]:
        return arr[m]
    elif k > arr[m]:
        return self.binarysearch(arr[m:], k)
    else:
        return self.binarysearch(arr[0:m], k)
~~~~

另外不要拘泥于python提供的简单写法：

~~~~
def binarysearch(self, arr, k, lo, hi):
    m = (lo + hi) // 2
    if k == arr[m]:
        return arr[m]
    elif k < arr[m]:
        return self.binarysearch(arr, k, lo, m)
    else:
        return self.binarysearch(arr, k, m + 1, hi)
~~~~

####35. Search Insert Position
Given a sorted array and a target value, return the index if the target is found. If not, return the index where it would be if it were inserted in order. You may assume no duplicates in the array.

本题几乎是二分搜索的“母题”，需要熟练掌握；由于他不会给一个超大的数组，不需要考虑溢出问题
~~~~
public static int searchInsert(int[] nums, int target) {
    if (nums == null)
        return - 1;
    int i = 0;
    int j = nums.length;
    if (target > nums[nums.length - 1]) return nums.length;
    else {
        while (i < j) {
            int m = (i + j) / 2;
            if (target > nums[m])
                i = m + 1;
            else
                j = m;
        }
    }
    return j;
}
~~~~

相关leetcode题目：这真的是要多少有多少……

##Union-find
Quick-find: 确定两个点是否处在同一个连通集里：遍历所有的边(p, q)，每次都把id[p]改成id[q]

~~~~
class UF:
    id = []
    count = 0

    def __init__(self, N):
        self.count = N
        for i in range(0, N):
            self.id.append(i)

    def dump(self):
        temp = ""
        for i in range(0, self.count):
            temp += (" " + str(i))
        print(temp)
        temp = ""
        for i in self.id:
            temp += (" " + str(i))
        print(temp)
        print()

    def find(self, p):
        return self.id[p]

    def quick_find(self, arr):
        for i in arr:
            pID = self.find(i[0])
            qID = self.find(i[1])
            if pID == qID:
                continue
            else:
                print(pID, qID)
                for j in range(0, self.count):
                    if self.id[j] == pID:
                        self.id[j] = qID
                self.dump()
~~~~

##Backtracking & Permutation
###1. Depth First Search
leetcode四大陈腔滥调：回溯搜索、动态规划、双指针，二分法之首的回溯搜索
####51. N-Queens
这题太老套就不叙述题目了，本题是回溯搜索的母题，注意不要为了图省事每次调用递归函数都复制一遍棋盘，放在640k内存的时代这么玩妥妥爆栈。至于位操作之类属于奇技淫巧，实际上很影响代码可读性。

回溯搜索的实质在于，调用递归函数前更改的条件，要在调用完之后再改回来。
~~~~
public List<List<String>> solveNQueens(int n) {
    List<List<String>> ret = new ArrayList<>();
    ArrayList<String> board = new ArrayList<>();
    for (int i = 0; i<n; i++) {
        char[] input = new char[n];
        Arrays.fill(input, '.');
        board.add(String.copyValueOf(input));
    }
    dfs(n, 0, board, ret);
    return ret;
}

public void dfs(int n, int line, ArrayList<String> board, List<List<String>> ret){
    for (int i = 0; i<n; i++){
        char[] input = new char[n];
        Arrays.fill(input, '.');
        input[i] = 'Q';
        board.set(line, String.copyValueOf(input));
        if (validate(line, board)) {
            if (line == n - 1)
                ret.add(new LinkedList<>(board));
        else if (line<n - 1) 
            dfs(n, line+1, board, ret);
        }
        Arrays.fill(input, '.');
        board.set(line, String.copyValueOf(input));
    }
}

public boolean validate(int line, List<String> board){
    int n = board.get(line).indexOf('Q');
    for (int i = 0; i<line; i++) {
        int m = board.get(i).indexOf('Q');
        if (m == n || m == n + line - i || m == n - (line - i)) return false;
    }
    return true;
}
~~~~
####22. Generate Parentheses
Given n pairs of parentheses, write a function to generate all combinations of well-formed parentheses.

把思考过程理解成一个完全二叉树，每向左走一格为加个左括号，向右走一格为加个右括号。深度优先回溯搜索即可（其实任何形式的搜索都可以）。
~~~~
public List<String> generateParenthesis(int n) {
    ArrayList<String> result = new ArrayList<String>();
    dfs(result, "", n, n);
    return result;
}

public void dfs(ArrayList<String> result, String s, int left, int right){
    if(left > right) return;
    if(left == 0 && right == 0){
        result.add(s);
        return;
    }
    if(left>0) dfs(result, s+"(", left - 1, right);
    if(right>0) dfs(result, s+")", left, right - 1);
}
~~~~

###2. Combinatorics
组合问题略微烧脑，高中学的排列组合在这里会派上用场
####31. Next Permutation
Implement next permutation, which rearranges numbers into the lexicographically next greater permutation of numbers. If such arrangement is not possible, it must rearrange it as the lowest possible order (ie, sorted in ascending order). The replacement must be inplace, do not allocate extra memory.

这个题目需要理解排列的性质，找到一个最长的从头递增序列，把序列中倒数第二个数字替换成从右至左的第一个大于它的数字，然后把这个数字右侧的序列排序即可。

~~~~
public void nextPermutation(int[] nums) {
    int l = nums.length;
    if (l<2) return;
    int i;
    boolean perm = false;
    for (i = l - 2; i>=0; i--)
    if (nums[i]<nums[i+1]) {
        perm = true;
        break;
    }
    if (!perm) Arrays.sort(nums);
    else {
        int temp = nums[i], j;
        for (j = l - 1; j>=0; j--)
        if (nums[j]>temp)
            break;
        nums[i] = nums[j];
        nums[j] = temp;
        int[] ret2 = Arrays.copyOfRange(nums, i+1, l);
        Arrays.sort(ret2);
        for (j = i+1; j<l; j++)
            nums[j] = ret2[j - i - 1];
    }
}
~~~~

####89. Gray Code
The gray code is a binary numeral system where two successive values differ in only one bit. Given a nonnegative integer n representing the total number of bits in the code, print the sequence of gray code. A gray code sequence must begin with 0.

这题目简直是脑筋急转弯，可以分成两段理解，首位是0的如何变化，那么首位是1的数字要想每一位变动只相差1个数字，那就只能按照首位0的序列倒序过来。
~~~~
public List<Integer> grayCode(int n) {
    List<Integer> ret = new ArrayList<>();
    if (n == 0){
        ret.add(0);
        return ret;
    } else {
        List<Integer> last = grayCode(n - 1);
        for (int i = last.size() - 1; i>=0; i--)
        last.add(last.get(i) + (1<<(n-1)));
        return last;
    }
}
~~~~
##Hash Table
####1. Two Sum
Given an array of integers, return indices of the two numbers such that they add up to a specific target. You may assume that each input would have exactly one solution.

从头至尾检索一遍数组，如果Hashmap里没有(target - nums[i])就丢进去{nums[i], i}，否则的话就是遇到了结果，返回(target - nums[i])的角标
（Hashmap.get(target - nums[i])）和当前角标。
~~~~
public int[] twoSum(int[] nums, int target) {
    if (nums == null || nums.length == 0)
            return new int[]{0, 0};
    Map<Integer, Integer> hashmap = new HashMap<Integer, Integer>();
    int index1 = 0, index2 = 0;
    for (int i = 0; i < nums.length; i++) {
        if (hashmap.containsKey(target - nums[i])) {
            index1 = hashmap.get(target - nums[i]);
            index2 = i;
            return new int[]{index1, index2};
        } else {
            hashmap.put(nums[i], i);
        }
    }
    return new int[]{0, 0};
}
~~~~

##Matrix
####48. Rotate Image
You are given an n x n 2D matrix representing an image. Rotate the image by 90 degrees (clockwise).

顺时针旋转90度 = 逆时针转90度再水平翻转；逆时针转90度就是求转置矩阵
~~~~
public void rotate(int[][] matrix) {
    for (int i = 0; i<matrix.length; i++)
        for (int j = i; j<matrix[i].length; j++){
            int temp = matrix[i][j];
            matrix[i][j] = matrix[j][i];
            matrix[j][i] = temp;
        }
    for (int i = 0; i<matrix.length; i++){
        for (int j = 0; j<matrix[i].length/2; j++){
            int temp = matrix[i][j];
            matrix[i][j] = matrix[i][matrix.length - 1 - j];
            matrix[i][matrix.length - 1 - j] = temp;
        }
    }
}
~~~~
####54. Spiral Matrix
Given a matrix of m x n elements (m rows, n columns), return all elements of the matrix in spiral order.

这个题不要想太深，维持四个变量记录接下来的最大行数和列数，然后按顺序每计数完一行（列）就更新这四个变量即可。
~~~~
public List<Integer> spiralOrder(int[][] matrix) {
    List<Integer> res = new ArrayList<Integer>();
    if (matrix.length == 0)
        return res;
    int rowBegin = 0;
    int rowEnd = matrix.length-1;
    int colBegin = 0;
    int colEnd = matrix[0].length - 1;
    while (rowBegin <= rowEnd && colBegin <= colEnd) {
        for (int j = colBegin; j <= colEnd; j ++)
            res.add(matrix[rowBegin][j]);
        rowBegin++;
        for (int j = rowBegin; j <= rowEnd; j ++)
            res.add(matrix[j][colEnd]);
        colEnd--;
        if (rowBegin <= rowEnd)
            for (int j = colEnd; j >= colBegin; j --)
                res.add(matrix[rowEnd][j]);
        rowEnd--;
        if (colBegin <= colEnd)
            for (int j = rowEnd; j >= rowBegin; j --) 
                res.add(matrix[j][colBegin]);
        colBegin++;
    }
    return res;
}
~~~~

##Greedy

##Heap

##Dynamic Programming
动态规划看书也看不懂，反正书上也是用例子来讲解，还不如实际做两道题就领悟的快。动态规划既可以理解成带记忆的递归，也可以理解成数学归纳法。
####62. Unique Paths
A robot is located at the top-left corner of a m x n grid. The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid. How many possible unique paths are there? Note: m and n will be at most 100.

Solution: 这个题目是二维动态规划的“母题”，很简单，想清楚2*2的情况就可以做出来。

ps: 高中讲数学归纳法的意义就在这了，动态规划的思想跟数学归纳法很相似，只需要证明两个情况下（0->1, x->x+1）状态转移方程成立即可。
~~~~
public int uniquePaths(int m, int n) {
    int[][] dp = new int[m][n];
    for (int i = 0; i<m; i++)
        dp[i][0] = 1;
    for (int i = 0; i<n; i++)
        dp[0][i] = 1;
    for (int i = 1; i<m; i++)
        for (int j = 1; j<n; j++)
            dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
    return dp[m - 1][n - 1];
}
~~~~
####1. Two Sum
Implement regular expression matching with support for '.' and '*'.

'.' Matches any single character.

'*' Matches zero or more of the preceding element.

The matching should cover the entire input string (not partial).

使用动态规划。字符串为s，正则表达式为p。

预处理：用dp[i][j]表示s串的前i个字符和p串的前j个字符是否匹配。dp[0][0] = true；

如果p[i]为'\*'，则dp[0][i] 需要p一直为'\*'才为真

1. 如果s[i] == p[j]或p[j] == '\.'，dp[i+1][j+1] = dp[i][j]；
2. 如果p[j] == '\*'
    1. 如果s[i] == p[j-1] 或p[j1] == '\.'，dp[i+1][j+1]取下列三者的或
        1. dp[i][j+1] （a*代表多个a）
        2. dp[i+1][j] （a*代表一个a）
        3. dp[i+1][j-1] （a*不代表a）
    2. 否则的话dp[i+1][j+1] = dp[i+1][j1]
~~~~
public boolean isMatch(String s, String p) {
    if (s == null || p == null)
        return false;
    boolean [][] dp = new boolean[s.length()+1][p.length()+1];
    dp[0][0] = true;
    for (int i = 0; i<p.length(); i++)
        if (p.charAt(i) == '*' && dp[0][i - 1])
            dp[0][i + 1] = true;
    for (int i = 0; i<s.length(); i++)
        for (int j = 0; j<p.length(); j++){
            if (s.charAt(i) == p.charAt(j) || p.charAt(j) == '.')
                dp[i+1][j+1] = dp[i][j];
            else if (p.charAt(j) == '*') {
                if (s.charAt(i) == p.charAt(j-1) || p.charAt(j-1) == '.')
                    dp[i+1][j+1] = dp[i+1][j-1] || dp[i+1][j] || dp[i][j+1];
                else
                    dp[i+1][j+1] = dp[i+1][j-1];
            }
        }
    return dp[s.length()][p.length()];
}
~~~~
####44. Wildcard Matching
Implement wildcard pattern matching with support for '?' and '\*'.

'?' Matches any single character.

'\*' Matches any sequence of characters (including the empty sequence).

The matching should cover the entire input string (not partial).

这题和第10题几乎完全一样，只是关键字略有不同
~~~~
public boolean isMatch(String s, String p) {
    boolean[][] dp = new boolean[s.length()+1][p.length()+1];
    dp[0][0] = true;
    for (int i = 1; i <= p.length(); i++)
        dp[0][i] = p.charAt(i-1) == '*' && dp[0][i-1];
    for (int i = 0; i<s.length(); i++)
        for (int j = 0; j<p.length(); j++){
            if (s.charAt(i) == p.charAt(j) || p.charAt(j) == '?')
                dp[i+1][j+1] = dp[i][j];
            else if (p.charAt(j) == '*')
                dp[i+1][j+1] = dp[i+1][j] || dp[i][j+1];
        }
    return dp[s.length()][p.length()];
}
~~~~

##Graph

####Connected & Reachable

####Topological Sort

####Strongly connected

####Dijkstra & Bellman-Ford

####Prim & Kruskal

##String
####StringBuffer

####KMP Algorithm

####Huffman Algorithm

##Bit manipulation

##Misc
####8. String to Integer (atoi)
1. 用trim()函数删除左右的空格，如果剩余字符串为空则返回0；
2. 处理首位的正负号；
3. 处理数字，一旦遇到非数字字符则退出循环，处理溢出。

####65. Valid Number
这题目纠结各种情况就是自讨苦吃，本质仅仅是一道正则表达式练习题。还有人用自动机做，我只想说真的是吃饱了撑的……
~~~~
"[‐+]?(([0‐9]+(.[0‐9]*)?)|.[0‐9]+)(e[‐+]?[0‐9]+)?"
~~~~
