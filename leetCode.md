# SmallFish的刷题小本

> 前言：为了提升自己编码及数据结构与算法的能力，进行了一系列的刷题，包括leetcode、oj、牛客、算法竞赛等等上的题目，并系统进行了分类。本文记录了比较经典的题型，同时也总结了一些自己的心得、感悟、方法，甚至模板，希望能对大家有所帮助。

> 目前仅为第一版，后续看大家需要情况更新吧...
>
> Leetcode昵称：小鱼游阿游 [文章部分题解已经上传至Leetcode题目对于题解]

# 栈

栈的简单了解：栈是一种线性结构，服从`先进后出`的原则。可以由数组、链表来进行设计。

## 单调栈

> 单调栈分为 单调递减栈 和 单调递增栈。单调栈这种数据结构，通常应用在一维数组上。
>
> 通过单调栈可以访问到==下一个==比它==大/小==的元素，当==需要通过比较前后元素的大小关系==来解决问题时我们通常使用单调栈

#### 例：接雨水

```
给定 n 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。
```

![img](F:\Typora\Picture\leetcode\rainwatertrap.png)

```
输入：height = [0,1,0,2,1,0,1,3,2,1,2,1]
输出：6
```

为什么会想到单调栈呢？因为如果能接到水，一定是两边高，中间低。

<img src="F:\Typora\Picture\leetcode\接雨水.jpg" alt="接雨水" style="zoom: 50%;" />

```cpp
class Solution {
public:
    int trap(vector<int>& height) {
        int sum = 0;
        stack<int> st;//定义单调递减栈
        int i = 0,len = height.size();
        while (i < len)
        {
            /*当栈不空 并且 当前元素大于栈顶元素时[如果加入该元素会打破单调递减栈的结构]
            开始依次弹出栈中元素来维护栈结构，并进行相应操作*/
            while (!st.empty() && height[st.top()] < height[i])//i:遇到的哪个比前面高的
            {
                /*获取待处理元素*/
                int top = st.top();
                st.pop();
                /*如果栈空了，左边没有圆柱，积攒不了水，直接退出*/
                if(st.empty())
                {
                    break;
                }
                /*积攒到了雨水*/
                sum += ((height[i] > height[st.top()] ? height[st.top()] : height[i]) - height[top]) * (i-st.top()-1) ;
            }
            /*当栈空或者当前元素小于栈顶元素[即：不打破单调递减栈的结构时，直接入栈]*/
            st.push(i++);
        }
        return sum;
    }
};
```

#### 例：下一个更大元素

```
给你两个 没有重复元素 的数组 nums1 和 nums2 ，其中nums1 是 nums2 的子集。

请你找出 nums1 中每个元素在 nums2 中的下一个比其大的值。
nums1 中数字 x 的下一个更大元素是指 x 在 nums2 中对应位置的右边的第一个比 x 大的元素。如果不存在，对应位置输出 -1 。
```

```
输入: nums1 = [4,1,2], nums2 = [1,3,4,2].
输出: [-1,3,-1]
```

> 由题目，要求`nums1`中每个元素在`nums2`中的==下一个比其大的值==。因为单调栈维护的就是单调递增或单调递减的栈，因此可以知道第一个大于或小于其值的元素。

```cpp
class Solution {
public:
    vector<int> nextGreaterElement(vector<int>& nums1, vector<int>& nums2) {
        //哈希表m，用来存储num1中每个元素的下标，方便获得答案后直接存储
        map<int,int> m;
        int n1 = nums1.size(),n2 = nums2.size();
        //结果数组
        vector<int> ans(n1,-1);
        //更新哈希表
        for(int i = 0;i < n1;i++){
            m.insert(make_pair(nums1[i],i));
        }
        //单调递减栈
        stack<int> less_st;
        for(int i = 0;i < n2;i++){
            //如果栈空或者当前元素符合单调递减栈的结构，则压入；否则弹出栈顶元素，并对其处理
            while(!less_st.empty() && nums2[i] > less_st.top()){
                int tmp = less_st.top();
                less_st.pop();
                //如果栈顶元素在哈希表中，则说明其是nums1和nums2中共有的元素。
                if(m.find(tmp) !=  m.end()){
                    //当前元素就是栈顶元素遇到的第一个大于其的值
                    ans[m[tmp]] = nums2[i];
                }
            }
            less_st.push(nums2[i]);
        }
        return ans;
    }
};
```



# 队列





# 链表

> 链表类型题解题方法比较单一，因此只给出解题大致思路，部分给出代码。

```
给你两个单链表的头节点 headA 和 headB ，请你找出并返回两个单链表相交的起始节点。如果两个链表没有交点，返回 null 。
```

![img](F:\Typora\Picture\leetcode\160_example_1.png)

> 方法1：哈希
>
> ​	哈希表内存储链表A的每个结点的地址，然后遍历链表B，当在哈希表中存在与链表B中结点地址相同的结点，那么这个就是公共结点[交点]
>
> 方法2：双指针
>
> - 只有当链表 `headA` 和 `headB` 都不为空时，两个链表才可能相交。因此首先判断链表 `headA `和 `headB` 是否为空，如果其中至少有一个链表为空，则两个链表一定不相交，返回 null。
>
>
> - 当链表  `headA`  和  `headB` 都不为空时，创建两个指针 `pA ` 和 `pB`，初始时分别指向两个链表的头节点 `headA` 和  `headB` ，然后将两个指针依次遍历两个链表的每个节点。具体做法如下：
>   - 每步操作需要同时更新指针 `pA ` 和 `pB`。
>   - 如果指针 `pA ` 不为空，则将指针 `pA ` 移到下一个节点；如果指针 `pB` 不为空，则将指针 `pB` 移到下一个节点。
>   - 如果指针 `pA ` 为空，则将指针 `pA ` 移到链表 `headB` 的头节点；如果指针 `pB` 为空，则将指针`pB` 移到链表  `headA` 的头节点。
>   - 当指针 `pA ` 和 `pB` 指向同一个节点或者都为空时，返回它们指向的节点或者 `null`。



# 字符串

**字符串字典序排序**：

c++ 自带`sort()`函数

```cpp
//cpp
bool cmp(string a,string b){
    return a < b;
}

vector<string> strs(n);
sort(strs.begin(),strs.end(),cmp);
```

c语言 `strcmp()`函数

```c
void SortStrs(char ** strs,int n){
    char tmp[100] = {0};
    //对n个数据进行n-1次排序，每次冒泡出当前最大的
    for(int i = 1;i < n;i++){
        for(int j = 0;j < n - i;j++){
            if(strcmp(strs[j],strs[j+1]) > 0){
                strcpy(tmp,strs[j]);
                strcpy(strs[j],strs[j+1]);
                strcpy(strs[j+1],tmp);
            }
        }
    }
}
```



---

**STL string常用方法**：

```cpp
//获取字符串长度
str.size() 和 str.length()
//字符串替换
str.replace(ind,len,data); //将str从ind起始位置的len长度字符替换为data
```

# 二叉树

### 二叉树基础知识简单回顾

**一些基本定理**：

性质1：二叉树第i层上的结点数目最多为 $2^{i-1}(i≥1)$ 。
性质2：深度为k的二叉树至多有$2^{k}-1(k≥1)$个结点。
性质3：包含n个结点的二叉树的高度至少为$\log_2(n+1)$。
性质4：任意一棵二叉树，若终端结点的个数为$n_0$，度为2的结点数为$n_2$，则$n_0=n_2+1$。

**满二叉树**：高度为h，并且由$2^{h} –1$个结点的二叉树，被称为满二叉树。

**完全二叉树**：一棵二叉树中，只有最下面两层结点的度可以小于2，并且最下一层的叶结点集中在靠左的若干位置上。

**二叉查找树(BST)**：又被称为二叉搜索树。设x为二叉查找树中的一个结点，x节点包含关键字key，节点x的key值记为key[x]。如果y是x的左子树中的一个结点，则key[y] <= key[x]；如果y是x的右子树的一个结点，则key[y] >= key[x]。

### 巧用性质类题目

有时候遇到二叉树的问题，需要巧用一些性质来解决。

#### 例：二叉树的完全性检验

> 题目来源：Leetcode 958. 

给定一个二叉树，确定它是否是一个完全二叉树。

**题目解析**：

假如我们在深度为 3 的行有 4 个节点，位置为 0，1，2，3；那么就有 8 个深度为 4 的新节点位置在 0，1，2，3，4，5，6，7；所以我们可以找到规律：对于一个节点，它的左孩子为：`(depth, position) -> (depth + 1, position * 2)`，右孩子为 `(depth, position) -> (depth + 1, position * 2 + 1)`。所以，对于深度为 d 的行恰好含有$ 2^{d-1} $ 个节点，所有节点都是靠左边排列的当他们的位置编号是 0, 1, ... 且没有间隙。

> 因此我们可以给每个结点标注一个位置pos，最后判断最后一个结点的位置 是否 等于树的结点个数。

```cpp
class Solution {
public:
    struct node {
        TreeNode * Node;
        unsigned long long pos;
    };
    //记录数结点个数
    unsigned long long treeSize = 0;
    //记录最大位置
    unsigned long long maxPos = 0;
    bool isCompleteTree(TreeNode* root) {
        if(root == nullptr) return true;
        queue<node> q;
        q.push({root,1});
        treeSize++;
        //层次遍历
        while(!q.empty()){
            node tmp = q.front();
            //更新最大位置
            maxPos = max(maxPos,tmp.pos);
            q.pop();
            if(tmp.Node->left){
                q.push({tmp.Node->left,tmp.pos*2});
                treeSize++;
            }
            if(tmp.Node->right){
                q.push({tmp.Node->right,tmp.pos*2+1});
                treeSize++;
            }
        }
        return maxPos == treeSize;
    }
};
```



**二叉树常见遍历方式**：

前序遍历：`根+左子树+右子树`

中序遍历：` 左子树+根+右子树`

后序遍历：` 左子树+右子树+根`

> 当然还有层次遍历[BFS] (后续会讲)、欧拉环游遍历等等，具体就不介绍了，有需要的可以了解下。

二叉树欧拉环游遍历简单介绍：https://blog.csdn.net/qq_42642142/article/details/107292639 

**根据前序遍历和中序遍历 构造二叉树**

只要我们在中序遍历中定位到根节点，那么我们就可以分别知道左子树和右子树中的节点数目。由于同一颗子树的前序遍历和中序遍历的长度显然是相同的，因此我们就可以对应到前序遍历的结果中，对上述形式中的所有左右括号进行定位。

```cpp
class Solution {
public:
    map<int,int> m;//哈希表 存储中序遍历中每个结点的 值-下标
    TreeNode * BuildTree_Recursive(vector<int> & preorder,vector<int>&inorder,int pre_left,int pre_right,int in_left,int in_right){
        if(pre_left > pre_right){
            return nullptr;
        }
        //得到根节点的值
        int pre_root = preorder[pre_left];
        //根据根结点的值获取中序遍历中根节点下标
        int in_root_index = m[pre_root];
        //左子树结点个数
        int left_count = in_root_index - in_left;
        //创建根节点
        TreeNode * root = new TreeNode(pre_root);
        //递归创建左子树
        root->left = BuildTree_Recursive(preorder,inorder,pre_left+1,pre_left+left_count,in_left,in_root_index-1);
        //递归创建右子树
        root->right = BuildTree_Recursive(preorder,inorder,pre_left+left_count+1,pre_right,in_root_index+1,in_right);
        return root;
    }
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        int n = preorder.size();
        //通过哈希表记录中序序列中每个结点的下标
        for(int i = 0;i < n;i++){
            m.insert(make_pair(inorder[i],i));
        }
        return BuildTree_Recursive(preorder,inorder,0,n-1,0,n-1);
    }
};
```

**根据后序遍历和中序遍历 构造二叉树**

> 其实根据后序和中序构造二叉树 基本思想是和 根据前序和中序构造二叉树一样的。

序遍历的数组最后一个元素代表的即为根节点。知道这个性质后，我们可以利用已知的根节点信息在中序遍历的数组中找到根节点所在的下标，然后根据其将中序遍历的数组分成左右两部分，左边部分即左子树，右边部分为右子树，针对每个部分可以用同样的方法继续递归下去构造。

```cpp
class Solution {
public:
    map<int,int> m;
    TreeNode *buildTree_recursive(vector<int> &inorder,vector<int> &postorder,int in_left,int in_right,int post_left,int post_right){
        //左边界大于有边界，说明已经没有了，则返回null
        if(in_left > in_right) return nullptr;
        //通过后序遍历获得根节点的值
        int in_root = postorder[post_right];
        //或许根节点在中序遍历中的索引下标
        int in_root_index = m[in_root];
        //通过中序遍历及根节点的索引下标计算左子树的结点个数
        int left_count =in_root_index - in_left;
        //创建一个根结点
        TreeNode * root = new TreeNode(in_root);
        //递归创建 中序中左子树范围left - root-1，后序中左子树范围 left - left+cnt-1
        root->left = buildTree_recursive(inorder,postorder,in_left,in_root_index-1,post_left,post_left+left_count-1);
        //递归创建 中序中右子树范围root + 1 - right，后序中左子树范围 left+cnt - right
        root->right = buildTree_recursive(inorder,postorder,in_root_index+1,in_right,post_left+left_count,post_right-1);
        return root;
    }
    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
        int n = inorder.size();
        //初始化哈希表 创建中序遍历中 值与索引的映射
        for(int i = 0;i < n;i++){
            m.insert(make_pair(inorder[i],i));
        }
        return buildTree_recursive(inorder,postorder,0,n-1,0,n-1);
    }
};
```

**根据 前序遍历 和 后序遍历 来构造二叉树**

根据前序遍历和后序遍历的结构可知，假设如果最终的二叉树可以被序列化的表述为 [1, 2, 3, 4, 5, 6, 7]，那么其前序遍历为 [1] + [2, 4, 5] + [3, 6, 7]，而后序遍历为 [4, 5, 2] + [6, 7, 3] + [1].

如果我们知道左分支有多少个结点，我们就可以对这些数组进行分组，并用递归生成树的每个分支。

我们令左分支有 L个节点。我们知道左分支的头节点为 pre[1]，但它也出现在左分支的后序表示的最后。所以 `pre[1] = post[L-1]`（因为结点的值具有唯一性），因此` L = post.indexOf(pre[1]) + 1`。

现在在我们的递归步骤中，左分支由 `pre[1 : L+1] `和 `post[0 : L] `重新分支，而右分支将由 `pre[L+1 : N]` 和 `post[L : N-1]` 重新分支。

```cpp
class Solution {
public:
    TreeNode* constructFromPrePost(vector<int>& preorder, vector<int>& postorder) {
        int n = preorder.size();
        //空 直接返回空指针
        if(n == 0) return nullptr;
        //新建一个结点作为根节点 前序遍历的第一个结点为根结点
        TreeNode * root = new TreeNode(preorder[0]);
        if(n == 1) return root;
        
        int L = 0;
        //遍历 后续序列 找到当前根结点左子树中根节点preorder[1]的下标
        for(int i = 0;i < n;i++){
            if(postorder[i] == preorder[1]){
                L = i + 1;//得到的L就是左子树中结点的个数
                break;
            }
        }
        vector<int> tmppre;
        vector<int> tmppost;
        //划分左子树和右子树，进行递归操作
        //注意 assign 是将[first,last)中的元素拷贝成为一个新的vector
        tmppre.assign(preorder.begin()+1,preorder.begin()+1+L);//前序中左子树的范围
        tmppost.assign(postorder.begin(),postorder.begin()+L);//后序中左子树的范围
        root->left = constructFromPrePost(tmppre,tmppost);//递归创建

        tmppre.assign(preorder.begin()+1+L,preorder.end());//前序中右子树的范围
        tmppost.assign(postorder.begin()+L,postorder.end()-1);//后序中右子树的范围
        root->right = constructFromPrePost(tmppre,tmppost);//递归创建

        return root;
    }
};
```

上述程序可以优化，即将循环搜索提前进行一次遍历，然后将后序遍历的 值-索引 存储起来，如下：

```cpp
class Solution {
public:
    map<int,int> m;//哈希表
    TreeNode * buildTree(vector<int>& preorder, vector<int>& postorder,int pre_left,int pre_right,int post_left,int post_right){
        if(pre_left > pre_right) return nullptr;//没有结点了，返回空指针
        //创建根节点
        TreeNode * root = new TreeNode(preorder[pre_left]);
        //如果 左边界 == 右边界，说明只有一个结点了，直接返回
        if(pre_left == pre_right) return root;
        //计算左子树的结点个数
        int L = m[preorder[pre_left+1]] - post_left + 1;
        //递归创建 方法类似
        root->left = buildTree(preorder,postorder,pre_left+1,pre_left+L,post_left,post_left+L-1);
        root->right = buildTree(preorder,postorder,pre_left+L+1,pre_right,post_left+L,post_right-1);
        return root;
    }
    TreeNode* constructFromPrePost(vector<int>& preorder, vector<int>& postorder) {
        int n = postorder.size();
        //初始化哈希表，存储后序遍历的 值-索引
        for(int i = 0;i < n;i++){
            m.insert(make_pair(postorder[i],i));
        }
        //创建树 递归
        return buildTree(preorder,postorder,0,n-1,0,n-1);
    }
};
```

> 总结：无论通过先序、中序、后序中的哪两个序列创建二叉树，递归创建方法的核心思想就是要找到左子树的个数，这样一来右子树结点的个数也可以通过根节点来确定，然后进行递归创建。

### 二叉树路径问题

二叉数常见的问题是路径问题，对于二叉树路径的问题大致可以分为两类：

- 自顶向下：就是从某一个节点(不一定是根节点)，从上向下寻找路径，到某一个节点(不一定是叶节点)结束
- 非自顶向下：从任意节点到任意节点的路径，不需要自顶向下,也就是说可能是路径中包含`兄弟结点等其他亲戚结点`的路径

### 自顶向下

注意点：

1. 是否要回溯?

   二叉树的问题大部分是不需要回溯的，原因如下：二叉树的递归部分：dfs(root->left),dfs(root->right)已经把可能的路径穷尽了,因此到任意叶节点的路径只可能有一条，绝对不可能出现另外的路径也到这个满足条件的叶节点的;

2. 找到路径后是否要return?
   取决于题目是否要求找到叶节点满足条件的路径,如果必须到叶节点,那么就要return;
   但如果是到任意节点都可以，那么必不能return,因为这条路径下面还可能有更深的路径满足条件，还要在此基础上继续递归

3. 是否要双重递归?(即调用根节点的dfs函数后，继续调用根左右节点的pathsum函数)

   看题目要不要求从根节点开始的，还是从任意节点开始

**模板框架**：

```cpp
//一般路径：
vector<vector<int>>res;
void dfs(TreeNode*root,vector<int>path) //注意此处path没有引用哦，因此每次调用函数，修改的都是path的拷贝
{
    //根节点为空直接返回
    if(!root) return;
    //作出选择 将该结点
    path.push_back(root->val);
    //如果到叶节点 
    if(!root->left && !root->right)  
    {
        //添加路径
        res.push_back(path);
        return;
    }
    //继续递归
    dfs(root->left,path);  
    dfs(root->right,path);
}

// **给定和的路径：**
void dfs(TreeNode*root, int sum, vector<int> path)
{
    //空结点 退出
    if (!root)
        return;
    //记录当前的和
    sum -= root->val;
    //加入该结点
    path.push_back(root->val);
    //符合条件，保存该结果
    if (!root->left && !root->right && sum == 0)
    {
        res.push_back(path);
        return;
    }
    //继续递归
    dfs(root->left, sum, path);
    dfs(root->right, sum, path);
}
```



### 非自顶向下

这类题目一般解题思路为：
设计一个maxPath函数，调用自身求出以一个节点为根节点的左侧最长路径left和右侧最长路径right，那么经过该节点的最长路径就是left+right接着只需要从根节点开始dfs,不断比较更新全局变量即可。

**模板框架**：

```cpp
int res=0;
int maxPath(TreeNode *root) //以root为路径起始点的最长路径
{
    if (!root)
        return 0;
    //左子树的最长路径
    int left=maxPath(root->left);
    //右子树的最长路径
    int right=maxPath(root->right);
    //更新全局变量 
    res = max(res, left + right + root->val);
    //返回左右路径较长者
    return max(left, right);   
}
```

#### 例：最长同值路径

> Leetcode 687. 

给定一个二叉树，找到最长的路径，这个路径中的每个节点具有相同值。 这条路径可以经过也可以不经过根节点。

**注意**：两个节点之间的路径长度由它们之间的边数表示。

```
输入:
              1
             / \
            4   5
           / \   \
          4   4   5
输出:
2
```

```cpp
//套用非自顶向下的方法模板
class Solution {
public:
    //全局结果
    int ans = 0;
    //递归函数：以root结点为根节点的相同的值结点的路径长度为多少
    int dfs(TreeNode * root){
        //如果空 返回0
        if(root == nullptr) return 0;
        //递归左子树
        int left = dfs(root->left);
        //如果左子树存在且其值等于根结点值，则其左子树路径长度+1
        if(root->left && root->left->val == root->val){
            left++;
        }else{//否则之间为0
            left = 0;
        }
        //递归右子树
        int right = dfs(root->right);
        //如果右子树存在且其值等于根结点值，则其右子树路径长度+1
        if(root->right && root->right->val == root->val){
            right++;
        }else{//否则之间为0
            right =0;
        }
        //更新结果，左右子树长度之和
        ans = max(ans,left+right);
        //返回两者之间大的哪个
        return max(left,right);
    }
    int longestUnivaluePath(TreeNode* root) {
        if(root == nullptr) return 0;
        dfs(root);
        return ans;
    }
};
```



