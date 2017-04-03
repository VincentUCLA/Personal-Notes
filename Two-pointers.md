## Two pointers
我已经不知道双指针跟回溯搜索相比哪个更陈腔滥调一点了，既然都这么陈腔滥调了，很显然这个思想是要透彻掌握的。
### 1. 双指针顺序操作
#### 26. Remove Duplicates from Sorted Array
Given a sorted array, remove the duplicates in place such that each element appear only once and return the new length.
Do not allocate extra space for another array, you must do this in place with constant memory.

很简单，维持两个指针，都在每次填数字时++，但后面那个指针在每次遇到重复数字时++。
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
### 2. 双指针窗口操作
双指针配合哈希表是字符串操作里极为陈腔滥调的题目，需要深入掌握
#### 3. Longest Substring Without Repeating Characters
Given a string, find the length of the longest substring without repeating characters.

本题是双指针窗口操作的母题，双指针开头i结尾j指代当前字符串，如果哈希表里包含j则删除表中i项并i++，否则添加表中j项并j++
~~~~
public int lengthOfLongestSubstring(String s) {     
    int left = 0, right = 0, max = 0;
    int[] hash = new int[26];
    char[] cs = s.toCharArray();
    while (right < cs.length){
        if (hash[cs[right] - 'a'] == 0) {
            hash[cs[right] - 'a']++;
            right++;
            max = Math.max(max, right - left);
        } else {
            hash[cs[left] - 'a']--;
            left++;
        }
    }
    return max;
}
~~~~
#### 5. Longest Palindromic Substring
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

#### 438. Find All Anagrams in a String
Given a string s and a non-empty string p, find all the start indices of p anagrams in s.

双指针+哈希表经典题目，我个人对leetcode上广泛流传的答案做了一个细节处的修改以更容易理解：既然哈希表里右指针所指代的字符大于等于1指代这个字符曾经在p中出现过，那么左指针指代的字符在哈希表里加回去之后大于等于1，也能代表这个字符曾经在p中出现过，否则的话左指针怎么加，右指针也都是减过的，最大不可能超过0。理解了这个机制就能破解掉大部分的双指针+哈希表题目。
~~~~
public List<Integer> findAnagrams(String s, String p) {
    List<Integer> ret = new ArrayList<>();
    char[] cs = s.toCharArray(), cp = p.toCharArray();
    int[] hash = new int[26];
    for (int i = 0; i<cp.length; i++)
        hash[cp[i] - 'a']++;
    int left = 0, right = 0, need = cp.length;
    while (right < cs.length){
        if (hash[cs[right++] - 'a']-- >= 1) need--;
        if (need == 0) ret.add(left);
        if (right - left == cp.length && ++hash[cs[left++] - 'a'] >= 1) need++;
    }
    return ret;
}
~~~~

### 3. 双指针夹逼操作
#### 11. Container With Most Water
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
#### 42. Trapping Rain Water
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
