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



#### 第五剑式：复杂链表的复制

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



#### 第六剑式：替换空格

> 题目来源：LeetCode 剑指 Offer 05

请实现一个函数，把字符串 `s` 中的每个空格替换成"%20"。

```
输入：s = "We are happy."
输出："We%20are%20happy."
```

题目很简单，就当回顾一下`StringBuilder`吧

```java
class Solution {
    public String replaceSpace(String s) {
        StringBuilder ans = new StringBuilder(s.length());
        for(int i = 0;i < s.length();i++){
            if(s.charAt(i) != ' '){
                ans.append(s.charAt(i));
            }else{
                ans.append("%20");
            }
        }
        return ans.toString();
    }
}
```



#### 第七剑式：左旋转字符串

> 题目来源：LeetCode 剑指 Offer 58

字符串的左旋转操作是把字符串前面的若干个字符转移到字符串的尾部。请定义一个函数实现字符串左旋转操作的功能。比如，输入字符串"abcdefg"和数字2，该函数将返回左旋转两位得到的结果"cdefgab"。

```
输入: s = "abcdefg", k = 2
输出: "cdefgab"
```

采样`StringBuilder + substring()`直接解决。

```java
class Solution {
    public String reverseLeftWords(String s, int n) {
        StringBuilder ans = new StringBuilder();
        ans.append(s.substring(n));
        ans.append(s.substring(0,n));
        return ans.toString();
    }
}
```





#### 第八剑式：在排序数组中查找数字

> 题目来源：LeetCode 剑指 Offer 53-I

统计一个数字在排序数组中出现的次数。

```
输入: nums = [5,7,7,8,8,10], target = 8
输出: 2
```

采用看到有序+查找，就想到二分法。采用基本二分法套路找到目标值，然后再向两边扩散查找。

```java
class Solution {
    public int search(int[] nums, int target) {
        int left  = 0,right = nums.length-1;
        int cnt = 0;
        while(left <= right){
            int mid = left + ((right - left) >> 1);
            if(nums[mid] == target){
                cnt += 1;
                int tmp = mid-1;
                while(tmp >= 0 && nums[tmp] == target){
                    cnt += 1;
                    tmp -= 1;
                }
                tmp = mid + 1;
                while(tmp < nums.length && nums[tmp] == target){
                    cnt += 1;
                    tmp += 1;
                }
                return cnt;
            }else if(nums[mid] < target){
                left = mid + 1;
            }else{
                right = mid - 1;
            }
        }
        return 0;
    }
}
```



#### 第九剑式：0～n-1中缺失的数字

> 题目来源：LeetCode 剑指 Offer 53-II

一个长度为n-1的递增排序数组中的所有数字都是唯一的，并且每个数字都在范围0～n-1之内。在范围0～n-1内的n个数字中有且只有一个数字不在该数组中，请找出这个数字。

```
输入: [0,1,2,3,4,5,6,7,9]
输出: 8
```

**题目解析：**

看到`有序+查找`先想下能不能 二分查找 的思路，由题知，在缺失数字之前的所有数字与自己对应的索引相等，即`nums[i] == i`，在缺失数字之后的所有数字与自己对应的索引下标不相等，即`nums[i] != i`。

因此我们可以用二分查找算法找到第一个数字与下标对应不等的位置。以示例为例子：

```
索引:  0 1 2 3 4 5 6 7 8 9
元素: [0,1,2,3,4,5,6,7,9,10]
===> 第一个元素与下标不对应的下标即为答案，即 8
```

**二分查找方法**：

将数组划分为两个区域，左区域的数组与下标相等，右区域的数字与下标不等

当`left <= right`时继续查找`计算mid = (left + right)/2`，如果`nums[mid] == mid`，说明在左区域，使得`left = mid + 1`，如果`nums[mid ] != mid `，说明在右区域，使得`right = mid - 1`。

最终不符合`left <= right`时，left 的值就是结果。

```java
    public int missingNumber(int[] nums) {
        int left = 0,right = nums.length-1;
        while(left <= right){
            int mid = left + ((right - left) >> 1);
            if(nums[mid] == mid){
                left = mid + 1;
            }else{
                right = mid - 1;
            }
        }
        return left;
    }
```





#### 第十剑式：二维数组中的查找

> 题目来源：LeetCode 剑指 Offer 04

在一个 n * m 的二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个高效的函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

```
示例:
现有矩阵 matrix 如下：
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
```



从二维数组的右上角开始查找。如果当前元素等于目标值，则返回 `true`。如果当前元素大于目标值，则移到左边一列。如果当前元素小于目标值，则移到下边一行。

```java
class Solution {
    public boolean findNumberIn2DArray(int[][] matrix, int target) {
        if(matrix == null || matrix.length == 0) return false;
        int row = 0,col = matrix[0].length-1;
        while(row < matrix.length && col >= 0){
            if(matrix[row][col] == target) return true;
            else if(matrix[row][col] > target) col -= 1;
            else row += 1;
        }
        return  false;
    }
}
```



