### Linked List
1. 反转链表
2. 找有环linked list的环的结点数
3. 怎么确定有没有环
4. 怎么找环的第一个结点

### Binary Search
1. 另一种rotated array，比如1,2,3,10,9,8,7,6,5,4就是说新的array是原来的递增array的后面一部分反转得到的。
```
class Solution(object):
    def findMin(self, nums):
        l = len(nums)
        if l < 1:
            return 0
        elif l == 1:
            return nums[0]
        elif nums[0] < nums[l-1]:
            return nums[0]
        else:
            return min(self.findMin(nums[0:l//2]), self.findMin(nums[l//2:]))
```
```
class Solution(object):
    def findMin(self, nums):
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
            return self.findMin(nums[0:l-1])
```
2. find a number in a sorted matrix
```
class Solution(object):
    def searchInsert(self, nums, target):
        if nums is None:
            return -1
        left, right = 0, len(nums)
        if target > nums[-1]:
            return right
        while left < right:
            mid = (left + right) // 2
            if nums[mid] < target:
                left = mid + 1
            else:
                right = mid
        return right
```
### Binary Tree
1. preorder
2. inorder
3. range bst
4. Serialize and Deserialize a Binary Tree
```
class Codec:
    def serialize(self, root):
        if root is None:
            return []
        que, ret = [root], []
        while len(que) > 0:
            cur = que.pop(0)
            if cur is not None:
                que.append(cur.left)
                que.append(cur.right)
            if cur is None:
                ret.append(None)
            else:
                ret.append(cur.val )
        return ret

    def deserialize(self, data):
        que = []
        if len(data) < 1:
            return None
        else:
            root = TreeNode(data.pop(0))
            que.append(root)
            while len(data) > 0:
                parent = que.pop(0)
                cur = data.pop(0)
                if cur is not None:
                    parent.left = TreeNode(cur)
                    que.append(parent.left)
                cur = data.pop(0)
                if cur is not None:
                    parent.right = TreeNode(cur)
                    que.append(parent.right)
            return root
```

### DP
1. pascal triangle
```
class Solution(object):
    def getRow(self, rowIndex):
        temp = [1]
        for i in range(0, rowIndex):
            tmp = [1]
            for j in range(0, i):
                tmp.append(temp[j] + temp[j+1])
            tmp.append(1)
            temp = tmp
        return temp
```
2. max profit
3. wildcard matching


### Misc
1. reverse words in a sentence. 
```
class Solution(object):
    def reverseWords(self, s):
        return " ".join(s.strip().split()[::-1])
```
2. find median of stream
3. Let say you are given all pairwise distances between n points, points in 3d, compute a coordinate representation of the points such that the euclid distance matches the input distance 
4. given 2 distance matrices M1, M2 that represent distances bw points in 2d, check if the point sets are equivalent (same upto translation + rotation) 
5. 给个时间，string格式，比如10:35，让你求时针和分针小的夹角
6. Merge Intervals
7. 从(1，...n)中打印所有的k, so that k=a^2+b^2, a,b都是整数
8. pow(x, y), both iterative and recursive.

### Two pointers
1. container with most water
```
class Solution(object):
    def maxArea(self, height):
        maxA, left, right = 0, 0, len(height) - 1
        while left < right:
            maxA = max(maxA, (right - left) * min(height[right], height[left]))
            if height[left] < height[right]:
                left += 1
            else:
                right -= 1
        return maxA
```
2. find longest palindrome in a string
3. maximum subarray sum, max product
4. Product of Array Except Self

### Hash map
1. three sum
2. 2G大文件，RAM只有1G，怎么sort

### BFS/DFS
1. 一个image，每个pixel一个颜色。给你其中一个pixel的位置，以及一个颜色，如果那个pixel颜色和给的一样，什么都不用做，如果不一样，就把这个pixel变成给定的颜色，同时把他的neighbor和这个pixel原来颜色一样的也换成新的颜色，然后再neighbor的neighbor这样下去。
2. word ladder
3. sudoku solver
4. Palindrome Permutation
5. permutation dices
```
class Solution(object):
    def dfs(self, ret, now, remain):
        if len(remain) == 0:
            ret.append(now[:])
            return
        else:
            for i in range(0, len(remain)):
                temp = remain[i]
                remain = remain[0:i] + remain[i+1:]
                now.append(temp)
                self.dfs(ret, now, remain)
                now.pop()
                remain = remain[0:i] + [temp] + remain[i:]

    def permute(self, nums):
        ret, now = [], []
        self.dfs(ret, now, nums)
        return ret
```

### Stack/queue
1. valid parentheses
```
class Solution(object):
    def isValid(self, s):
        stk = []
        for i in s:
            if i == '(' or i == '[' or i == '{':
                stk.append(i)
            else:
                if len(stk) == 0:
                    return False
                elif i == ')':
                    if stk[-1] == '(':
                        stk.pop()
                    else:
                        return False
                elif i == ']':
                    if stk[-1] == '[':
                        stk.pop()
                    else:
                        return False
                elif i == '}':
                    if stk[-1] == '{':
                        stk.pop()
                    else:
                        return False
        if len(stk) == 0:
            return True
        else:
            return False

```
2. bounded queue
