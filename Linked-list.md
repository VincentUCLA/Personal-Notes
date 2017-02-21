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