#### 第十一剑式：旋转数组的最小数字

> 题目来源：LeetCode 剑指 Offer 11

把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。

给你一个可能存在 重复 元素值的数组 numbers ，它原来是一个升序排列的数组，并按上述情形进行了一次旋转。请返回旋转数组的最小元素。例如，数组 [3,4,5,1,2] 为 [1,2,3,4,5] 的一次旋转，该数组的最小值为1。  

```java
class Solution {
    public int minArray(int[] numbers) {
        int left = 0, right = numbers.length - 1;
        while (left < right){
            int mid = left + ((right - left) >> 1);
            if(numbers[mid] < numbers[right]){
                right = mid;
            }else if(numbers[mid] > numbers[right]){
                left = mid + 1;
            }else{
                right -= 1;
            }
        }
        return numbers[left];
    }
}
```

#### 第十二剑式：第一个只出现一次的字符

> 题目来源：LeetCode 剑指 Offer 50

在字符串 s 中找出第一个只出现一次的字符。如果没有，返回一个单空格。 s 只包含小写字母。

```
输入：s = "abaccdeff"
输出：'b'
```

方法一：哈希表 + 遍历

```java
class Solution {
    public char firstUniqChar(String s) {
        Map<Character,Integer> m = new HashMap<>();
        for(int i = 0;i < s.length();i++){
            if(m.get(s.charAt(i)) == null){
                m.put(s.charAt(i),1);
            }else{
                int cnt = m.get(s.charAt(i));
                m.put(s.charAt(i),cnt+1);
            }
        }
        for(int i = 0; i < s.length();i++){
            if(m.get(s.charAt(i)) < 2){
                return s.charAt(i);
            }
        }
        return ' ';
    }
}
```



#### 第十三剑式： 从上到下打印二叉树

> 题目来源：LeetCode 剑指 Offer 32

从上到下打印出二叉树的每个节点，同一层的节点按照从左到右的顺序打印。

例如:

    给定二叉树: [3,9,20,null,null,15,7],
    	3
       / \
      9  20
        /  \
       15   7
    返回：
    [3,9,20,15,7]
就是一个简单的层次遍历，采用BFS解决！

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class node{
    public int floor;
    public TreeNode treenode;
    public node(){
        this.floor = 0;
        this.treenode = null;
    }
    public node(int floor,TreeNode treenode){
        this.floor = floor;
        this.treenode = treenode;
    }
}
class Solution {
    public int[] levelOrder(TreeNode root) {
        ArrayList<Integer> ans = new ArrayList<>();
        if(root == null) return new int[0];
        int cnt = 0;
        Deque<node> dq = new ArrayDeque<>();
        //初始化队列
        dq.offerLast(new node(0,root));
        while(!dq.isEmpty()){
            int nowfloor = dq.peekFirst().floor;
            while (!dq.isEmpty() && dq.peekFirst().floor == nowfloor){
                node now = dq.pollFirst();
                ans.add(now.treenode.val);
                if(now.treenode.left != null){
                    dq.offerLast(new node(nowfloor+1,now.treenode.left));
                }
                if(now.treenode.right != null){
                    dq.offerLast(new node(nowfloor+1,now.treenode.right));
                }
            }
        }
        return ans.stream().mapToInt(Integer::valueOf).toArray();
    }
}
```

#### 第十四剑式： 从上到下打印二叉树 II

> 题目来源：LeetCode 剑指 Offer 32.II

例如:
给定二叉树: `[3,9,20,null,null,15,7]`,

```
    3
   / \
  9  20
    /  \
   15   7
```

返回其层次遍历结果：

```
[
  [3],
  [9,20],
  [15,7]
]
```

 换汤不换药，只是改变了返回结果的形式，直接上一个代码一改 起飞！

```java
class node{
    public int floor;
    public TreeNode treenode;
    public node(){
        this.floor = 0;
        this.treenode = null;
    }
    public node(int floor,TreeNode treenode){
        this.floor = floor;
        this.treenode = treenode;
    }
}
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> ans = new ArrayList<List<Integer>>();
        if(root == null) return ans;
        Deque<node> dq = new ArrayDeque<>();
        //初始化队列
        dq.offerLast(new node(0,root));
        while(!dq.isEmpty()){
            int nowfloor = dq.peekFirst().floor;
            List<Integer> vals = new ArrayList<>();
            while (!dq.isEmpty() && dq.peekFirst().floor == nowfloor){
                node now = dq.pollFirst();
                vals.add(now.treenode.val);
                if(now.treenode.left != null){
                    dq.offerLast(new node(nowfloor+1,now.treenode.left));
                }
                if(now.treenode.right != null){
                    dq.offerLast(new node(nowfloor+1,now.treenode.right));
                }
            }
            ans.add(vals);
        }
        return ans;
    }
}
```



#### 第十五剑式： 从上到下打印二叉树 III

> 题目来源：LeetCode 剑指 Offer 32.III

请实现一个函数按照之字形顺序打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右到左的顺序打印，第三行再按照从左到右的顺序打印，其他行以此类推。

例如:
给定二叉树: `[3,9,20,null,null,15,7]`,

```
    3
   / \
  9  20
    /  \
   15   7
