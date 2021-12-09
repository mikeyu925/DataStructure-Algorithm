插个眼！截至目前`2021年12月8日`，自己已经坚持刷题10个月了！已经再LeetCode做了大约300道题目了，因此想着**尝试不看任何题解**把剑指Offer系列的题目全做一遍，看看自己的水平如何。祝好！

#### 第一剑式：用两个栈实现队列

> 题目来源：LeetCode 剑指 Offer 09

用两个栈实现一个队列。队列的声明如下，请实现它的两个函数 appendTail 和 deleteHead ，分别完成在队列尾部插入整数和在队列头部删除整数的功能。(若队列中没有元素，deleteHead 操作返回 -1 )

```java
输入：
["CQueue","appendTail","deleteHead","deleteHead"]
[[],[3],[],[]]
输出：[null,null,3,-1]
```

**解题思路**：

因为栈是要符合 先进后出 ，而队列要符合 先进先出 。利用两个栈实现队列功能。假设第一个栈中内容[2,4,1) `[表示栈底 )表示栈顶`，如果要实现队列功能，那么拿出一个元素必须是 2，我们首先依次将栈中内容拿出再压入第二个栈，得到第二个栈[1,4,2)，那么每次需要或许元素时直接将第二个栈的元素弹出即可。

```java
class CQueue {
    //定义两个栈
    private Deque<Integer>  stack1;
    private Deque<Integer>  stack2;
    public CQueue() {
        stack1 = new ArrayDeque<>();
        stack2 = new ArrayDeque<>();
    }
	
    public void appendTail(int value) {
        stack1.offerLast(value);
    }
	
    public int deleteHead() {
        // 
        if(stack1.isEmpty() && stack2.isEmpty()) return -1;
        if(!stack2.isEmpty()) return stack2.pollLast();
        while(!stack1.isEmpty()){
            stack2.offerLast(stack1.pollLast());
        }
        return stack2.pollLast();
    }
}
```



#### 第二剑式：包含min函数的栈

> 题目来源：LeetCode 剑指 Offer 30

定义栈的数据结构，请在该类型中实现一个能够得到栈的最小元素的 min 函数在该栈中，调用 min、push 及 pop 的时间复杂度都是 O(1)。

**题目解析**：

定义两个栈，一个栈1用来进行存储元素，另一个栈2存储插入当前元素之前的最小值，即栈顶始终是最小值。比如分别插入2,4,1,5，则栈1内容为：[2,4,1,5)`[表示栈底 )表示栈顶`，栈2中的内容为：[2,2,1,1)`[表示栈底 )表示栈顶`。每次插入新元素时，栈1直接压入，栈2需要比较栈顶元素和插入元素，将两者小的元素压入栈。

```java
class MinStack {
    private Deque<Integer> stack;
    private Deque<Integer> min;
    public MinStack() {
        stack = new ArrayDeque<>();
        min = new ArrayDeque<>();
    }
    public void push(int x) {
        stack.offerLast(x);
        if(min.isEmpty()) min.offerLast(x);
        else min.offerLast(Math.min(min.getLast(),x));
    }

    public void pop() {
        stack.pollLast();
        min.pollLast();
    }

    public int top() {
        return stack.getLast();
    }

    public int min() {
        return min.getLast();
    }
}
```

#### 第三剑式：从尾到头打印链表

> 题目来源：LeetCode 剑指 Offer 06

输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。

解题思路：

先将链表反转，然后再遍历一次反转后的链表，保存结点的值。==> 顺便练一下反转链表 。

还有种方法是直接用栈，遍历一次链表，然后再将栈中的元素一一弹出，保存在数组中。

> 时间复杂度都是O(n)

```java
//方法1：先反转再遍历
class Solution {
    public int[] reversePrint(ListNode head) {
        ListNode tailList = new ListNode();
        ListNode tmp = head;
        int cnt = 0;
        while(tmp != null){
            tmp = head.next;
            head.next = tailList.next;
            tailList.next = head;
            head = tmp;
            cnt++;
        }
        tmp = tailList.next;
        int[] ans = new int[cnt];
        cnt = 0;
        while(tmp != null){
            ans[cnt] = tmp.val;
            cnt += 1;
            tmp = tmp.next;
        }
        return ans;
    }
}
```



#### 第四剑式：反转链表

> 题目来源：LeetCode 剑指 Offer 24

定义一个函数，输入一个链表的头节点，反转该链表并输出反转后链表的头节点。

题目解析：

会了第三剑式，那第四剑式肯定手到擒来呀。直接一波反转链表提交答案，双100！

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode reverseList(ListNode head) {
        ListNode ans = new ListNode();
        ListNode tmp = head;
        while(tmp != null){
            tmp = head.next;
            head.next = ans.next;
            ans.next = head;
            head = tmp;
        }
        return ans.next;
    }
}
```



#### 第四剑式：复杂链表的复制

> 题目来源：LeetCode 剑指 Offer 35

请实现 copyRandomList 函数，复制一个复杂链表。在复杂链表中，每个节点除了有一个 next 指针指向下一个节点，还有一个 random 指针指向链表中的任意节点或者 null。

![img](🗡指Offer系列.assets/e1.png)

题目解析：

因为是深拷贝，我们需要直到原来的链表中的每个结点对应着拷贝后的链表中的哪个结点。因此需要采用哈希表来进行映射。第一次遍历进行哈希表的初始化映射，第二次遍历来更新拷贝后的链表的指针指向。

```java
/*
class Node {
    int val;
    Node next;
    Node random;

    public Node(int val) {
        this.val = val;
        this.next = null;
        this.random = null;
    }
}
*/
class Solution {
    Map<Node,Node> map = new HashMap<Node,Node>();
    public Node copyRandomList(Node head) {
        Node ans = new Node(-1);
        Node ans_tail = ans;
        Node now = head;
        while(now != null){
            Node add = new Node(now.val);
            ans_tail.next = add;
            ans_tail = ans_tail.next;
            map.put(now,add);
            now = now.next;
        }
        Node tmp_now = ans.next;
        now = head;
        while(tmp_now != null){
            tmp_now.random = map.get(now.random);
            tmp_now = tmp_now.next;
            now = now.next;
        }
        return ans.next;
    }
}
```

