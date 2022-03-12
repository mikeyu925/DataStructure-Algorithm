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

### 怎么建立一个块状数组

```java

```

再给一个示例：

给定一个长度为 n 的序列$(a_i)$ ，需要执行$n$次操作。操作分为两种:

1. 更改操作：给$[a_l,a_r]$之间的所有数加上x
2. 查询操作：查询$[a_l,a_r]$之间大于等于x的元素个数

解析：

我们要询问一个块内大于等于一个数的数的个数，所以需要一个 `t` 数组对块内排序，`a` 为原来的（未被排序的）数组。对于整块的修改，使用类似于标记永久化的方式，用 `delta` 数组记录现在块内整体加上的值。设$q$为查询和修改的操作次数总和，则时间复杂度$O(q\sqrt n \log n)$ 。