```

返回其层次遍历结果：

```
[
  [3],
  [20,9],
  [15,7]
]
```

```java
class node{
    public int floor;
    public TreeNode treenode;
    public node(){
        this.floor = 0;
        this.treenode = null;
    }
    public node(int floor,TreeNode treenode){
        this.floor = floor;
        this.treenode = treenode;
    }
}
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> ans = new ArrayList<List<Integer>>();
        if(root == null) return ans;
        Deque<node> dq = new ArrayDeque<>();
        //初始化队列
        dq.offerLast(new node(0,root));
        while(!dq.isEmpty()){
            int nowfloor = dq.peekFirst().floor;
            LinkedList<Integer> vals = new LinkedList<>();
            while (!dq.isEmpty() && dq.peekFirst().floor == nowfloor){
                node now = dq.pollFirst();
                //这点不一样咯！~~ 根据奇偶层来判断是从前插入结果还是从后插入结果
                if(nowfloor % 2 == 0){
                    vals.offerLast(now.treenode.val);
                }else{
                    vals.offerFirst(now.treenode.val);
                }
                if(now.treenode.left != null){
                    dq.offerLast(new node(nowfloor+1,now.treenode.left));
                }
                if(now.treenode.right != null){
                    dq.offerLast(new node(nowfloor+1,now.treenode.right));
                }
            }
            ans.add(vals);
        }
        return ans;
    }
}
```



#### 第十六剑式： 树的子结构

> 题目来源：LeetCode 剑指 Offer 26

输入两棵二叉树A和B，判断B是不是A的子结构。(约定空树不是任意一个树的子结构) B是A的子结构， 即 A中有出现和B相同的结构和节点值。

```
给定的树 A:
     3
    / \
   4   5
  / \
 1   2
给定的树 B：
   4 
  /
 1
返回 true，因为 B 与 A 的一个子树拥有相同的结构和节点值。
```

题目解析：

首先是找到A中有没有和B根节点值相同的结点，有的话就和B一起递归深度遍历树，判断两个树是否一样。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    //比较以A和B为根节点的树，B是否是A的子结构
    public boolean check(TreeNode A,TreeNode B){
        if(B == null) return true; //B是null true
        else if(A == null && B != null) return false;//A中结点null，B有值，则false
        else if(A.val != B.val) return false; //两个结点的值不等 false
        else return check(A.left,B.left) && check(A.right,B.right); //继续递归比较
    }
    //找A中与B根节点相同的子结点
    public boolean dfs(TreeNode A,TreeNode B){
        //如果A是空，说明找到了底层没找到，返回false
        if(A == null) return false;
       	//如果找到了，开始比较B是否是A中的一个子树，是的话返回错误，不是的话则继续在A中找与B根节点相同的结点
        if(A.val == B.val && check(A,B)) {
            return true;
        }//继续递归A的左右子节点寻找与B根节点相同的结点
        else return dfs(A.left,B) || dfs(A.right,B);
    }
    public boolean isSubStructure(TreeNode A, TreeNode B) {
        //根据题目要求，空树不是任意一个树的子结构，因此可以作为特殊情况直接判断
        if (A == null || B == null) return false;
        //深度优先搜索找A中与B根节点相同的子结点
        return dfs(A,B);
    }
}
```

#### 第十七剑式： 二叉树的镜像

> 题目来源：LeetCode 剑指 Offer 27

请完成一个函数，输入一个二叉树，该函数输出它的镜像。

```
例如输入：
     4
   /   \
  2     7
 / \   / \
1   3 6   9
镜像输出：
     4
   /   \
  7     2
 / \   / \
9   6 3   1
输入：root = [4,2,7,1,3,6,9]
输出：[4,7,2,9,6,3,1]
```

**题目解析**：

个人感觉就是递归搜索A树的同时，创建B树的结点，将A的左节点作为B的右节点，将A的右节点作为B的左节点。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public void dfs(TreeNode A,TreeNode B){
        //如果A是空结点，直接返回
        if(A == null) return ;
        //A 的左子树不空则创建该结点作为B的右节点，然后递归
        if(A.left != null){
            B.right = new TreeNode(A.left.val);
            dfs(A.left,B.right); //注意是递归A的左 和 B的右
        }
        //A 的右子树不空则创建该结点作为B的左节点，然后递归
        if(A.right != null){
            B.left = new TreeNode(A.right.val);
            dfs(A.right,B.left); //注意是递归A的右 和 B的左
        }
    }
    public TreeNode mirrorTree(TreeNode root) {
        //如果root空，直接返回null
        if(root == null) return null;
        //先创建一个根结点
        TreeNode ans = new TreeNode(root.val);
        dfs(root,ans);
        return ans;
    }
}
```

#### 第十八剑式：对称的二叉树

> 题目来源：LeetCode 剑指 Offer 28

请实现一个函数，用来判断一棵二叉树是不是对称的。如果一棵二叉树和它的镜像一样，那么它是对称的。

```
例如，二叉树 [1,2,2,3,4,4,3] 是对称的。
    1
   / \
  2   2
 / \ / \
