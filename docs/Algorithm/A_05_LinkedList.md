---
sidebar_position: 
---

# 链表

## [141. 环形链表](https://leetcode.cn/problems/linked-list-cycle/)

### 哈希表

**`if(!seen.add(head))`**:

- `seen.add(head)` 尝试将当前节点 `head` 添加到 `seen` 集合中。
- 如果 `head` 已经存在于 `seen` 集合中，`add` 方法会返回 `false`。因此，`!seen.add(head)` 会为 `true`，表示我们发现了一个重复的节点，也就是说链表中存在环。
- 如果 `head` 不在集合中，`add` 方法会返回 `true`，那么代码会继续执行，移动到下一个节点。

```java
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public boolean hasCycle(ListNode head) {
        Set<ListNode> seen = new HashSet<ListNode>();
        while(head != null){
            if(!seen.add(head)){
                return true;
            }
            head = head.next;
        }
        return false;
    }
}
```

### 快慢指针

```java
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public boolean hasCycle(ListNode head) {
        if(head == null || head.next == null) return false;
        ListNode fast = head.next;
        ListNode slow = head;
        while(slow != fast){
            if(fast == null || fast.next == null){
                return false;
            }
            fast = fast.next.next;
            slow = slow.next;
        }
        return true;
    }
}
```

## [2. 两数相加](https://leetcode.cn/problems/add-two-numbers/)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode head = null, tail = null;
        int carry = 0;

        while(l1 != null || l2 != null){
            int n1 = l1 != null ? l1.val : 0;
            int n2 = l2 != null ? l2.val : 0;
            int sum = n1 + n2 + carry;
            if(head == null){
                head = tail = new ListNode(sum % 10);
            } else {
                tail.next = new ListNode(sum % 10);
                tail = tail.next;
            }
            carry = sum / 10;
            if(l1 != null){
                l1 = l1.next;
            }
            if(l2 != null){
                l2 = l2.next;
            }
        }
        if(carry > 0){
            tail.next = new ListNode(carry);
        }
        return head;
    }
}
```

## [21. 合并两个有序链表](https://leetcode.cn/problems/merge-two-sorted-lists/)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        ListNode l1 = list1;
        ListNode l2 = list2;
        ListNode head = null, tail = null;
        while(l1 != null && l2 != null){
            if(l1.val < l2.val){
                if(head == null){
                    head = tail = new ListNode(l1.val);
                } else {
                    tail.next = new ListNode(l1.val);
                    tail = tail.next;
                }
                l1 = l1.next;
            } else {
                if(head == null){
                    head = tail = new ListNode(l2.val);
                } else {
                    tail.next = new ListNode(l2.val);
                    tail = tail.next;
                }
                l2 = l2.next;
            }
        }
        while(l1 != null){
            if(head == null){
                head = tail = new ListNode(l1.val);
            } else {
                tail.next = new ListNode(l1.val);
                tail = tail.next;
            }
            l1 = l1.next;
        }
        while(l2 != null){
            if(head == null){
                head = tail = new ListNode(l2.val);
            } else {
                tail.next = new ListNode(l2.val);
                tail = tail.next;
            }
            l2 = l2.next;
        }
        return head;
    }
}
```

## [19. 删除链表的倒数第 N 个结点](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        // 如果链表为空或者 n 小于等于 0，直接返回头节点
        if (head == null || n <= 0) {
            return head;
        }

        // 计算链表长度
        int length = 0;
        ListNode current = head;
        while (current != null) {
            length ++;
            current = current.next;
        }
        // 如果要删除的节点是头节点
        if (n == length) {
            return head.next;
        }

        // 找到要删除节点的前一个节点
        int delNodeIndex = length - n; // 计算要删除节点的索引
        current = head;
        for (int i = 0; i < delNodeIndex - 1; i++) {
            current = current.next;
        }

        // 删除目标节点
        current.next = current.next.next;

        return head;
    }
}
```

## [82. 删除排序链表中的重复元素 II](https://leetcode.cn/problems/remove-duplicates-from-sorted-list-ii/)

### 两次遍历 哈希表

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        ListNode curr = head;
        HashMap<Integer, Integer> map  = new HashMap<>();
        while(curr != null){
            map.put(curr.val, map.getOrDefault(curr.val, 0) + 1);
            curr = curr.next;
        }
         // 创建一个新的链表头节点
        ListNode dummy = new ListNode(0);
        ListNode newCurr = dummy;

        // 第二次遍历，构建新的链表
        curr = head;
        while (curr != null) {
            // 只添加出现一次的节点
            if (map.get(curr.val) == 1) {
                newCurr.next = new ListNode(curr.val);
                newCurr = newCurr.next;
            }
            curr = curr.next;
        }

        return dummy.next; // 返回新的链表的头节点
    }
}
```

### 一次遍历

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if(head == null) return head;

        ListNode dummy = new ListNode(0, head);

        ListNode cur = dummy;
        while(cur.next != null && cur.next.next != null) {
            if(cur.next.val == cur.next.next.val){
                int x = cur.next.val;
                while(cur.next != null && cur.next.val == x){
                    cur.next = cur.next.next;
                }
            } else {
                cur = cur.next;
            }
        }
        return dummy.next;
    }
}
```

## [61. 旋转链表](https://leetcode.cn/problems/rotate-list/)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode rotateRight(ListNode head, int k) {
        if(head == null || head.next == null) return head;
        // 求Length
        int length = 1;
        ListNode tail = head;
        while(tail.next != null){
            length ++;
            tail = tail.next;
        }
        if(k%length == 0) return head;
        // 旋转
        int curLength = 1;
        ListNode cur = head;
        while(curLength < length - k%length){
            cur = cur.next;
            curLength ++;
        }
        ListNode res = cur.next;
        cur.next = null;
        tail.next = head;
        return res;
    }
}
```



