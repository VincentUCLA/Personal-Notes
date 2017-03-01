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
####47. Permutations II
这个题目的剪枝很有趣，为了恰当剪枝需要事先排序，然后比如说有三连出现的数字，它只能以1, 11, 111形式各出现一次。比如说[1,1,1,2]，当bfs完两个1之后，遇到第三个1需要跳过，为什么呢？因为递归[1,1]子树时已经搜索过[1,1,1]了。所以每一个子树（在这个例子中是[1]子树的第二位搜索的时候看到第三个1需要跳过，因为之前第二个1已经被跳过了，符合(nums[i - 1] == nums[i] and not used[i - 1])的条件)——这一轮的i-1个值没用过，说明上一轮搜索的时候已经用过了。
~~~~
def perm(self, nums, current, result, used):
    if len(current) == len(nums):
        result.append(current[:])
        return
    else:
        for i in range(0, len(nums)):
            if used[i]:
                continue
            if i > 0 and nums[i - 1] == nums[i] and not used[i - 1]:
                continue
            used[i] = True
            current.append(nums[i])
            self.perm(nums, current, result, used)
            current.pop()
            used[i] = False

def permuteUnique(self, nums):
    """
    :type nums: List[int]
    :rtype: List[List[int]]
    """
    [ret, cur, used] = [[], [], []]
    nums = sorted(nums)
    for i in nums:
        used.append(False)
    self.perm(nums, cur, ret, used)
    return ret
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
