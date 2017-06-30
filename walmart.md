1. reverse words in a sentence. 
```
class Solution(object):
    def reverseWords(self, s):
        """
        :type s: str
        :rtype: str
        """
        return " ".join(s.strip().split()[::-1])
```
2. find longest palindrome in a string
3. Let say you are given all pairwise distances between n points, points in 3d, compute a coordinate representation of the points such that the euclid distance matches the input distance 
4. given 2 distance matrices M1, M2 that represent distances bw points in 2d, check if the point sets are equivalent (same upto translation + rotation) 
5. 反转链表
6. three sum
7. 2G大文件，RAM只有1G，怎么sort
8. 一个image，每个pixel一个颜色。给你其中一个pixel的位置，以及一个颜色，如果那个pixel颜色和给的一样，什么都不用做，如果不一样，就把这个pixel变成给定的颜色，同时把他的neighbor和这个pixel原来颜色一样的也换成新的颜色，然后再neighbor的neighbor这样下去。
9. 给个时间，string格式，比如10:35，让你求时针和分针小的夹角
10. 另一种rotated array，比如1,2,3,10,9,8,7,6,5,4就是说新的array是原来的递增array的后面一部分反转得到的。
```
class Solution(object):
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
        else:
            return min(self.findMin(nums[0:l//2]), self.findMin(nums[l//2:]))
```
```
class Solution(object):
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
            return self.findMin(nums[0:l-1])
```
11. 找有环linked list的环的结点数
12. 怎么确定有没有环
13. 怎么找环的第一个结点
14. maximum subarray sum
15. word ladder
16. valid parentheses
17. preorder
18. inorder
19. max profit
20. range bst
21. leetcode 266
22. permutation dices
23. js\ruby\node基础概念
24. Given two strings A and B, find if B can be formed with letters in A. 
25. Suppose you have a website like imdb.com. How do you design the database schema? Say you need to store movie and actor, describe the schema. Follow up: what if you need to information like producer, director, screenplay writer, etc., how would you modify your schema? 
26. leetcode 238
27. leetcode 56
28. https://instant.1point3acres.com/thread/202464
29. all permutations
30. 从(1，...n)中打印所有的k, so that k=a^2+b^2, a,b都是整数
31. permutations of string
32. wildcard matching
33. find a number in a sorted matrix
```
class Solution(object):
    def searchInsert(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: int
        """
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
34. find median of stream
35. sudoku solver
36. container with most water
```
class Solution(object):
    def maxArea(self, height):
        """
        :type height: List[int]
        :rtype: int
        """
        maxA, left, right = 0, 0, len(height) - 1
        while left < right:
            maxA = max(maxA, (right - left) * min(height[right], height[left]))
            if height[left] < height[right]:
                left += 1
            else:
                right -= 1
        return maxA
```
37. pow(x, y), both iterative and recursive.
38. pascal triangle
```
class Solution(object):
    def getRow(self, rowIndex):
        """
        :type rowIndex: int
        :rtype: List[int]
        """
        temp = [1]
        for i in range(0, rowIndex):
            tmp = [1]
            for j in range(0, i):
                tmp.append(temp[j] + temp[j+1])
            tmp.append(1)
            temp = tmp
        return temp
```
39. Serialize and Deserialize a Binary Tree
```
class Codec:
    def serialize(self, root):
        """Encodes a tree to a single string.

        :type root: TreeNode
        :rtype: str
        """
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
        """Decodes your encoded data to tree.

        :type data: str
        :rtype: TreeNode
        """
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
40. max product
41. max parenthesis nesting
42. bounded queue