3  4 4  3
但是下面这个 [1,2,2,null,3,null,3] 则不是镜像对称的:
    1
   / \
  2   2
   \   \
   3    3
```

**题目解析**：

其实就是对照着示例给的这个就可以理解比如当前是结点1，将左子结点2和右子结点对比，然后递归将(1)的左子结点(2)的左子结点(3)和(1)的右子结点(2)的右子节点(3)对比，将(1)的左子结点(2)的右子结点(4)和(1)的右子结点(2)的左子节点(4)对比

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public boolean dfs(TreeNode A,TreeNode B){
        if(A == null && B == null) return true;
        else if ((A == null && B != null) || (A != null && B == null)) return false;
        else if(A.val == B.val) return dfs(A.left,B.right) && dfs(A.right,B.left);
        else return false;
    }
    public boolean isSymmetric(TreeNode root) {
        if(root == null) return true;
        return dfs(root.left,root.right);
    }
}
```



#### 第十九剑式：连续子数组的最大和

> 题目来源：LeetCode 剑指 Offer 42

输入一个整型数组，数组中的一个或连续多个整数组成一个子数组。求所有子数组的和的最大值。

要求时间复杂度为O(n)。

```
输入: nums = [-2,1,-3,4,-1,2,1,-5,4]
输出: 6
解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
```

题目解析：

定义一个dp[]，并规定dp[i]表示以 nums[i]结尾的子数组的最大和是多少。