#### 例：二叉树剪枝

> 题目来源：leetcode 814. 

给你二叉树的根结点 root ，此外树的每个结点的值要么是 0 ，要么是 1 。返回移除了所有不包含 1 的子树的原二叉树。

节点 node 的子树为 node 本身加上所有 node 的后代。

![img](F:\Typora\LeetCode\pic\1028_1.png)

```
输入：root = [1,0,1,0,0,0,1]
输出：[1,null,1,null,1]
```

```cpp
class Solution {
public:
    //递归函数：判断以root为根结点的子树是否包含1
    bool hasOne(TreeNode *root){
        //如果为空结点 直接返回false
        if(root == nullptr) return false;
        //递归左右子树
        bool lefthas = hasOne(root->left);
        bool righthas = hasOne(root->right);
        //如果左子树没有1,直接删除
        if(!lefthas){
            root->left = nullptr;
        }
        //如果右子树没有1,直接删除
        if(!righthas){
            root->right = nullptr;
        }
        //如果当前结点为1直接返回true
        if(root->val == 1) return true;
        //左右子树如果都没false，否则true
        return lefthas || righthas;
    }

    TreeNode* pruneTree(TreeNode* root) {
        if(root == nullptr) return nullptr;
        if(root->val == 0 && !root->left && !root->right) return nullptr;
        if(!hasOne(root)) return nullptr;
        return root;
    }
};
```

### 二叉树另类问题

我把二叉树另类问题解释为在进行二叉树遍历的过程中，可能需要记录一些其他的信息，来方便进行解题。常见的就是哈希表、数组等数据结果来存储临时信息。

这类题比较巧，当你想了想采用不了上述的一些二叉树方法解决时，可以考虑是否能牺牲些内存空间来帮助你记录些有用的信息。

#### 例：二叉树中所有距离为 K 的结点

> 来源：leetcode 863. 

给定一个二叉树（具有根结点 root）， 一个目标结点 target ，和一个整数值 K 。

返回到目标结点 target 距离为 K 的所有结点的值的列表。 答案可以以任何顺序返回。

```
输入：root = [3,5,1,6,2,0,8,null,null,7,4], target = 5, K = 2
输出：[7,4,1]
```

![img](F:\Typora\LeetCode\pic\sketch0.png)



```
提示：
1.给定的树是非空的。
2.树上的每个结点都具有唯一的值 0 <= node.val <= 500 。
```

**解题分析**：

如果我们从target结点出发dfs进行递归搜索，只能找到其子树下相距为k的结点，找不到距离为k的祖先结点。===> 因此 我们就可以想**能否通过一个数据结果记录每个结点的父亲结点，这样我们就可以递归找到对应的祖先结点的解**

可以通过哈希表来记录父亲结点信息，其中key为当前结点的值,val为祖先结点的指针。为此，我们从根结点root 出发，使用深度优先搜索遍历整棵树，同时用一个哈希表记录每个结点的父结点。

然后从 target 出发，使用深度优先搜索遍历整棵树，除了搜索左右儿子外，还可以顺着父结点向上搜索。

代码实现时，由于每个结点值都是唯一的，哈希表的键可以用结点值代替。**此外，为避免在深度优先搜索时重复访问结点，递归时额外传入来源结点 from，在递归前比较目标结点是否与来源结点相同，不同的情况下才进行递归**。这句话开始可能不好理解，但是你把递归图画一下就理解了。如果对图的dfs了解比较清楚的同学那么肯定一下就想明白了，因为**此时遍历这颗树就像遍历一个有向图**。

```cpp
class Solution {
public:
    vector<int> ans;
    map<int,TreeNode *> m; //key:node val:father
    void buildMap(TreeNode * root){
        if(root->left != NULL){
            //建立映射
            m.insert({root->left->val,root});
            //继续递归
            buildMap(root->left);
        }
        if(root->right != NULL){
            //建立映射
            m.insert({root->right->val,root});
            //继续递归
            buildMap(root->right);
        }
    }
    void findTarget(TreeNode * node,TreeNode * from,int floor,int k){
        if(node == NULL){
            return ;
        }
        //找到了 存入结果
        if(floor == k){
            ans.push_back(node->val);
            return ;
        }
        //好好理解一下下面的内容，不然可能会进入死循环
        //如果当前结点的左节点是上次递归进来的结点，则跳过
        if(node->left != from){
            findTarget(node->left,node,floor+1,k);    
        }
        //如果当前结点的右节点是上次递归进来的结点，则跳过
        if(node->right != from){
            findTarget(node->right,node,floor+1,k);  
        }
        //如果当前结点的父亲节点是上次递归进来的结点，则跳过
        if(m[node->val] != from){
            findTarget(m[node->val],node,floor+1,k);
        }
        
    }
    vector<int> distanceK(TreeNode* root, TreeNode* target, int k) {
        buildMap(root);
        findTarget(target,NULL,0,k);
        return ans;
    }
};
```

#### **例：具有所有最深节点的最小子树**

> 题目来源：Leetcode 865.

给定一个根为 root 的二叉树，每个节点的深度是 该节点到根的最短距离 。

如果一个节点在 整个树 的任意节点之间具有最大的深度，则该节点是 最深的 。

一个节点的 子树 是该节点加上它的所有后代的集合。

返回能满足 以该节点为根的子树中包含所有最深的节点 这一条件的具有最大深度的节点。

> 提示：每个结点的值都是独一无二的

```
输入：root = [3,5,1,6,2,0,8,null,null,7,4]
输出：[2,7,4]
```

![img](F:\Typora\LeetCode\pic\sketch1.png)

**题目解析**：

首先我们需要知道哪个结点是深度最大的结点，因此需要进行一次`递归遍历`，在递归遍历过程中记录每个结点的`深度信息`及`整棵树的最大深度`，可以将其对应存放在`哈希表`及`一个变量中`，然后我们再进行一次`递归遍历`[深度优先搜索]，返回`拥有所有的最深节点的子树根节点`

定义第二次深度优先搜索方法为 getMaxDepthSubTree(node)，每次递归有以下四种情况需要处理：

- 如果 node 为空，返回 null。
- 如果node为最深结点，返回node
- 如果 node 左右子树的后代中都有最深节点，返回 node。
- 如果只有左子树或右子树中有且拥有所有的最深节点，返回 node 的左/右孩子。
- 否则，当前子树中不存在答案,返回null

```cpp
class Solution {
public:
    int maxDepth = -1;
    TreeNode * ans;
    //记录所有结点深度的哈希表
    map<TreeNode *,int> m;
    //记录最大结点深度的哈希表
    map<TreeNode *,int> maxDepthNode;
    //第一次深度优先搜索，记录结点深度
    void dfs(TreeNode * root,int depth){
        if(root == nullptr) return ;
        //存储信息
        m.insert(make_pair(root,depth));
        //更新最大深度
        maxDepth = max(maxDepth,depth);
        //递归遍历
        dfs(root->left,depth+1);
        dfs(root->right,depth+1);
    }
    TreeNode * getMaxDepthSubTree(TreeNode * root){
        if(root == nullptr) return nullptr;
        //如果当前结点是最深结点，直接返回该结点
        if(maxDepthNode.find(root) != maxDepthNode.end()){
            return root;
        }
        //递归
        TreeNode * left = getMaxDepthSubTree(root->left);
        TreeNode * right = getMaxDepthSubTree(root->right);
        //如果左右子树中都有最深结点，返回当前结点
        if(left && right){
            return root;
        }else if(left && !right){//否则其中之一是，则返回对应的结点
            return left;
        }else if(!left && right){
            return right;
        }else{//否则返回null
            return nullptr;
        }
    }
    TreeNode* subtreeWithAllDeepest(TreeNode* root) {
        if(root == nullptr) return nullptr;
        //获取所有结点深度
        dfs(root,0);
        //找最大深度结点
        for(auto iter = m.begin(); iter != m.end();iter++){
            if(iter->second == maxDepth){
                maxDepthNode.insert(make_pair(iter->first,iter->second));
            }
        }
        //求取拥有所有的最深节点的子树根节点
        return getMaxDepthSubTree(root);
    }
};
```





# 二分法

**二分法求方程的近似解**：

![1](F:\Typora\LeetCode\pic\1.png)

```c
#define EPS 0.0000001
double f(int p,int q,double x){
    return p * x + q;
}
double bisection(int p,int q,double (*func)(int,int,double)){
    double left = -20,right = 20;
    double mid = (left + right) / 2;
    do{
        if(fabs(func(p,q,mid)) <= EPS){
            return mid;   
        }else if(f(p,q,mid) > EPS){
            if(p > 0) right = mid;
            else if(p < 0) left = mid;
        }
        else{
            if(p > 0) left = mid;
            else if(p < 0) right = mid;
        }
        mid = (left + right) / 2;
    }while(fabs(func(p,q,mid)) > EPS);
    return mid;    
}
```

## 二分查找基本框架

> 二分查找算法思想不难！难的是算法细节！

```c
bool  binSearch(int * arr,int n,int tar){
    int left = 0,right = n-1;
    //什么时候是 < 什么时候是 <=要考虑清除
    while(left <= right){
        //求中值mid优化 右移运算代替除法运算，运算更快，相减代替相加防止溢出 int mid = (left + right) / 2;
        int mid = left + ((right - left)>>1);
        //重点
        if(arr[mid] == tar) return true;
        else if(arr[mid] > tar) right = mid - 1;
        else left = mid + 1;
    }
    return false;
}
```

## 二分查找变形

### 查找第一个值等于给定值的元素

其实这只是对基本二分查找的变形：

```cpp
//**查找第一个值等于给定值的元素**
int bsearchFirst(vector<int> & nums,int val){
    int n = nums.size();    
    int left = 0;
    int right = n - 1;
    while (left <= right)
    {
        int mid = left + ((right - left)>>1);
        if (nums[mid] < val){
            left = mid + 1;
        }else if (nums[mid] > val){
            right = mid - 1;
        }else {
            if (mid == 0 || nums[mid - 1] != val)
                return mid;
            else
                right = mid - 1;
        }
    }
    return -1;
}
```

> 如果我们查找的是任意一个值等于给定值的元素，当 `nums[mid]`等于要查找的值时，`nums[mid]`就是我们要找的元素。但是，如果我们求解的是第一个值等于给定值的元素，当 `nums[mid]`等于要查找的值时，我们就需要确认一下这个 `nums[mid]`是不是第一个值等于给定值的元素。

>  我们重点看`if (mid == 0 || nums[mid - 1] != val)`行代码。如果 `mid` 等于 0，那这个元素已经是数组的第一个元素，那它肯定是我们要找的；如果 `mid` 不等于 0，但 `nums[mid]`的前一个元素 `nums[mid-1]`不等于 `value`，那也说明 `nums[mid]`就是我们要找的第一个值等于给定值的元素。

> 如果经过检查之后发现 `nums[mid]`前面的一个元素 `nums[mid-1]`也等于 `value`，那说明此时的 `nums[mid]`肯定不是我们要查找的第一个值等于给定值的元素。那我们就更新 `high=mid-1`，因为要找的元素肯定出现在`[low, mid-1]`之间。

### 查找最后一个值等于给定值的元素

同样的，查找最后一个值等于给定值的元素也和上述思路相同。

```cpp
//**查找最后一个值等于给定值的元素**
int bsearchLast(vector<int> & nums,int val){
    int n = nums.size();
    int left = 0;
    int right = n - 1;
    while (left <= right)
    {
        int mid = left+((right - left) >> 1);
        if (nums[mid] < val){
            left = mid + 1;
        }else if (nums[mid] > val){
            right = mid - 1;
        }else{
            if (mid == n - 1 || nums[mid + 1] != val){
                return mid;
            }else{
                left = mid + 1;
            }
        }
    }
    return -1;
}
```

### 查找第一个大于等于给定值的元素

```cpp
//**查找第一个大于等于给定值的元素**
int bsearchOverFirst(vector<int> &nums,int val){
    int n = nums.size();
    int left = 0;
    int right = n - 1;
    while (left <= right){
        int mid = left + ((right - left)>>1);
        if (nums[mid] >= val){
            if (mid == 0 || (nums[mid - 1] < val)){
                return mid;    
            }
            right = mid - 1;
        }else{
            left = mid + 1;
        }
    }
    return -1;
}
```

### 查找最后一个小于等于给定值的元素

```cpp
//**查找最后一个小于等于给定值的元素**
int bsearchOverLast(vector<int> & nums,int val){
    int n = nums.size();
    int left = 0;
    int right = n - 1;
    while (left <= right){
        int mid = left + ((right - left)>>1);
        if (nums[mid] <= val){
            if (mid == n-1 || nums[mid + 1] > val){
                return mid;
            }
            left = mid + 1;
        }else{
            right = mid - 1;
        }
    }
    return -1;
}
```

## 二分"结果"题型

要点：

- 是否单调？

- 上下界范围？
- “分”什么？

为什么叫二分结果？因为**分的是结果**

### 示例——**原木切割**

```
某林业局现在 N 根原木，长度分别为 Xi，为了便于运输，需要将他们切割成长度相等的 M 根小段原木（只能切割成整数长度，可以有剩余），小段原木的长度越大越好，现求小段原木的最大长度。例如，有 3 根原木长度分别为 6,15,22，现在需要切成 8 段，那么最大长度为 5。
输入
​ 第一行两个整数 N,M。（1≤N≤100,000，1≤M≤100,000,000）
​ 接下来 N 行，每行一个数，表示原木的长度 Xi。（1≤Xi≤100,000,000）
输出
​ 输出小段原木的最大长度， 保证可以切出 M 段。
```

<img src="F:\Typora\Picture\leetcode\原木切割题解.jpg" alt="原木切割题解" style="zoom: 50%;" />

```cpp
//程序代码——原木切割
#include<iostream>
#include<algorithm>
#include<stdio.h>
using namespace std;
int n,m,num[100005],r,l = 1;
//根据切割距离x求取段数t
int func(int x){
    int t = 0;
    for(int i = 1;i <= n;i++){
        t += num[i] / x;
    }
    return t;
}
//二分查找
int bs(void){
    while(l != r){
        int mid = ((long long)l + r + 1 ) /2;//防止出现死循环
        int t = func(mid);
        if(t >= m){
            l = mid;
        }else{
            r = mid - 1;
        }
    }
    return r; //此时r == l，返回哪个都行
}
int main(){
    scanf("%d %d",&n,&m);
    for(int i = 1;i <= n;i++){
        scanf("%d",&num[i]);
        r = max(r,num[i]); //更新最右边界
    }
    printf("%d\n",bs());
    return 0;
}
```

```
小密招：
左绿右红
	mid = (l + r + 1) / 2;
	l = mid;
	r = mid - 1;
左红右绿
	mid = (l + r) / 2
	r = mid;
	l = mid + 1;
```



### 示例——**切绳子**

```
 有 N 条绳子，它们的长度分别为 Li。如果从它们中切割出 K 条长度相同的绳子，这 K 条绳子每条最长能有多长？答案保留到小数点后 2 位(直接舍掉 2 位后的小数)。
输入
​ 第一行两个整数 N 和 K，接下来 N 行，描述了每条绳子的长度 Li。
输出
​ 切割后每条绳子的最大长度，保证答案大于零。
```

注意：当数据类型为浮点型时，while判断内容变了！

```cpp
#include<iostream>
#include<algorithm>
using namespace std;
int n,k;
double num[10005],maxr;
//切割长度为m时可以切t条绳子
int func(double m){
    int t = 0;
    for(int i = 0;i < n;i++){
        t += num[i] / m;//浮点型转int整形，直接去掉了小数点后数字
    }
    return t;
}
//二分答案
double bs(void){
    double l = 0,r = maxr;
    while(r - l > 0.0001){
        double mid = (l + r)/2;//虽然为左绿有红，但因为判断改变，不用+1
        int t = func(mid);
        if(t >= k){
            l = mid;//注意 不需要+1
        }else{
            r = mid;//注意 不需要-1
        }
    }
    return r;
}
int main(){
    scanf("%d %d",&n,&k);
    for(int i = 0;i < n;i++){
        scanf("%lf",&num[i]);
        maxr = max(maxr,num[i]);//更新右边界
    }
    printf("%.2f\n",bs() - 0.005f);
    return 0;
}
```

## 伪有序二分

> 这个==伪有序==二分是我自己起的名字，可能不太形象，但是我们通常会遇到一些题型比如【畸形的有序】，依然可以采用二分方法解决

### 在旋转排序数组中搜索

```
整数数组 nums 按升序排列，数组中的值 互不相同 。

在传递给函数之前，nums 在预先未知的某个下标 k（0 <= k < nums.length）上进行了 旋转，使数组变为 [nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]（下标 从 0 开始 计数）。
例如， [0,1,2,4,5,6,7] 在下标 3 处经旋转后可能变为 [4,5,6,7,0,1,2] 。

给你 旋转后 的数组 nums 和一个整数 target ，如果 nums 中存在这个目标值 target ，则返回它的下标，否则返回 -1 。
```

```
输入：nums = [4,5,6,7,0,1,2], target = 0
输出：4
```

> 在前面的二分题目中，重点就是找到目标值和当前`num[mid]`的==相对位置==，以此来判断向左还是向右搜索，此题的重点也是这样，只是条件可能复杂些。

![旋转排序数组中搜索](F:\Typora\Picture\leetcode\旋转排序数组中搜索.jpg)

```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int n = nums.size();
        int left = 0,right = n - 1;
        while(left <= right){
            int mid = left + ((right - left) >> 1);
            //找到答案，直接返回
            if(target == nums[mid]) return mid;
            if(nums[mid] >= nums[0]){//nums[mid]在左半区间
                //开始判断target在哪
                if(target < nums[0])//目标值在右半区间
                    left = mid + 1;
                else{//目标值在左半区间，此时要判断和nums[mid]的相对位置
                    if(target < nums[mid]) right = mid - 1;
                    else left = mid + 1;
                }
            }else{//nums[mid]在右半区间
                if(target >= nums[0])//目标值在左半区间
                    right = mid - 1;
                else{//目标值在右半区间，要判断和nums[mid]的相对位置
                    if(target < nums[mid]) right = mid - 1;
                    else left = mid + 1;
                }
            }
        }
        return -1;
    }
};
```

### 寻找旋转排序数组中的最小值

```
已知一个长度为 n 的数组，预先按照升序排列，经由 1 到 n 次 旋转 后，得到输入数组。例如，原数组 nums = [0,1,2,4,5,6,7] 在变化后可能得到：
若旋转 4 次，则可以得到 [4,5,6,7,0,1,2]
若旋转 7 次，则可以得到 [0,1,2,4,5,6,7]
```

> 给你一个元素值 `互不相同` 的数组 `nums` ，它原来是一个升序排列的数组，并按上述情形进行了多次旋转。请你找出并返回数组中的 最小元素 。

```
输入：nums = [3,4,5,1,2]
输出：1
```

> 本题和上一题"在旋转排序数组中搜索"很相似，都是伪有序的搜索，在搜素过程中需要判断当前处于的区域，并要清楚查找结束条件

<img src="F:\Typora\Picture\leetcode\寻找旋转排序数组中的最小值.jpg" alt="寻找旋转排序数组中的最小值" style="zoom: 67%;" />

```cpp
class Solution {
public:
    int findMin(vector<int> &nums){
        int n = nums.size();
        //如果是单调递增的，直接返回第一个值
        if(nums[0] < nums[n-1]) return nums[0];
        int left = 0,right = n-1;
        while(left < right){
            int mid = left + ((right - left) >> 1);
            //mid在左半区域，一定不是最小值
            if(nums[mid] >= nums[0]){
                left = mid + 1;
            }else{//在右半区域
                if(mid > 0 && nums[mid] < nums[mid-1]) return nums[mid];
                right = mid - 1;
            }
        }
        return nums[left];
    }
};
```

### 寻找旋转排序数组中的最小值II

```
已知一个长度为 n 的数组，预先按照升序排列，经由 1 到 n 次 旋转 后，得到输入数组。例如，原数组 nums = [0,1,4,4,5,6,7] 在变化后可能得到：
```

> 给你一个可能存在 `重复` 元素值的数组 `nums` ，它原来是一个升序排列的数组，并按上述情形进行了多次旋转。请你找出并返回数组中的 最小元素 。

> 当存在重复元素后，就不好明确区分是在哪个区间了，但是我们依旧要缩小查找范围，如何缩小？这便是要解决的重要问题之一

![寻找旋转排序数组中的最小值II](F:\Typora\Picture\leetcode\寻找旋转排序数组中的最小值II.jpg)

```cpp
class Solution {
public:
    int findMin(vector<int> &nums){
        int n = nums.size();
        int left = 0,right = n-1;
        while (left < right){
            int mid = left + ((right - left) >> 1);
            //如果在右半部分区域
            if (nums[mid] < nums[right]){
                //注意下面的两种判断方法都可以，但是要清楚为什么要这样
                #if 0    
                    if(mid > 0 && nums[mid] < nums[mid-1]) return nums[mid];
                    right = mid - 1;
                #else
                    right = mid;
                #endif
            }else if (nums[mid] > nums[right])//左半部分区域
            {
                left = mid + 1;
            }else{//将nums[right]排除在下次查找区域
                right--;
            }
        }
        return nums[left];
    }
};
```



# 滑动窗口

