## 分块思想

分块的基本思想是，通过对原数据的适当划分，并在划分后的每一个块上预处理部分信息，从而较一般的暴力算法取得更优的时间复杂度。

分块的时间复杂度主要取决于分块的块长，一般可以通过均值不等式求出某个问题下的最优块长，以及相应的时间复杂度。

**优点**：

分块是一种很灵活的思想，相较于树状数组和线段树，分块的优点是通用性更好，可以维护很多树状数组和线段树无法维护的信息。

**缺点**：

渐进意义的复杂度，相较于线段树和树状数组不够好。



### 示例-区间和

给定一个长度为 n 的序列$(a_i)$ ，需要执行$n$次操作。操作分为两种:

1. 更改操作：给$a_l - a_r$之间的所有数加上x
2. 查询操作：求$\sum_{i=l}^r a_i$

**题目解析**：

方法一：

我们将序列按每$s$个元素一块进行分块，并记录每块的区间和$b_i$ 。

<img src="块状Data Structure.assets/2022-03-09_133610.jpg" alt="2022-03-09_133610" style="zoom:50%;" />

> 最后一个块可能是不完整的，但是这对于我们的讨论来说并没有太大影响。

对于更改操作：

- 若$l$ 和 $r$ 在同一个块内，直接暴力修改即可，因为块长为 $s$ ，因此最坏复杂度为$O(s)$  。
- 若$l$ 和 $r$ 不在同一个块内，则需要修改三部分：以$l$开头的不完整块，中间几个完整块，以$r$结尾的不完整块。对于不完整的块，仍然是暴力修改每个元素的值（别忘了更新区间和$b_i$ ），对于完整块，则直接修改$b_i$即可。这种情况下，最坏复杂度和仍然为$O(n/s + s)$ 。**注：这是官方给的想法，但是我认为不能直接修改$b_i$就行，因为会有Bug**

对于查询操作：

- 若$l$ 和 $r$ 在同一个块内，直接暴力就和即可，因为块长为 $s$ ，因此最坏复杂度为$O(s)$  。
- 若$l$ 和 $r$ 不在同一个块内，则需要修改三部分：以$l$开头的不完整块，中间几个完整块，以$r$结尾的不完整块。对于不完整的块，仍然是暴力求和每个元素的值，对于完整块，采用求出的$b_i$。这种情况下，最坏复杂度和仍然为$O(n/s + s)$ 。

```java
class SectionSum{
    int [] nums;
    int [] block_sum;
    int block_num;
    int block_size;
    // java 不支持默认参数值
    SectionSum(int[] nums,int block_size){
        int n = nums.length;
        this.block_size = block_size;
        this.block_num = n  % block_size == 0 ? (n / block_size) : (n / block_size) + 1;
        this.block_sum = new int[this.block_num];
        this.nums = new int[n];
        for (int i = 0;i < n;i++){
            this.nums[i] = nums[i];
            this.block_sum[i/this.block_size] += nums[i];
        }
    }
    // 默认10个区间
    SectionSum(int [] nums){
        int n = nums.length;
        this.block_size = 10;
        this.block_num = n  % this.block_size == 0 ? (n / this.block_size) : (n / this.block_size) + 1;
        this.block_sum = new int[this.block_num];
        this.nums = new int[n];
        for (int i = 0;i < n;i++){
            this.nums[i] = nums[i];
            this.block_sum[i/this.block_size] += nums[i];
        }
    }

    public void add(int l,int r,int x){
        for (int i = l;i <=r;i++){
            this.nums[i] += x;
            this.block_sum[i / this.block_size] += x;
        }
    }
    public int query(int l,int r){
        boolean start_head = l % this.block_size == 0;
        boolean end_head = (r + 1) % this.block_size == 0;
        int start_id = l / 4;
        int end_id = r / 4;
        int sum = 0;
        if (!start_head){
            int t = l;
            while (t % this.block_size != 0){
                sum += this.nums[t++];
            }
            start_id += 1;
        }
        if (!end_head){
            int t = r;
            while ((t+1) % this.block_size != 0){
                sum += this.nums[t--];
            }
            end_id -= 1;
        }
        for (int i = start_id;i <= end_id;i++){
            sum += this.block_sum[i];
        }
        return sum;
    }
}
```

