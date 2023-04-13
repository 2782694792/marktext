## 1. ListNode 线性链表

### 1.1 类定义

```cpp
class operateList
{
public:
    /*创建链表*/
    void createList(ListNode*, int);
    /*输出链表*/
    void outputList(ListNode *);
    /*链表长度*/
    int lengthOfList(ListNode *);
    /*链表元素取值*/
    int getValue(LinkList *);
    /*定位插入元素*/
    void insertList(ListNode*,int,int)
    /*删除定位元素*/
    void deleteE(ListNode*,int)
    /*删除链表元素*/
    void deleteList(ListNode *);
}
```

### 1.2 结构体

```cpp
//定义表结点的数据结构：数据+指针
struct ListNode{
    int val;
    ListNode * next;
}
```

### 1.3 创建链表

```cpp
void operateList::createList(ListNode * head,int n)
{
    int i = 0;
    //避免破坏头指针
    ListNode * phead = head;
    //插入 n-1 个元素
    for(i = 1;i < n;i++)
    {
        //动态创建新的链表节点并初始化
        ListNode * node = new ListNode;
        node->val = i;
        node->next = NULL;
        //辅助指针先是把新创建的节点接到链表的结尾
        phead->next = node;
        //辅助指针后移指向最新的节点
        phead = node;
        //delete node;    此处不能释放，phead = node，是链表的最后一个节点
    }
    //head = head->next;    头指针指向第一个元素
}
```

### 1.4 输出链表

```cpp
void operateList::outputList(ListNode * linkList)
{
    if(linkList == NULL || linkList->next == NULL)
    {
        //链表为空
        return;
    }
    else
    {
        LinkNode* p = linkList;
        while(p->next != NULL)
        {
            p = p->next;
            cout << p->value << " ";
        }
    }
}
```

### 1.5 链表长度

```cpp
int operateList::lengthOfList(ListNode * linkList,int n)
{
    if(linkList == NULL || linkList->next == NULL)
    {
        //链表为空
        return 0;
    }
    else
    {
        int count = 0;
        LinkNode* p = linkList;
        while(p->next != NULL)
        {
            p = p->next;
            count ++;
        }
        return count;
    }
}
```

### 1.6 链表元素取值

```cpp
int operateList::getValue(ListNode * linkList,int index)
{
    if(index <= lengthOfList(linkList))
    {
        LinkList *p = linkList;
        //定位到索引节点
        for(int i = 0;i < index;i ++)
        {
            p = p->next;
        }
        return p->value;
    }
    else
    {
        //索引越界
        return -1;
    }
}
```

### 1.7 定位插入元素

```cpp
void operateList::insertList(ListNode* linkList,int site,int e)
{
    if(site <= lengthOfList(linkList))
    {
        LinkList *p = linkList;
        //定位到索引节点
        for(int i = 0;i < site;i ++)
        {
            p = p->next;
        }
        ListNode* pTemp = new ListNode;
        pTemp->value = e;
        //辅助指针连接链表后的所有节点
        pTemp->next = p->next;
        //链表连接辅助指针
        p->next = pTemp;
        cout << "插入元素成功";
    }
    else
    {
        //索引越界
        cout << "索引越界";
        return;
    }
} 
```

### 1.8 删除定位节点

```cpp
void operateList::deleteE(ListNode * linkList,int site)
{
    if(site <= lengthOfList(linkList))
    {
        LinkList *p = linkList;
        //定位到索引节点
        for(int i = 0;i < index;i ++)
        {
            p = p->next;
        }
        ListNode* pTemp = new ListNode;
        pTemp = p->next;
        p->next = pTemp->next;
        delete pTemp;
        cout << "删除元素成功";
    }
    else
    {
        //索引越界
        return -1;
    }
}
```

### 1.9 删除链表元素

```cpp
void operateList::deleteList(ListNode * linkList)
{
    //头节点
    ListNode* p = linkList->next;
    while(p != NULL)
    {
        ListNode* pTemp = p;
        p = p->next;
        //删除下一节点
        delete pTemp;
    }
    //头节点指向空元素
    linkList->next = NULL;
    cout << "删除链表成功";
}
```

## 2. 树

### 2.1 二叉搜索树（BST）

#### 2.1.1 特性

1. 左子树上的所有结点（不为空）的值均小于或等于它的根结点的值；