> 一般都是基于字符串和数组的,需要控制前后指针的移动来控制窗口，这样的移动是有条件的，也就是要想清楚在什么情况下移动，在什么情况下保持不变。
>
> - 通常是移动了右指针后，窗口内的条件被打破，不符合目标条件，此时需要移动左指针使窗口内的条件依旧成立。

> 以下是之前看到别人的一个模板，感觉不错，贴出来。

```cpp
int slidingWindowTemplate(string a, ...) {
    // 输入参数有效性判断
    if (...) {
        ...
    }
 
    // 申请一个散列，用于记录窗口中具体元素的个数情况
    // 这里用数组的形式呈现，也可以考虑其他数据结构
    int[] hash = new int[...];
 
    // 预处理(可省), 一般情况是改变 hash
    ...
 
    // l 表示左指针
    // count 记录当前的条件，具体根据题目要求来定义
    // result 用来存放结果
    int l = 0, count = ..., result = ...;
    for (int r = 0; r < A.length; ++r) {
        // 更新新元素在散列中的数量
        hash[A[r]]--;
 
        // 根据窗口的变更结果来改变条件值
        if (hash[A[r]] == ...) {
            count++;
        }
 
        // 如果当前条件不满足，移动左指针直至条件满足为止
        while (count > K || ...) {
            ...
            if (...) {
                count--;
            }
            hash[A[l]]++;
            l++;
        }
 
        // 更新结果
        results = ...
    }
 
    return results;
}
```

### 长度最小的子数组

```
给定一个含有 n 个正整数的数组和一个正整数 target 。

找出该数组中满足其和 ≥ target 的长度最小的 连续子数组 [numsl, numsl+1, ..., numsr-1, numsr] ，并返回其长度。如果不存在符合条件的子数组，返回 0 。
```

```
输入：target = 7, nums = [2,3,1,2,4,3]
输出：2
```

> 题解：我们维护一个可变长的滑动窗口，并且`要求`窗口内元素和sum小于`target`，当将窗口向右移动至不符合`要求`[即窗口内元素和sum≥target]，此时开始将窗口缩小[即左边界向有边界],直到符合`要求`，此时计算窗口长度并更新结果ans。

```cpp
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int n = nums.size();
        int left = 0,right = 0;
        int winSum = 0,ans = INT_MAX;
        while(right < n){
            winSum += nums[right];
            while(winSum >= target){
                ans = min(right - left + 1,ans);
                winSum -= nums[left++];
            }
            right++;
        }
        return ans == INT_MAX ? 0 : ans;
    }
};
```

### 找到字符串中所有字母异位词

```
给定两个字符串 s 和 p，找到 s 中所有 p 的 异位词 的子串，返回这些子串的起始索引。不考虑答案输出的顺序。
异位词 指字母相同，但排列不同的字符串。
```

```
输入: s = "cbaebabacd", p = "abc"
输出: [0,6]
```

> 我们维护一个大小等于p串长度的窗口，并将其从左向右滑动，依次匹配窗口中元素的信息和p串信息是否相同。
>
> - 滑动即：右指针和左指针均右移一个单位，并更新窗口内元素信息

```cpp
class Solution {
public:
    int p_cnt[26] = {0};
    int s_cnt[26] = {0};

    bool compare(void){
        for(int i = 0;i < 26;i++){
            if(p_cnt[i] != s_cnt[i]) return false;
        }
        return true;
    }

    vector<int> findAnagrams(string s, string p) {
        int np = p.length();
        int ns = s.length();
        //p串长度大于s串，直接返回{}
        if(np > ns) return vector<int> {};
        //初始化字符串p内字母的个数
        for(int i = 0;i < np;i++){
            p_cnt[p[i] - 'a']++;
        }
        vector<int> ans;
        //初始化左、右指针
        int left = 0,right = 0;
        while(right <= ns){
            //如果当前串口小于指定值，右移右指针，并更新窗口内元素信息
            if(right - left <= np-1){
                s_cnt[s[right++] - 'a']++;
                continue;
            }
            //如果匹配到，加入答案
            if(compare()){
                ans.push_back(left);
            }
            if(right == ns) break;
            //右吞左吐
            s_cnt[s[right++] - 'a']++;
            s_cnt[s[left++] - 'a']--;
        }
        return ans;
    }
};
```

### 乘积小于K的子数组

```
给定一个正整数数组 nums和整数 k 。

请找出该数组内乘积小于 k 的连续的子数组的个数。
```

```
输入: nums = [10,5,2,6], k = 100
输出: 8
解释: 8个乘积小于100的子数组分别为: [10], [5], [2], [6], [10,5], [5,2], [2,6], [5,2,6]。
```

<img src="F:\Typora\Picture\leetcode\乘机小于K的子数组.jpg" alt="乘机小于K的子数组" style="zoom:67%;" />

<img src="F:\Typora\Picture\leetcode\乘机小于K的子数组2.jpg" alt="乘机小于K的子数组2" style="zoom:67%;" />

```cpp
class Solution {
public:
    int numSubarrayProductLessThanK(vector<int>& nums, int k) {
        if(k <= 1) return 0;
        int n = nums.size();
        int left = 0,right = 0,count = 0;
        unsigned long long mul = 1;
        while(right < n){
            mul = mul * nums[right];
            while(mul >= k){
                mul /= nums[left++];
            }
            count += right - left + 1;//本题的精髓
            right++;
        }
        return count;
    }
};
```





# 双指针

> 双指针类型的题目有三种：
>
> - 对撞指针[左右两指针向中间缩小]
>   - 要处理排序数组（或链接列表）并需要查找满足某些约束的一组元素的问题
> - 滑块指针[类似窗口长度动态变化的滑动窗口算法]  右"吞"左"吐"
>   - 查找最长/最短的子字符串、子数组或所需的值
>   - 解决一些查找满足一定条件的连续区间 或 长度 的问题
> - 快慢指针[常用于链表]
>   - 判断链表是否有环
>   - 删除链表倒数的第几个元素

## 对撞指针

### **独木舟**

（贪心+双指针）

```
题目描述
​ 一群人去旅行，要租用独木舟，每艘独木舟最多乘两人，且所有独木舟有一个统一的载重限度。给出独木舟的载重限度和每个人的体重，现求最少需要租用多少独木舟。
输入
​ 第一行一个整数 w，表示独木舟的载重量。（80≤w≤200）
​ 第二行一个整数 n，表示旅游人数。 （1≤n≤30000）
​ 接下来 n 行，每行一个数表示 ai，即每个人的重量 （5≤ai≤w）
输出
​ 输出一个数表示最少需要的独木舟数量。
```

> 独木舟的载重量是==约束==

重量大的尽可能和能够一起坐船的人里面较轻的去匹配。

```cpp
#include<iostream>
#include<vector>
#include<algorithm>
using namespace std;

int w,n,ans = 0;

int main(){
    cin>>w>>n;
    vector<int> a(n);
    vector<bool> isOn(n,false);
    for(int i = 0;i < n;i++){
        cin>>a[i];
    }
    sort(a.begin(),a.end());
    int l = 0,r = n-1;
    while(l <= r){
        if(a[l] + a[r] <= w){ //如果两者重量和低于载重值，则匹配
            l++;
            r--;
            ans++;
        }else{//如果两者和大于载重值，则重量大的单独匹配
            r--;
            ans++;
        }
    }
    cout<<ans<<endl;
    return 0;
}
```

### 两数之和

```
给定一个已按照 升序排列  的整数数组 numbers ，请你从数组中找出两个数满足相加之和等于目标数 target 。
函数应该以长度为 2 的整数数组的形式返回这两个数的下标值。

numbers 的下标 从 1 开始计数 ，所以答案数组应当满足 1 <= answer[0] < answer[1] <= numbers.length 。
你可以假设每个输入只对应唯一的答案，而且你不可以重复使用相同的元素。
```

> 约束: `a + b = target`

```
输入：numbers = [2,7,11,15], target = 9
输出：[1,2]
解释：2 与 7 之和等于目标数 9 。因此 index1 = 1, index2 = 2 。
```

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
        int n = numbers.size();
        int left = 0,right = n-1;
        while(left < right){
            if(target == numbers[left] + numbers[right]){
                return {left+1,right+1};
            }else if(target > numbers[left] + numbers[right]) left++;
            else right--;
        }
        return {};
    }
};
```

### 三数之和

```
给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有和为 0 且不重复的三元组。
输入：nums = [-1,0,1,2,-1,-4]
输出：[[-1,-1,2],[-1,0,1]]
```

> 注意：题目要求找出的答案是==不重复的==。比如[-1,0,1,2,-1,-4]
>
> - 重复类型1：顺序改变，但内容一样，即(-1,0,1)和(0,-1,1)是重复的
> - 重复类型2：设下标为0和4的 -1分为为-1和-1'，那么(-1,0,1)和(-1',0,1)也是重复的
>
> 对于重复类型1：我们可以自己==规定自己找到的三元组答案(a,b,c)是非降序的==，即a≤b≤c，也就可以排除重复的元素。因此，我们需要首先进行排序，这样遍历数组得到的答案都是非降序的，也就保证了==答案不重复==。
>
> 对于重复类型2：当我们将数组排序过后，假设得到了[-4,-1,-1',0,1,2]，假设当前得到了结果(-1,0,1)，然后下一次对于a将会到达-1'，此时我们需要跳过。
>
> 为什么会要想到要用==双指针==？
>
> 首先我们肯定是要分别遍历数组，来标定a,b,c的值然后判断其是否符合a+b+c = 0。对于已经排序的数组，当我们获得了一组数(a,b,c)符合a+b+c=0时，对于b下一个值b'一定符合b≤b',为了依旧符合a+b'+c'=0，那么c'一定符合c'≤c，因此对于非降序的数组，b是从左向右遍历[升序]，c是从右向左[降序]，这便联想到了双指针

```cpp
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        int n = nums.size();
        //对数组进行排序
        sort(nums.begin(),nums.end());
        vector<vector<int>> ans;
        //固定a
        for(int a = 0;a < n;a++){
            //如果a重复，直接跳过
            if(a > 0 && nums[a] == nums[a-1]) continue;
            int target = -nums[a];
            //初始化b和c的边界
            int b = a + 1,c = n-1;
            while(b < c){
                //如果b也重复上一次，那么直接跳过
                if(b > a + 1 && nums[b] == nums[b-1]){
                    b++;
                    continue;
                }
                //如果符合条件，加入结果；不符合则变化双指针
                if(nums[b] + nums[c] ==  target) {
                    ans.push_back({nums[a],nums[b],nums[c]});
                    b++;
                }else if(nums[b] + nums[c] < target){
                    b++;
                }else{
                    c--;
                }
            }            
        }
        return ans;
    }
};
```

### 盛水最多的容器

```
给你 n 个非负整数 a1，a2，...，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0) 。
找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。
```

```
输入：[1,8,6,2,5,4,8,3,7]
输出：49 
```

> Area = min(height[left],height[right]) * (right - left)

![盛水最多的容器](F:\Typora\Picture\leetcode\盛水最多的容器.jpg)

```cpp
class Solution {
public:
    int maxArea(vector<int>& height) {
        int n = height.size();
        int left = 0,right = n-1;
        int ans = -1;
        while(left < right
              //获取当前面积
            int area = min(height[left],height[right]) * (right - left);
              //更新最大值
            ans = max(area,ans);
              //选择排除哪一个木棒
            height[left] <= height[right] ? left++ : right--;
        }
        return ans;
    }
};
```



## 滑块指针

> 与滑动窗口的思想差不多一样

## 快慢指针





# 递归

### 图形打印

```
题目描述
​ 当 n 为 1 时，图形如下图：
X
​ 当 n 为 2 时，图形如下图：
X X
 X 
X X
​ 当 n≥2 时，图形规律如下：
图形n-1    图形n-1
    图形n-1
图形n-1    图形n-1
​ 给定 n 组数据，输出每组数据对应的图形。

输入
​ 输入共 n+1 行，前 n行每行一个数，表示要输出的图形的大小，最后一行输入 −1 代表程序结束。（1≤n≤7）
输出
​ 每输入一个数输出一组图形，并在图形后的下一行输出一个 −。
​ 注意，图形后应补齐空格。
```

当遇到这种有规律，大图形可以由小图形组成的图形打印题目，往往可以采用递归解决：

<img src="F:\Typora\Picture\leetcode\图形打印.jpg" alt="图形打印" style="zoom: 67%;" />

```cpp
#include<iostream>
using namespace std;

int n;
int num[10] = {0,1,3,9,27,81,243,729};
char map[2000][2000];
void func(int x,int y,int n){
    //如果是最小图形，直接在(x,y)打印
    if(n == 1){
        map[x][y] = 'X';
        return ;
    }
    //左上角打印
    func(x,y,n-1);
    //右上角打印
    func(x,y+num[n]/3 * 2,n-1);
    //左下角打印
    func(x+num[n]/3 * 2,y,n-1);
    //中心打印
    func(x+num[n]/3,y+num[n]/3,n-1);
    //右下角打印
    func(x+num[n]/3*2,y+num[n]/3*2,n-1);
}

int main(){
    func(1,1,7);
    while(cin >> n){
        if(n == -1) break;
        for(int i = 1;i <= num[n];i++){
            for(int j = 1;j <= num[n];j++){
                if(map[i][j] == 'X'){
                    cout<<'X';
                }else{
                    cout<<' ';
                }
            }
            cout<<endl;
        }
        cout<<"-"<<endl;
    }
    return 0;
}

```



### 递归实现指数型枚举

```
题目描述
​ 从 1−n 这 n 个整数中随机选取任意多个，每种方案里的数从小到大排列，按字典序输出所有可能的选择方案。

输入
​ 输入一个整数 n。（1≤n≤10）
输出
​ 每行一组方案，每组方案中两个数之间用空格分隔。
​ 注意每行最后一个数后没有空格。

样例输入
3
样例输出
1
1 2
1 2 3
1 3
2
2 3
3
```

```cpp
#include<iostream>
using namespace std;
int num[15];
int n;
//打印
void print(int ind){
    for(int j = 0;j <= ind;j++){
        if(j != 0) cout<<" ";
        cout<<num[j];
    }
    cout<<endl;
}
//递归函数
void func(int s,int ind){
    for(int i = s;i <= n;i++){
        num[ind] = i;
        //从起始打印至下标ind
 	  print(ind);
        func(i+1,ind + 1);
    }
}
int main(){
    cin>>n;
    func(1,0);
    return 0;
}
```



### 递归实现组合型枚举

```
题目描述
​ 从 1−n 这 n 个整数中随机选取 m 个，每种方案里的数从小到大排列，按字典序输出所有可能的选择方案。
输入
​ 输入两个整数 n,m。（1≤m≤n≤10）
输出
​ 每行一组方案，每组方案中两个数之间用空格分隔。
​ 注意每行最后一个数后没有空格。

样例输入2
5 3
样例输出2
1 2 3
1 2 4
1 2 5
1 3 4
1 3 5
1 4 5
2 3 4
2 3 5
2 4 5
3 4 5
```

```cpp
#include<iostream>
using namespace std;
int n,m;
int num[15];
void func(int start,int left){
    //递归结束
    if(left == 0){
        for(int i = 0;i < m;i++){
            if(i != 0){
                cout<<" ";
            }
            cout<<num[i];
        }
        cout<<endl;
        return ;
    }
    // i 最多到达 n - left + 1
    for(int i = start;i <= n-left+1;i++){
        num[m - left] = i;
        //进行下一层递归
        func(i + 1,left - 1);
    }
}

int main(){
    cin>>n>>m;
    func(1,m);
    return 0;
}
```



### 递归实现排列型枚举

```
题目描述
​ 从 1−n 这 n 个整数排成一排并打乱次序，按字典序输出所有可能的选择方案。
输入
​ 输入一个整数 n。（1≤n≤8）
输出
​ 每行一组方案，每组方案中两个数之间用空格分隔。
​ 注意每行最后一个数后没有空格。

样例输入
3
样例输出
1 2 3
1 3 2
2 1 3
2 3 1
3 1 2
3 2 1
```

```cpp
#include<iostream>
using namespace std;
int n;
int num[10],mark[10] = {0};

void func(int ind){
    //遍历的层数到达第n层了，结束递归
    if(ind == n){
        for(int i = 0;i < n;i++){
            if(i != 0) cout<<" ";
            cout<<num[i];
        }
        cout<<endl;
        return ;
    }
    //遍历1~n，判断有没有被使用，没有被使用的话使用并标记
    for(int i = 1;i <= n;i++){
        //如果 i 没有被使用
        if(mark[i] == 0){
            //先标记
            mark[i] = 1;
            //存储值
            num[ind] = i;
            //进行下一层递归
            func(ind + 1);
            //解除标记
            mark[i] = 0;
        }
    }
}

int main(){
    cin>>n;
    func(0);
    return 0;
}
```

> 排列组合应用：
>
> - 上述的排序组合均是 数字 => 数组下标，可以转化为
>   - 在5名同学中选出2名同学，递归得到的是数组下标，但是可以映射至其他数组[比如：存储同学名字的数组]
> - 排列组合问题 相当于 深度优先搜索问题 的一种

# 记忆化数组优化递归

记忆化数组用来存储中间结果   递归+记忆化≈递推



# 动态规划

有一说一，这个东西是真的难，不过我觉得通过 思考+刷题 是可以解决大部分的DP的。加油！打工人!

> 刷题怎么刷呢？也是从简单=>中等=>苦难吗？本人的路线是：先去找一个简单的动态规划类型题(比如去Leetcode刷)=>写完后查看与该题类似的题目=>然后根据这道题的经验看能否应用到类似的DP问题中. 这样循序渐进的同时也让你掌握了一系列问题的解决套路。

> 动态规划是一种将原始问题分解成多个规模较小的子问题，而各个子问题之间相互联系，最终由子问题的解合并成原始问题解的算法

动态规划问题的一般形式就是来==求最值==，因此当遇到求最大值/最小值等问题时，可以考虑一手是不是要用动态规划，此时你就要考虑该题符不符合其==四要素==了，不符合的还是另想办法！

动态规划算法四要素：

- 最优子结构

  > 当一个问题的最优解必然包含其子问题的最优解时，称该问题具有最优子结构。

  > 子问题：对于一个问题而言，只有规模比该问题小、其他均与该问题一致的问题才可称为其子问题，同时，子问题不会对规模等大的问题造成影响[**无后效性**]

- 重叠子问题

  > 当求取子问题解的时候，往往会出现许多**重复计算**。而动态规划是先解决小的问题，当后面再次出现该子问题时，可以直接求解

- 状态与状态转移方程

  > 动态规划是一种**自底向上**解决问题的思想，“状态”就是子问题的解，”状态转移方程“就是子问题之间的递推关系[说白了就是递推公式]

- 边界条件

  > 边界条件就是程序的停止条件。

```
递归算法通常是自顶向下构造函数，而动态规划为自底向上构造程序结构的算法思想。简单来说，动态就是先解决小问题，然后大问题的解可以由小问题的解直接转换求得。
```

动态规划最难的部分就是写出`动态转移方程`，这里有几点需要关心：

1. 最小的子问题的情况是什么?
2. 一个问题有哪些状态？
3. 对于每个当前的状态，可以选择什么？
4. 如何通过dp数组来表示你的"状态和选择"



**常见问题类型**：

- xxx的方式有几种/组合问题

- 最大/最小值等问题

  > 上面这两类问题难度一般较高，会仔细讲

- 明显递推方式的问题$,  f(x) 由 f(x-1)得来$

**常见优化**：

- 空间上原来二维数组采用滚动数组变为一维
- 原来的一维采用O(1)的变量代替



### **最大/最小值类型**

#### 例：打家劫舍

你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。
给定一个代表每个房屋存放金额的非负整数数组，计算你 不触动警报装置的情况下 ，一夜之内能够偷窃到的最高金额。

```
输入：[1,2,3,1]
输出：4
解释：偷窃 1 号房屋 (金额 = 1) ，然后偷窃 3 号房屋 (金额 = 3)。
     	偷窃到的最高金额 = 1 + 3 = 4 。