方法二：

利用前缀和。

```java
class SectionSum{
    int [] nums;
    int [] presum;
    // java 不支持默认参数值
    SectionSum(int[] nums){
        int n = nums.length;
        this.nums = new int[n];
        this.presum = new int[n];
        for (int i = 0;i < n;i++){
            this.nums[i] = nums[i];
            if (i == 0){
                this.presum[i] = nums[i];
            }else{
                this.presum[i] = this.presum[i-1] + nums[i];
            }
        }
    }

    public void add(int l,int r,int x){
        int cnt = 1;
        for (int i = l;i <= r;i++){
            this.nums[i] += x;
            this.presum[i] += x * cnt;
            cnt += 1;
        }
    }
    public int query(int l,int r){
        return this.presum[r] - this.presum[l] + this.nums[l];
    }
}
```





## 块状数组

块状数组，即把一个数组分为几个块，块内信息整体保存，若查询时遇到两边不完整的块直接暴力查询。一般情况下，块的长度为$O(\sqrt n)$

再给一个示例：

### 示例-区间修改，区间查询

给定一个长度为 n 的序列$(a_i)$ ，需要执行$n$次操作。操作分为两种:

1. 更改操作：给$[a_l,a_r]$之间的所有数加上x
2. 查询操作：查询$[a_l,a_r]$之间大于等于x的元素个数

解析：

我们要询问一个块内大于等于一个数的数的个数，所以需要一个 `t` 数组对块内排序，`a` 为原来的（未被排序的）数组。对于整块的修改，使用类似于标记永久化的方式，用 `delta` 数组记录现在块内整体加上的值。设$q$为查询和修改的操作次数总和，则时间复杂度$O(q\sqrt n \log n)$ 。

```java
class BlockArr {
    int [] arr;   //存储元素的数组
    int block_num; // 分块的数量
    int [] start;  // 每个块的起始索引
    int [] end;  //每个块的结束索引
    int [] belong;   // arr[i] 属于哪个块
    int [] size;   //每个块的size大小
    int [] delat;  //记录每个块的整体赋值情况  好像有问题
    int [] sort_arr;  // 对 每个块 排序后的 arr
    BlockArr(int [] nums){
        int n = nums.length;
        // Init Memory Space
        this.arr = new int[n+1];
        this.block_num = (int)Math.sqrt(n);  
        this.delat = new int[this.block_num+1];  
        this.start = new int[this.block_num + 1]; 
        this.end = new int[this.block_num + 1];  
        this.belong = new int[n+1]; 
        this.size = new int[this.block_num+1]; 
        this.sort_arr = new int[n+1]; 
        // Init Value
        for (int i = 0;i < nums.length;i++){
            this.arr[i+1] = nums[i];
            this.sort_arr[i+1] = nums[i];
        }
        for (int i = 1;i <= this.block_num ;i++){
            this.start[i] = n / this.block_num * (i-1) + 1;
            this.end[i] = n / this.block_num * i;
        }
        this.end[this.block_num] = n;
        for (int i = 1;i <= this.block_num;i++){
            for (int j = this.start[i];j <= this.end[i];j++){
                this.belong[j] = i;
            }
            this.size[i] = this.end[i] - this.start[i] + 1;
        }
    }
    public void inSort(int block_id){
        for (int i = this.start[block_id];i <= this.end[block_id];i++){
            this.sort_arr[i] = this.arr[i];
        }
        Arrays.sort(this.sort_arr,this.start[block_id],this.end[block_id]+1);
    }
    //修改操作
    public void Modify(int l,int r,int c){
        int x = this.belong[l];
        int y = this.belong[r];
        if (x == y){ // 区间在一个块内的话直接修改
            for (int i = l;i <= r;i++){
                this.arr[i] += c;
            }
            // 对该区间进行排序修改
            inSort(x);
        }
        // 不在同一个区间
        for (int i = l; i <= this.end[x];i++) this.arr[i] += c;
        for (int i = this.start[y];i <= r;i++) this.arr[i] += c;
        for (int i = x + 1;i < y;i++) this.delat[i] += c;
        inSort(x);
        inSort(y);
    }
    //查询操作
    public int Answer(int l,int r,int c){
        int ans = 0,x = this.belong[l],y = this.belong[r];
        if (x == y){
            for (int i = l; i <= r;i++) if (this.arr[i] + this.delat[x] >= c) ans+=1;
            return ans;
        }
        for (int i = l;i <= this.end[x];i++){
            if (this.arr[i] + this.delat[x] >= c) ans += 1;
        }
        for (int i = this.start[y];i <= r;i++){
            if (this.arr[i] + this.delat[y] >= c) ans += 1;
        }
        for (int i = x + 1;i <= y-1;i++){
            int idx = lower_bound(i,c-this.delat[i]);
            ans += this.end[i] - idx + 1;
        }
        return ans;
    }
    public int lower_bound(int x,int c){
        int l = this.start[x],r = this.end[x];
        while (l < r){
            int m = l + ((r - l) >> 1);
            if (this.sort_arr[m] < c){
                l = m + 1;
            }else{
                r = m;
            }
        }
        return l;
    }
    public void print(){
        for (int i = 1;i < this.arr.length;i++){
            System.out.print((this.arr[i] + this.delat[this.belong[i]]) + " ");
        }
        System.out.println();
    }
}
```