2. 右子树上的所有结点（不为空）的值均大于或等于它的根结点的值；

3. 左、右子树也分别为二叉搜索树；

4. 没有键值相等的结点；

5. 每个结点。

#### 2.2.2 查找

在二叉搜索树 T 中查找 x ：

- 若 T 是空树，则查找失败；

- 若 T 等于 x 的根结点的数据域的值，则查找成功；

- 若 T 小于 x 的根结点的数据域的值，则搜索左子树，再判断；

- 若 T 大于 x 的根结点的数据域的值，则搜索右子树。

<img src="https://img-blog.csdn.net/20180305152022990?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2h1ZG9uZ2ZhbmcyMDE1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" title="" alt="这里写图片描述" data-align="center">

#### 2.2.3 构造

从有序数组中构造一个二叉搜索树：

二分法进行中位数查找，以中位数建立根节点，再进行左右两边进行左右子树的根结点建立，直到无下一个中位数：

<img src="https://img-blog.csdn.net/20180305152218175?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2h1ZG9uZ2ZhbmcyMDE1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" title="" alt="这里写图片描述" data-align="center">

#### 2.2.4 插入

向一个二叉搜索树 T 中插入一个结点n ：

- 若 T 是空树，则将 n 所指节点作为根结点插入；

- 若 T->data 等于 x 的根结点的数据域的值，则返回；

- 若 T->data 小于 x 的根结点的数据域的值，则将 n 所指节点插入到左子树中；

- 若 T->data 大于 x 的根结点的数据域的值，则将 n 所指节点插入到右子树中；

- （新插入节点总是叶子结点）

<img src="https://img-blog.csdn.net/20180305152333430?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2h1ZG9uZ2ZhbmcyMDE1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" title="" alt="这里写图片描述" data-align="center">

#### 2.2.5 删除

从一个二叉搜索树 T 中删除一个结点N：

- 若 N 没有孩子结点，则直接删除，并修改父结点，用 NULL 替换 N；

- 若 N 只有一个孩子结点H，则删除的同时，将 H 提升到 N 的位置替换 N；

- 若 N 有两个孩子结点HL 和 HR，则查找 N 的后继 Y，且 Y 一定在 N 的右子树中，Y 替换 N。

<img src="file:///D:/Download/MarkText/workspace/image/2022-09-16-19-25-55-image.png" title="" alt="" data-align="center">

> 结点 45 只有一个左孩子 23，用 23 替换 45；
> 
> 删除结点 17，结点 17 有后继（以 45 为根节点的子树，以 45 的左子树 23 进行判断，
> 
> 若左子树结点 X 大于结点 17 且小于结点 17 的左子树 ，则上升 X 的位置到结点 17 进行替换；
> 
> 若 X 小于结点 17 的左子树节点 L0，则 X 替换 L0；
> 
> 若 X 还有左子树结点 XL，则进行 XL 的判断。

## 3. 图

    图（Graph）是一种比较复杂的非线性数据结构，由顶点的有穷非空集合和顶点之间边的集合组成，通常表示为：G(V, E)，其中，

> G 表示一个图，V 是图 G 中顶点的集合，E 是图 G 中边的集合。

    图，分为有向图和无向图。有些图的边或弧具有与它相关的数值，这种与图的边或弧相关的数值叫做权（Weight），这是一个求最小生成树的关键值。

### 3.1 无向图

    如果图中任意两个顶点之间的边都是无向边，则称该图为无向图（Undirected graphs）。

> 相关概念：顶点、边（权重）

<img src="file:///D:/Download/MarkText/workspace/image/2022-10-28-20-19-36-image.png" title="" alt="" data-align="center">

### 3.2 有向图

    如果图中任意两个顶点之间的边都是有向边，则称该图为有向图（Directed graphs）。

> 相关概念：顶点、弧（弧头、弧尾 出度、入度）（权重）

    通过节点的属性，对节点的数值、访问值进行类的构造和初始化：