```

下面给了两种解题方案，体会一下状态转移的重要性。

```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        int n = nums.size();
        //dp[i][0]:上一个i-1没偷，当前偷之后的最多金额
        //dp[i][1]:上一个i-1没偷，当前也没偷的最多金额
        //dp[i][2]:上一个i-1偷了，当前不偷的最多金额
        vector<vector<int>> dp(n,vector<int> (3,0));
        dp[0][0] = nums[0];
        dp[0][1] = 0;
        dp[0][2] = 0;
        for(int i=1;i<n;i++){
            //上一个没偷：那么上一个状态只可能是dp[i-1][1]或者dp[i-1][2].同时当前偷了，需要加上当前金额，取两者最大值
            dp[i][0] = max(dp[i-1][1],dp[i-1][2]) + nums[i];
            //上一个没偷：那么上一个状态只可能是dp[i-1][1]或者dp[i-1][2]，当前也不偷，取上一个状态两者最大值
            dp[i][1] = max(dp[i-1][1],dp[i-1][2]);
            //上一个偷了，当前状态即为上一次偷之后的状态，上一次只有可能dp[i-1]0]偷了
            dp[i][2] = dp[i-1][0];
        }
        //返回3者可能最大值
        return max(max(dp[n-1][0],dp[n-1][1]),dp[n-1][2]);
    }
};
```

空间优化：

```cpp
//空间优化
class Solution {
public:
    int rob(vector<int>& nums) {
        int n = nums.size();
        //dp[0]:上一个没偷，当前偷之后的最多金额
        //dp[1]:上一个没偷，当前也没偷的最多金额
        //dp[2]:上一个偷了，当前不偷的最多金额
        vector<int> dp(3,0);
        dp[0] = nums[0];
        dp[1] = 0;
        dp[2] = 0;
        for(int i=1;i<n;i++){
            //暂存dp[2];
            int tdp2 = dp[2];
            //上一个偷了，当前状态即为上一次偷之后的状态，上一次只有可能dp[0]偷了
            dp[2] = dp[0];
            //上一个没偷：那么上一个状态只可能是dp[1]或者dp[2].同时当前偷了，需要加上当前金额，取两者最大值
            dp[0] = max(dp[1],tdp2) + nums[i];
            //上一个没偷：那么上一个状态只可能是dp[1]或者dp[2]，当前也不偷，取上一个状态两者最大值
            dp[1] = max(dp[1],tdp2);

        }
        //返回3者可能最大值
        return max(max(dp[0],dp[1]),dp[2]);
    }
};
```

不要急，我们再看一下另外一个代码，这个输入比较常见的动态规划的框架类型。

我们定义dp表的意义为dp[i]]表示对于`nums[0]~nums[i]的房屋所能偷的最大金额`，`那么对于第0~i的房屋所能偷的最大金额取决于0~i-1的房屋偷的金额(此时第i个屋子就不能偷了)和0~i-2的房屋的最大值+第i个房屋的金额这两者的较大值。`

```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        if (nums.empty()) {
            return 0;
        }
        int size = nums.size();
        //只有一间房则肯定偷
        if (size == 1) {
            return nums[0];
        }
        //创建dp表 dp[i]表示对于nums[0]~nums[i]的房屋所能偷的最大金额
        vector<int> dp = vector<int>(size, 0);
        //dp表的初始情况初始化
        dp[0] = nums[0];
        dp[1] = max(nums[0], nums[1]);
        for (int i = 2; i < size; i++) {
            dp[i] = max(dp[i - 2] + nums[i], dp[i - 1]);
        }
        return dp[size - 1];
    }
};
```

> 我们可以看出，dp表的定义方式不同，我们解题的框架就不太相同，但是总体思想都是通过先前状态来推导当前的最优状态。
>
> 其中，第二种方法为比较常见的dp表定义方式

#### 例：打家劫舍II

你是一个专业的小偷，计划偷窃沿街的房屋，每间房内都藏有一定的现金。这个地方**所有的房屋都 围成一圈** ，这意味着**第一个房屋和最后一个房屋是紧挨着的**。同时，相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警 。

给定一个代表每个房屋存放金额的非负整数数组，计算你 在不触动警报装置的情况下 ，今晚能够偷窃到的最高金额。

> 题目是对原本的升级版，其实就是将原本的数组修改为了循环数组，解决方案和之前一样，只是我们需要遍历两次。分别求`0~n-2范围内房屋的可以偷到金额最大值`和`0~n-1范围内房屋的可以偷到金额最大值`，两者取较大值即可.

```cpp
class Solution {
public:
    /*此部分将原本的dp表进行了压缩，因为dp[i]只和dp[i-1]和dp[i-2]有关*/
    int getmaxRobval(int start,int end,vector<int> & nums){
        int first = nums[start],second = max(nums[start],nums[start+1]);
        //不太理解的就把first看作dp[i-2],second看作dp[i-1]
        for(int i = start + 2;i <= end;i++){
            int temp = second;//暂存second,因为接下来要修改second
            second = max(nums[i]+first,second);
            first = temp;
        }
        return second;
    }
    
    int rob(vector<int>& nums) {
        int n = nums.size();
        if(n == 1)return nums[0];//如果只有一个房屋直接偷
        if(n == 2)return max(nums[0],nums[1]);//两个房屋偷贵的
        //因为首尾相连，因此需要两次判断，并取较大值。
        return max(getmaxRobval(0,n-2,nums),getmaxRobval(1,n-1,nums));
    }
};
```

#### 例：打家劫舍III

在上次打劫完一条街道之后和一圈房屋后，小偷又发现了一个新的可行窃的地区。这个地区只有一个入口，我们称之为“根”。 除了“根”之外，每栋房子有且只有一个“父“房子与之相连。一番侦察之后，聪明的小偷意识到“这个地方的所有房屋的排列类似于一棵二叉树”。 如果两个直接相连的房子在同一天晚上被打劫，房屋将自动报警。计算在不触动警报的情况下，小偷一晚能够盗取的最高金额。

```
输入: [3,2,3,null,3,null,1]

     3
    / \
   2   3
    \   \ 
     3   1

输出: 7 
解释: 小偷一晚能够盗取的最高金额 = 3 + 3 + 1 = 7.
```

刚看这道题还是比较懵的，但是后来想想就是跟前面的思想一样的，只是此时创建数组形式的dp表不太现实了，因此改为哈希表来作为dp表。

思考，对于结点x，以x为根的树能够被偷的最大金额有两种**状态**：`结点x被偷`和`结点x不被偷`。

对于**选择**，当`结点x被偷`时，那么两个子节点都不能被偷，当`结点x不被偷`时，两个子节点可以被偷，也可以不被偷，取两者较大的值。

**此时，一个思路就慢慢浮现...**

我们可以用 selectMap(x) 表示偷 x 节点的情况下，x 节点的子树上被偷的节点的最大权值和；noselectMap(x) 表示不选择 x 节点的情况下，x 节点的子树上被选择的节点的最大权值和；l 和 r 代表 x 的左右孩子。

- 当 x 被选中时，x 的左右孩子都不能被选中，故 x 被选中情况下子树上被选中点的最大权值和为 l 和 r 不被选中的最大权值和相加，即 $selectMap(x) = noselectMap(l)+noselectMap(r)$。
- 当 x 不被选中时，x 的左右孩子可以被选中，也可以不被选中。对于 x 的某个具体的孩子 x，它对 x 的贡献是 x 被选中和不被选中情况下权值和的较大值。故 $noselectMap(x)=max\{selectMap(l),noselectMap(l)\}+max\{selectMap(r),noselectMap(r)\}$

至此，我们可以用哈希表`map<Treenode *,int>`来存 selectMap和 noselectMap 的函数值，用深度优先搜索的办法后序遍历这棵二叉树，我们就可以得到每一个节点的 selectMap和 noselectMap。

```cpp
class Solution {
public:
    map<TreeNode *,int> selectMap;
    map<TreeNode *,int> noselectMap;
    void dfs(TreeNode *root){
        if(!root) return;
        dfs(root->left);
        dfs(root->right);
        selectMap.insert(make_pair(root,root->val+noselectMap[root->left]+noselectMap[root->right]));
        noselectMap.insert(make_pair(root,max(selectMap[root->left],noselectMap[root->left])+max(selectMap[root->right],noselectMap[root->right])));
        
    }
    int rob(TreeNode* root){
        dfs(root);
        return max(selectMap[root],noselectMap[root]);
    }
};
```

#### 例：删除并获得点数

给你一个整数数组`nums`，你可以对它进行一些操作。每次操作中，选择任意一个 nums[i] ，删除它并获得 nums[i] 的点数。之后，你必须删除 所有 等于 nums[i] - 1 和 nums[i] + 1 的元素。

开始你拥有 0 个点数。返回你能通过这些操作获得的最大点数。

```
输入：nums = [2,2,3,3,3,4]
输出：9
```

通过理解题目后，我们发现 不如我们挑选了值为 k 的元素，那么 k-1 和 k-2 的元素我就不能挑选了（也就是 相邻大小的值的元素 不能被选择）如果你做过`打家劫舍`那么你会突然意识到，这不就类似打家劫舍问题，偷其中一家，则相邻的就不能偷了。两者的原理是一样的，只不过 打家劫舍 问题是选择索引下标 （即，限制的是下标），而此题限制的是 值。下标是唯一的，但是值可能有很多重复的，因此，我们在进行选择前需要提前统计 值大小为 k 的元素的和 sum 是多少，并进行记录，我们可以用一个数组来进行存储，sums[k] = x 表示 `值大小为 k 的元素的和是 x`，之后我们就可以采用与打家劫舍相似的方法来处理 sums 数组。

```cpp
class Solution {
public:

    int rob(vector<int> &sums){
        int n = sums.size();
        if(n == 1) return sums[0];
        int first = sums[0],second = max(sums[0],sums[1]);
        for(int i = 2;i < n;i += 1){
            int tmp = second;
            second = max(first + sums[i],second);
            first = tmp;
        }
        return max(first,second);
    }
    int deleteAndEarn(vector<int>& nums) {
        int n = nums.size();
        vector<int> sums(10000+5,0);
        for(int i = 0;i < n;i++){
            sums[nums[i]] += nums[i];
        }
        return rob(sums);
    }
};
```

> 由此，我们可以看出，有些问题其实本质是相似的，只是需要再解决问题的时候进行一些逻辑转换，而这，也是比较难的，非常考验解题者的逻辑能力。



#### 例：股票买卖

给定一个数组 prices ，它的第 i 个元素 prices[i] 表示一支给定股票第 i 天的价格。

你只能选择 某一天 买入这只股票，并选择在 未来的某一个不同的日子 卖出该股票。设计一个算法来计算你所能获取的最大利润。

返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 0 。

```
输入：[7,1,5,3,6,4]
输出：5
```

我们还是看看**选择**先，对于每一天，可以选择买入、卖出、不买不卖三种选择。但是呢，题目具有一些限制条件，即**只能买卖一次股票**。

接下来就是定义dp表含义，我们定义`dp[i][0]`为在**第i天手上没有股票时的收益**，`dp[i][1]`为在**第i天手上持有股票时的收益**。考虑一下它可以由哪些状态转移过来呢？

显然`dp[i][0]`可以由**前一天没有股票今天也不买**和**前一天手持股票今天卖出**两种方式转移而来，即$dp[i][0] = max(dp[i-1][0],dp[i-1][1]+prices[i])$

对于`dp[i][1]`可以由前一天持有股票今天不卖和前一天没有股票今天买入两种方式转移，即$dp[i][1] = max(dp[i-1][1],-prices[i])$，这里为什么不是`dp[i-1][0]-prices[i]`而是`-prices[i]`呢？因为有一个限制条件：**只能买卖一次股票**。因此买股票时不能从前一天没有股票的状态转移而来，因为可能很早以前已经买过并卖出了。

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        n = len(prices)
        if n == 0: return 0
        #dp[i][0]：至第i天，手里没有股票时的收益
        #dp[i][1]：至第i天，手里有股票时的收益
        dp = [[0 for i in range(2)] for j in range(n)]
        dp[0][0],dp[0][1] = 0,-prices[0]
        for i in range(1,n):
            dp[i][0] = max(dp[i-1][0],dp[i-1][1] + prices[i])
            dp[i][1] = max(dp[i-1][1],-prices[i])
        return dp[n-1][0]
```

这里贴一个python版的代码，希望大家能够在刷题的时候能多掌握几种语言，因为每种语言都有各自的优势，有的语言可以借助本身提供的数据结构来进行求解，会很方便。这里推荐python+Cpp两种方式刷题，当然java也是不错的选择。

#### 例：股票买卖II

给定一个数组 prices ，其中 prices[i] 是一支给定股票第 i 天的价格。

计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。

> 注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

```
输入: prices = [7,1,5,3,6,4]
输出: 7
```

此题相较于上一题多了一个条件就是可以多次买卖股票，不过基本思想还是一样，我们还是按照上题创建dp表，只是在动态转移方程处需要进行修改。原本只能买卖一次，因此$dp[i][1] = max(dp[i-1][1],-prices[i])$，而此时可以多次买卖，因此可以由上一个没有股票时的状态转移而来，因此此时$dp[i][1] = max(dp[i-1][1],dp[i-1][0]-prices[i])$

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        /*
        dp[i][0]:到第i天手上没有股票时所获得最大利润
        dp[i][1]:到第i天手上有股票时所获得最大利润
        */
        vector<vector<int>> dp(n,vector<int> (2,0));
        dp[0][0] = 0;
        dp[0][1] = -prices[0];
        for(int i = 1;i < n;i++){
            dp[i][0] = max(dp[i-1][0],dp[i-1][1] + prices[i]);
            dp[i][1] = max(dp[i-1][1],dp[i-1][0] - prices[i]);
        }
        return dp[n-1][0];
    }
};
```

> 可以看到，其实大体思路是不变的，改变的只是题目的限制条件



#### 例：股票买卖III

给定一个数组，它的第 i 个元素是一支给定的股票在第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你最多可以完成 两笔 交易。

> 注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

```
输入：prices = [3,3,5,0,0,3,1,4]
输出：6
```

此题在上一题的基础上更新了限制条件，就是最多只能买卖两次股票。我们还是先考虑**选择**和**状态**，选择还是哪几个选择`保留、卖出、买入`，而状态变了，对于第i天，有5种状态：`一张没买、买了一张、卖了一张此时什么没有股票、买了第二张、卖了第二张股票`，我们还根据前面的两题的想法写出状态转移方程。但是我们要注意，`一张没买`的状态只能从`前一天一张没买的状态`进行转移。因此其值一直为0，我们可以将其省略，对于剩余4种状态，我们分别定义dp表将其对应。

-  dp`[i][0]`:在第i天时，手上持有一张股票时的最大利益
- dp`[i][1]`:在第i天时，手上没有股票，并已经卖出了一次时的最大利益
- dp`[i][2]`:在第i天时，手上持有第二次买入的股票时的最大利益
- dp`[i][3]`:在第i天时，手上没有股票，已经完成两次卖出时的最大利益

则可以分别写出动态转移方程：

$dp[i][0] = max(dp[i-1][0],0 - prices[i])，此处的0 就是前一天一张没买的状态$

$dp[i][1] = max(dp[i-1][1],dp[i-1][0] + prices[i])$

$dp[i][2] = max(dp[i-1][2],dp[i-1][1] - prices[i])$

$dp[i][3] = max(dp[i-1][3],dp[i-1][2] + prices[i])$

最后的结果肯定就是最后一天两张都卖出的状态，即`dp[n-1][3]`

> 当然，因为第i天的状态只和第i-1天有关，因此可以进行状态压缩，减少空间复杂度如:  
>
> ```cpp
> buy1 = max(buy1,-prices[i]);
> sell1 = max(sell1,buy1+prices[i]);
> buy2 = max(buy2,sell1-prices[i]);
> sell2 = max(sell2,buy2+prices[i]);
> ```

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        n = len(prices)
        dp = [ [0 for i in range(4)] for j in range(n) ]
        '''
        dp[i][0]:在第i天时，手上持有一张股票时的最大利益
        dp[i][1]:在第i天时，手上没有股票，并已经卖出了一次时的最大利益
        dp[i][2]:在第i天时，手上持有第二次买入的股票时的最大利益
        dp[i][3]:在第i天时，手上没有股票，已经完成两次卖出时的最大利益
        '''
        dp[0][0],dp[0][1],dp[0][2],dp[0][3] = -prices[0],0,-prices[0],0
        for i in range(1,n):
            dp[i][0] = max(dp[i-1][0],0 - prices[i])
            dp[i][1] = max(dp[i-1][1],dp[i-1][0] + prices[i])
            dp[i][2] = max(dp[i-1][2],dp[i-1][1] - prices[i])
            dp[i][3] = max(dp[i-1][3],dp[i-1][2] + prices[i])
        return dp[n-1][3]
```

> 记录一下：当我做到这里时，我发现好像如果能够用动态规划首先要了解清除状态和选择，因为这是你构建状态转移方程的源泉之一。

#### 例：股票买卖IV

给定一个整数数组 prices ，它的第 i 个元素 prices[i] 是一支给定的股票在第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你最多可以完成 k 笔交易。

```
输入：k = 2, prices = [3,2,6,5,0,3]
输出：7
```

题目又得到了升级...，此时由原来的2次变为了k次，此时，如果你是一路认真做过来的，那么你应该会想到不就是把k=2的情况转换为一个k遍历，原理还是一样的。确实，原理还是一样的，只不过有许多细节需要注意。不管什么时候，你都要清除你的dp表的含义是什么？你的动态转移方程应该怎么来求。好的，我们来看一下。

我们定义`buys[i][j]表示第i天已经买卖了j次股票并且当前有股票时的最大收益`，定义`sells[i][j]:表示第i天已经买卖了j次股票并且当前没有股票时的最大收益`。当然你也可以定义成`dp[2][i][j]将buys和sells合并`，这里是为了方便讲解分开定义。

那么我们根据前几道股票买卖问题可以比较容易的推导出状态转移方程：

$sells[i][j] = max(sells[i-1][j],buys[i-1][j-1] + prices[i])$，$buys[i][j] = max(buys[i-1][j],sells[i-1][j] - prices[i])$

这里有个小细节需要注意`sells[i][j]是由buys[i-1][j-1] + prices[i]转移而来而不是buys[i-1][j]`，因为`buys[i-1][j-1]`表示前一天(i-1)交易了 j-1 次并且当前还有股票，将其在第 i 天交易，就得到了`sells[i][j]`，表示在第 i 天交易了 j 次并且没有股票。

另外，还有一些**注意事项**：

- 就是我们在第0天的时候，只有可能 也 最多可能交易0次，因此$sells[0][k]和buys[0][k],k\in[1,n)$都应该是一个无效的值，因此我们将其设置为一个极小值(因为我们是求极大值，因此其不会对结果造成影响)。
- 因为`sells`的转移方程为：$sells[i][j] = max(sells[i-1][j],buys[i-1][j-1] + prices[i])$，状态转移涉及到`buys[i-1][j-1]`，因此j肯定不可能从0开始遍历，**会造成数组访问越界**。但是我们又需要它！因此在每一天的开始时，我们需要对`buys[i][0]`初始化（`buys[0][0]`在最开始初始化了)
- 最后我们返回的是`sells[n-1][i]中的最大值`。

```cpp
class Solution:
    def maxProfit(self, k: int, prices: List[int]) -> int:
        if not prices: return 0
        n = len(prices)
        '''
        buys[i][j]:表示第i天已经买卖了j次股票并且当前有股票时的最大收益
        sells[i][j]:表示第i天已经买卖了j次股票并且当前没有股票时的最大收益
        '''
        buys = [[ 0 for i in range(k+1)] for j in range(n)]
        sells = [[0 for i in range(k+1)] for j in range(n)]
        buys[0][0] = -prices[0] #第0天买卖了0次并且当前有股票时受益为-prices[0]
        sells[0][0] = 0         #第0天买卖了0次并且当前没有股票时受益为0
        #第0天不可能交易任何股票，因此buys[0][i] (0,k] 都设置为一个无穷小数表示无效
        for i in range(1,k+1):
            buys[0][i] = sells[0][i] = float("-inf")
        for i in range(1,n): # 1 ~ n-1
            #因为sells[i][j]的动态转移需要用到buys[i-1][j-1]，而j是从1~k，需要对buys[i][0]进行计算
            buys[i][0] = max(buys[i-1][0],sells[i-1][0]-prices[i])
            for j in range(1,k+1): # 1 ~ k
                sells[i][j] = max(sells[i-1][j],buys[i-1][j-1] + prices[i])
                buys[i][j] = max(buys[i-1][j],sells[i-1][j] - prices[i])
        #print(sells[n-1])
        return max(sells[n-1])
```

这些基本上就是股票问题基本的解决方法了，有的时候题目还有添加什么`交易手续费`等等额外条件，其实就是对`动态转移方程`进行一些条件限制和修改而已。同时，对于`股票买卖IV`，还有一种`基于Wqs二分`的方法：https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iv/solution/yi-chong-ji-yu-wqs-er-fen-de-you-xiu-zuo-x36r/

#### 例：最大连续子数组的和

给定一个整数数组 `nums` ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

```
输入：nums = [-2,1,-3,4,-1,2,1,-5,4]
输出：6
```

由题可知，所求的是连续子数组且其和最大。经过之前的训练，我们可以定义`dp[i]表示以nums[i]结尾的子数组所具有的最大和`。

> 有时候你可能不能一下想出比较好的dp定义方式，这个时候就要尝试定义，切记不要害怕去尝试！

我们为什么会想到这样的dp表定义方式呢？我们首先看题目：要求的是连续子数组。同时呢，又要求是最大和，便想到选择是：`对于一个nums[i]，加入或者不加入目前的最大连续子数组`，同时`又要维护连续的要求`，那么其只能选择`加入以nums[i-1]结尾的子数组的后面`或者`重新另起一个子数组`。这样不就可以想到定义`dp[i]表示以nums[i]结尾的子数组所具有的最大和`

那么状态转移方程也就出来了：$dp[i] = max(dp[i-1]+nums[i],nums[i])$

即，要么选择将nums[i]加入以nums[i-1]结尾的子数组后，要么就是将nums[i]作为一个新的子数组

```python
#python 3
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        n = len(nums)
        dp = [0 for i in range(n)]
        dp[0] = nums[0]
        for i in range(1,n):
            dp[i] = max(dp[i-1]+nums[i],nums[i])
        return max(dp)
```



#### 例：乘积最大子数组

给你一个整数数组 `nums` ，请你找出数组中乘积最大的连续子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。

```
输入: [2,3,-2,4]
输出: 6
```

这题乍一看！好像跟上一道`最大连续子数组的和`，然后直接`定义dp[i]表示以nums[i]结尾的子数组所具有的最大乘积`并且一波操作写出状态转移方程$dp[i] = max(dp[i-1]*nums[i],nums[i])$，然后就提示**失败**，哈哈哈哈哈哈~

我们看一下为什么！如果经验老道的童鞋可能一眼就会看出 乘积 问题涉及的负数问题，即 负负得正 这一个坑。举个例子：

