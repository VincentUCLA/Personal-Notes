# Personal-Notes
##Stack & Queues
####Infix Expression Evaluation

中缀到后缀转换：要点在于，为什么遇到计算符时要把优先级大于等于自己的运算符全部弹出？因为在后缀表达式里他们放在前面就等于先去计算他们啊；栈在这里实际上就是起到的一个保存计算顺序的作用，而“有控制地”从栈中弹出运算符可以达到把中缀式代换成语法树（也就是后缀式）的目的。

相关leetcode题目：150. Evaluate Reverse Polish Notation, 224. Basic Calculator

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

计算后缀式：这个不难，唯一需要注意的部分是两个操作数的弹出顺序。

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

##Sorting
选择排序最好理解，每次迭代都从子数组里寻找最小值即可，第k次迭代抽出来的即是第k小值

    def swap(self, arr, i, j):
        temp = arr[j]
        arr[j] = arr[i]
        arr[i] = temp

    def selectSort(self, arr):
        l = len(arr)
        for i in range(0, l):
            min = 2**31
            m = 0
            for j in range(i, l):
                if arr[j] < min:
                    min = arr[j]
                    m = j
            self.swap(arr, i, m)
        return arr

插入排序的话需要假设前面第i-1个数字是排好序的，然后把第i个数字插到已经排好序的数组里的恰当位置；既然是插入那么其实用链表比用数组要经济


    def insertSort(self, arr):
        l = len(arr)
        for i in range(1, l):
            temp = arr[i]
            j = i - 1
            while j >= 0 and arr[j]>temp:
                arr[j+1] = arr[j]
                j -= 1
            arr[j+1] = temp
        return arr

合并排序很容易理解，要点是每排完一个数组就把另一个数组剩余值贴后面


    def merge(self, arr1, arr2):
        arr = []
        [l1, l2] = [len(arr1), len(arr2)]
        [i1, i2] = [0, 0]
        while i1<l1 and i2<l2:
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

快速排序没什么好说的


    def quickSort(self, arr):
        l = len(arr)
        if l <= 1:
            return arr
        pivot = arr[int(random.random()*200)%l]
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

###Quick-select
这个只要体会了思想，题目倒并不是很难，但如果不理解这个思想的话有的题目是完全无从措手的

相关leetcode题目：4. Median of Two Sorted Arrays，这道题因为是两个已经排好序的数列，可以略去一些选择的步骤

    def quickselect(self, arr, k):
        print(arr)
        print(k)
        if k == 0:
            return arr[0]
        else:
            l = len(arr)
            r = int(random.random()*200) % l
            temp = arr[r]
            [a, b, c] = [[], [], []]
            for i in arr:
                if i < temp:
                    a.append(i)
                elif i == temp:
                    b.append(i)
                else:
                    c.append(i)
            if len(a) < k <= len(a)+len(b):
                return temp
            elif k <= len(a):
                return self.quickselect(a, k)
            else:
                return self.quickselect(c, k - len(a) - len(b))

####4. Median of Two Sorted Arrays
There are two sorted arrays nums1 and nums2 of size m and n respectively. Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).

本题就是采用的Quick-select的思想，把求两个有序数列中位数，转化成求两个有序数列中第k大的数。采取减治法：将两个数列各自分成两段，两数列前半段共有k个数字；如果两数列前半段的末尾数字相等，那即为所求；否则的话，尾数较小的一个前半段中是不可能出现这个第k大数字的，下次递归时略去即可。	

	public double findMedianSortedArrays(int[] nums1, int[] nums2) {
		int l = nums1.length + nums2.length;
		if (l % 2 == 1) return findKth(nums1, nums2, (l/2+1));
		else return (findKth(nums1, nums2, l/2) + findKth(nums1, nums2, l/2+1))/2.0;
	}
	public double findKth(int[] nums1, int[] nums2, int k){
		int l = k/2, l1 = nums1.length, l2 = nums2.length;
		if (l1>l2) return findKth(nums2, nums1, k);
		if (l1==0) return nums2[k‐1];
		if (k==1) return Math.min(nums1[0], nums2[0]);
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
##Binary Search
二分搜索的思想是很简单的，但一次性写对也不容易：

    def binarysearch(self, arr, k):
        m = len(arr) // 2
        if k == arr[m]:
            return arr[m]
        elif k > arr[m]:
            return self.binarysearch(arr[m:], k)
        else:
            return self.binarysearch(arr[0:m], k)

另外不要拘泥于python提供的简单写法：

    def binarysearch(self, arr, k, lo, hi):
        m = (lo + hi) // 2
        if k == arr[m]:
            return arr[m]
        elif k < arr[m]:
            return self.binarysearch(arr, k, lo, m)
        else:
            return self.binarysearch(arr, k, m+1, hi)

相关leetcode题目：这真的是要多少有多少……

##Union-find
Quick-find: 确定两个点是否处在同一个连通集里：遍历所有的边(p, q)，每次都把id[p]改成id[q]


	class UF:
	    id = []
	    count = 0
	
	    def __init__(self, N):
	        self.count = N
	        for i in range(0,N):
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
	                for j in range(0,self.count):
	                    if self.id[j] == pID:
	                        self.id[j] = qID
	                self.dump()

##BST Backtracking & Permutation

##Hash Table

##Linked List

##Two pointers

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