```cpp
class Node
{
public:
    /// @brief 数据
    char m_cData;
    /// @brief 是否访问
    bool m_bVisited;

public:
    /// @brief 节点：数据+可访问
    /// @param data 节点值
    Node(char data = 0)
    {
        m_cData = data;
        m_bVisited = false;
    }

    /// @brief 当前节点数据与访问控制
    /// @param node
    /// @return 返回详细的节点
    Node operator=(const Node &node)
    {
        if (this == &node)
        {
            return *this;
        }
        this->m_cData = node.m_cData;
        this->m_bVisited = node.m_bVisited;
        return *this;
    }

    /// @brief 指向当前节点
    /// @param node 当前目标节点
    Node(const Node &node)
    {
        if (this == &node)
        {
            return;
        }
        *this = node;
    }
};
```

    明确了边的属性以及两节点之间的连接关系，明确存在连接起点、连接终点、权重以及访问值，从而对边进行类构造：

```cpp
class Edge
{
public:
    // 头顶点
    int m_iNodeIndexA;
    // 尾顶点
    int m_iNodeIndexB;
    // 权重
    int m_iWeightValue;
    // 是否被选中
    bool m_bSelected;

public:
    Edge(int nodeIndexA = 0, int nodeIndexB = 0 
        , int weightValue = 0) : m_iNodeIndexA(nodeIndexA)
        , m_iNodeIndexB(nodeIndexB), m_iWeightValue(weightValue)
        , m_bSelected(false)
    {
    };

    Edge(const Edge &edge)
    {
        if (this == &edge)
        {
            return;
        }
        *this = edge;
    };

    Edge &Edge::operator=(const Edge &edge)
    {
        if (this == &edge)
        {
            return *this;
        }
        this->m_iNodeIndexA = edge.m_iNodeIndexA;
        this->m_iNodeIndexB = edge.m_iNodeIndexB;
        this->m_iWeightValue = edge.m_iWeightValue;
        this->m_bSelected = edge.m_bSelected;
        return *this;
    }
};
```

<img src="file:///D:/Download/MarkText/workspace/image/2022-10-28-20-21-11-image.png" title="" alt="" data-align="center">

### 3.3 图的存储

    图的存储结构要存储图中的各个顶点本身的信息，以及存储顶点与顶点之间的关系，比较复杂。图的存储结构有邻接矩阵、邻接表、十字链表、邻接多重表等。

    进行节点和边的构造存储，以图的形式对两个节点之间进行矩阵构造，通过节点的总数量、节点集合、矩阵、：

```cpp
class CMap
{
private:
    // 顶点总数
    int m_iCapacity;
    // 当前顶点数量
    int m_iNodeCount;
    // 顶点集合
    Node *m_pNodeArray;
    // 邻接矩阵
    int *m_pMatrix;
    // 最小生成树边集合
    Edge *m_pEdgeArray; 

public:
    /// @brief 构造邻接矩阵
    /// @param iCapacity 顶点总数
    CMap(int iCapacity)
    {
        m_iCapacity = iCapacity;
        m_iNodeCount = 0;
        m_pNodeArray = new Node[m_iCapacity];
        m_pMatrix = new int[m_iCapacity * m_iCapacity];
        memset(m_pMatrix, 0, m_iCapacity * m_iCapacity * sizeof(int));
        m_pEdgeArray = new Edge[m_iCapacity - 1];
    }
    ~CMap()
    {
        delete[] m_pNodeArray;
        delete[] m_pMatrix;
        // delete[] m_pEdgeArray;
    }
}
```

#### 3.3.1 邻接矩阵

![](D:\Download\MarkText\workspace\image\2022-10-28-20-37-37-image.png)

### 3.4 图的遍历

    图的遍历思想是从指定一个顶点出发，开始访问其他顶点，不能重复访问（每个顶点只能访问一次），直至所有点被访问。

#### 3.4.1 深度优先遍历

（depth_first_search）思想：

> 从指定的第一个点开始，沿着向下的定点不重复的一直遍历下去，若走到尽头，退到上一个顶点，寻找附近有没有顶点，有而且不重复的话，接着遍历，否则退到上一个顶点。

```cpp
void depthFirstTraverse(int index)
    {
        // 当前访问
        cout << m_pNodeArray[index].m_cData << "  ";

        m_pNodeArray[index].m_bVisited = true;

        int val = 0;
        // 节点遍历
        for (int i = 0; i < m_iCapacity; i++)
        {
            // 获取权重
            getValueFromMatrix(index, i, val);
            if (0 != val)
            {
                // 绕开已经访问
                if (m_pNodeArray[i].m_bVisited)
                {
                    continue;
                }
                // 递归深度
                depthFirstTraverse(i);
            }
            else
            {
                continue;
            }
        }
    } 
```