因此，对于dp[i]，其值取决于dp[i-1]，如果`dp[i-1] + nums[i]`(即，以nums[i-1]结尾的最大和再加上当前值) 小于 `nums[i]`，那么肯定就舍去前面的`dp[i-1]`(以nums[i-1]结尾的那串连续子数组)，将`nums[i]`作为新的连续子数组的头。因此就可以推导出状态转移方程：`dp[i] = Math.max(dp[i-1]+nums[i],nums[i])`

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int n = nums.length;
        int[] dp = new int[n];
        dp[0] = nums[0];
        int ans = nums[0];
        for(int i = 1;i < n;i++){
            dp[i] = Math.max(dp[i-1] + nums[i],nums[i]);
            ans = Math.max(ans,dp[i]);
        }
        return ans;
    }
}
```

#### 第二十剑式：礼物的最大价值

> 题目来源：LeetCode 剑指 Offer 47

在一个 m*n 的棋盘的每一格都放有一个礼物，每个礼物都有一定的价值（价值大于 0）。你可以从棋盘的左上角开始拿格子里的礼物，并每次向右或者向下移动一格、直到到达棋盘的右下角。给定一个棋盘及其上面的礼物的价值，请计算你最多能拿到多少价值的礼物？

```
输入: 
[
  [1,3,1],
  [1,5,1],
  [4,2,1]
]
输出: 12
解释: 路径 1→3→5→2→1 可以拿到最多价值的礼物
```

**题目解析**：

就是一个简单的动态规划问题，对于除了第一行和第一列，每个点的状态都可以由左边的点和上边的点转移过来，为了拿到最大价值的礼物，当然是选择上一个状态价值高的点来状态转移。

```java
class Solution {
    public int maxValue(int[][] grid) {
        if(grid == null) return 0;
        int m = grid.length;
        int n = grid[0].length;
        int[][] dp = new int[m][n];
        dp[0][0] = grid[0][0];
        for(int i = 1;i < m;i++){
            dp[i][0] = dp[i-1][0] + grid[i][0];
        }
        for(int i = 1;i < n;i++){
            dp[0][i] = dp[0][i-1] + grid[0][i];
        }
        for(int i = 1;i < m;i++){
            for (int j = 1; j < n;j++){
                dp[i][j] = Math.max(dp[i-1][j],dp[i][j-1]) + grid[i][j];
            }
        }
        return dp[m-1][n-1];
    }
}
```



#### 第二十一剑式：把数字翻译成字符串

> 题目来源：LeetCode 剑指 Offer 46

给定一个数字，我们按照如下规则把它翻译为字符串：0 翻译成 “a” ，1 翻译成 “b”，……，11 翻译成 “l”，……，25 翻译成 “z”。一个数字可能有多个翻译。请编程实现一个函数，用来计算一个数字有多少种不同的翻译方法。

```
输入: 12258
输出: 5
解释: 12258有5种不同的翻译，分别是"bccfi", "bwfi", "bczi", "mcfi"和"mzi"
```

**题目解析**：

我们定义`dp[i]`表示以下标`i`结束的字符串其最多有多少种不同的方案，对于以下标为`i`的字符，其可以单独作为一个字符和前面的字符串连接，获得`dp[i-1]`种方案，同样，它还能和前面一个字符结合称为一个值在`[10,25]`的字符，获得`dp[i-2]`种方案。因此对于`dp[i]`的状态，可以由`dp[i-1]和dp[i-2]`来转移。

```java
class Solution {
    public int translateNum(int num) {
        String strNum = String.valueOf(num);
        if (strNum.length() == 0) return 0;
        else if(strNum.length() == 1) return 1;
        int[] dp = new int[strNum.length()];
        dp[0] = 1;
        int tmp = Integer.parseInt(strNum.substring(0,2)) ;
        if(tmp >= 10 && tmp < 26){
            dp[1] = dp[0] + 1;
        }else{
            dp[1] = dp[0];
        }
        for(int i = 2;i < strNum.length();i++){
            tmp = Integer.parseInt(strNum.substring(i-1,i+1)) ;
            if(tmp >= 10 && tmp < 26){
                dp[i] = dp[i-1] + dp[i-2];
            }else{
                dp[i] = dp[i-1];
            }
        }
        return dp[strNum.length()-1];
    }
}
```



#### 第二十二剑式：最长不含重复字符的子字符串

> 题目来源：LeetCode 剑指 Offer 48

请从字符串中找出一个最长的不包含重复字符的子字符串，计算该最长子字符串的长度。

```
输入: "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3
```



**题目解析**：

动态规划
状态定义： 设动态规划列表 dp[j] 代表以字符 s[j] 为结尾的 “最长不重复子字符串” 的长度。

转移方程： 固定右边界 j，设字符 s[j] 左边距离最近的相同字符为 s[i]。

1. 当 `i < 0 `，即 s[j] 左边无相同字符，则` dp[j] = dp[j-1] + 1`
2. 当 `dp[j - 1] < j - i`，说明字符 s[i] 在子字符串` dp[j−1] `区间之外 ，则 `dp[j]=dp[j−1]+1` 
3. 当 `dp[j−1]≥j−i` ，说明字符 s[i] 在子字符串 `dp[j−1] `区间之中 ，则` dp[j] `的左边界由` s[i] `决定，即` dp[j]=j−i`

> 当` i<0 `时，由于 `dp[j−1]≤j `恒成立，因而 `dp[j−1]<j−i `恒成立，因此分支 1. 和 2. 可被合并。

- **哈希表统计：** 遍历字符串 s 时，使用哈希表统计 **各字符最后一次出现的索引位置** 。

```java
class Solution {
    //动态规划
    public int lengthOfLongestSubstring(String s) {
        int n = s.length();
        if(n == 0) return 0;
        Map<Character,Integer> m = new HashMap<>();
        int [] dp = new int[n];
        dp[0] = 1;
        m.put(s.charAt(0),0);
        int maxLen = 1;
        for(int i = 1;i < n;i++) {
            int lastIndex = m.get(s.charAt(i)) == null ? -1 : m.get(s.charAt(i));
            if(dp[i-1] >= i - lastIndex){ //上一个当前元素在dp[i-1]范围内
                dp[i] = i - lastIndex;
            }else{ //上一个当前元素不在dp[i-1]范围内 包含 lastIndex == -1
                dp[i] = dp[i-1] + 1;
            }
            maxLen = Math.max(maxLen,dp[i]);
            m.put(s.charAt(i),i);
        }
        return maxLen;
    }
}
```

双指针

哈希表 dic 统计： 指针 j 遍历字符 s ，哈希表统计字符 s[j] 最后一次出现的索引 。
更新左指针 i ： 根据上轮左指针 i 和 dic[s[j]] ，每轮更新左边界 i ，保证区间`[i+1,j] `内无重复字符且最大。
`i=max(dic[s[j]],i)`

更新结果 res ： 取上轮 res 和本轮双指针区间 `[i+1,j]` 的宽度（即 j−i ）中的最大值。`res=max(res,j−i)`

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int n = s.length();
        if (n == 0) return 0;
        Map<Character,Integer> m = new HashMap<>();
        int maxLen = 1;
        int left = -1,right = 0;
        while(right < n){
            if(m.get(s.charAt(right)) != null){
                left = Math.max(left,m.get(s.charAt(right)));//注意是 取两者较大值
            }
            m.put(s.charAt(right),right);
            maxLen = Math.max(maxLen,right-left);
            
            right += 1;
        }
        return maxLen;
    }
}
```



#### 第二十三剑式：最长不含重复字符的子字符串

> 题目来源：LeetCode 剑指 Offer 18

给定单向链表的头指针和一个要删除的节点的值，定义一个函数删除该节点。

返回删除后的链表的头节点。

题目解析：

很容易的一道链表题，直接ko

