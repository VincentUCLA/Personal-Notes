
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
#### First/Last Occurrence of Binary Search

这个题目也是简单的二分搜索，唯一需要讨论的就是边界条件

~~~~
int first(int arr[], int low, int high, int x, int n) {
    if(high >= low) {
        int mid = low + (high - low)/2;
        if (( mid == 0 || x > arr[mid-1]) && arr[mid] == x)
            return mid;
        else if(x > arr[mid])
            return first(arr, (mid + 1), high, x, n);
        else
            return first(arr, low, (mid -1), x, n);
    }
    return -1;
}
	 
int last(int arr[], int low, int high, int x, int n) {
    if (high >= low) {
        int mid = low + (high - low)/2;
        if (( mid == n-1 || x < arr[mid+1]) && arr[mid] == x)
            return mid;
        else if (x < arr[mid])
            return last(arr, low, (mid -1), x, n);
        else
            return last(arr, (mid + 1), high, x, n);
    }
    return -1;
}
~~~~
 
### 3. Binary Search Tree
#### 285. Inorder Successor in BST

货真价实的简单题，BST中序遍历是单调递增的，所以实际就是在BST里寻找比p大的最小值咯

~~~~
def inorderSuccessor(self, root, p):
    ret = None
    while root:
        if p.val < root.val:
            ret = root
            root = root.left
        else:
            root = root.right
    return ret
~~~~
#### 235/6 Lowest Common Ancestor of BST / Binary Tree

BST的话要注意方向，如果不是BST的话要注意他是如何变换状态的

~~~~
def lowestCommonAncestor(self, root, p, q):
    if root.val < p.val and root.val < q.val:
        return self.lowestCommonAncestor(root.right, p, q)
    elif root.val > p.val and root.val > q.val:
        return self.lowestCommonAncestor(root.left, p, q)
    else:
        return root

def lowestCommonAncestor(self, root, p, q):
    if root is None or root is p or root is q:
        return root
    else:
        left = self.lowestCommonAncestor(root.left, p, q)
        right = self.lowestCommonAncestor(root.right, p, q)
        if left and right:
            return root
        elif left:
            return left
        elif right:
            return right
~~~~
#### 543. Diameter of Binary Tree

这个做法似乎是唯一解？但反正在二叉树问题里时间复杂度是很不漂亮的一种了

~~~~
def diameterOfBinaryTree(self, root):
    self.best = 1
    def depth(root):
        if not root: return 0
        depL = depth(root.left)
        depR = depth(root.right)
        self.best = max(self.best, depL + depR + 1)
        return max(depL, depR) + 1
    depth(root)
    return self.best - 1
~~~~
### 4. Graph
#### 133. Clone graph

这个问题的题眼是，复制一个图，其中每个节点都只能创造一次，第二次调用它的时候你就得重复调用以前使用过的节点

其他的其实就是简单的BFS而已

~~~~
def cloneGraph(self, node):
    if not node:
        return 
    nodeCopy = UndirectedGraphNode(node.label)
    dic = {node: nodeCopy}
    queue = collections.deque([node])
    while queue:
        node = queue.popleft()
        for neighbor in node.neighbors:
            if neighbor not in dic:
                neighborCopy = UndirectedGraphNode(neighbor.label)
                dic[neighbor] = neighborCopy
                dic[node].neighbors.append(neighborCopy)
                queue.append(neighbor)
            else:
                dic[node].neighbors.append(dic[neighbor])
    return nodeCopy
~~~~