假设`nums = [3,-2,2,3,-2]`，显然最大连续子数组乘积是所有元素相乘为 72，但是按照我们上面定义的dp及状态转移方程，我们推导一下：`dp = [3,-2,2,6,-2]`，最后返回的是 6。为什么会这样呢？是因为`当前位置的最优解未必是由前一个位置的最优解转移得到的`。因为对于最后一个-2来说，它的最优子问题的解不是 6，而是 3 * -2 * 2 * 3 = -36，这是因为它本身是个负数，因此**期望有一个最小的数**和它相乘成为一个比较大的正数。===>因此我们在状态转移的时候就要考虑 符号的问题了 我们不仅需要知道`以nums[i]结尾的子数组所具有的最大乘积`还需要知道`以nums[i]结尾的子数组所具有的最小乘积`来进行状态转移。

考虑当前位置如果是**一个负数**的话，那么我们希望以它前一个位置结尾的某个段的积也是个负数，这样就可以负负得正，并且我们希望这个积**尽可能负得更多**，即尽可能小。如果当前位置是**一个正数**的话，我们更希望以它前一个位置结尾的某个段的积也是个正数，并且希望它**尽可能地大**。于是我们定义`dpMax[i]：以nums[i]结尾的子数组所具有的最大乘积`和`dpMin[i]：以nums[i]结尾的子数组所具有的最小乘积`

则状态转移方程：

$dpMax[i] = max(dpMax[i-1]*nums[i],dpMin[i-1]*nums[i],nums[i]);$

$dpMin[i] = min(dpMax[i-1]*nums[i],dpMin[i-1]*nums[i],nums[i]);$

最后返回的结果是`dpMax中的最大值`

```cpp
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        int n = nums.size();
        vector<int> dpMax(n,0),dpMin(n,0);
        dpMax[0] = dpMin[0] = nums[0];
        for(int i = 1;i < n;i++){
            dpMax[i] = max(max(dpMax[i-1]*nums[i],dpMin[i-1]*nums[i]),nums[i]);
            dpMin[i] = min(min(dpMax[i-1]*nums[i],dpMin[i-1]*nums[i]),nums[i]);
        }
        return *max_element(dpMax.begin(), dpMax.end());
    }
};
```



### **xxx的方式有几种**/组合问题

> 这类问题也比较多，一般属于简单和中等难度题目。

#### 例： 不同路径 

> 题目来源：Leetcode 63

一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为 “Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为 “Finish” ）。

问总共有多少条不同的路径？

```
输入：m = 3, n = 7
输出：28
```