测试：

```java
    public static void main(String [] args){
        BlockArr br = new BlockArr(new int[]{1,1,1,1,1,1,1,1,1,1});
        br.Modify(1,4,2);
        br.print();
        br.Modify(2,7,1);
        br.print();
        br.Modify(5,9,1);
        br.print();
        System.out.println(br.Answer(3,6,4));
    }
```

```
3 3 3 3 1 1 1 1 1 1 
3 4 4 4 2 2 2 1 1 1 
3 4 4 4 3 3 3 2 2 1 
2
```

## 分块链表

块状链表就是，每个链表的结点还包括一个 数组 的元素

数据结构形式如下：

```java
class node{
    node next;
    int size;
    char [] arr;
    node(){
        this.size = 0;
        this.next = null;
        arr = new char[(int)Math.sqrt(1000000)];
    }
    public void push_(char ch){
        this.arr[this.size++] = ch;
    }
}
```

块状链表应该至少支持：分裂、插入、查找。 什么是分裂？分裂就是分裂一个 `node`，变成两个小的 `node`，以保证每个 `node` 的大小都接近$\sqrt{n}$ （否则可能退化成普通数组）。当一个 `node` 的大小超过$2\sqrt{n}$时执行分裂操作。

分裂操作怎么做呢？先新建一个节点，再把被分裂的节点的后$\sqrt{n}$个值 `copy` 到新节点，然后把被分裂的节点的后 个值删掉$\sqrt{n}$（`size--`），最后把新节点插入到被分裂节点的后面即可。

块状链表的所有操作的复杂度都是$\sqrt{n}$ 的。

还有一个要说的。 随着元素的插入（或删除），${n}$ 会变，$\sqrt{n}$ 也会变。这样块的大小就会变化，我们难道还要每次维护块的大小？

其实不然，把$\sqrt{n}$ 设置为一个定值即可。比如题目给的范围是$10^6$ ，那么$\sqrt{n}$就设置为大小为 的常量$10^3$，不用更改它。

### 示例-Big String

你得到一个字符串并且应该做一些字符串操作。