#### 3.4.2 广度优先遍历

（breadth_first_search）思想：

> 从指定的第一点开始，先寻找跟它直接相连的所有顶点，然后继续这几个顶点再次深入，每次搜寻的都是同一级别的。

```cpp
void breadthFirstTraverse(int index)
    {
        // 输出首个访问点
        cout << m_pNodeArray[index].m_cData << "  ";
        m_pNodeArray[index].m_bVisited = true;

        vector<int> curVec;
        curVec.push_back(index);

        breadthFirstTraverseImpl(curVec);
    } 

/// 实现
void breadthFirstTraverseImpl(vector<int> preVec)
    {
        int val = 0;
        vector<int> curVec;
        // 1
        for (int i = 0; i < (int)preVec.size(); i++)
        {
            // 6
            for (int j = 0; j < m_iCapacity; j++)
            {
                // 获取权重 : 行、列、权重值
                getValueFromMatrix(preVec[i], j, val);
                if (0 != val)
                {
                    // 遍历未访问过的节点
                    // fixme : 访问设置：访问过、权重值、不超过最后一个节点
                    if (m_pNodeArray[i].m_bVisited)
                    {
                        continue;
                    }
                    // 遍历数据
                    cout << m_pNodeArray[i].m_cData << "  ";
                    // 节点确定访问
                    m_pNodeArray[i].m_bVisited = true;
                    // 存入当前容器，进行判断，
                    curVec.push_back(j);
                }
                else
                {
                    continue;
                }
            }
        }

        if (curVec.empty())
        {
            return;
        }
        else
        {
            breadthFirstTraverseImpl(curVec);
        }
    }
```

### 3.5 最小生成树

<img src="file:///D:/Download/MarkText/workspace/image/2022-11-10-10-42-39-image.png" title="" alt="" data-align="center">

#### 3.5.1 普利姆（Prim）算法

    思路：   

> 首先，从图中的一个起点 a 开始，把 a 加入 U 集合，
> 
> 然后，寻找从与 a 有关联的边中，权重最小的那条边并且该边的终点 b 在顶点集合：（V-U）中，我们也把 b 加入到集合 U 中，并且输出边（a，b）的信息，这样我们的集合 U 就有：{a, b}，
> 
> 然后，我们寻找与 a 关联和 b 关联的边中，权重最小的那条边并且该边的终点在集合：（V-U）中，我们把 c 加入到集合 U 中，并且输出对应的那条边的信息，这样我们的集合 U 就有：{a, b, c} 这三个元素了，
> 
> 以此类推，直到所有顶点都加入到了集合 U。

```cpp
void primTree(int index)
    {
        // 边总数
        int iEdgeCount = 0;
        // 待选边
        vector<Edge> edgeVec;

        // 权重
        int val;

        // 从传入点开始
        vector<int> nodeIndexVec;
        nodeIndexVec.push_back(index);

        // 循环遍历：直到所有边遍历完成
        while (iEdgeCount < m_iCapacity - 1)
        {
            // 查找符合要求关联边（权重不为 0 且目标点未被访问）
            int row = nodeIndexVec.back();
            // 确定起点
            std::cout << m_pNodeArray[row].m_cData << std::endl;
            m_pNodeArray[row].m_bVisited = true;
            // 取值所有边
            for (int i = 0; i < m_iCapacity; i++)
            {
                getValueFromMatrix(row, i, val);
                if (val == 0)
                {
                    continue;
                }
                if (m_pNodeArray[i].m_bVisited)
                {
                    continue;
                }
                Edge edge(row, i, val);
                edgeVec.push_back(edge);
            }

            // 边集合中取最小
            int ret = getMinEdge(edgeVec);
            if (-1 != ret)
            {
                edgeVec[ret].m_bSelected = true;
                m_pEdgeArray[iEdgeCount] = edgeVec[ret];
                // 确定双向连接点和权重
                cout << m_pNodeArray[m_pEdgeArray[iEdgeCount].m_iNodeIndexA].m_cData << " - ";
                cout << m_pNodeArray[m_pEdgeArray[iEdgeCount].m_iNodeIndexB].m_cData << " (";
                cout << m_pEdgeArray[iEdgeCount].m_iWeightValue << ") " << endl;
                iEdgeCount++;
                int iNodeIndex = edgeVec[ret].m_iNodeIndexB;
                // 双向设置已访问
                m_pNodeArray[iNodeIndex].m_bVisited = true;
                nodeIndexVec.push_back(iNodeIndex);
            }
        }
    }
```