题目知道，我们在每个点的**选择**可以有“向下”或者"向右"。对于此题来说，**状态**就是从左上角至`[i][j]`点有多少。因此，对于当前的状态，可以由其左边的和上边的点状态转移过来。我们定义`dp[i][j]`的意义为：从左上角`[0][0]`至`[i][j]`有多少种路径，那么我们可以很容易推出递推公式：
$$
dp[i][j] =
\begin{cases} 
1,  & \text{if } i==0 \text{||}j == 0 \\
dp[i-1][j]+dp[i][j-1, & \text{if }  i \geq 1 \text{&&} j\geq1
\end{cases}
$$

```cpp
class Solution {
public:
    int uniquePaths(int m,int n)
    {
        vector<vector<int>> res(m);
        for (int i = 0; i < m; i++){
            res[i].resize(n);
        }
        //初始化
        res[0][0] = 1;
        for (int i = 1; i < n; i++){
            res[0][i] = 1;
        }
        for (int i = 1; i < m; i++){
            res[i][0] = 1;
        }
        //根据状态转移方程更新dp表
        for (int row = 1; row < m; row++){
            for (int col = 1; col < n; col++){
                res[row][col] = res[row-1][col] + res[row][col-1];
            }
        }
        return res[m-1][n-1];
    }
};
```



#### 例： 不同路径 II

> 题目来源：Leetcode 63

一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为“Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。

现在考虑网格中有障碍物。那么从左上角到右下角将会有多少条不同的路径？

网格中的障碍物和空位置分别用 `1` 和 `0` 来表示。

```
输入：obstacleGrid = [[0,0,0],[0,1,0],[0,0,0]]
输出：2
```

此题在上一题只是加上了一些限制条件，有的地方加上了石块不能经过，那么我们就按照题目要求意思，如果obstacleGrid`[i][j]`  == 1，那么到达该点的路径有0条。其余考虑点还是和上题一样。

```cpp
class Solution {
public:
    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        int m = obstacleGrid.size();
        int n = obstacleGrid[0].size();
        vector<vector<int>> dp(m,vector<int> (n,0));
        for(int i = 0;i < m;i++){
            if(obstacleGrid[i][0] == 1)
                break;
            dp[i][0] = 1;
        }
        for(int i = 0;i < n;i++){
            if(obstacleGrid[0][i] == 1)
                break;
            dp[0][i] = 1;
        }
        for(int i = 1;i < m;i++){
            for(int j = 1;j < n;j++){
                if(obstacleGrid[i][j] == 1) continue;
                dp[i][j] = dp[i-1][j] + dp[i][j-1];
            }
        }
        return dp[m-1][n-1];
    }
};
```



### **明显递推方式的问题**

这类问题比较明显，通常递归就可以解决，但是递归会消耗大量的栈空间。通过动态规划方法解决也是不错的方法。

比如常见的斐波那契数列等等，这类问题比较简单，就不细讲了。举个简单的例子应付一下哈哈哈哈

#### 例:获取生成数组中的最大值

> 题目来源：Leetcode 1646. 

给你一个整数 n 。按下述规则生成一个长度为 n + 1 的数组 nums ：

- nums[0] = 0
- nums[1] = 1
- 当$ 2 <= 2 * i <= n $时，$nums[2 * i] = nums[i]$
- 当 $2 <= 2 * i + 1 <= n $时，$nums[2 * i + 1] = nums[i] + nums[i + 1]$

要求：返回生成数组 nums 中的 **最大** 值。

```cpp
class Solution {
public:
    int getMaximumGenerated(int n) {
        vector<int> nums(n+1);
        if(n == 0) return 0;
        if(n == 1) return 1;
        int ans = 1;
        nums[0] = 0;
        nums[1] = 1;
        for(int i = 1;i <= n / 2;i++){
            //根据题目给的递推关系进行递推。
            nums[i*2] = nums[i];
            if(i * 2 + 1<= n){
                nums[i*2 + 1] = nums[i] + nums[i+1];
                ans = max(ans,max(nums[i*2],nums[i*2+1]));
            }else{
                ans = max(ans,nums[i*2]);
            }
        }
        return ans;
    }
};
```

# 回溯

回溯的思想很重要，因为它和搜索、递归都有着极大地联系，也可以说搜索就是递归的一种具化形式。比如递归的全排列问题，DFS搜索的N皇后问题，其实本源就是回溯的思想。

回溯要非常注意的三个点：

- 已选择序列：也就是已经做出的选择组成的序列（路径）。
- 选择列表：也就是当前可以做的下一步选择。
  - 有时候是两种可能：当前层`选择`该结点和`不选择`该节点。
  - 有时候要列举后面的所有元素，还可能是图论里面下一步去哪（遍历整个图）
- 结束条件：也就是到达决策树底层（没有选择）的条件。

这个是一般回溯算法常用的框架，在你没有想法的时候可以试着套套~

```python
path = []
def backtrack(路径, 选择列表):
    if 满足结束条件/没有选择了:
        path.add(路径)
        return
    
    for 选择 in 选择列表:
        做选择===> 标记
        backtrack(路径, 选择列表)
        撤销选择===> 撤销标记
```

通常在我们遇到的回溯法解决的问题中，还可以进行一些优化，比如`剪枝`、`去重`等

**剪枝**：

1. **记忆化搜索**：`把已经计算出的答案记录到一个数组`里面，这样后面如果要用的话，就可以直接取出来，不必再搜索一遍。
2. **排序**：有时只需要一个解便可退出循环，此时`不同的搜索顺序可能对时间影响会很大`。比方类似背包，确定要拿多少东西，我们从大的拿，放不下就可以直接剪枝了，可以节省时间。
3. **前缀和剪枝**：可以用这种方法来`判断说是不是后面所有元素都加上也不够（或者刚好）`，那么就不必再往后搜索。

**去重**：

1. 利用集合set：把所有答案先放入一个set中，等遍历结束，再把所有答案放回到数组。

3. 记录一层内每一个数是否有被用过，例如：递增子序列、活字印刷

   - 这种在答案序列有序的情况下是比较好用的 ==> 回溯前先排序

#### 示例.所有子集的异或总和再求和

> 题目来源：Leetcode 1863.

一个数组的 异或总和 定义为数组中所有元素按位 XOR 的结果；如果数组为 空 ，则异或总和为 0 。

> 例如，数组 [2,5,6] 的 异或总和 为 2 XOR 5 XOR 6 = 1 。

给你一个数组 nums ，请你求出 nums 中每个 子集 的 异或总和 ，计算并返回这些值相加之 和 。

注意：在本题中，元素 相同 的不同子集应 多次 计数

**解题思路**：

**子集问题**常常可以采用**回溯**的方法解决，对于数组中的每个元素，我们有两种选择（选或不选），从而交叉构成了不同的子集。

![异或子集和](F:\Typora\Picture\leetcode\异或子集和.jpg)

```java
//java版本
class Solution {
    int ans;
    int n;
    /**
     @val:截至目前的异或值
     @idx:当前的索引下标
    */
    public void dfs(int val,int idx,int[] nums){
        //没有选择了,结束
        if(idx == n){
            ans += val;
            return;
        }
        //不选择（跳过）当前点
        dfs(val,idx + 1,nums);
        //选择当前点
        dfs(val^nums[idx],idx + 1,nums);
    }
    public int subsetXORSum(int[] nums) {
        ans = 0;
        n = nums.length;
        dfs(0,0,nums);
        return ans;
    }
}
```



#### 示例. 二进制手表

> 题目来源：Leetcode.401

二进制手表顶部有 4 个 LED 代表 **小时（0-11）**，底部的 6 个 LED 代表 **分钟（0-59）**。每个 LED 代表一个 0 或 1，最低位在右侧。

给你一个整数 turnedOn ，表示当前亮着的 LED 的数量，返回二进制手表可以表示的所有可能时间。你可以 按任意顺序 返回答案。

小时不会以零开头：

> 例如，"01:00" 是无效的时间，正确的写法应该是 "1:00" 。

分钟必须由两位数组成，可能会以零开头：

> 例如，"10:2" 是无效的时间，正确的写法应该是 "10:02" 。

解题思路：

本题有很多比较好的解题方法，但是毕竟在讲回溯，因此我们还是用回溯方法求解。

对于每个点有两种选择

- 选择
  - 选则是否会越界？
- 不选择

终止条件：选够了turnedOn个点 或者 没有选择了

> 注：没有选择了 并且 选择的点数小于turnedOn个点说明不符合要求。

```java
class Solution {
    int hour;  //表示“时”的值
    int minute;  //表示“分”的值
    List<String> ans; //结果存放列表
    /**
    @hourVal: 当前"时"的值
    @minuteVal: 当前"分"的值
    @indx: 索引0~5在分区域，6~9在时区域
    @count: 已经选择了几个位置
    @turnedOn: 总共需要选择几个位置
    */
    public void dfs(int hourVal,int minuteVal,int indx,int count,int turnedOn){
        //如果 没有选择了 或者 选够了 
        if(count == turnedOn || indx == 10){
            String time = null;
            if(minuteVal < 10){
                time = hourVal + ":0" + minuteVal;
            }else{
                time = hourVal + ":" + minuteVal;
            }
            //如果选够了，否则就是没选择了（比如一个都没选或者选的数量小于count)
            if(count == turnedOn){
                ans.add(time);//保存结果
            }
            return;
        }
        //如果当前在 "分"区域
        if(indx >= 0 && indx < 6){
            //判断如果选了该 点 是否会越界；不越界就选
            if(minuteVal + (int)Math.pow(2,indx) <= 59){
                dfs(hourVal,minuteVal + (int)Math.pow(2,indx),indx+1,count+1,turnedOn);
            }
            //不选择该点，即跳过
            dfs(hourVal,minuteVal,indx+1,count,turnedOn);
        }else{//当前在 "时"区域
            if(hourVal + (int)Math.pow(2,indx-6) <= 11){
                dfs(hourVal + (int)Math.pow(2,indx-6),minuteVal,indx+1,count+1,turnedOn);
            }
            dfs(hourVal,minuteVal,indx+1,count,turnedOn);
        }
    }
    public List<String> readBinaryWatch(int turnedOn) {
        //初始化
        ans = new ArrayList<String>();
        hour = 0;
        minute = 0;
        //回溯，深度优先搜索
        dfs(0,0,0,0,turnedOn);
        return ans;
    }
}
```



#### 示例. 格雷编码

> 题目来源：Leetcode.89
>
> 相似题目：1238. 循环码排列

n 位格雷码序列 是一个由 $2n$ 个整数组成的序列，其中：

1. 每个整数都在范围 $[0,2n-1]$内（含 0 和 $2n - 1$ ）
2. **第一个整数是 0**
3. 一个整数在序列中出现 不超过一次 
4. 每对 **相邻 整数的二进制表示 恰好一位不同** ，且 **第一个** 和 **最后一个** 整数的二进制表示 **恰好一位不同**

给你一个整数 n ，返回任一有效的 n 位格雷码序列 。

**解题思路**：

我们假设 n = 2，则其中一个结果是$[00,01,11,10]$。由题可以，开始时设`val = 0 = 00（二进制表示）`，那么第 4 条约束限制，下一个数只能是`01 或 10`，我们选择将低位设置为1，即`01`，加入结果集合中。此时`val = 01`，我们依然从低位开始设置为 1 ，显然不能`01 ==> 01`，因为`01`已经存在了，违反了第 3 条约束。那么我们就设置下一位`01 ==> 11`，符合要求，加入结果集。显然后面就是不断的递归。

![格雷编码](F:\Typora\Picture\leetcode\格雷编码.jpg)

```java
class Solution {
    List<Integer> ans;
    public void dfs(int val,int n,boolean[] label){
        label[val] = true; //标志已经保存
        ans.add(Integer.valueOf(val)); //存入结果
        for(int i = 0;i < n;i++){//开始依次从低位设置1
            int tmp =val | (1 << i);
            if(label[tmp] != true){//判断是否被标志了
                dfs(tmp,n,label);
            }
        }
    }
    public List<Integer> grayCode(int n) {
        int num = (int)Math.pow(2,n); 
        boolean [] label = new boolean[num];//标志数组
        ans = new ArrayList<Integer>(); //创建一个对象保存结果
        dfs(0,n,label);
        return ans;
    }
}
```





#### 示例. 组合总和II

> 题目来源：Leetcode.216

找出所有相加之和为 n 的 k 个数的组合。组合中只允许含有 1 - 9 的正整数，并且每种组合中不存在重复的数字。

>- 所有数字都是正整数。
>- 解集不能包含重复的组合。

```java
class Solution {
    List<List<Integer>> ans;  //存放最后所有结果
    List<Integer> tmp;   //存放一种结果
    public void dfs(int val,int sumval,int cnt,int k,int n){
        tmp.add(Integer.valueOf(val));
        if(cnt == k){
            if(sumval == n){
                //注意：因为是引用对象，不能使用ans.add(tmp);不然修改tmp,ans中的tmp也会被修改
                ans.add(new ArrayList<Integer>(tmp));
            }
            tmp.remove(Integer.valueOf(val));
            //返回前是不是要移除对应元素
            return ;
        }
        //为了防止子集重复，可以使得选择的元素都是递增的
        for(int i = val+1;i <= 9;i++){
            if(sumval + i > n) break;
            dfs(i,sumval+i,cnt+1,k,n);
        }
        tmp.remove(Integer.valueOf(val));
    }
    public List<List<Integer>> combinationSum3(int k, int n) {
        tmp = new ArrayList<Integer>();
        ans = new ArrayList<List<Integer>>();
        //初始点，每个点都选择一遍
        for(int i = 1;i <= 9 ;i++){
            dfs(i,i,1,k,n);
        }
        return ans;
    }
}
```

```java
    List<List<Integer>> ans;
    List<Integer> tmp;
    public void dfs(int val,int sumval,int k,int n){
        if(val > 9) return;
        if(tmp.size() == k){
            if(sumval == n){
                ans.add(new ArrayList<Integer>(tmp));
            }
            return ;
        }
        //选择当前点
        tmp.add(Integer.valueOf(val + 1));
        dfs(val+1,sumval+val+1,k,n);
        tmp.remove(tmp.size() - 1);
        //不选择当前点--->跳过
        dfs(val+1,sumval,k,n);
    }
    public List<List<Integer>> combinationSum3(int k, int n) {
        tmp = new ArrayList<Integer>();
        ans = new ArrayList<List<Integer>>();
        dfs(0,0,k,n);
        return ans;
    }
```



#### 示例. 递增子序列

> 题目来源：Leetcode.419

给你一个整数数组 nums ，找出并返回所有该数组中不同的递增子序列，递增子序列中 至少有两个元素 。你可以按 任意顺序 返回答案。

数组中可能含有重复元素，如出现两个整数相等，也可以视作递增序列的一种特殊情况。

```
输入：nums = [4,6,7,7]
输出：[[4,6],[4,6,7],[4,6,7,7],[4,7],[4,7,7],[6,7],[6,7,7],[7,7]]

输入：nums = [4,4,3,2,1]
输出：[[4,4]]
```

解题思路：

其实这道题就是求子序列，只是加了个限制条件（子序列是递增的）。子序列比较棘手的一点就是其中的重复元素处理。如示例一：`4,6,7(第一个)`和`4,6,7(第二个)`属于同一种情况。因此，去重就是这道题的一大要点。

去重：对于重复的元素，我们可以进行及时的剪枝来进行。我们规定，对于`4,6,7(第一个)`和`4,6,7(第二个)`这种情况，我们选择第2种来作为添加元素（即选择后一个）

举个例子：比如ABBCD，如果我们选择了第1个B，那么剩余的字符就变成了ABCD，这个时候我们再选择第2个B是可以的。但如果我们没选择第1个B，直接选择第2个B，那么剩余的字符就是ABCD，和上面重复了。所以代码大致是这样的

```java
if (i - 1 >= 0 && chars[i] == chars[i - 1] && !used[i - 1]) continue;
```

![递增子序列](F:\Typora\Picture\leetcode\递增子序列.jpg)

```java
class Solution {
    List<List<Integer>> paths;
    List<Integer> path;
    public void dfs(int idx,int last,int [] nums){
        //没有选择了
        if(idx == nums.length){
            if(path.size() > 1){//判断数组内元素
                paths.add(new ArrayList<Integer>(path)); //注意：要创建一个新的对象
            }
            return ;
        }
        //如果当前元素  大于 上一个元素，则是递增的
        if(idx == 0 || nums[idx] >= last){ //保证序列是递增的
            path.add(nums[idx]); //添加元素（选择）
            dfs(idx+1,nums[idx],nums);  //进行 下一层递归
            path.remove(path.size() - 1); //撤销，恢复到原来的状态
        }
        //如果当前元素 == 上一个元素，就结束
        if(nums[idx] != last){ //剪枝去重
            dfs(idx+1,last,nums);
        }
        
    }
    public List<List<Integer>> findSubsequences(int[] nums) {
        paths =new ArrayList<List<Integer>>();
        path = new ArrayList<Integer>();
        dfs(0,Integer.MIN_VALUE,nums);
        return paths;
    }
}
```

#### 示例. 活字印刷

> 题目来源：Leetcode.1079

你有一套活字字模 `tiles`，其中每个字模上都刻有一个字母 `tiles[i]`。返回你可以印出的非空字母序列的数目。

**注意：**本题中，每个活字字模只能使用一次。

```
输入："AAB"
输出：8
解释：可能的序列为 "A", "B", "AA", "AB", "BA", "AAB", "ABA", "BAA"。
```

![image.png](F:\Typora\java\pic\10.png)

这道题有几要点：

1. **结果不重复**，所以每次选择的时候都只能从前往后选。同时`[A，B]`和`[B，A]`被认为是两种不同的结果。

2.  **每个字符只能被使用一次**，因此使用一个数组`boolean[] used`来标记有没有被使用。

3. **结果非空**，即不能一个都不选

4. **对于输入ABBCD，$A_1 BCD$ 和 $A_2BCD$ 相当于重复**，因此如果我们选择了第1个A，那么剩余的字符就变成了ABCD，这个时候我们再选择第2个A是可以的。但如果我们没选择第1个A，直接选择第2个A，那么剩余的字符就是ABCD，和上面重复了。

   > 也就是说，对于一个位置，我们如果选择了相同的字符，那么结果会重复。
   >
   > **解决办法**：先排序，然后如果上一个没有用，并且当前字符和上一个字符相同，则跳过



方法一：排序 + 回溯

```java
class Solution {
    char[]  arrtile; //存储每个字符
    int ans = 0;  //结果
    boolean[] used;  //标记数组
    public void dfs(int idx){
        //没有选择了，到底层了
        if(idx == arrtile.length){
            return ;
        }
        //从头开始一个一个选择 ==> 对于要点1
        for(int i = 0;i < arrtile.length;i++){
            //每个字符只能用一次，如果用过就跳过  ==> 对于要点2
            if(used[i]) continue;   
            //如果上一个没有用，并且当前字符和上一个字符相同，则跳过 ==> 对于要点4
            if(i > 0 && arrtile[i] == arrtile[i-1] && !used[i-1]) continue;
            //更新结果，只有选了更新结果 ==> 对于要点3
            ans++;
            //标记
            used[i] = true;
            dfs(idx+1);
            //撤销标记
            used[i] = false;
        }
    }
    public int numTilePossibilities(String tiles) {
        arrtile = tiles.toCharArray();//String ==> char[]
        used = new boolean[arrtile.length]; 
        Arrays.sort(arrtile); //排序，这样相同的字符一定是相邻的
        dfs(0); //进行第一次递归
        return ans;
    }
}
```

方法二：统计字符+回溯

```java
class Solution {
    char[]  arrtile;
    int ans = 0;
    int[] cnt = new int[26];
    public void dfs(int idx){
        if(idx == arrtile.length) return;
        //依次判断每个位置是否都有
        for(int i = 0;i < 26;i++){
            if(cnt[i] == 0) continue; //相当于剪枝
            cnt[i] -= 1;
            ans++;
            dfs(idx+1);
            cnt[i] += 1;
        }
    }
    public int numTilePossibilities(String tiles) {
        arrtile = tiles.toCharArray();
        for(int i = 0;i < arrtile.length;i++){ //统计字符个数
            cnt[arrtile[i] - 'A'] += 1;
        }
        dfs(0);
        return ans;
    }
}
```

#### 示例. 火柴拼正方形

> 题目来源：Leetcode.437

输入火柴的数目，每根火柴用其长度表示。输出即为是否能用所有的火柴拼成正方形。

```
输入: [1,1,2,2,2]
输出: true

解释: 能拼成一个边长为2的正方形，每边两根火柴。
```

对于给定的若干根火柴，我们需要：

- 将它们分成四组，每一根火柴恰好属于其中的一组；
- 每一组火柴的长度之和都相同，等于所有火柴长度之和的四分之一。

使用深度优先搜索枚举出所有的分组情况，并对于每一种情况，判断是否满足上述的两个条件。

依次对每一根火柴进行搜索，当搜索到第` i `根火柴时，我们可以把它放到四组中的任意一种。对于每一种放置方法，我们继续对第` i + 1` 根火柴进行递归搜索。当我们搜索完全部的 N 根火柴后，再判断每一组火柴的长度之和是否都相同。

```java
class Solution {
    List<Integer> nums; //存储每条火柴长度
    int[] sums;  //存储每条边的当前长度和
    int edgeLen;  //每条边的边长
    public boolean dfs(int idx){
        //所有火柴均分组完成
        if(idx == nums.size()){
            //四组火柴长度相同
            if(sums[0] == sums[1] && sums[1] == sums[2] && sums[3] == sums[2]){
                return true;
            }
        }
        //获取当前火柴长度
        int tmp = nums.get(idx);
        //依次将该火柴放置在4组中
        for(int i = 0; i < 4;i++){
            if(sums[i] + tmp > edgeLen) continue;//如果 放入该火柴，长度超过要求边长，则放弃放入该组
            sums[i] += tmp; //放入
            if(dfs(idx+1)) return true; //进入下一层递归 ==> 放下一根火柴
            sums[i] -= tmp; //拿出
        }
        return false;
    }

    public boolean makesquare(int[] matchsticks) {
        //如果火柴长度小于4，则不可能拼成
        if(matchsticks == null || matchsticks.length < 4){
            return false;
        }
        //求取火柴长度和
        int perimeter = 0;
        for(int matchstick: matchsticks){
            perimeter += matchstick;
        }
        //因为要求所有火柴均使用，则必须能够被4整除
        if(perimeter % 4 != 0) return false;
        this.edgeLen = perimeter / 4; //求取每条边要求的长度
        this.sums = new int[]{0,0,0,0}; //初始化每组中的长度和
        this.nums = Arrays.stream(matchsticks).boxed().collect(Collectors.toList()); //int[]转List
        Collections.sort(this.nums, Collections.reverseOrder()); //排序
        return dfs(0); //递归
    }
}
```

#### 示例. 火柴拼正方形

> 题目来源：Leetcode.437

给你一个字符串 s ，请你拆分该字符串，并返回拆分后唯一子字符串的最大数目。

字符串 s 拆分后可以得到若干 非空子字符串 ，这些子字符串连接后应当能够还原为原字符串。但是拆分出来的每个 子字符串 都必须是 唯一的 。

注意：子字符串 是字符串中的一个连续字符序列

**解题思路**：

首先需要一个集合Map或者Set来记录有哪些 子字符串 已经使用了。

定义递归函数`dfs(int idx,String s,int count)`，idx表示起始搜索索引位置，count表示当前有了几个子字符串。对于从起始位置 idx ，我们可以将  $s[idx,i]，i \in (idx,len(s))$ 加入结果，然后继续递归

```java
class Solution {
    //记录已经存储了哪些字符串
    Map<String,Integer> m = new HashMap<String,Integer>();
    //最终结果
    int ans = Integer.MIN_VALUE;

    public void dfs(int idx,String s,int count){
        //如果没有选择了，更新答案
        if(idx == s.length()){
            ans = Math.max(ans,count);
            return ;
        }
        //依次遍历所有从当前位置idx开始的所有子字符串
        for(int i = idx;i < s.length();i++){
            String sub = s.substring(idx,i+1); //获取子字符串
            if(m.get(sub) != null && m.get(sub) == 1) continue; //如果已经存在，跳过
            m.put(sub,1); //进行记录
            dfs(i+1,s,count+1);  //进入下一层递归
            m.put(sub,-1); //撤销记录
        }
    }
    public int maxUniqueSplit(String s) {
        dfs(0,s,0);
        return ans;
    }
}
```









# 搜索

## 深度优先遍历/搜索  DFS

> 能走就一直走，走不了就“退”回    不撞南墙不回头

> 深度优先搜索特别适用于那些探索所有的可能性的这些问题，问题解决的可能性是非常多的，所以需要去探索所有的可能性

> 假设已经有邻接矩阵matrix [N+1] [N+1]，标记数组mark[N+1]

**解决相关问题**：

连通性问题[走地图]、有关 树 的遍历，路径等问题

### DFS在图的应用

**要点**：

- 需要一个数组mark[]来标记结点是否被遍历过，防止重复遍历
- 利用 递归/栈FILO
- 要结合回溯来进行使用

```cpp
//递归的方法
void func(int x){
	cout<<x<<" ";//进行相关操作，这里进行输出
	for(int i = 1;i <= N;i++){//对结点x的邻接结点进行遍历
		if(matrix[x][i] == 1 && mark[i] == 0){
			mark[i] = 1;//标记
			func(i);
		}
	}
}

for(int i = 1;i <= N;i++){
	if(mark[i] == 0){
		mark[i] = 1;//标记
		func(i); //递归函数
	}
}
```

#### 示例-连续相同的数字

```
题目描述
返回所有长度为 n 且满足其每两个连续位上的数字之间的差的绝对值为 k 的 非负整数 。
请注意，除了 数字 0 本身之外，答案中的每个数字都 不能 有前导零。例如，01 有一个前导零，所以是无效的；但 0 是有效的。
你可以按 任何顺序 返回答案。
示例 1：
输入：n = 3, k = 7
输出：[181,292,707,818,929]
解释：注意，070 不是一个有效的数字，因为它有前导零。

示例 2：
输入：n = 2, k = 1
输出：[10,12,21,23,32,34,43,45,54,56,65,67,76,78,87,89,98]

示例 3：
输入：n = 2, k = 0
输出：[11,22,33,44,55,66,77,88,99]
```

这道题目通过分析就是从以1~9开头的数字开始搜索，搜索长度为n

<img src="F:\Typora\Picture\leetcode\连续相同的数字.jpg" alt="连续相同的数字" style="zoom: 67%;" />

```cpp
class Solution {
public:
    vector<int> ans;
    void func(int x,int n,int k){
        //剩余位数为0，则压入结果容器中
        if(n == 0){
            ans.push_back(x);
            return ;
        }
        //左判断
        int t1 = x % 10 - k;
        int t2 = x % 10 + k;
        //右判断
        if(t1 >= 0)
            //递归判断下一位
            func(x*10+t1,n-1,k);
        //需要特殊判断下k=0时的情况，不然会出现重复元素
        if(k != 0 && t2 <= 9)
            func(x*10+t2,n-1,k);
    }
    vector<int> numsSameConsecDiff(int n, int k) {
        for(int i = 1;i < 10;i++){
            func(i,n-1,k);
        }
        return ans;
    }
};
```

#### 示例-最大黑色区域

````
题目描述
​ 现在给出一个 n∗m 的二维矩阵，矩阵上的每个点只可能是 0 （代表白色）或 1 （代表黑色）。现规定某一点的颜色与它的上下左右某点的颜色相同，则它们为同一区域，现求最大黑色区域的大小。
输入
​ 第一行两个正整数 n,m。（1≤n,m≤100）
​ 接下来输入一个二维字符矩阵，每个字符为 0 或 1。
输出
​ 输出一个整数，表示可以最大黑色区域面积。

样例输入
5 6
011001
110101
010010
000111
101110
样例输出
7
````

根据题目描述：明显是一个连通性问题，即求`最大连通区域的面积`

```cpp
#include<iostream>
#include<vector>
using namespace std;

int n,m,cnt,ans = 0;
//方向数组
int dirs[4][2] = {-1,0,1,0,0,1,0,-1};

void func(vector<vector<char>> &map,int x,int y){
    //对四个方向进行遍历
    for(int i = 0;i < 4;i++){
        int nx = x + dirs[i][0];
        int ny = y + dirs[i][1];
        //是否是黑色区域
        if(map[nx][ny] == '1'){
            //是黑色则增加连通域面积
            cnt++;
            //标记该点
            map[nx][ny] = '0';
            //递归遍历
            func(map,nx,ny);
        }
    }
}

int main(){
    cin>>n>>m;
    vector<vector<char>> map(n+2,vector<char> (m+2,'0'));
    //初始化地图
    for(int i = 1;i <= n;i++){
        //从map[i][1]开始输入一行，遇到回车结束
        cin>>&map[i][1];
    }
    //遍历每个结点，求其连通域面积
    for(int i = 1;i <= n;i++){
        for(int j = 1;j <= m;j++){
            //如果是黑色区域，则开始求取面积
            if(map[i][j] == '1'){
                cnt = 1;
                //标记已经走过
                map[i][j] = '0';
                //递归搜索
                func(map,i,j);
                //更新最大面积
                ans = ans > cnt ? ans : cnt;
            }
        }
    }
    cout<<ans<<endl;
    return 0;
}
```

#### 示例-01迷宫

```
题目描述
​ 有一个仅由数字 0 与 1 组成的 n×m 格迷宫。若你位于一格 0 上，那么你可以移动到相邻 4 格中的某一格 1 上，同样若你位于一格 1 上，那么你可以移动到相邻 4 格中的某一格 0 上。
​ 你的任务是：对于给定的迷宫，询问 k 次从某一格开始能移动到多少个格子（包含自身）。

输入
​ 第 1 行为两个正整数 n,m,k。
​ 下面 n 行，每行 m 个字符，字符只可能是 0 或者 1，字符之间没有空格。
​ 接下来 k 行每行给出一个点的在坐标。
输出
​ 对于每个坐标输出一行一个整数表示该点能移动到的格子数（包括自身）。

样例输入
2 3 4
011
100
1 1
2 2
1 3
2 3
样例输出
4
4
2
2
数据保证 1≤n,m≤3000，1≤k≤30000
```

因为本题的数据量k太大，如果一个一个求，则会超时。我们可以提前将整个图的每个点的结果求出，然后当获得坐标后直接输出。

<img src="F:\Typora\Picture\leetcode\01迷宫.jpg" alt="01迷宫" style="zoom:50%;" />

```cpp
#include<iostream>
#include<stack>
using namespace std;

int n,m,k,sx,sy,ans;
char map[3002][3002];
//标记数组
int mark[3002][3002] = {0};
//栈结点
struct node {
    int x,y;
};
stack<node> st;
//方向数组
int dirs[4][2] = {0,1,0,-1,1,0,-1,0};

void func(int x,int y){
    //将坐标压入栈中
    st.push((node){x,y});
    //搜索周围4个坐标
    for(int i = 0;i < 4;i++){
        int nx = x + dirs[i][0];
        int ny = y + dirs[i][1];
        //非0 代表标记过了 直接跳过
        if(mark[nx][ny] != 0) continue;
        //超出界限 直接跳过
        if(nx < 1 || nx > n || ny < 1 || ny > m) continue;
        //符合题目要求则进行递归搜索
        if((map[x][y] == '0' && map[nx][ny] == '1') || (map[x][y] == '1' && map[nx][ny] == '0')){
            mark[nx][ny] = 1;//标记
            ans++;//联通数 + 1
            func(nx,ny);//递归搜索
        }
    }
}

int main(){

    cin>>n>>m>>k;
    //初始化map
    for(int i = 1;i <= n;i++){
        for(int j = 1;j <= m;j++){
            cin>>map[i][j];
        }
    }
	//依次搜索整个map
    for(int i = 1;i <= n;i++){
        for(int j = 1;j <= m;j++){
            //如果没有标记过[0：没标记 非0: 标记过]
            if(mark[i][j] == 0){
                //初始化连通数
                ans = 1;
                mark[i][j] = 1;//标记
                func(i,j);//开始递归搜索
				//将栈中坐标依次弹出，更新mark
                while(!st.empty()){
                    node temp = st.top();
                    mark[temp.x][temp.y] = ans;
                    st.pop();
                }
            }
        }
    }
	//直接根据mark输出结果
    for(int i = 0;i < k;i++){
        cin>>sx>>sy;
        cout<<mark[sx][sy]<<endl;
    }
    return 0;
}
```



通过上面几个示例的学习，我们总结一下深度优先搜索的策略：

- 通常用于求解图、树中的连通性等问题
- 首先是要针对题目**创建对应的结构体 struct node{}**;来作为队列中的元素
- 根据题目 **创建/初始化 标记数组**
- **初始化队列**，加入初始元素
- 遍历初始情况，并均进行深度优先遍历
- 递归函数func
  - 以当前情况作为起始条件，继续判断其他可能发送情况，进行递归判断

### DFS在树的应用

> DFS通常会用于树这种数据结构的题型中。

#### 验证二叉搜索树

```
给定一个二叉树，判断其是否是一个有效的二叉搜索树。

假设一个二叉搜索树具有如下特征：
节点的左子树只包含小于当前节点的数。
节点的右子树只包含大于当前节点的数。
所有左子树和右子树自身必须也是二叉搜索树。
输入:
    2
   / \
  1   3
输出: true
```

```cpp
class Solution {
public:
/*
函数表示考虑以 root 为根的子树，判断子树中所有节点的值是否都在 (lower,bigger)的范围内（注意是开区间）。如果 root 节点的值 val 不在 (lower,bigger) 的范围内说明不满足条件直接返回。
根据二叉搜索树的性质，在递归调用左子树时，我们需要把上界 bigger 改为 root.val，即调用 helper(root.left, lower, root.val)，因为左子树里所有节点的值均小于它的根节点的值。同理递归调用右子树时，我们需要把下界 lower 改为 root.val，
*/
    bool func(TreeNode * root,long long int lower,long long int bigger){
        if(root == nullptr) return true;
        //如果当前结点不在上下界范围，说明不符合
        if(root->val <= lower || root->val >= bigger) return false;
        //依次判断左右子树
        return func(root->left,lower,root->val) && func(root->right,root->val,bigger);
    }

    bool isValidBST(TreeNode* root) {
        //如果树空，则返回1
        if(root == nullptr) return true;
        //进行递归判断
        return func(root,LONG_MIN,LONG_MAX);
    }
};
```

#### 路径总和II

```
给你二叉树的根节点 root 和一个整数目标和 targetSum ，找出所有 从根节点到叶子节点 路径总和等于给定目标和的路径。
叶子节点 是指没有子节点的节点。
```

<img src="https://assets.leetcode.com/uploads/2021/01/18/pathsumii1.jpg" alt="img" style="zoom:50%;" />

>  分析：也就是DFS应用于树的指定路径求取

```cpp
class Solution {
public:
    //两个全局变量来存储结构
    vector<vector<int>> ans;//所有路径的结果集合
    vector<int> temp;//存储根节点至当前结点的路径
    void func(TreeNode * root,int targetSum,int nowgetSum){
        if(root == nullptr) return;
        //在当前路径中标记当前结点
        temp.push_back(root->val);
        //获得了符号题意的路径，存储
        if(root->val + nowgetSum == targetSum && (!root->left) && (!root->right)) ans.push_back(temp);
        //遍历左右子树
        func(root->left,targetSum,root->val+nowgetSum);
        func(root->right,targetSum,root->val+nowgetSum);
        //遍历完左右子树，要回退，因此也要把当前路径中的当前结点弹出
        temp.pop_back();
    }
    vector<vector<int>> pathSum(TreeNode* root, int targetSum) {
        if(root == nullptr) return ans;
        //从根节点开始遍历
        func(root,targetSum,0);
        return ans;
    }
};
```



## 广度优先遍历/搜索  BFS

> 广搜是对某种数据结构进行层次遍历

**应用场景**：

- 最少步数/最短路径 等问题


> 一般最短，用到广度优先搜索

- 连通性问题
- 树的层次遍历

要点：

- 需要一个数组mark[]来标记结点是否被遍历过，防止重复遍历
- 利用 队列FIFO
- 一般要抽象成树、图等模型

### BFS在图的应用

>  假设已经有邻接矩阵matrix [N+1] [N+1]，标记数组mark[N+1]

```cpp
struct node{//根据不同题意可以设置相应结构体
	int id; //编号
	int floor; //第几层
};
//start 为出发搜索的点，dst 为目标点
queue<int> que;
que.push((node){strat,0});
mark[start] = 1;//进行标记
while(!que.empty()){
	node temp = que.front();
	que.pop();
    if(temp.id == dst){
       //找到了目标结点，并进行某种操作，此处打印其层数
       cout<<temp.id + 1<<endl;
        return 0;
    }
	for(int i = 1;i <= n;i++){//广度遍历改点的邻接点
		if(matrix[temp.id][i] == 1 && mark[i] == 0){
			mark[i] = 1;
			que.push((node){i,temp.floor + 1});
		}
	}
}
```

#### 示例--警察找车

```
题目描述
​ 年轻的拉尔夫开玩笑地从一个小镇上偷走一辆车，但他没想到的是那车属于警察局。并且车上装有用于发射车子移动路线的装置。那个装置太旧了，以至于只能发射关于那辆车的移动路线的方向信息。
​ 通过使用一张小镇的地图，帮助警察局找到那车。表示出该车最终所有可能的位置。
​ 小镇的地图是矩形的，上面的符号用来标明那儿可以行车和那儿不行。“.” 表示小镇上那块地方是可以行车的，符号 “X” 表示此处不能行车。拉尔夫所开小车的初始位置，用字符的 “∗”，表示，且汽车能从初始位置通过。汽车能向四个方向移动：向北（上），南（下），西（左），东（右）；拉尔夫所开小车的行动路线是通过一组给定的方向来描述的，在每个给定的方向，拉尔夫驾驶小车通过小镇上的一个或更多的可行车地点。

输入
​ 第一行两个整数 n,m，表示小镇地图大小。（1≤n,m≤50）
​ 接下来是一个 n∗m 的矩阵，由 .∗X 组成，表示小镇的地图。
​ 再接下来一行一个整数 N(1≤N≤1000)，表示接下来有 N 个方向。
​ 接下来 N 行，每行一个方向单词，NORTH,SOUTH,WEST,EAST 分别表示上下左右，代表汽车的移动方向，任何两个连续的方向都不相同。
输出
​ 输出小镇的地图，在地图上，汽车最终可能出现的点用 ∗ 来表示。

​ 样例输入
4  5
.....
.X...
...*X
X.X..
3
NORTH
WEST
SOUTH
​ 样例输出
.....
*X*..
*.*.X
X.X..
```

题目分析：

<img src="F:\Typora\Picture\leetcode\警察找车题目分析.jpg" alt="警察找车题目分析" style="zoom:50%;" />

```cpp
#include<iostream>
#include<vector>
#include<queue>
#include<string>
using namespace std;
//记录矩阵大小及起始点坐标
int n,m,sx,sy;
//自定义结构体 存储结点的坐标
struct node{
    int x,y;
};
//方向数组：表示搜索的方向
vector<vector<int>> dirs{{-1,0},{1,0},{0,-1},{0,1}};
//判断是否在矩阵有效范围内
bool inMatrix(int i,int j){
    return i >= 1 && i <= n && j >= 1 && j <= m;
}

int main(){
    cin>>n>>m;
    vector<vector<char>> matrix(n+2,vector<char> (m+2,'X'));
    for(int i = 1;i <= n;i++){
        for(int j = 1;j <= m;j++){
            cin>>matrix[i][j];
            if(matrix[i][j] == '*'){
                matrix[i][j] = '.';
                sx = i;
                sy = j;
            }
        }
    }
    //结点队列
    queue<node> q;
    q.push((node){sx,sy});
    int k;
    cin>>k;
    //遍历每次获得的方向信息
    for(int i = 0;i < k;i++){
        //标记数组，标记结点是否被访问过
        vector<vector<bool>> mark(n+2,vector<bool>(m+2,false));
        string com;
        int dir;
        cin>>com;
        if(com[0] == 'N') dir = 0;
        else if(com[0] == 'S') dir = 1;
        else if(com[0] == 'W') dir = 2;
        else dir = 3;
        //因为是按 层 遍历，并且每层结点都存在一个队列中，需要知道每层有多少个结点
        int nodes_cnt = q.size();
        for(int j = 1;j <= nodes_cnt;j++){
            //获取本层的一个结点
            node tmp = q.front();
            q.pop();
            int t = 1;
            //从该结点开始朝着指定方向遍历，知道走不了或超出范围
            while(t){
                int nx = tmp.x + t * dirs[dir][0];
                int ny = tmp.y + t * dirs[dir][1];
                if(!inMatrix(nx,ny) ||  matrix[nx][ny] == 'X') break;
                if(mark[nx][ny] == 0){
                    mark[nx][ny] = 1;
                    q.push((node){nx,ny});//将能够走到的地点坐标加入到下一层
                }
                t++;
            }
        }
    }
    //队列中剩下的结点坐标值及为最后可能出现的地点坐标
    while(!q.empty()){
        matrix[q.front().x][q.front().y] = '*';
        q.pop();
    }
    for(int i = 1; i <= n;i++){
        for(int j = 1;j <= m;j++){
            cout<<matrix[i][j];
        }
        cout<<endl;
    }
    return 0;
}
```

#### 示例-奇怪的电视

```
题目描述
​ 小明过年的时侯去如姥姥家，除夕之夜，大家都想看春节联欢晚会，而可以依赖的就是一台旧电视。
​ 那一台旧电视不是遥控器控制的，上面有许多按钮，按下某一按钮，其他按钮都将被释放，只有被按的按钮工作（如果其他按钮本来就是释放的状态，那么它们保持不变，处于按下状态的按钮不能重复按下，这对下文依旧适用）。当小明到来的那一天，上面的许多按钮突然无法正常工作。现在按下某个按钮后，有一些按钮将被释放，而另外的一些按钮将不改变原状态。经过一番惨无人道的折腾，小明知道按下每一个按钮会产生什么样的效果。现在他只需要第3个按钮正常工作。
​ 现在帮助小明计算，从给定的状态到只有按钮3工作，而其他按钮都被释放这个最终状态所需按下的按钮序列的最短长度。

输入
​ 第一行一个整数 N，表示按钮数。（3≤N≤20）
​ 第二行包含 N 个数，表示各按钮的初始状态。0 表示相应的按钮是释放的，1 表示相应的按钮是按下的。

​ 接下来的 N 行，表示按下某个按钮时将有那些按钮被释放。第 M+2 行由数字 K 开头，紧跟着 K 个数字（按升序排列），表示当按下按钮 M 时被释放的按钮数及按钮号码（按钮号码用数字 1∼M 表示）。每个按钮不能释放其本身，也可能不释放任何按钮。

输出
​ 输出一个数，表示从给定的状态到只有按钮3工作而其他按钮都被释放这个最终状态所需按下的按钮序列的最短长度。

样例输入
5
1 1 0 0 1
4 2 3 4 5
4 1 3 4 5
2 2 4
0
4 1 2 3 4
样例输出
3
```

```cpp
#include<iostream>
#include<queue>
using namespace std;

struct node{
    int status;//当前组按钮的状态[每一位代表一个小按钮 1：被按下0：弹起]
    int step;//到达该状态的最小步数
};

int n,start,b2[25],num[25],mark[40000005];
int main(){
    b2[0] = 1;
    //初始化二进制每一位代表的权值
    for(int i = 1;i <= 20;i++){
        b2[i] = b2[i-1] * 2;
    }
    cin>>n;
    for(int i = 1; i <=n ;i++){//获取起始状态
        int t;
        cin>>t;
        if(t == 1){
            start += b2[i];
        }
    }
    //记录每个按钮被按下 其他按钮的状态变化
    for(int i = 1;i <= n;i++){
        int cnt,t;
        cin>>cnt;
        for(int j = 0;j < cnt;j++){
            cin>>t;
            num[i] += b2[t];
        }
    }
    //准备搜索
    queue<node> que;
    que.push((node){start,0});
    mark[start] = 1;//标记初始状态
    while(!que.empty()){
        node temp = que.front();
        que.pop();
        if( temp.status == 8 ){//如果当前状态即目标状态，输出步数
            cout<<temp.step<<endl;
            return 0;
        }
        //对当前按钮组中没有按下去的按钮依次进行按下【即广度遍历后续状态】
        for(int i = 1;i <= n;i++){
            //判断当前按钮是否被按下
            if((temp.status & b2[i]) == 0){
                //求按下之后的状态
                int t = temp.status + b2[i];
                //其他按钮弹起
                t &= ~num[i];
                if(mark[t] == 0){
                    mark[t] = 1;//标记
                    que.push((node){t,temp.step + 1});//将当前状态加入队列，等待遍历
                }
            }
        }
    }
    cout<<-1<<endl;
    return 0;
}
```

#### 示例-获取所有钥匙的最短路径

```
题目描述：
​ 给定一个二维网格 grid。 "." 代表一个空房间， "#" 代表一堵墙， "@" 是起点，（"a", "b", ...）代表钥匙，（"A", "B", ...）代表锁。
​ 我们从起点开始出发，一次移动是指向四个基本方向之一行走一个单位空间。我们不能在网格外面行走，也无法穿过一堵墙。如果途经一个钥匙，我们就把它捡起来。除非我们手里有对应的钥匙，否则无法通过锁。
​ 假设 K 为钥匙/锁的个数，且满足 1 <= K <= 6，字母表中的前 K 个字母在网格中都有自己对应的一个小写和一个大写字母。换言之，每个锁有唯一对应的钥匙，每个钥匙也有唯一对应的锁。另外，代表钥匙和锁的字母互为大小写并按字母顺序排列。
​ 返回获取所有钥匙所需要的移动的最少次数。如果无法获取所有钥匙，返回 -1 。


示例 1：
输入：["@.a.#","###.#","b.A.B"]
输出：8

示例 2：
输入：["@..aA","..B#.","....b"]
输出：6
```

题目解析：

<img src="F:\Typora\Picture\leetcode\获取钥匙的最短路径.jpg" alt="获取钥匙的最短路径" style="zoom: 50%;" />

```cpp
class Solution {
public:
    int sx,sy,cnt = 0;
    struct node{
        int x,y;
        int statu;
        int step;
    };
    //方向数组
    vector<vector<int>> dirs{{-1,0},{1,0},{0,-1},{0,1}};
    //每一把药匙对于的权重 a:0 b:1 c:2 ...
    vector<int> b2{1,2,4,8,16,32,64,128};
    //标记数组 mark[i][j][x] : 状态为x时是否走过grid[i][j]
    int mark[35][35][130] = {0};
    int shortestPathAllKeys(vector<string>& grid) {
        int n = grid.size(),m = grid[0].length();
        for(int i = 0;i < n;i++){
            for(int j = 0;j < m;j++){
                if(grid[i][j] == '@') {
                    sx = i;
                    sy = j;
                    grid[i][j] = '.';//修改为'.' 简化后续操作
                }else if(grid[i][j] >= 'a' && grid[i][j] <= 'z'){
                    cnt++;//总共钥匙个数
                }
            }
        }
        queue<node> q;
        q.push((node){sx,sy,0,0});
        while(!q.empty()){
            node temp = q.front();
            q.pop();
            //如果 当前药匙总 数等于 钥匙总数
            if(temp.statu == b2[cnt]-1){
                return temp.step;
            }
            //向四个方向遍历
            for(int i = 0; i < 4;i++){
                int nx = temp.x + dirs[i][0];
                int ny = temp.y + dirs[i][1];
                //如果越界 或者 遇到枪， 直接继续
                if(nx < 0 || nx >= n || ny < 0 || ny >= m || grid[nx][ny] == '#') continue;
                //如果可以走，并且此时状态没有走过，则走并入队
                if(grid[nx][ny] == '.' && mark[nx][ny][temp.statu] == 0){
                    mark[nx][ny][temp.statu] = 1;
                    q.push((node){nx,ny,temp.statu,temp.step+1});
                //如果遇到药匙，则且之前此状态没走过，则获取药匙并更新状态，入队
                }else if(grid[nx][ny] >= 'a' && grid[nx][ny] <= 'z' && mark[nx][ny][temp.statu] == 0){
                    //此时因为获得了药匙，需要设置两个标记
                    mark[nx][ny][temp.statu] = 1;
                    mark[nx][ny][temp.statu | b2[grid[nx][ny]-'a']] = 1;
                    q.push((node){nx,ny,temp.statu | b2[grid[nx][ny]-'a'],temp.step+1});
                //如果遇到门，有对应的药匙并且此状态之前未到达过，则走并入队
                }else if(grid[nx][ny] >= 'A' && grid[nx][ny] <= 'Z' && mark[nx][ny][temp.statu] == 0 && (temp.statu & b2[grid[nx][ny]-'A']) != 0){
                    mark[nx][ny][temp.statu] = 1;
                    q.push((node){nx,ny,temp.statu,temp.step+1});
                }
            }
        }
        return -1;
    }
};
```

#### 示例-小明买手机回家

```
题目描述
​ 小明看完了电影，是时候回家了，可是这时他突然得知小米之家的小米9现货开卖了，这款手机小明已经想了一个多月，知道这个消息后的他十分兴奋，一定要在回家之前先去小米之家买手机（城市中有一个或多个小米之家），请计算小明从电影院到任意一个小米之家买手机后回家的最短距离（只能朝上下左右四个方向行走，除了障碍物外，其他地方都可以通过），数据保证可以买完手机后回家。

输入
​ 第 1 行两个数 n 和 m 表示地图有 n 行 m 列 2≤n,m≤2000​ 第 2 行至第 n+1 行为地图 其中 S 表示电影院 T 表示家 P 表示小米之家​ . 为可以通过的点 # 为障碍物
输出
​ 一个整数 表示小明从电影院去小米之家再回家的总距离

样例输入
5 5
.S...
###..
....T
.P##.
P....
样例输出
11
```

```cpp
#include<iostream>
#include<queue>
using namespace std;
int n,m,sx,sy;
struct node{
    int x,y,step,statu;//0 没手机 1 有手机
};
char map[2005][2005] = {0}; // 不用判断边界
int mark[2005][2005] = {0}; // 0什么状态都没走过  1无手机状态走过 2有手机状态走过 3都走过
int dirs[4][2] = {0,1,0,-1,1,0,-1,0};//方向数组
int main(){
    cin>>n>>m;
    for(int i = 1;i <= n;i++){
        for(int j = 1;j <= m;j++){
            cin>>map[i][j];
            if(map[i][j] == 'S'){
                sx = i;
                sy = j;
                mark[i][j] = 1;
                map[i][j] = '.';
            }
        }
    }
    queue<node> q;
    q.push((node){sx,sy,0,0});
    while(!q.empty()){
        node temp = q.front();
        q.pop();
        for(int i = 0;i < 4;i++){
            int nx = temp.x + dirs[i][0];
            int ny = temp.y + dirs[i][1];
            //如果当前状态已经走过 直接跳过
            if(temp.statu == 0 && (mark[nx][ny] & 1) != 0 || temp.statu == 1 && (mark[nx][ny] & 2) != 0) continue;
            //如果买到手机并且到家，直接返回结果
            if(temp.statu == 1 && map[nx][ny] == 'T'){
                cout<<temp.step + 1<<endl;
                return 0;
            }
            //当前状态[没手机没走过]/[有手机没走过]
            if(map[nx][ny] == '.' || map[nx][ny] == 'T'){
                mark[nx][ny] = mark[nx][ny] | (1 << temp.statu);
                q.push((node){nx,ny,temp.step + 1,temp.statu});
            }
            //遇到手机店，不管当前什么状态，到手机店后状态一定是1，并且标记mark为3
            if(map[nx][ny] == 'P'){
                mark[nx][ny] = 3;
                q.push((node){nx,ny,temp.step + 1,1});
            }
        }
    }
    cout<<-1<<endl;
    return 0;
}
```

#### 示例-01矩阵

```
给定一个由 0 和 1 组成的矩阵 mat ，请输出一个大小相同的矩阵，其中每一个格子是 mat 中对应位置元素到最近的 0 的距离。

两个相邻元素间的距离为 1 。
```

![img](F:\Typora\Picture\leetcode\lc542.png)

```
输入：mat = [[0,0,0],[0,1,0],[1,1,1]]
输出：[[0,0,0],[0,1,0],[1,2,1]]
```

解题思路：

假设这个矩阵中恰好只有一个0时，可以从 00 的位置开始进行 广度优先搜索。广度优先搜索可以找到从起点到其余所有点的 最短距离，因此如果我们从 0 开始搜索，每次搜索到一个 1，就可以得到 0 到这个 1 的最短距离，也就离这个 1 最近的 0 的距离了（因为矩阵中只有一个 0）。

题目中可能有许多个0，我们可以将连通的0看成一个整体，通过将求解`每个1到最近0的距离`转化为`求解这个连通的0整体距离周围每个1的距离`

```cpp
class Solution {
public:
    struct node{
        int x,y;
        int step;
    };
    int dirs[4][2] = {-1,0,1,0,0,-1,0,1};

    vector<vector<int>> updateMatrix(vector<vector<int>>& mat) {
        int n = mat.size();
        int m = mat[0].size();
        //存放结果的矩阵
        vector<vector<int>> ans(n,vector<int> (m,0));
        //标记矩阵
        vector<vector<bool>> mark(n,vector<bool> (m,false));
        //队列
        queue<node> q;
        //先将所有0压入队列，并标记。那么剩下的都是1了
        for(int i = 0;i < n;i++){
            for(int j = 0 ;j < m;j++){
                if(mat[i][j] == 0){
                    q.push((node){i,j,0});
                    mark[i][j] = true;
                }
            }
        }
        //BFS
        while(!q.empty()){
            node tmp = q.front();
            q.pop();
            //遍历周围4个方向
            for(int i = 0;i < 4;i++){
                int nx = tmp.x + dirs[i][0];
                int ny = tmp.y + dirs[i][1];
                if(nx < 0 || nx >= n || ny < 0 || ny >= m) continue;
                //如果没有被标记，那么入队标记，并更新结果
                if(mark[nx][ny] == false){
                    mark[nx][ny] = true;
                    ans[nx][ny] = tmp.step + 1;
                    q.push((node){nx,ny,tmp.step + 1});
                }
            }
        }
        return ans;
    }
};
```

通过上面几个示例的学习，我们总结一下广度优先搜索的策略：

- 通常用于求解图、树中的最短路径/最短步数，连通性等问题
- 首先是要针对题目**创建对应的结构体 struct node{}**;来作为队列中的元素
- 根据题目 **创建/初始化 标记数组**，有时可能是个集合，将其从集合中删去代表标记
- **初始化队列**，加入初始元素
- `while(!q.empty()) `当队列不空一直循环操作
  - 弹出队头元素
  - 判断是否符合结果，`符合则进行相应操作并return`
  - 对该元素下一步可以遍历的元素进行遍历[**入队**]，并标记
- 队空则没有找到，进行相应操作

### BFS在树的应用

```
给定一个 完美二叉树 ，其所有叶子节点都在同一层，每个父节点都有两个子节点。二叉树定义如下：

struct Node {
  int val;
  Node *left;
  Node *right;
  Node *next;
}
填充它的每个 next 指针，让这个指针指向其下一个右侧节点。如果找不到下一个右侧节点，则将 next 指针设置为 NULL。
初始状态下，所有 next 指针都被设置为 NULL。
```

<img src="https://assets.leetcode.com/uploads/2019/02/14/116_sample.png" alt="img" style="zoom:67%;" />

```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* left;
    Node* right;
    Node* next;
    
    Node() : val(0), left(NULL), right(NULL), next(NULL) {}
    Node(int _val) : val(_val), left(NULL), right(NULL), next(NULL) {}
    Node(int _val, Node* _left, Node* _right, Node* _next)
        : val(_val), left(_left), right(_right), next(_next) {}
};
*/
class Solution {
public:
    struct node{
        Node * p;
        int step;//当前层数
    };
    queue<node> que;
    Node* connect(Node* root) {
        //如果是空树之间返回
        if(root == NULL) return root;
        //先将根节点入队
        que.push((node){root,0});
        while(!que.empty()){
            //得到当前需要遍历的层数
            int nowstep = que.front().step;
            //当队列不空并且队列内结点层数等于当前要遍历的层数使，一直出队
            while(!que.empty() && que.front().step == nowstep){
                //头节点出队
                node temp = que.front();
                que.pop();
                //如果当前结点和队头结点的层数一样，将next指针指向它
                if(que.front().step == temp.step){
                    temp.p->next = que.front().p;
                }
                //如果当前结点的左右孩子结点存在，则入队
                if(temp.p->left) que.push((node){temp.p->left,temp.step+1});
                if(temp.p->right) que.push((node){temp.p->right,temp.step+1});
            }
        }
        return root;
    }
};
```

## A*算法

`A*`算法常常可以和BFS、DFS结合来进一步优化算法的性能。如果你能够掌握其算法原理的话在面试的时候也可以提一嘴`"可以结合A*算法来优化"`，面试官必定对你夸赞叫好!



# 并查集

在一些应用问题中，需要将n个不同的元素划分成一些不相交的集合。开始时，每个元素自成一个单元素集合，然后按一定的规律将归于同一组元素的集合合并。在此过程中要反复用到查询某一个元素归属于那个集合的运算。

先简单了解下并查集及其操作：

在使用并查集时常常用到了数组，我们设数组`s[i]`表示元素i所属的集合，开始时每个元素的所属集合都是自己。具体见下图

<img src="F:\Typora\LeetCode\pic\微信图片_20211026135355.png" alt="微信图片_20211026135355" style="zoom: 50%;" />

有时候，对元素进行合并后可能最终成为一个`链表`，比如`1->2->3->4->5`，造成性能退化，可以采用路径压缩的方法

<img src="F:\Typora\LeetCode\pic\微信图片_20211026135405.png" alt="微信图片_20211026135405" style="zoom:50%;" />

具体实现代码如下：

> 注：已加注释

**基本操作**：

```cpp
//并查集类
class UnionFind
{
private:
    vector<int> parent; //就是图中的s[i]
    vector<int> rank;   //可以理解为每个集合的深度，合并的时候把深度低的合并到深度高的
    int count;//连通分量个数——也就是集合的个数
public:
    UnionFind(int n);
    ~UnionFind();
    //查找x的父亲
    int Find(int x){
    	//查找时进行路径压缩
        return parent[x] == x ? x : parent[x] = Find(parent[x]);
    }
    //将 x和y 合并
    bool Union(int x,int y){
        //查找父亲
        int rx = Find(x);
        int ry = Find(y);
        if (rx == ry)
        {//父亲相同说明同一个集合
            return false;
        }
        //如果深度相同，将rx 加入 ry
        if (rank[rx] == rank[ry])
        {
            parent[rx] = ry;
            rank[ry]++;
        }else if (rank[rx] < rank[ry])
        {//否则哪个深度小，就挂在对方集合中
            parent[rx] = ry;
        }else
        {
            parent[ry] = rx;
        }
		//连通数量减一
        count --;
        return true;
    }
    int getCount(void){
        return count;
    }
};

UnionFind::UnionFind(int n)
{
    parent.resize(n);
    rank.resize(n,1);
    for (int i = 0; i < n; i++)
    {
        //初始化每个元素的集合为其本身
        parent[i] = i;
    }
    count = n;
}

UnionFind::~UnionFind()
{
}
```

#### 示例-最长连续序列

```
给定一个未排序的整数数组 nums ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。
进阶：你可以设计并实现时间复杂度为 O(n) 的解决方案吗？

示例 1：
输入：nums = [100,4,200,1,3,2]
输出：4
解释：最长数字连续序列是 [1, 2, 3, 4]。它的长度为 4。
```

![最长连续序列](F:\Typora\Picture\leetcode\最长连续序列.jpg)

```cpp
class Unionfind {
    private:
    vector<int> parent;
    vector<int> rank;
    int count;//联通数
    int maxSize;//联通集合中的最大数量
    public:
    int Find(int x){
        return parent[x] == x ? x : parent[x] = Find(parent[x]);
    }
    bool Union(int x,int y){
        int rx = Find(x);
        int ry = Find(y);
        if(rx == ry) return false;
        if(rank[rx] <= rank[ry]){
            //加入ry
            parent[rx] = ry;
        }else{
            //加入rx
            parent[ry] = rx;
        }
        //更新集群内部数量
        rank[rx] += rank[ry];
        rank[ry] = rank[rx];
        //更新最大集群数
        maxSize = max(maxSize,rank[ry]);
        return true;
    }
    int Count(void){
        return count;
    }
    int getmaxSize(void){
        return maxSize;
    }
    Unionfind(int n){
        parent.resize(n);
        rank.resize(n,1);
        for(int i = 0;i < n;i++){
            parent[i] = i;
        }
        count = n;
        maxSize = 1;
    }
};

class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        int len = nums.size();
        if(len <= 1) return len;
        map<int,int> m;//值:下标
        for(int i = 0;i < len;i++){
            if(m.find(nums[i]) == m.end()){
                m.insert(make_pair(nums[i],i));
            }
        }
        Unionfind myUnion(len);
        for(int i = 0;i < len;i++){
            //找nums[i]-1
            if(m.find(nums[i]-1) != m.end()){
                myUnion.Union(m[nums[i]],m[nums[i]-1]);
            }
            //找nums[i]+1
            if(m.find(nums[i]+1) != m.end()){
                myUnion.Union(m[nums[i]],m[nums[i]+1]);
            }
        }
        return myUnion.getmaxSize();
    }
};
```

#### 示例-被围绕的区域

```
给你一个 m x n 的矩阵 board ，由若干字符 'X' 和 'O' ，找到所有被 'X' 围绕的区域，并将这些区域里所有的 'O' 用 'X' 填充。

输入：board = [["X","X","X","X"],["X","O","O","X"],["X","X","O","X"],["X","O","X","X"]]
输出：[["X","X","X","X"],["X","X","X","X"],["X","X","X","X"],["X","O","X","X"]]
解释：被围绕的区间不会存在于边界上，换句话说，任何边界上的 'O' 都不会被填充为 'X'。 任何不在边界上，或不与边界上的 'O' 相连的 'O' 最终都会被填充为 'X'。如果两个元素在水平或垂直方向相邻，则称它们是“相连”的。s
```

![img](https://assets.leetcode.com/uploads/2021/02/19/xogrid.jpg)

> 这道题采用DFS、BFS均可解决，本次采用并查集的方法解决

```cpp
//并查集 类
class Unionfind{
private:
    vector<int> parent;
public:
    //查找父亲
    int Find(int x){
        //查找过程进行路径压缩
        return parent[x] == x ? x : parent[x] = Find(parent[x]);
    }
    //合并
    bool Union(int x,int y){
        int rx = Find(x);
        int ry = Find(y);
        if(rx == ry) return false;
        if(rx == 0){
            parent[ry] = rx;
        }else{
            parent[rx] = ry;
        }
        return true;
    }
    Unionfind(int n){
        parent.resize(n);
        for(int i = 0; i < n;i++){
            parent[i] = i;
        }
    }
};
class Solution {
public:
    int n,m;
    int dirs[4][2] = {-1,0,1,0,0,-1,0,1};
    //得到(i,j)的索引
    int getIndex(int x,int y){
        return x * m + y + 1;//为什么+1 ？ 我们将0结点作为一个虚拟结点，所有与虚拟结点相连的均是没有被X围绕的O
    }
    void solve(vector<vector<char>>& board){
        n = board.size();
        m = board[0].size();
        Unionfind myUnion(n * m + 1);
        //首先遍历board 四周，将四周的O与虚拟结点合并
        for(int i = 0;i < m;i++){
            if(board[0][i] == 'O')
                myUnion.Union(getIndex(0,i),0);
            if(board[n-1][i] == 'O')
                myUnion.Union(getIndex(n-1,i),0);
        }
        for(int i = 1;i < n-1;i++){
            if(board[i][0] == 'O')
                myUnion.Union(getIndex(i,0),0);
            if(board[i][m-1] == 'O')
                myUnion.Union(getIndex(i,m-1),0);
        }
        //遍历内部区域，当遇到O时判断其周围是否有“与虚拟结点相连”的结点，如果有的话，那么该O也是不被围绕的，将其并入
        for(int i = 1;i < n - 1;i++){
            for(int j = 1;j < m - 1;j++){
                if(board[i][j] == 'O'){
                    for(int k = 0;k < 4;k++){
                        int nx = i + dirs[k][0];
                        int ny = j + dirs[k][1];
                        if(board[nx][ny] == 'O'){
                            myUnion.Union(getIndex(i,j),getIndex(nx,ny));
                        }
                    }
                }
            }
        }
        for(int i = 0;i < n;i++){
            for(int j = 0;j < m;j++){
                if(board[i][j] == 'O'){
                    if(myUnion.Find(getIndex(i,j)) != 0){
                        board[i][j] = 'X';
                    } 
                }
                
            }
        }
    }
};
```



# 位运算



什么时候可以想到位运算？

- 搜索过程的优化

  - 比如只26个小写英文字母的字符串中每个字母可以对应整型变量的一位

    ```
    比较两个字符串中是否有相同字符 ==> 判断两个整型变量&操作是否为0 ==> 非0则有相同字符
    拼接两个字符串 ===> 两个整型变量|操作
    ```

- 运算过程效率的优化

#### 示例.串联字符串的最大长度

> 题目来源：Leetcode 1239

给定一个字符串数组 arr，字符串 s 是将 arr 某一子序列字符串连接所得的字符串，如果 s 中的每一个字符都只出现过一次，那么它就是一个可行解。请返回所有可行解 s 中最长长度。

```
输入：arr = ["un","iq","ue"]
输出：4
解释：所有可能的串联组合是 "","un","iq","ue","uniq" 和 "ique"，最大长度为 4。
```

题解:

由题知，我们需要计算可行解的长度，至于可行解具体是什么，以及可行解中各个字符的顺序我们是不用考虑的。因此构成**可行解的每个字符串均可以视作一个字符集合，且集合不含重复元素**

那么，我们可以用一个二进制数来表示该字符串的字符集合，二进制的第 i 位为 1 表示字符集合中含有第 i 个小写字母。

因此首先遍历 arr 数组，将每个字符串转化为一个整型变量并存储于数组 masks ，然后我们开始递归操作:`void dfs(int idx,int mask)`，idx表示当前要操作的元素下标，mask表示目前的可行解所对应的整型值大小。

**对于第 idx 个数，我们有两种方法：选或者不选**。如果 mask 和 masks[idx] 无公共元素，即$ mask\& masks[idx] == 0$，则可以选这个数，此时我们调用`dfs(idx+1,mask|masks[pos]) `进行递归。如果我们不选这个数，那么我们调用`dfs(idx+1,mask) `进行递归。

记 masks 的长度为 n，当 pos=n 时，计算 mask 中 1 的个数，即为可行解的长度，用其更新可行解的最长长度ans。

```java
class Solution {
    ArrayList<Integer> masks = new ArrayList<Integer>();
    int ans = Integer.MIN_VALUE;
    public void dfs(int idx,int val){
        if(idx == masks.size()){
            ans = Math.max(ans,Integer.bitCount(val));
            return ;
        }
        if((val & masks.get(idx)) == 0){
            dfs(idx+1,val | masks.get(idx));
        }
        dfs(idx+1,val);
    }
    public int maxLength(List<String> arr) {
        for(String s:arr){
            int mask = 0;
            for(int i = 0;i < s.length();i++){
                if(((1 << ((int)s.charAt(i) - 'a')) & mask) != 0){
                    mask = 0;
                    break;
                }
                mask = mask | (1 << ((int)s.charAt(i) - 'a'));
            }
            if(mask == 0) continue;
            masks.add(mask);
        }
        dfs(0,0);
        return ans;
    }
}
```





常用位运算优化方法：

- 判断整数奇(1)偶(0)性：

  ```java
  //判断是否为奇数
  boolean IsOdd(int x){
      return x&1;
  }
  ```

- 乘以或除以2的幂:

  ```java
  乘以2: x<<1;
  乘以2的n次方：x<<n;
  除以2：x>>2;
  除以2的n次方：x>>n;
  变式计算2的n次方：2<<(n-1);
  ```

- 比较两个数是否相等

  ```java
  boolean IsEqual(int a,int b)
  {
      return !(a^b);//利用异或比较，相等=>0
  }
  ```

- 计算整数x和y的平均值，可防止溢出

  ```java
  int average(int x, int y) //返回X,Y 的平均值 
  { 
  　　return (x&y)+((x^y)>>1); 
  }
  ```

- 判断第 n 位是否是 1：`x & (1<<n)`

- 将第 n 位设为 1：`y = x | (1<<n)`

- 将第 n 为设为 0：`y = x & ~(1<<n)`

- 将第 n 位的值取反：`y = x ^ (1<<n)`

- 将最右边的 1 设为 0：`y = x & (x-1)`

- 分离最右边的 1：`y = x & (-x)`

- 将最右边的 1 后面都设位 1：`y = x | (x-1)`

- 分离最右边的 0：`y = ~x & (x+1)`

- 将最右边的 0 设为 1：`y = x | (x+1)`






# 前缀和

前缀和快速求取区间和





# 线性筛、素数筛

By listing the first six prime numbers: 2, 3, 5, 7, 11, and 13, we can see that the 6th prime is 13.

What is the 10001st prime number?

```
前个质数分别是2、3、5、7、11和13。第10001个质数是多少？
```

为什么素数这么重要？？

- 许多加密方法、密码学，都是由素数产生的。

题解：

首先什么是素数？ 素数是只能被1和其本身整除，其为**素数（质数）**，否则为**合数**。

**暴力破解**：时间复杂度O(n^2)

```c
#include<stdio.h>
#include<stdbool.h>
#include<math.h>
//根据 素数的定义 判断是否是素数  
bool isPrime(unsigned int num){
    unsigned int n = sqrt(num);  //注意 1！
    for(int i = 2;i <= n;i++){
        if(num % i == 0){
            return false;
        }
    }
    return true;
}
int main(){
    int cnt = 1;
    unsigned int num = 3;
    while(cnt < 10001){
        if(isPrime(num)) cnt++;
        num+=2;
    }
    printf("%u",num);
    return 0;
}
```

> 注意1：为什么可以只遍历 2 ~ sqrt(num) 内的数呢？ 

$$
假设n为一个合数，则一定存在 n = a * b，并且a <=\sqrt(n)，b >= \sqrt(n)，因此只要我们遍历至 \sqrt(n)，就可以判断n是否为质数。
$$

**素数筛**：时间复杂度O(n*loglogn)，空间复杂度O(n)

> 核心思想：当 i 是 素数时，2 * i 、 3 * i 、...都不是素数。 即用素数去标记合数

首先定义 prime[i] = 1表示 i 为合数，prime[i] = 0，表示 i 为 素数。

```c
#define MAX_N 200000   
int prime[MAX_N + 5] = {0};  //为什么要定义这么大的范围呢???
void init_prime(){
    for(int i = 2;i <= MAX_N;i++){
        if(prime[i]) continue;
		prime[++prime[0]] = i;
        for(int j = 2 * i;j <= MAX_N;j += i){ 					 
            prime[i] = 1;
         }
    }    
}
int main(){
    init_prime();
    printf("%d\n",prime[10001]);
    return 0;
}
```

> 优化目的：减少重复标记，比如对于素数2，我们会找到合数4、6、8、10...，对于素数3，我们会找到6、9、12...，此时6、12...就重复了。

```c
//优化1：直接从 i^2开始筛
for(int j = i * i;j <= MAX_N;j += i){ 					 
    prime[i] = 1;
}
```

```c
//优化2：对于优化1的 i * i可能会溢出
for( int j = i,I = MAX_N / i;j<= I;j++){ 					 
    prime[i * j] = 1;
}
```

练习：

**请写一个程序，快速求出一个范围内（假设为10000）所有数字的最小素因子。**

```c
#define MAX_N 100
int prime[MAX_N+5] = {0};
void init_prime(void){
    for(int i =2;i<MAX_N;i++){
        //如果是合数，直接跳过
        if(prime[i]) continue;
        //如果是素数，则从此位置开始遍历其倍数（全是合数）
        for(int j = i; j <= MAX_N;j+=i){
            //如果prime[i]!=0，说明已经被标记了素因子，直接跳过
            if(prime[j]) continue;
            //标记当前合数的最小素因子为i
            prime[j] = i;
        }
    }
}
int main(){
    
    init_prime();
    for(int i = 2;i <= MAX_N;i++){
        printf("min_factor[%d] = %d\r\n",i,prime[i]);
    }
    return 0;
}
```

**请写一个程序，快速求出一个范围内（假设为10000）所有数字的最大素因子。**

```c
void init_prime(void){
    for(int i =2;i<MAX_N;i++){
        //如果是合数，直接跳过
        if(prime[i]) continue;
        //如果是素数，则从此位置开始遍历其倍数（全是合数）
        for(int j = i; j <= MAX_N;j+=i){
            //标记当前合数的最小素因子为i,最后标记的肯定是最大的素因子
            prime[j] = i;
        }
    }
}
```

**思考**：

6被标记了几次？30被标记了几次？

> 显然分布是2次和3次

如果一个数字N的素数分解式中含有m种不同的素数，N被标记了几次？

> 被标记了m次

素数筛的标记过程中出现了过多的重复标记，效率下降。

**线性筛**：时间复杂度0(n)，空间复杂度O(n)

总体思想：用一个整数M去标记合数N，其中N和M具有如下性质：

- N中最小的素数为p
- N可以表示为 p * M
- p一定小于等于M中最小的素因子  ===>  保证了M是除了N本身以外的最大因子
- 利用 M*p'(所有**小于等于M中最小素数的素数集合**)，标记N1，N2，N3...

整理了一下思路，好像就是用最后一个能标记N的M去标记N1，N2，N3...

> 比如，175应该被谁来标记，如果采用 175 = 25 * 7 来标记，就违反了第3点**[p一定小于等于M中最小的素因子]**，因为 175 = 5 * 5 * 7 = 35 * 5，即N中最小的素数为 5。那么据此以及N' = M * p'，我们可以利用35标记 70(p' = 2)、105(p'=3)、175(p'=5)。

```c
#include<stdio.h>
#define MAX_N 200000
int prime[MAX_N + 5] = {0};
void init_prime(void){
    //线性筛 N' = M * P'
    for(int i=2;i<=MAX_N;i++){// i : M
        //如果当前是素数，则存储
        if(!prime[i])prime[++prime[0]] = i;
        //遍历当前的素数表
        for(int j = 1;j <= prime[0];j++){
            //超出范围了直接退出
            if(prime[j] * i > MAX_N) break;
            //标记
            prime[prime[j] * i] = 1;//prime[j] : N'
            //保证p'不能大于M的最小素因子
            if(i % prime[j] == 0) break;
        }
    }
}
int main(){
    init_prime();
    printf("%d\r\n",prime[10001]);
    return 0;
}
```

**圆周素数**

197被称为圆周素数，因为将它逐位旋转所得到的数：197/971和719都是素数。

小于100的圆周素数有十三个：2、3、5、7、11、13、17、31、37、71、73、79和97。

小于一百万的圆周素数有多少个？

```c
#include<stdio.h>
#include<inttypes.h>
#include<stdbool.h>
#include<math.h>
#define MAX_N 1000000

uint32_t prime[MAX_N] = {0};
int isprime[MAX_N] = {0,1,0};

int getDigit(uint32_t num){
    if(num == 0) return 1;
    return floor(log10(num)) + 1;   
}

bool isCircularPrime(uint32_t num){
    int digit = getDigit(num);
    int m = pow(10,digit-1); 
    for(int i = 1;i < digit;i++){
        num = num / m + (num % m) * 10;   
        if(isprime[num]) return false;
    }    return true;
}

void initPrime(){
    for(uint32_t i = 2;i<=MAX_N;i++){
        //如果是素数
        if(!isprime[i]) prime[++prime[0]]=i;
        for(uint32_t j=1;j<=prime[0];j++){
            if(prime[j] * i > MAX_N) break;
            isprime[prime[j] * i] = 1;
            if(i % prime[j] == 0) break;
        }
    }
}

int main(){
    int cnt = 0;
    initPrime();
    for(uint32_t i = 1;i <= prime[0];i++){
        if(isCircularPrime(prime[i])) cnt++;
    }
    printf("%d\r\n",cnt);
    return 0;
}
```



# 快速幂

实现 pow(*x*, *n*) ，即计算 x 的 n 次幂函数（即，x^n）。

**暴力解法**：

通过循环将 n 个 x 乘起来，依次求:x^1,x^2,x^3，...，x^n-1,x^n，时间复杂度为O(n)

**快速幂**：

> 利用十进制数字 n 的二进制表示，可进行快速幂次运算

**二进制角度理解**：

设十进制数 n 的二进制表示为 b1b2b3b4...bm，其中 $ b_i $ 为0或1，$ i∈[1,m] $

则十进制 $n = 1b_1+2b_2+4b_3+...+2^(m-1)b_m$ ，从而得到 $x^n = $ x^1b1 x^2b2 x^4b4  ... x^(2^m-1)bm 

<img src="F:\Typora\LeetCode\pic\2021-06-11_231352.jpg" alt="2021-06-11_231352" style="zoom: 80%;" />

**分治角度理解**：

<img src="F:\Typora\LeetCode\pic\2021-06-12_112140.jpg" alt="2021-06-12_112140" style="zoom:80%;" />

```cpp
double myPow(double x,int n){
    if(x == 0.0) return 0.0;
    double res = 1;
    if(n<0) {
        n = -n;
        x = 1 /x;
    }
    while(n){
        //判断n是否为奇数
        if(n & 1) res *= x;
        x *=x;
        n >>= 1; //向下整除2
    }
    return res;
}
```









# 其它常用

### 找 长度为n的序列的所有子序列

> 需要找到一个长度为n的序列a的所有子序列，代码框架：

```cpp
vector<int> t;
void dfs(int cur, int n) {
    if (cur == n) {
        // 记录答案
        // ...
        return;
    }
    // 考虑选择当前位置
    t.push_back(cur);
    dfs(cur + 1, n, k);
    t.pop_back();
    // 考虑不选择当前位置
    dfs(cur + 1, n, k);
}
```



### 判断 完全平方数

```c
//判断一个数是否是完全平方数
bool isSqrt(int n){
    int tmp = (int)(sqrt(n) + 0.5);
    if(tmp * tmp != n) return false;
	return false;
}
```

### 判断 回文数

```c
//判断一个十进制数是否是回文数
bool isPlalindrome(int n){
    int tmp = 0,row = n;
    while(n){
        tmp = tmp * 10 + n % 10;
        n /= 10;
    }
    return row == tmp;
}
//判断一个x进制数是否是回文数
bool isXPlalindrome(int n,int x){
    int tmp = 0,row = n;
    while(n){
        tmp = tmp * x + n % x;
        n /= x;
    }
    return row == tmp;
}
```

### 判断 二进制中0和1的个数

#### 求整数二进制中1个数

```cpp
int get1Nums(int n){
    int cnt = 0;
    while(n){
        cnt++;
        n = n & (n-1);
    }
    return cnt;
}
```

#### 求整数二进制中0个数

```cpp
int get0Nums(int n){
	int cnt = 0;
    while(n + 1){
        cnt++;
        n = n | (n + 1);
    }
    return cnt;
}
```

### 最大公约数

#### 辗转相除法（欧几里得法）

```c
int gcd(int a,int b){//前提 a大，b小
    if(a < b) swap(a,b);
    if(a % b == 0)
        return b;
    return gcd(b,a % b);
}
```



### 大整数运算

>  均采用模拟的方法

#### 加法

```
求以下一百个50位数之和的前十位数字:
37107287533902102798797998220837590246510135740250 46376937677490009712648124896970078050417018260538 74324986199524741059474233309513058123726617309629 91942213363574161572522430563301811072406154908250 23067588207539346171171980310421047513778063246676 89261670696623633820136378418383684178734361726757 28112879812849979408065481931592621691275889832738 44274228917432520321923589422876796487670272189318...
//详细见欧拉计划 13题
```

要点：

- 字符串读入：长度过长，使用基础数据类型存储会溢出
- 倒序存储：方便运算
  - 假设原数字 1246917 ，则将其存在起始下标为1的字符数组中 7196421，下标0处存放其长度 7
- 运算：先相加 再进行进位操作
  - 进位操作
  - 数长可能会变长

```cpp
#include<stdio.h>
#include<string.h>
char str1[100] = {0};
char str2[100] = {0};
int num1[100] = {0};
int num2[100] = {0};

//num1 + num2 结果存放于 num2中
void add(int * num1,int * num2,int len){
    for(int i=1;i <= len;i++){
        num2[i] += num1[i];
        if(num2[i] > 9){
            num2[i+1] += num2[i] / 10;
            num2[i] = num2[i] % 10;
            //当i == len 并且 num2[i] > 9说明长度改变
            if(i == len){
                num2[0]++;
            }
        }
    }
}

int main(){
    int maxlen;
    scanf("%s",str1);
    int len1 = strlen(str1);
    //技巧1：num数组下标0位置存储字符串长度
    num1[0] = len1;
    //倒序存储
    for(int i = 1,j = num1[0];i < num1[0];i++,j--){
        num1[j] = str1[i-1] - '0';
    }
    //遍历其他字符串
    for(int i = 0;i < 99;i++){
        scanf("%s",str2);
        int len2 = strlen(str2);
        num2[0] = len2;
        for(int i = 1,j = num2[0];i < num2[0];i++,j--){
            num2[j] = str2[i-1] - '0';
        }
        //获取较大数字长度
        maxlen = num1[0] > num2[0] ? num1[0] : num2[0];
        add(num2,num1,maxlen);
    }
    for(int i = num1[0],j = 0;j < 10;j++,i--){
        printf("%d",num1[i]);
    }    
    printf("\n");
    return 0;
}
```



#### 乘法

要点：模拟 乘法运算

- 与大整数加法相似，如字符串读入数据，倒序存储
- 长度问题: m位数 乘 n位数，结果长度最小m+n-1，最大m+n
- 相乘结果存放位置：第x位和第y位相乘结果存放在数组第x+y-1处下标



```cpp

```



#### 除法

要点：

- **待除数**和被除数的比较
  - 大于则转换为减法
  - 小于补结果补0并更新待除数
- 剩下的是余数



#### 减法

要点：

- 借位
- 结果为负数



### Sort 排序

`#include<algorithm>`

```cpp
//排序基本类型
bool cmp(const int &a,const int &b){
    return a > b; //不能写≥、≤等语句！
}
sort(nums.begin(),nums.end(),cmp);
```

```cpp
//排序自定义类型
//方法1：自定义cmp函数
struct node{
    int x,y;
};

bool cmpnode(const node &a,const node &b){
    if(a.x == b.x){
        return a.y < b.y;
    }
    return a.x > b.x;
}

sort(nodes.begin(),nodes.end(),cmpnode);

//方法2 重载
struct node{
    int x,y;
    bool operator< (const node &b) const {
        return this->x > b.x;
    }
};

sort(nodes.begin(),nodes.end());
```