输入的第一行包含初始字符串。您可以假设它是非空的，并且它的长度不超过 1,000,000。

第二行包含操作命令的数量*N* (0 < *N* ≤ 2,000)。以下*N*行每行描述一个命令。命令采用以下两种格式之一：

1. `I ch p` ：在当前字符串的第 p个字符之前插入一个字符*ch 。*如果p大于字符串的长度，则将字符附加到字符串的末尾。
2. `Q p`：查询当前字符串的第 p个字符。输入确保第p个字符存在。

输入中的所有字符都是英文字母的数字或小写字母。

```
输入：
ab 
7 
Q 1 
I c 2 
I d 4 
I e 2 
Q 5 
I f 1 
Q 3
输出：
a
d
e
```

下面只是写了比较重要的部分的代码，没有写`main()`函数，如果想要取提交此题需要再稍微写个main读取函数就可以啦

```java
// 块状链表中的结点类
class node{
    static final int limit = (int)Math.sqrt(1000000); // 每个结点的arr最多存储的元素数，超过需要进行分裂
    node next; //指向下一个结点
    int size; // arr当前的size
    char [] arr; // arr:存储元素
    node(){
        this.size = 0;
        this.next = null;
        arr = new char[(int)Math.sqrt(limit) + 5];
    }
    // 向 arr 压入元素
    public void push_(char ch){
        this.arr[this.size++] = ch;
    }
}

class BlockLink{
    node head; // 链表的头节点
    int n;  // 存储的
    BlockLink(char [] str_arr){
        this.n = str_arr.length;  // 初始化字符串的长度
        head = new node(); // 创建一个头节点
        node now = head;
        for (int i = 0;i < this.n;i++){
            if (i % node.limit == 0)  { // 如果当前结点已经到达插入数量的上限
                now.next = new node(); // 创建一个新的结点
                now = now.next; // 更新当前结点
            }
            now.push_(str_arr[i]); // 插入字符
        }
    }
    public void insert(char ch,int pos){
        node t = this.head;
        if (pos > this.n){ //如果插入位置是在最后一个位置或者超过索引范围，则插入在最后
            while (t.next != null){ //找到最后一个结点
                t = t.next;
            }
            t.push_(ch); // 插入字符
            check(t); // 检查是否需要分裂
            return;
        }
        int tot;  // 记录已经经过几个字符
        for (tot = head.size;t != null && tot < pos;){ // t != null && tot < pos时说明还没找到哪个范围
            t = t.next;  //后移至另外一个结点
            tot += t.size; //增加增量 t.size 个字符数
        }
        tot -= t.size; // 过界了， 因此减去当前的数量，然后开始逐个寻找
        int cnt = pos - tot - 1; //
        for (int i = t.size - 1;i >= cnt;i--){
            t.arr[i+1] = t.arr[i]; // 后移元素，为插入位置空出位置
        }
        t.arr[cnt] = ch;
        t.size += 1;
        check(t); // 如果超过界限就分裂
        n += 1;  // 总元素个数 + 1
    }
    public char query(int pos){
        node p = head;
        int tot;
        for (tot = head.size;p != null && tot < pos;){
            p = p.next;
            tot += p.size;
        }
        tot -= p.size;
        return p.arr[pos - tot - 1];
    }
    // 判断 是否超出界限，进行分裂
    public void check(node t){
        if (t.size >= node.limit){  // 如果当前结点中数组的元素个数超过上线，则加一个新结点
            node add = new node();
            for (int i = node.limit;i < t.size;i++){
                add.push_(t.arr[i]);  // 添加结点
            }
            // 将新分裂的结点添加到当前结点后面
            t.size = node.limit;
            add.next = t.next;
            t.next = add;
        }
    }

    public void print(){
        node t = this.head;
        while (t != null){
            for (int i = 0;i < t.size;i++){
                System.out.print(t.arr[i]);
            }
            t = t.next;
        }
        System.out.println();
    }
}
```