```java
class Solution {
    public ListNode deleteNode(ListNode head, int val) {
        if(head == null) return null;
        ListNode pre = null;
        ListNode now = head;
        if(head.val == val) return head.next;
        while(now != null){
            if(now.val == val){
                pre.next = now.next;
            }
            pre = now;
            now = now.next;
        }
        return head;
    }
}
```



#### 第二十四剑式：最长不含重复字符的子字符串

> 题目来源：LeetCode 剑指 Offer 22

输入一个链表，输出该链表中倒数第k个节点。

题目解析：

也是比较容易的链表题，采用倒退的想法，开始时一个标记指向尾结点的下一个null，另外一个指向倒数第k个结点，然后让这两个结点一次向前遍历，当原本指向倒数第k个结点的标记指向了头节点（此时可以理解为一个初始状态），而另外一个指向的正数的第k个结点，那么我们再正向来想，开始的状态就是上述的初始状态，那么逐个向后遍历，就可以在初始状态指向正数第k个结点的指针指向尾部null时，原本指向头结点的指针就指向了倒数第k个结点。

```java
class Solution {
    public ListNode getKthFromEnd(ListNode head, int k) {
        int cnt = 1;
        ListNode now = head;
        ListNode ans = head;
        while(cnt <= k){
            ans = ans.next;
            cnt += 1;
        }
        while(ans != null){
            ans = ans.next;
            now = now.next;
        }
        return now;
    }
}
```



#### 第二十五剑式：合并链表

> 题目来源：LeetCode 剑指 Offer 25

输入两个递增排序的链表，合并这两个链表并使新链表中的节点仍然是递增排序的。

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
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        //把second加first上面
        if(l1 == null && l2 == null) return null;
        else if(l1 == null && l2 != null) return l2;
        else if(l1 != null && l2 == null) return l1;
        ListNode first = l1;
        ListNode second = l2;
        //第一个值小的作为first
        if(l1.val > l2.val ){
            first = l2;
            second = l1;
        }
        ListNode root = first;
        ListNode t1 = null,t2 = null;
        while(first.next != null && second != null){
            t2 = second.next;
            if(first.next.val < second.val){
                first = first.next;
                continue;
            }else{
                second.next = first.next;
                first.next = second;
            }
            second = t2;
            first = first.next;
        }
        if(second != null){
            first.next = second;
        }
        return root;
    }
}
```

#### 第二十六剑式：两个链表的第一个公共结点

> 题目来源：LeetCode 剑指 Offer 52

输入两个链表，找出它们的第一个公共节点。

哈希表：

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        Set<ListNode> s = new HashSet<>();
        while(headA != null){
            s.add(headA);
            headA = headA.next;
        }
        while(headB != null){
            if(s.contains(headB)) return headB;
            headB = headB.next;
        }
        return null;
    }
}
```

双指针：

只有当链表 headA 和 headB 都不为空时，两个链表才可能相交。因此首先判断链表 headA 和 headB 是否为空，如果其中至少有一个链表为空，则两个链表一定不相交，返回 null。

当链表 headA 和 headB 都不为空时，创建两个指针 pA 和 pB，初始时分别指向两个链表的头节点 headA 和 headB，然后将两个指针依次遍历两个链表的每个节点。具体做法如下：

每步操作需要同时更新指针 pA 和 pB。

- 如果指针 pA 不为空，则将指针 pA 移到下一个节点；如果指针 pB 不为空，则将指针 pB 移到下一个节点
- 如果指针 pA 为空，则将指针 pA 移到链表 headB 的头节点；如果指针 pB 为空，则将指针  pB 移到链表 headA 的头节点。
- 当指针 pA 和  pB 指向同一个节点或者都为空时，返回它们指向的节点或者  null。

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        if (headA == null || headB == null) {
            return null;
        }
        ListNode pA = headA, pB = headB;
        while (pA != pB) {
            pA = pA == null ? headB : pA.next;
            pB = pB == null ? headA : pB.next;
        }
        return pA;
    }
}
```



#### 第二十七剑式：调整数组顺序使奇数位于偶数前面

> 题目来源：LeetCode 剑指 Offer 21

输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有奇数在数组的前半部分，所有偶数在数组的后半部分。

**题目解析**：

维护两个奇、偶指针，分别从前和从后开始，然后寻找对于不符合条件的数，然后交换位置。

```java
class Solution {
    public int[] exchange(int[] nums) {
        int left = 0,right = nums.length - 1;
        while(left < right){
            while(left < right && nums[left] % 2 == 1){
                left += 1;
            }
            while(left < right && nums[right] % 2 == 0){
                right -= 1;
            }
            int tmp = nums[left];
            nums[left] = nums[right];
            nums[right] =tmp;
        }
        return nums;
    }
}
```



#### 第二十八剑式：和为s的两个数字

> 题目来源：LeetCode 剑指 Offer 

输入一个递增排序的数组和一个数字s，在数组中查找两个数，使得它们的和正好是s。如果有多对数字的和等于s，则输出任意一对即可

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int left = 0,right = nums.length-1;
        while(left < right){
            if(nums[left] + nums[right] == target){
                return new int[]{nums[left],nums[right]};
            }else if(nums[left] + nums[right] < target){
                left += 1;
            }else{
                right -= 1;
            }
        }
        return null;
    }
}
```