#### 3.5.2 克鲁斯卡尔（Kruskal）算法

    思路（贪心策略）：

> 1. 将图中的所有边都去掉；
> 
> 2. 将边按权值从小到大的顺序添加到图中，保证添加的过程中不会形成环；
> 
> 3. 重复上一步直到连接所有顶点，此时就生成了最小生成树。

```cpp
void kruskalTree()
    {
        int val;
        int edgeCount = 0;

        // 存放节点集合数组
        vector<vector<int>> nodeSets;

        // 所有边
        vector<Edge> edgeVec;
        for (int i = 0; i < m_iCapacity; i++)
        {
            for (int j = 0; j < m_iCapacity; j++)
            {
                getValueFromMatrix(i, j, val);
                if (0 == val)
                {
                    continue;
                }
                if (m_pNodeArray[i].m_bVisited)
                {
                    continue;
                }
                Edge edge(i, j, val);
                edgeVec.push_back(edge);
            }
        }

        // 所有边中取出最小生成树的边
        while (edgeCount < m_iCapacity - 1)
        {
            // 从边集合中取出最小边
            int retIndex = getMinEdge(edgeVec);
            if (-1 != retIndex)
            {
                edgeVec[retIndex].m_bSelected = true;
                // 最小边连接点
                int nodeAIndex = edgeVec[retIndex].m_iNodeIndexA;
                int nodeBIndex = edgeVec[retIndex].m_iNodeIndexB;

                // 找出对应点所在集合
                bool nodeAInSet = false;
                bool nodeBInSet = false;
                int nodeAInSetLabel = -1;
                int nodeBInSetLabel = -1;
                for (int i = 0; i < (int)nodeSets.size(); i++)
                {
                    nodeAInSet = isInSet(nodeSets[i], nodeAIndex);
                    if (nodeAInSet)
                        nodeAInSetLabel = i;
                }
                for (int i = 0; i < (int)nodeSets.size(); i++)
                {
                    nodeBInSet = isInSet(nodeSets[i], nodeBIndex);
                    if (nodeBInSet)
                        nodeBInSetLabel = i;
                }

                // 不同点不同处理
                if (nodeAInSetLabel == -1 && nodeBInSetLabel == -1) // 两点均不在集合中
                {
                    vector<int> vec;
                    vec.push_back(nodeAIndex);
                    vec.push_back(nodeBIndex);
                    nodeSets.push_back(vec);
                }
                else if (nodeAInSetLabel == -1 && nodeBInSetLabel != -1) // 起点不在集合中
                {
                    nodeSets[nodeBInSetLabel].push_back(nodeAIndex);
                }
                else if (nodeAInSetLabel != -1 && nodeBInSetLabel == -1) // 终点不在集合中
                {
                    nodeSets[nodeAInSetLabel].push_back(nodeBIndex);
                }
                else if (nodeAInSetLabel != -1 && nodeBInSetLabel != -1 && nodeAInSetLabel != nodeBInSetLabel) // 都在集合中
                {
                    // 存在且标记不同，不形成回路
                    nodeSets[nodeAInSetLabel].insert(nodeSets[nodeAInSetLabel].end(), nodeSets[nodeBInSetLabel].begin(), nodeSets[nodeBInSetLabel].end());

                    for (int k = nodeBInSetLabel; k < (int)nodeSets.size() - 1; k++)
                    {
                        nodeSets[k] = nodeSets[k + 1];
                    }
                }
                // 存在且标记相同，形成回路，不可插入
                else if (nodeAInSetLabel != -1 && nodeBInSetLabel != -1 && nodeAInSetLabel == nodeBInSetLabel)
                {
                    continue;
                }

                m_pEdgeArray[edgeCount] = edgeVec[retIndex];
                edgeCount++;

                cout << m_pNodeArray[edgeVec[retIndex].m_iNodeIndexA].m_cData << " - ";
                cout << m_pNodeArray[edgeVec[retIndex].m_iNodeIndexB].m_cData << " (";
                cout << edgeVec[retIndex].m_iWeightValue << ") " << endl;
            }
        }
    }
```