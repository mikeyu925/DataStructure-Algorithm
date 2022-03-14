## ST表

T 表是用于解决 **可重复贡献问题** 的数据结构。

![ST 表示意图](ST表.assets/st.svg)

**可重复贡献问题** 是指对于运算 $opt$，满足 $x \ opt\ x = x$，则对应的区间询问就是一个可重复贡献问题。例如，最大值有 $max(x,x) = x$，gcd 有 $gcd(x,x) = x$，所以 RMQ(区间最大/最小值) 和区间 GCD 就是一个可重复贡献问题。像区间和就不具有这个性质，如果求区间和的时候采用的预处理区间重叠了，则会导致重叠部分被计算两次，这是我们所不愿意看到的。另外， $opt$还必须满足结合律才能使用 ST 表求解。



ST 表是基于 倍增 思想，可以做到$(n\log(g))$预处理， $O(1)$回答每个询问。但是不支持修改操作。



### 示例-静态区间最大值

题目描述

> 给定一个长度为 N 的数列，和 M  次询问，求出每一次询问的区间内数字的最大值。

输入格式

> 第一行包含两个整数 N,M，分别表示数列的长度和询问的个数。
> 第二行包含 N 个整数（记为 $a_i$），依次表示数列的第 i 项。
> 接下来 M 行，每行包含两个整数 $l_i,r_i$，表示查询的区间为 $l_i,r_i$。

输出格式

> 输出包含 M 行，每行一个整数，依次表示每一次询问的结果。

**题目解析**

预处理部分：

令 $f(i,j)$表示区间 $[i,i+2^j-1]$的最大值。

显然 $f(i,0)= a_i$。

根据定义式，第二维就相当于倍增的时候“跳了$2^j - 1$步”，依据倍增的思路，写出状态转移方程：
$$
f(i,j) = max(f(i,j-1),f(i+2^{j-1},j-1))
$$
![img](ST表.assets/st-preprocess-lift.svg)

查询部分：

对于每个查询$[l,r]$，可以将其切分为两部分：$f[l,l+2^s-1]和f[r-2^s+1,r]$，其中，$s = \lfloor \log_2(r-l+1)\rfloor$,两部分结果的最大值就是回答。如下图求$[2,7]$为例子：

![ST 表的查询过程](ST表.assets/st-query.svg)

根据上面对于“可重复贡献问题”的论证，由于最大值是“可重复贡献问题”，重叠并不会对区间最大值产生影响。又因为这两个区间完全覆盖了$[l,r]$ ，可以保证答案的正确性。

```java
class STtable{
    static final  int maxn = 2000001;
    static final  int logn = 21;
    int [][] f;
    int [] Logn;
    STtable(int[] nums){
        int n = nums.length;
        this.f = new int[STtable.maxn][STtable.logn+1];
        this.Logn = new  int[STtable.maxn + 1];
        for (int i = 0;i < nums.length;i++){
            f[i][0] = nums[i];  // 等价于 ai
        }
        prelogn();
        for (int j = 1;j <= STtable.logn;j++){
            for (int i = 1;i + (1 << j) - 1 <= n; i++){
                //等价于f(i,j) = max(f(i,j-1),f(i+2^{j-1},j-1))
                f[i][j] = Math.max(f[i][j-1],f[i+ (1 << (j-1))][j-1]); //ST表初始化
            }
        }
    }
    private void prelogn(){
        Logn[1] = 0;
        Logn[2] = 1;
        for (int i = 3;i < maxn;i++){
            Logn[i] = Logn[i / 2] + 1;
        }
    }
    public int query(int l,int r){
        int s = Logn[r - l + 1];
        return Math.max(f[l][s],f[r-(1<<s) + 1][s]);
    }
}
```