#### 第二十九剑式：翻转单词顺序

> 题目来源：LeetCode 剑指 Offer 58

输入一个英文句子，翻转句子中单词的顺序，但单词内字符的顺序不变。为简单起见，标点符号和普通字母一样处理。例如输入字符串"I am a student. "，则输出"student. a am I"

```
输入: "the sky is blue"
输出: "blue is sky the"
```

题目解析：

先翻转每个单词，再将整个句子翻转  - -时间复杂度有点高呀~

```java
class Solution {
    public String reverse(String word){
        StringBuilder sb = new StringBuilder(word.length());
        for (int i = word.length()-1;i >= 0;i--){
            sb.append(word.charAt(i));
        }
        return sb.toString();
    }
    public String reverseWords(String s) {
        String[] words = s.split(" ");
        String tmp = "";
        int cnt = 0;
        for(int i = 0;i < words.length;i++){
            if(words[i].equals("")) continue;
            if (cnt == 0){
                tmp += reverse(words[i]);
            }else{
                tmp += " " + reverse(words[i]);
            }
            cnt += 1;
        }
        return reverse(tmp);
    }
}
```

直接到序输出呀，人傻了

```java
class Solution {
    public String reverseWords(String s) {
        String[] strs = s.trim().split(" "); // 删除首尾空格，分割字符串
        StringBuilder res = new StringBuilder();
        for(int i = strs.length - 1; i >= 0; i--) { // 倒序遍历单词列表
            if(strs[i].equals("")) continue; // 遇到空单词则跳过
            res.append(strs[i] + " "); // 将单词拼接至 StringBuilder
        }
        return res.toString().trim(); // 转化为字符串，删除尾部空格，并返回
    }
}
```



#### 第三十剑式：矩阵中的路径

> 题目来源：LeetCode 剑指 Offer  12

给定一个 m x n 二维字符网格 board 和一个字符串单词 word 。如果 word 存在于网格中，返回 true ；否则，返回 false 。

单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。

![img](🗡指Offer系列.assets/word2.jpg)

```
输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"
输出：true
```

题目解析：

比较简单的深搜问题

```java
class Solution {
    boolean [][] used;
    int [][] dirs = new int[][]{{-1,0},{1,0},{0,1},{0,-1}};
    int m,n;
    public boolean dfs(char [][] board,String word,int idx,int x,int y){
        if(idx == word.length()){
            return true;
        }
        used[x][y] = true;
        for(int i = 0;i < 4;i++){
            int nx = x + dirs[i][0];
            int ny = y + dirs[i][1];
            if(nx >= m || nx < 0 || ny >= n || ny < 0 || used[nx][ny] == true) continue;
            if(board[nx][ny] != word.charAt(idx)) continue;
            if (dfs(board,word,idx+1,nx,ny)) return true;
        }
        used[x][y] = false;
        return false;
    }
    public boolean exist(char[][] board, String word) {
        if(board == null) return false;
        m = board.length;
        n = board[0].length;
        used = new boolean[m][n];
        for(int i = 0; i < m;i++){
            for(int j = 0;j < n;j++){
                if(board[i][j] == word.charAt(0)){
                    used[i][j] = true;
                    if(dfs(board,word,1,i,j)) return true;
                    used[i][j] = false;
                }
            }
        }
        return false;
    }
}
```





#### 第三十一剑式：机器人的运动范围

> 题目来源：LeetCode 剑指 Offer  13

地上有一个m行n列的方格，从坐标 [0,0] 到坐标 [m-1,n-1] 。一个机器人从坐标 [0, 0] 的格子开始移动，它每次可以向左、右、上、下移动一格（不能移动到方格外），也不能进入行坐标和列坐标的数位之和大于k的格子。例如，当k为18时，机器人能够进入方格 [35, 37] ，因为3+5+3+7=18。但它不能进入方格 [35, 38]，因为3+5+3+8=19。请问该机器人能够到达多少个格子？

```
输入：m = 2, n = 3, k = 1
输出：3
```

题目解析：

比较简单的搜索遍历问题，小优化：只需要从[0,0]开始向下和向右搜索。

