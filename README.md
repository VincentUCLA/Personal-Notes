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

####31. Next Permutation
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

##Linked List
####2. Add Two Numbers
Input: (2 > 4 > 3) + (5 > 6 > 4)

Output: 7 > 0 > 8

这个题目的难点在于corner case多而且庞杂，但实际上不需要转换成十进制数字，因为他本身提供的链表就是从小到大的，符合加法的计算习惯，生加即可
~~~~
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
    ListNode dummy = new ListNode(0);
    ListNode cur = dummy;
    int c = 0;
    while (l1 != null || l2 != null){
        int a = 0, b = 0, d = 0;
        if (l1 != null) {
            a = l1.val;
            l1 = l1.next;
        }
        if (l2 != null) {
            b = l2.val;
            l2 = l2.next;
        }
        if (a + b + c >= 10) {
            d = (a + b + c) % 10;
            c = 1;
        } else {
            d = a + b + c;
            c = 0;
        }
        cur.next = new ListNode(d);
        cur = cur.next;
    }
    if (c != 0) {
        cur.next = new ListNode(c);
        cur = cur.next;
    }
    cur.next = null;
    return dummy.next;
}
~~~~

##Two pointers
####26. Remove Duplicates from Sorted Array
Given a sorted array, remove the duplicates in place such that each element appear only once and return the new length.
Do not allocate extra space for another array, you must do this in place with constant memory.

本题目是双指针类型题目的“母题”，所有本类型题目都是从本题变化而来。很简单，维持两个指针，都在每次填数字时++，但后面那个指针在每次遇到重复数字时++。
~~~~
public int removeDuplicates(int[] nums) {
    int i = 1, j = 1;
    while (j<nums.length){
        if (nums[j‐1] == nums[j]) j++;
        else nums[i++] = nums[j++];
    }
    return i;
}
~~~~

####3. Longest Substring Without Repeating Characters
Given a string, find the length of the longest substring without repeating characters.

双指针开头i结尾j指代当前字符串，如果哈希表里包含j则删除表中i项并i++，否则添加表中j项并j++
~~~~
public int lengthOfLongestSubstring(String s) {     
    int i = 0, j = 0, max = 0;
    HashSet<Character> set = new HashSet<Character>();
    while (j < s.length()){
        if (!set.contains(s.charAt(j))){
            set.add(s.charAt(j));
            j++;
            max = Math.max(max, j - i);
        } else {
            set.remove(s.charAt(i));
            i++;
        }
    }
    return max;
}
~~~~
####5. Longest Palindromic Substring
Given a string s, find the longest palindromic substring in s. You may assume that the maximum length of s is 1000.

从头到尾i遍历字符串s，如果(i - l - 1, i)处为回文，则回文长度l+2；否则如果(i - l, i)处为回文，则回文长度l+1。
~~~~
public String longestPalindrome(String s) {
    String res = "";
    int l = 0;
    for(int i=0;i<s.length();i++){
        if(isPalindrome(s, i - l - 1, i)){
            res = s.substring(i - l - 1, i + 1);
            l += 2;
        } else if(isPalindrome(s, i - l, i)){
            res = s.substring(i - l, i + 1);
            l += 1;
        }
    }
    return res;
}

public boolean isPalindrome(String s, int begin, int end){
    if(begin<0) return false;
    while(begin<end)
        if(s.charAt(begin++)!=s.charAt(end--)) return false;
    return true;
}
~~~~
####11. Container With Most Water
Given n nonnegative integers a1, a2, ..., an, where each represents a point at coordinate (i, ai). n vertical lines are drawn such that the two endpoints of line i is at (i, ai) and (i, 0). Find two lines, which together with x=axis forms a container,
such that the container contains the most water.

维持一个max函数，一开始从两端测量面积“夹逼”，每次迭代都删除两端中的短边，然后继续测量面积，直到两端相遇
~~~~
public static int maxArea(int[] height) {
    int len = height.length, low = 0, high = len - 1 ;
    int maxArea = 0;
    while (low < high) {
        maxArea = Math.max(maxArea, (high - low) * Math.min(height[low], height[high]));
        if (height[low] < height[high]) low++;
        else high--;
    }
    return maxArea;
}
~~~~
####42. Trapping Rain Water
Given n nonnegative integers representing an elevation map where the width of each bar is 1, compute how much water it is able to trap after raining.

双指针从左右向中间“夹逼”，以一个变量记录当前蓄水池的高度（初值为0），夹逼时每遇到一个小于此高度的边，则移动指针并加入水量，否则在本轮迭代结束时改变当前蓄水池的高度
~~~~
public static int trap(int[] height) {
    int l = 0, r = height.length - 1, water = 0, curH = 0;
    while (l<r){
        while (l<r && height[l]<=curH) water += (curH - height[l++]);
        while (l<r && height[r]<=curH) water += (curH - height[r--]);
        curH = Math.min(height[l], height[r]);
    }
    return water;
}
~~~~

##Matrix
####48. Rotate Image
You are given an n x n 2D matrix representing an image. Rotate the image by 90 degrees (clockwise).

顺时针旋转90度=逆时针转90度再水平翻转；逆时针转90度就是求转置矩阵
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

##Graph

####Connected & Reachable

####Topological Sort

####Strongly connected

####Dijkstra & Bellman-Ford

####Prim & Kruskal

##String
####Palindrome

####Anagram

####StringBuffer

####KMP Algorithm

####Huffman Algorithm

##Bit manipulation

##Misc
####1. Two Sum

~~~~
~~~~
