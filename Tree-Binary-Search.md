
## Tree & Binary Search
### 1. Tree
树是最简单的数据结构，主要是因为一般不会用迭代来处理树的内容，只有递归的话比较好想
#### 111. Minimum Depth of Binary Tree
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
### 2. Binary Search
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

#### 154. Find Minimum in Rotated Sorted Array II
这个题和153基本一样，简单的二分搜索，唯一的问题是如果两侧一样的话怎么办呢？153题不含重复值，含重复值的154题，去掉重复值不就跟153一样了吗
~~~~
def findMin(self, nums):
    """
    :type nums: List[int]
    :rtype: int
    """
    l = len(nums)
    if l < 1:
        return 0
    elif l == 1:
        return nums[0]
    elif nums[0] < nums[l-1]:
        return nums[0]
    elif nums[0] > nums[l-1]:
        return min(self.findMin(nums[0:l//2]), self.findMin(nums[l//2:]))
    else:
        return self.findMin(nums[1:])
~~~~

#### 162. Find Peak Element

这个题目就是分情况讨论的二分搜索，如果找到了peak那当然好，找不到的话，增长趋势就向右找，下跌趋势就向左找，如果搜索范围只有一个那只能返回他，如果有两个就返回那个较大值，一共这是五种情况
~~~~
def findPeakElement(self, nums):
    """
    :type nums: List[int]
    :rtype: int
    """
    left, right = 0, len(nums) - 1
    while True:
        if left == right: return left
        elif left + 1 == right:
            if nums[left] > nums[right]: return left
            else: return right
        m = (left + right) // 2
        if nums[m-1] < nums[m] and nums[m+1] < nums[m]:
            return m
        elif nums[m-1] > nums[m] > nums[m+1]:
            right = m - 1
        else:
            left = m + 1
~~~~


#### 300. Longest Increasing Subsequence
这个题目很不好想，表面上是dp，但实际上是二分搜索，从左向右便利整个数列，逐个插入dp数列，维持dp数列的从小到大顺序即可
~~~~
def lengthOfLIS(self, nums):
    """
    :type nums: List[int]
    :rtype: int
    """
    dp = []
    for x in nums:
        i = self.bs(dp, x)
        if i < 0:
            i = - (i + 1)
        if i == len(dp):
            dp.append(x)
        else:
            dp[i] = x
    return len(dp)
~~~~
