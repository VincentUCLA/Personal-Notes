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

##BST Backtracking & Permutation

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

##Greedy

##Heap

##Dynamic Programming

##Graph

####Connected & Reachable

####Topological Sort

####Strongly connected

####Dijkstra & Bellman-Ford

####Prim & Kruskal

##DFS & BFS
leetcode四大陈腔滥调：回溯搜索、动态规划、双指针，二分法之首的回溯搜索

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
