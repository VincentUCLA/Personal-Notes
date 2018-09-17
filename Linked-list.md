## Linked List

### 1. 各种反转链表
#### 206. Reverse Linked List
反转链表母题，需要熟练掌握各种变量的变化顺序，追踪两个变量法
```java
public ListNode reverseList(ListNode head) {
    ListNode newHead = null;
    while (head != null){
        ListNode headNext = head.next;
        head.next = newHead;
        newHead = head;
        head = headNext;
    }
    return newHead;
}
```
#### 92. Reverse Linked List II
反转链表有很多种方式，上一题只是其中一种，这次我们来试试第二种，实际只追踪一个变量
```java
public ListNode reverseBetween(ListNode head, int m, int n) {
    ListNode dummy = new ListNode(0);
    dummy.next = head;
    ListNode startL = dummy;
    for (int i = 0; i<m-1; i++)
        startL = startL.next;
    ListNode cur = startL.next;
    for (int i = m; i<n; i++){
        ListNode curNext = cur.next;
        cur.next = curNext.next;
        curNext.next = startL.next;
        startL.next = curNext;
    }
    return dummy.next;
}
```
#### 234. Palindrome Linked List
快慢指针求中点，然后用慢指针作为中点往后遍历，前半部分是本题的难点，如果希望不改动链表的话，需要从头到尾再从尾到头翻转两次

```java
def isPalindrome(self, head):
    slow, fast = head, head
    rev = None
    while fast and fast.next:
        fast = fast.next.next
        rev, rev.next, slow = slow, rev, slow.next
    if fast: tail = slow.next
    else: tail = slow
    while rev:
        if rev.val != tail.val: return False
        slow, slow.next, rev = rev, slow, rev.next
        tail = tail.next
    return True
```
#### 24. Swap Nodes in Pairs
简单题，注意顺序即可
```java
public ListNode swapPairs(ListNode head) {
    ListNode dummy = new ListNode(0);
    dummy.next = head;
    ListNode prev = dummy;
    ListNode cur;
    ListNode move;
    while (prev.next != null && prev.next.next != null){
        cur = prev.next;
        move = cur.next;
        prev.next = move;
        cur.next = move.next;
        move.next = cur;
        prev = cur;
    }
    return dummy.next;
}
```
### 2. 其他杂题
#### 2. Add Two Numbers
Input: (2 > 4 > 3) + (5 > 6 > 4)

Output: 7 > 0 > 8

这个题目的难点在于corner case多而且庞杂，但实际上不需要转换成十进制数字，因为他本身提供的链表就是从小到大的，符合加法的计算习惯，生加即可
```java
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
    ListNode dummy = new ListNode(0);
    ListNode cur = dummy;
    int c = 0;
    while (l1 != null || l2 != null){
        int a = 0, b = 0, d = 0;
        // a, b
        if (l1 != null) {
            a = l1.val;
            l1 = l1.next;
        }
        if (l2 != null) {
            b = l2.val;
            l2 = l2.next;
        }
        // c is carrier
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
```
#### 23. Merge k Sorted Lists
题目很直接就不再重复叙述了，这题最直截了当的想法是分治调用merge 2 sorted lists的函数，而后者是个人就会写
```java
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
    if (l1 == null && l2 == null) return null;
    else if (l1 == null) return l2;
    else if (l2 == null) return l1;
    else {
        ListNode dummy = new ListNode(0), cur = dummy;
        ListNode cur1 = l1, cur2 = l2;
        while (cur1 != null && cur2 != null){
            if (cur1.val <= cur2.val){
                cur.next = cur1;
                cur1 = cur1.next;
            } else {
                cur.next = cur2;
                cur2 = cur2.next;
            }
            cur = cur.next;
        }
        if (cur1 == null)
            cur.next = cur2;
        else if (cur2 == null)
            cur.next = cur1;
        return dummy.next;
    }
}

public ListNode mergeKLists(ListNode[] lists) {
    if (lists.length == 0)
        return null;
    else if (lists.length == 1)
        return lists[0];
    else if (lists.length == 2)
        return mergeTwoLists(lists[0], lists[1]);
    else {
        ListNode[] l1 = Arrays.copyOfRange(lists, 0, lists.length/2);
        ListNode[] l2 = Arrays.copyOfRange(lists, lists.length/2, lists.length);
        return mergeTwoLists(mergeKLists(l1), mergeKLists(l2));
    }
}
```
#### 147. Insertion Sort List
这题还真不是一个简单题，需要注意的就是如果插入位置后面的最小值就是插入位置本身的话，就不需要再移动
```py
def insertionSortList(self, head):
    dummy = ListNode(0)
    dummy.next = head
    inscur = dummy;
    while inscur.next != None:
        min = 2**32
        cur = inscur
        tempNode = None
        while cur.next != None:
            if cur.next.val < min:
                min = cur.next.val
                tempNode = cur
            cur = cur.next
        if tempNode != inscur:
            temp = tempNode.next.next
            tempNode.next.next = inscur.next
            inscur.next = tempNode.next
            tempNode.next = temp
        inscur = inscur.next
    return dummy.next
```
#### 148. Sort List
这题反而不难。。。注意细节
```py
def sortList(self, head):
    """
    :type head: ListNode
    :rtype: ListNode
    """
    # Corner case
    if head == None or head.next == None:
        return head
    # find middle node using fast & slow pointer
    slow = head
    fast = head
    while fast.next != None and fast.next.next != None:
        slow = slow.next
        fast = fast.next.next
    a = head
    b = slow.next
    # truncate list by middle node
    slow.next = None
    # recursion
    a = self.sortList(a)
    b = self.sortList(b)
    # use merge sort
    return self.mergeTwoLists(a, b)
```
