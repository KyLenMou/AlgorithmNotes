# 算法笔记(AlgorithmNotes)

>  内容均为自学时的笔记，学习内容来源于为[Acwing的算法基础课](https://www.acwing.com/activity/content/introduction/11/)
>
>  本人喜欢下标从1开始，以下笔记基本都是按下标为1分析的

## 基础算法(BasicAlgorithm)

### 快速排序(QuickSort)

>  算法步骤

1. 对于一个区间q[N]，找中间的数k(q[mid])作为基准值
2. 前后两个指针i,j往中间扫描
3. i往后扫描，直到q[i] > k
4. j往前扫描，直到q[j] < k
5. 此时，q[i]比基准值k大，q[j]比基准值k小，交换q[i]和q[j]
6. 重复3、4、5步，直到i和j相遇
7. 此时他们的相遇点左边的元素全部小于基准值k，右边的元素全部大于基准值k
8. 对这两部分区间递归操作前面所有的步骤，递归的终止条件为：当区间内的元素只有一个时，无需排序，即终止条件
9. 排序完成

> 注意事项（边界问题）

1. 对于上面的第3和第4步操作，无论怎样都应该先移动再判断，否则就会出现死循环的状况：q[i]和基准值k相同时
2. 对于上面这种做法，会跳过首尾元素的判断，所以前后两个指针应先往外退一个，否则，举个例子，当区间是[3,6]时，一开始q[i]是3，q[j]是6，然后直接移动，没有判断，i往后移，j往前移，此时他们相遇过了，交换两者，区间变成了[6,3]，这样就不对了。如果一开始往外退了一个，那么他们先移动，然后判断，之后，i不用移动，j再移动一个，此时i和j相遇，他们对应的值都是3，交不交换都一样，这样才正确
3. i和j每次相遇后，他们可能会相等，可能会i比j大1，两区间如何划分取决于基准值如何取，在这里我选择只用q[l+r>>1]这一种基准值，区间划分时用j和j+1划分，具体分析见[Acwing](https://www.acwing.com/solution/content/16777/)

> C++

```c++
void quick_sort(int q[], int l, int r)
{
    if(l >= r) return; //终止条件
    int k = q[l + r >> 1], i = l - 1, j = r + 1; //取中间值为基准值，i和j为左右端点往外一格
    while(i < j)
    {
        i++; //不论怎样都先移动，防止后面死循环
        j--;
        while(q[i] < k) i++; //判断并移动
        while(q[j] > k) j--;
        if(i <= j) swap(q[i],q[j]); //移动完后，如果还没相遇就交换
    }
    //while循环结束，标志着两个指针相遇
  	//对两个区间进行递归
    quick_sort(q,l,j);
    quick_sort(q,j+1,r); 
}
```



### 归并排序(MergeSort)

> 算法步骤

1. 对于一个区间q，把其分成两半
2. 把分成两半的区间递归操作上面的步骤，递归的终止条件是区间内只有一个数
3. 每次递归把两个区间合并，变成一个有序的区间
   1. 两个区间都是有序的，每次选择两个区间中最小的数放到一个区间内
   2. 每次放完后，因为原来区间是有序的，放完之后的区间仍保持有序
   3. 继续第一步，直到两个区间合并为一个新的有序区间 
4. 递归完后排序完成

> 注意事项（边界问题）

1. 把区间分为两半，按mid = l + r >> 1（l+r除以2向下取整）来算，第一个区间为[l,mid]，第二个区间为[mid+1,r]

> C++

```c++
int temp[N]; //临时存放合并的区间
void merge_sort(int q[], int l, int r)
{
    if(l >= r) return; //区间内只有一个数时终止递归
    int mid = l + r >> 1, i = l, j = mid + 1; //把区间分为两半
    merge_sort(q,l,mid); //递归处理两个区间
    merge_sort(q,mid+1,r);
    int k = 1;
    while(i <= mid && j <= r) //合并区间
    {
        if(q[i] <= q[j]) temp[k++] = q[i++];
        else temp[k++] = q[j++];
    }
    while(i <= mid) temp[k++] = q[i++];
    while(j <= r) temp[k++] = q[j++];
    for(int i = l, j = 1; i <= r; i++,j++) q[i] = temp[j]; //把临时合并的区间还给原来的区间
}
```

#### 应用：逆序对的数量

> 算法步骤

1. 根据归并排序，当递归到最后一层时，每个区间只有一个元素，每两对区间可以直接判断是否为逆序对，返回逆序对的数量
2. 他们合并之后变成了一个有序区间并且和另一个也合并完的有序区间进行逆序对判断，返回逆序对的数量
   1. 对于两个区间[l,mid]和[mid+1,r]，如果第二个区间的一个数q[j]小于第一个区间的数q[i]，因为有序性，i和i后面的数都可以和q[j]组成逆序对
   2. 巧妙地运用了归并排序过程中区间的有序性，在两个区间内元素顺序对判断是否是逆序对无关
3. 层层往上递归，最后得出逆序对的数量

> C++

```c++
//原题数据范围为1e5，会爆int，所以要开ll
int q[N],temp[N];
ll merge_sort(int l, int r)
{
    if(l >= r) return 0; //区间内只有一个元素时，没法组成逆序对，返回0
    int mid = l + r >> 1, i = l, j = mid + 1;
    ll res = merge_sort(l,mid) + merge_sort(mid+1,r); //记录子区间的逆序对数量
    int k = 1;
    while(i <= mid && j <= r)
    {
        if(q[i] > q[j]) //满足逆序对的情况
        {
            res += mid - i + 1; //加上逆序对的数量
            temp[k++] = q[j++];
        }
        else temp[k++] = q[i++];
    }
    while(i <= mid) temp[k++] = q[i++]; //扫尾
    while(j <= r) temp[k++] = q[j++];
    for(int i = l, j = 1; i <= r; i++,j++) q[i] = temp[j];
    return res; //返回逆序对数量
}
```

###  二分(BinarySearch)



## 数据结构(DataStructure)

### KMP

> 算法思路 

1. 核心是求next数组，匹配时根据next数组匹配。对于next数组：对于一个长度为n的字符串s，next[i]表示字符串从下标1到下标i即s[1,i]中，最长的公共前后缀长度是多少，如下图，假设最长公共前后缀长度为3![image-20230324160928551](AlgorithmNotes.assets/image-20230324160928551.png)
2. 假设我们已经得到了next数组，那么接下来我们就可以匹配了，我们开始遍历一个字符串str，用上面的字符串s匹配
   1. 我们用i指针遍历str，j指针遍历s
      ![image-20230324164616531](AlgorithmNotes.assets/image-20230324164616531.png)
   2. 每次判断str[i]和s[j+1]是否相同，如果相同j向后一位，否则j移动到next[j]（即对应的最长公共前缀的位置） 
   3. 当j遍历完s时匹配成功
3. 求next数组：跟匹配的原理一样，用s来匹配s，i指针要从下标2开始，因为下标1的next值为0，一个字符不能组成公共前后缀。 每次遍历时next[i]的值即为j的位置
   ![image-20230324164508942](AlgorithmNotes.assets/image-20230324164508942.png)
4. 具体原理请见[B站视频](https://www.bilibili.com/video/BV1AY4y157yL)和[y总讲解](https://www.acwing.com/video/259/)，本人目前无法讲述得很清楚

> C++

```c++
int ne[N]; //next数组
char s[N],str[N];
int n,m; //n为s的长度，m为str的长度

void get_next() //得到next数组
{
    for(int i = 2, j = 0; i <= n; i++)
    {
        while(j != 0 && s[i] != s[j+1]) j = ne[j];
        if(s[i] == s[j+1]) j++;
        ne[i] = j;
    }
}

void kmp() //匹配
{
    for(int i = 1, j = 0; i <= m; i++)
    {
        while(j != 0 && str[i] != s[j+1]) j = ne[j];
        if(str[i] == s[j+1]) j++;
        if(j == n) cout << "匹配成功" << endl;
    }
}
```

> 一个人能走的多远不在于他在顺境时能走的多快，而在于他在逆境时多久能找到曾经的自己。	——KMP

## 图论(GraphTheory)

### 搜索

#### 深度优先搜索(DFS)

#### 宽度优先搜索(BFS)

### 最短路径算法

![image-20230323165331690](AlgorithmNotes.assets/image-20230323165331690.png)

#### 迪杰斯特拉(Dijkstra)

>  算法思路

1. （单源最短路，找的是一个点到其他点的最短距离，接下来都以1为起点。）创建一个dist数组，用来存1到各个点的最短距离（初始状态全为无穷大，即1到所有点的距离为无穷大）；创建一个bool类型的st数组，用来判断一个点是否已确定1到该点的最短距离（初始状态全为false，即都没有确定最短距离）。当然需要一个存各个点之间的距离的空间，稠密图用邻接矩阵，稀疏图用邻接表
2. 算法步骤主要是一个循环：
   1. 找出未标记的距离最短的点
   2. 标记距离最短的点
   3. 用这个点更新到其他点的距离
   4. 重复123，直到所有点被确认了1到该点的最短距离
3. 第一步很特殊，我们要算1到其他点的距离，此时1到1的距离最短，为0，我们在dist数组给1赋值为0；并且没有其他点到1的距离比1到1的距离更短，我们在st数组给1赋值为true即1已经确定最短距离
4. 此时我们已经做了 *<u>1.找出未标记的距离最短的点</u>* 和 *<u>2.标记距离最短的点</u>*。接下来我们要  *<u>3.用这个点更新到其他点的距离</u>*，即用1更新1到其他点的距离
5. 更新完后，我们又要 *<u>1.找出未标记的距离最短的点</u>* 了，我们在刚才用1更新的所有点中，*<u>1.找出未标记的距离最短的点</u>* 并且 *<u>2.标记距离最短的点</u>* 接着 *<u>3.用这个点更新到其他点的距离</u>*，如此往复，*<u>直到所有点被确认了1到该点的最短距离</u>*。
6. 该算法基于贪心思想，可以证明在这样操作后一定能得出1到所有点的最短距离

##### 朴素版

1. 朴素版时间复杂度和空间复杂度都很高，所以仅适用于数据范围较小的稠密图

> C++

```c++
int g[N][N]; //邻接矩阵，主函数里要初始化为0x3f3f3f3f
int dist[N]; 
bool st[N];

void dijkstra()
{
    memset(dist,0x3f,sizeof dist);
    dist[1] = 0;
    
    for(int i = 1; i <= n; i++)	
    {
        int t = -1;
        //1.找出未标记的距离最短的点
        for(int j = 1; j <= n; j++)	
        {
            if(!st[j] && (t == -1 || dist[j] < dist[t]))
            {
                t = j;
            }
        }
        //2.标记距离最短的点
        st[t] = true;
        //3.用这个点更新到其他点的距离
        for(int j = 1; j <= n; j++)
        {
            dist[j] = min(dist[j],dist[t] + g[t][j]);
        }
    }
}
```

##### 堆优化版

1. 朴素版每次更新距离时不管两点之间是否有边，都要做min操作，这是没有意义的。所以我们进行存储优化：使用邻接表，每次只需更新相邻的点而无需更新所有的点，减少复杂度
2. 朴素版每次找出未标记的距离最短的点也很麻烦，要遍历所有未标记的点来找出最小值。所以我们使用一个数据结构：小根堆来储存，每次都可以以O(1)的复杂度直接得到未标记的距离最短的点；我们添加未标记的距离最短的点时，复杂度为O(logn)
3. 在使用堆优化后，我们可以把去掉朴素版中没有意义的操作与存储，且把复杂度降低到了O(nlogn)

```c++
#define x first
#define y second
typedef pair<int,int> PII;
int h[N],e[N],w[N],ne[N],idx = 0; //邻接表，主函数里需要把h初始化为-1
int dist[N];
int n,m;
bool st[N];
priority_queue<PII,vector<PII>,greater<PII>> heap; 

void add(int a,int b,int c) //建一条a指向b权重为c的边
{
    e[idx] = b;
    w[idx] = c;
    ne[idx] = h[a];
    h[a] = idx++;
}

void dijkstra()
{
    memset(dist,0x3f,sizeof dist);
    dist[1] = 0; //添加1
    heap.push({0,1}); //把1加入到小根堆中，1到1的距离为0
    
    while(heap.size())
    {
        auto t = heap.top();
        heap.pop();
        //t.x表示1号点到t.y的距离 t.y表示点的编号
        for(int i = h[t.y]; i != -1; i = ne[i])
        {
            int j = e[i]; //j表示与t.y相邻的点
            if(dist[j] > dist[t.y] + w[i])
            {
                dist[j] = dist[t.y] + w[i];
                heap.push({dist[j],j});
            }	
        }
    }
}
```

> 图解

![image-20230323203128341](AlgorithmNotes.assets/image-20230323203128341.png)

---

***Made By KyLen***

​	