```java
class node{
    public int x;
    public int y;
    node(int x,int y){
        this.x = x;
        this.y = y;
    }
}
class Solution {
    
    int[][] dirs = new int[][]{{0,1},{1,0}};

    public int getSum(int x,int y){
        int sum = 0;
        while (x != 0){
            sum += (x % 10);
            x /= 10;
        }
        while (y != 0){
            sum += (y % 10);
            y /= 10;
        }
        return sum;
    }

    public int movingCount(int m, int n, int k) {
        boolean[][] used = new boolean[m][n];
        Deque<node> dq = new ArrayDeque<>();
        dq.offerLast(new node(0,0));
        used[0][0] = true;
        int cnt = 1;
        while (!dq.isEmpty()){
            node nowNode = dq.pollFirst();
            for(int i = 0; i < 2;i++){
                int nx = nowNode.x + dirs[i][0];
                int ny = nowNode.y + dirs[i][1];
                if(nx >= m || ny >= n || used[nx][ny] == true) continue;
                if(getSum(nx,ny) <= k){
                    used[nx][ny] = true;
                    dq.offerLast(new node(nx,ny));
                    cnt += 1;
                }
            }
        }
        return cnt;

    }
}
```



#### 第三十二剑式：二叉树中和为某一值的路径

> 题目来源：LeetCode 剑指 Offer  34

给你二叉树的根节点 root 和一个整数目标和 targetSum ，找出所有 从根节点到叶子节点 路径总和等于给定目标和的路径。

叶子节点 是指没有子节点的节点。

![img](🗡指Offer系列.assets/pathsumii1.jpg)

```
输入：root = [5,4,8,11,null,13,4,7,2,null,null,5,1], targetSum = 22
输出：[[5,4,11,2],[5,8,4,5]]
```

**题目解析**：

一个简单的树的深度优先搜索问题

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    List<List<Integer>> ans = new ArrayList<List<Integer>>();
    List<Integer> path = new ArrayList<>();
    public void dfs(TreeNode node,int val,int target){
        if(node == null){
            return ;
        }
        if(node.left == null && node.right == null && val + node.val == target){
            path.add(node.val);
            ans.add(new LinkedList<>(path));
            path.remove(path.size() - 1);
            return ;
        }
        path.add(node.val);
        dfs(node.left,val+ node.val,target);
        dfs(node.right,val+ node.val,target);
        path.remove(path.size()-1);
    }
    public List<List<Integer>> pathSum(TreeNode root, int target) {
        if (root == null) return ans;
        dfs(root,0,target);
        return ans;
    }
}
```



#### 第三十三剑式：二叉搜索树的第k大结点

> 题目来源：LeetCode 剑指 Offer  54

给定一棵二叉搜索树，请找出其中第 `k` 大的节点的值。

**题目解析**：

一个中序遍历搞定！

```java
class Solution {
    List<Integer> ans = new ArrayList<>();
    public void dfs(TreeNode root){
        if(root == null) return;
        dfs(root.left);
        ans.add(root.val);
        dfs(root.right);
    }
    public int kthLargest(TreeNode root, int k) {
        dfs(root);
        return ans.get(ans.size() - k);
    }
}
```



#### 第三十四剑式：二叉搜索树与双向链表

> 题目来源：LeetCode 剑指 Offer  36

输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的循环双向链表。要求不能创建任何新的节点，只能调整树中节点指针的指向。

为了更好地理解问题，以下面的二叉搜索树为例:

![img](🗡指Offer系列.assets/bstdlloriginalbst.png)

将这个二叉搜索树转化为双向循环链表。链表中的每个节点都有一个前驱和后继指针。对于双向循环链表，第一个节点的前驱是最后一个节点，最后一个节点的后继是第一个节点。

下图展示了上面的二叉搜索树转化成的链表。“head” 表示指向链表中有最小元素的节点。

![img](🗡指Offer系列.assets/bstdllreturndll.png)

题目解析：

这题还是需要一点技巧的，我们想要修改一个结点的后继指针(即right)，我们可以标记一个pre指针指向前一个结点，利用`pre.right = now`来修改结点的后继指针，通过`now.left = pre`来修改当前结点的前驱指针。对于二叉搜索树，如果需要结果是递增有序，则就联系到了`中序遍历`。

因此，本题目采用中序遍历来遍历树，然后在操作区间修改结点的前驱指针和后继指针。

注意：

- 在修改前驱指针和后继指针时，如果当`pre == null`时，说明当前结点是第一个结点（即树的最左端的结点，双向循环链表的第一个结点）此时修改`head = now`；如果当`pre != null`，修改`pre.right = now`。同时注意修改当前结点的前驱指针`node.left = pre`，更新pre指针为`pre = now`。

```java
class Solution {
    Node head = null;
    Node pre = null;
    public void dfs(Node node){
        if(node == null) return;
        dfs(node.left); //递归左子树
        /*前驱指针、后继指针处理部分*/
        if(pre == null){
            head = node; //说明当前结点是头节点
        }else{
            pre.right = node; //修改前驱结点的后继指针
        }
        node.left = pre; //修改当前结点的前驱指针
        pre = node; //更新pre为当前结点
        dfs(node.right); //递归处理右子树
    }
    public Node treeToDoublyList(Node root) {
        if(root == null) return null; //特殊情况，特殊处理
        dfs(root); //遍历
        /*修改头节点和尾结点的指针*/
        head.left = pre; 
        pre.right = head;
        return head;
    }
}
```

