---
title: binary tree
date: 2023-8-18 18:36:00
updated: 2023-8-18 18:36:00
description: 二叉树专题
cover: https://images6.alphacoders.com/128/thumbbig-1288241.webp
tags: 
    - 数据结构 
    - 二叉树
---
### 一、二叉树定义

```c++
struct TreeNode{
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x){
        val = x;
        this.left = NULL;
        this.right = NULL;
    }
};
```

```java
public class TreeNode{
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode(){}
    TreeNode(int val){this.val = val;}
    TreeNode(int val,TreeNode left,TreeNode right){
        this.val = val;
        this.left = left;
        this.right = right;
    }
}
```

```python
class TreeNode:
    def _init_(self,value):
        self.value = value
        self.left = None
        self.right = None
```

### 二、递归

1、确定递归函数的参数和返回值

2、确定终止条件

3、确定单层递归的逻辑

递归的实现：每一次递归调用都会把函数的局部变量，参数值，返回地址等压入调用栈中，等递归返回时，从栈顶弹出上一次递归的各项参数。



#### 前序遍历（递归）

打印遍历节点的值，除此之外无需返回其他值，函数返回类型为void,参数vec存储节点的数值

```c++
void traversal(TreeNode* cur,vector<int>& vec)
```

本轮递归终止的条件是当前节点为空

```C++
if(cur == NULL) return;
```

前序遍历按照左中右顺序，先取中节点的值（单层递归）

```C++
vec.push_back(cur -> val);//中
traversal(cur -> left,vec);//左
traversal(cur -> right,vec);//右
```

前序遍历总过程：

```C++
class solution{
    public:
    	void traversal(TreeNode* cur,vector<int>& vec){
                if(cur == NULL)
                    return;
                vec.push_back(cur -> val);
                traversal(cur -> left,vec);
                traversal(cur -> right,vec);
            }
    	vector<int> preordertraversal(TreeNode* root){
            vector<int> result;//初始化一个vec
            traversal(root,result);//从根节点开始遍历
            return result;
        }	
};
```

#### 中序遍历（递归）

```C++
void traversal(TreeNode* cur,vector<int>& vec){
    if(cur == NULL)
        return;
    traversal(cur -> left,vec);
    vec.push_back(cur -> val);
    traversal(cur -> right,vec);
}
```

#### 后序遍历（递归）

```c++
void traversal(TreeNode* cur,vector<int>& vec){
    if(cur == NULL)
        return;
    traversal(cur -> left,vec);
    traversal(cur -> right,vec);
    vec.push_back(cur -> val);
}
```



### 三、迭代

#### 前序遍历（迭代）

```c++
class solution{
    public:
    	vector<int> preorderTraversal(TreeNode* root){
            stack<TreeNode*> s;
            vector<int> vec;
            if(root == NULL)
                return vec;
            s.push(root);
            while(!s.empty()){
                TreeNode* node = s.top();
                s.pop();
                vec.push_back(node -> val);//栈顶节点值加入vec
                if(node -> right)//先压入右节点，空节点不执行
                    s.push(node -> right);
                if(node -> left)//后压入左节点，空节点不执行
                    s.push(node -> left);
            }
            return vec;
        }
};
```

#### 后序遍历（迭代）

```c++
class solution{
    public:
    vector<int> Traversal(TreeNode* root){
    	stack<TreeNode*> s;
        vector<int> vec;
        if(root == NULL)
            return vec;
        s.push(root);
        while(!s.empty()){
            TreeNode* node = s.top();
            s.pop();
            vec.push_back(node -> val);
            if(node -> left)
                s.push(node -> left);
            if(node -> right)
                s.push(node -> right);//遍历顺序改为中右左
        }
        reverse(vec.begin(),vec.end());//逆序为左右中
        return vec;
	}
};
```

前序遍历访问节点（遍历节点）和处理节点（将节点放入vec数组中）可以进行同步处理（既处理顺序和访问顺序一致），但是中序遍历无法同步



#### 中序遍历（迭代）

```c++
class solution{
    public:
    	vector<int> inorderTraversal(TreeNode* root){
            vector<int> vec;
            stack<TreeNode*> s;
            if(root == NULL)
            	return vec;
            TreeNode* cur = root;//使用指针来进行节点访问
            while(!s.empty()){
                if(cur != NULL){//指针访问节点，一直访问至二叉树最底层
                    s.push(cur);//将访问节点放入栈
                    cur = cur -> left;//左
                }else{
                    cur = s.top();//从栈顶弹出的数据即为需要处理的数据（放进vec数组中的数据）
                    s.pop();
                    vec.push_back(cur -> val);//中
                    cur = cur -> right;//右
                }
            }
        }
};
```

中序遍历先是访问根节点，再逐步向左向下访问节点直至到达最底层，进而再开始处理节点（将节点数值放入vec数组），导致访问顺序和处理数据顺序是不一样的。





### 四、二叉树的统一迭代法

#### 中序遍历

```C++
class solution{
    public:
    	vector<int> inorderTraversal(TreeNode* root){
            vector<int> vec;
            stack<TreeNode*> s;
            if(root != NULL)
                s.push(root);
            while(!s.empty()){
                TreeNode* node = s.top();//将该节点弹出，避免重复操作，下面再将右中左节点加入栈
                if(node != NULL){
                    s.pop();
                    if(node -> right)
                        s.push(node -> right);//非空右节点入栈
                    s.push(node);//中节点入栈
                    s.push(NULL);//此时中节点未进行处理，再其后加入NULL标记（表示待处理）
                    if(node -> left)
                        s.push(node -> left);//非空左节点入栈
                }else{//遇到空节点（即NULL标记）时，将下一个节点加入vec数组
                    s.pop();//将空节点弹出
                    node = s.top();//重新取出栈中元素
                    s.pop();
                    vec.push_back(node -> val);
                }
            }
            return vec;
        }
};
```

#### 前序遍历

```c++
class solution{
    public:
    	vector<int> preorderTraversal(TreeNode* root){
            vector<int> vec;
            stack<TreeNode*> s;
            if(root != NULL)
                s.push(root);
            while(!s.empty()){
                TreeNode* node = s.top();
                if(node != NULL){
                    s.pop();
                    if(node -> right)
                        s.push(node -> right);
                    if(node -> left)
                        s.push(node -> left);
                    s.push(node);
                    s.push(NULL)
                }else{
                    s.pop();
                    node = s.top();
                    s.pop();
                    vec.push_back(node -> val);
                }
                    
            }
        }
};
```

#### 后序遍历

```c++
class solution{
    public:
    vector<int> Traversal(TreeNode* root){
        vector<int> vec;
        stack<TreeNode*> s;
        if(root != NULL)
            s.push(root);
        while(!s.empty()){
            TreeNode* node = s.top();
            if(node != NULL){
                s.pop();
                s.push(node);
                s.push(NULL);
                if(node -> right)
                    s.push(node -> right);
                if(node -> left)
                    s.push(node -> left);
                
            }else{
                s.pop();
                node = s.top();
                s.pop();
                vec.push_back(node -> val);
            }
        }
        return vec;
    }
};
```

到此，我们就将前中后序三种遍历用统一的代码风格写出来，关键在于加入NULL标记来表示需要处理的元素。



### 五、二叉树的层序遍历

层序遍历一个二叉树，就是一层一层从左往右进行遍历二叉树，需要借助数据结构遍历来实现，队列先进先出，符合一层一层遍历的逻辑，而使用栈先进后出适合深度优先遍历即递归的逻辑。

层序遍历是图论里的广度优先遍历。

#### 1、自上而下的层序遍历

```c++
class solution{
    public:
    	vector<vector<int>> levelOrder(TreeNode* root){
            queue<TreeNode*> que;
            vector<vector<int>> result;//每层一个数组，每行合起来则是二维数组
            if(root != NULL)
                que.push(root);
            while(!que.empty()){
                int size = que.size();
                vector<int> vec;
                //使用固定大小的size，而不用que.size()，因为que.size()会不断变化
                for(int i = 0;i < size;i ++){
                    TreeNode* node = que.front();
                    que.pop();
                    vec.push_back(node -> val);
                    if(node -> left)
                        que.push(node -> left);
                    if(node -> right)
                        que.push(node -> right);
                }
                result.push_back(vec);
            }
            return result;
        }
};
#递归法
class solution{
    public:
    	void order(TreeNode* cur,vector<vector<int>>& result,int depth){
            if(cur == nullptr)
                return;
            if(result.size() == depth)
                result.push_back(vector<int>());
            result[depth].push_back(cur -> val);
            order(cur -> left,result,depth + 1);
            order(cur -> right,result,depth + 1);
        }
    	
    	vector<vector<int>> levelorder(TreeNode* root){
            vector<vector<int>> result;
            int depth = 0;
            order(root,result,depth);
            return result;
        }
};
```

#### 2、自底向上的层序遍历（将数组逆序然后输出）

```c++
class solution{
    public:
    	vector<vector<int>> order(TreeNode* root){
            queue<TreeNode*> que;
            vector<vector<int>> result;
            if(root != NULL)
                que.push(root);
            while(!que.empty()){
                int size = que.size();
                vector<int> vec;
                for(int i = 0;i < size;i ++){
                    TreeNode* node = que.front();
                    que.pop();
                    vec.push_back(node -> val);
                    if(node -> left)
                        vec.push(node -> left);
                    if(node -> right)
                        vec.push(node -> right);
                }
                result.push_back(vec);
            }
            reserve(result.begin(),result.end());//反转数组
            return result;
        }
};
```

#### 3、二叉树的右视图（从右看二叉树所能得到的节点值）

```c++
class solution{
    public:
    	vector<int> rightSizeView(TreeNode* root){
            queue<TreeNode*> que;
            vector<int> result;
            if(root != NULL)
                que.push(root);
            while(!que.empty()){
                int size = que.size();
                for(int i = 0;i < size;i ++){
                    TreeNode* node = que.front();
                    que.pop();
                    if(i ==z (size - 1))
                        result.push_back(node -> val);//将每层最后一个元素放入result数组
                    if(node -> left)
                        que.push(node ->left);
                    if(node -> right)
                        que.push(node ->right);
                } 
            }
            return result;
        }
};
```

#### 4、二叉树的层平均值

```c++
class solution{
    public:
    	vector<double> averageOfLevels(TreeNode* root){
            queue<TreeNode*> que;
            vector<double> result;
            if(root != NULL)
                que.push(root);
            while(!que.empty()){
                int size = que.size();
                for(int i = 0;i < size;i ++){
                    TreeNode* node = que.front();
                    que.pop();
                    double sum += node -> val;//统计每一层的和
                    if(node -> left)
                        que.push(node -> left);
                    if(node -> right)
                        que.push(node -> right);
                }
                result.push_back(sum / size);//将每一层均值放入result数组中
            }
            return result;
        }
};
```

#### 5、N叉树的层序遍历

```c++
class solution{
    public:
    	vector<vector<int>> levelOrder(TreeNode* root){
            queue<TreeNode*> que;
            vector<vector<int>> result;
            if(root != NULL)
                que.push(root);
            while(!que.empty()){
                int size = que.size();
                vector<int> vec;
                for(int i = 0;i < size;i ++){
                    TreeNode* node = que.front();
                    que.pop();
                    vec.push_back(node -> val);
                    for(int j = 0;i < node -> children.size();j ++){//将节点孩子加入队列
                        if(node -> children[j])
                            que.push(node -> children[j]);
                    }
                }
                result.push_back(vec);
            }
            return result;
        }
};
```

#### 6、在每个二叉树行中找最大值

```c++
class solution{
    public:
    	vector<int> largestValues(TreeNode* root){
            queue<TreeNode*> que;
            vector<int> result;
            if(root != NULL)
                que.push(root);
            while(!que.empty()){
                int size = que.size();
                int maxValue = INT_MIN;//现将最大值设为INT_MIN(即整数最小)
                for(i = 0;i < size;i ++){
                    TreeNode* node = que.front();
                    //通过每一层不断比较，最终得到maxValue（即最大值）
                    maxValue = ((node -> val) > maxValue)?node -> val:maxValue;
                    if(node -> left)
                        que.push(node -> left);
                    if(node -> right)
                        que.push(node -> right);
                }
                result.push_back(maxValue);//将每一层的最大值加入数组result
            }
            return result;
        }
};
```



#### 7、填充每一个节点的下一个右侧节点

```c++
struct Node{
    int val;
    Node* left;
    Node* right;
    Node* next;//指向节点的右侧节点
};

class solution{
    public:
    	TreeNode* connect(TreeNode* root){
            queue<TreeNode*> que;
            if(root != NULL)
                que.push(root);
            while(!que.empty()){
                int size = que.size();
                Node* preNode;//用来记录首节点和前一个节点
                Node* node;//遍历节点
                for(int i = 0;i < size;i ++){
                    if(i == 0){
                        preNode = que.front();
                        que.pop();
                        node = preNode;
                    }else{
                        node = que.front();
                        que.pop();
                        preNode -> next = node;
                        preNode  = preNode -> next;//= node同理
                    }
                    if(node -> left)
                        que.push(node -> left);
                    if(node -> right)
                        que.push(right);
                }
                node -> next = NULL;//本层最后一个节点指向NULL
            }
            return root;
        }
};
```

#### 8、二叉树的最大深度

给定一个二叉树，找出其最大深度

二叉树的深度为根节点到最远叶子节点的最长路径上的节点数（叶子节点没有子节点）

```c++
class solution{
    public:
    int maxDepth(TreeNode* root){
        queue<TreeNode*> que;
        int depth = 0;
        if(root != NULL)
            que.push(root);
        while(!que.empty()){
            int size = que.size();
            depth ++;//记录深度
            for(int i = 0;i < size;i ++){
                TreeNode* node = que.front();
                que.pop();
                if(node -> left)
                    que.push(node -> left);
                if(node -> right)
                    que.push(node -> right);
            }
        }
        return depth;
    }
};
```

#### 9、二叉树的最小深度

```c++
class solution{
    public:
    	int minDepth(TreeNode* root){
            queue<TreeNode*> que;
            int depth = 0;
            if(root != NULL)
                que.push(root);
            while(!que.empty()){
                int size = que.size();
                depth ++;
                for(int i = 0;i < size;i ++){
                    TreeNode* node = que.front();
                    que.pop();
                    if(node -> left)
                        que.push(node -> left);
                    if(node -> right)
                        que.push(node -> right);
                    //当节点左右孩子都为空（即叶子节点），到达最小深度
                    if(!node -> left && !node -> right)
                        return depth;
                }
            }
            return depth;
        }
};
```

总结，二叉树的层序遍历，就是图论中的广度优先搜索在二叉树中的应用，需要借助队列来实现。



### 六、翻转二叉树

#### 1、深度优先遍历

```c++
//递归（前序遍历）
//先进行交换左右孩子节点，然后反转左子树，然后反转右子树
class solution{
    public:
    	TreeNode* invertTree(TreeNode* root){
            if(root == NULL)
                return root;
            swap(root -> left,root -> right);//中
            invertTree(root -> left);//左
            invertTree(root -> right);//右
            
            return root;
        }
};
```



```c++
//前序遍历（迭代）
class solution{
    public:
    	TreeNode* invertTree(TreeNode* root){
            stack<TreeNode*> st;
            if(root != NULL)
                st.push(root);
            while(!st.empty()){
                TreeNode* node = st.top();//中
                st.pop();
                swap(node -> left,node ->right);
                if(node -> right)
                    st.push(node -> right);//右
                if(node -> left)
                    st.push(node -> left);//左
            }
            return root;
        }
};
```

```c++
//前序遍历（统一迭代法）
class solution{
    public:
    	TreeNode* invertTree(TreeNode* root){
            stack<TreeNode*> st;
            if(root != NULL)
                st.push(root);
            while(!st.empty()){
                TreeNode* node = st.top();
                if(node != NULL){
                    st.pop();
                    if(node -> right)
                        st.push(right);
                    if(node -> left)
                        st.push(left);
                    st.push(node);
                    st.push(NULL);
                }else{
                    st.pop();
                    TreeNode* node = st.top();
                    st.pop();
                    swap(node -> left,node ->right);//节点处理逻辑
                }
            }
            return root;
        }
};
```

#### 2、广度优先遍历

```c++
;//层序遍历
class solution{
    public:
    	TreeNode* invertTree(TreeNode* root){
            queue<TreeNode*> que;
            if(root != NULL)
                que.push(root);
            while(!que.empty()){
                int size = que.size();
                for(int i = 0;i < size;i ++){
                    TreeNode* node = que.front();
                    que.pop();
                    swap(node -> left,node -> right);//左右子树交换
                    if(node -> left)
                        que.push(node -> left);
                    if(node -> right)
                        que.push(node -> right);
                }
            }
            return root;
        }
};
```



针对二叉树的问题，解题之前要想清楚究竟选择哪种遍历方式





### 七、对称二叉树

给定一个二叉树，检查二叉树是否镜像对称

```c++
//自己的第一遍思考
class solution{
    public:
    	bool Tree(TreeNode* root){
            stack<TreeNode*> que;
            vector<int> vec;
            int flag = 1;
            if(root != NULL)
                que.push(root);
            while(!que.empty()){
                int size = que.size();
                for(int i = 0;i < size;i ++){
                    TreeNode* node = que.front();
                    que.pop();
                    vec.push_back(node -> val);
                    if(node -> left)
                        que.push(node -> left);
                    if(node -> right)
                        que.push(node -> right);
                }
                for(j = 0;j < size;j ++){
                    if((vec[j] != vec[size - 1 -j] || vec[j] == NULL || vec[size - 1-                         1 - j] == NULL)
                       flag =1;
                       return flag;       
                }
            }
            return flag;
        }  
};
```

针对自己第一次实现的想法进行改进：首先判断二叉树是否对称，不需要加入根节点（遍历则需要加入），因为是否对称只和左右子树有关。同时鉴于对队列数据结构的不熟练，调用了vector数组来储存节点数值后在数组中进行比较（考虑麻烦了），直接对称的将左右子树的节点加入到队列中，两个两个一组弹出比较。还加了flag标记来输出bool数，直接true or false即可。

```c++
//迭代
class solution{
    public:
    	bool isSymmetric(TreeNode* root){
            if(root == NULL)
                return true;
            queue<TreeNode*> que;
            que.push(root -> left);//加入左子树头结点
            que.push(root -> right);//加入右子树头结点
            while(!que.empty()){
                TreeNode* leftNode = que.front();
                que.pop();
                TreeNode* rightNode = que.front();
                que.pop();
                if(!leftNode && !leftNode)
                    continue;
                
                
                if(!leftNode || !rightNode || (leftNode -> val != rightNode -> val))
                    return false;
                
                que.push(leftNode -> left);
                que.push(rightNode -> right);
                que.push(leftNode -> right);
                que.push(rightNode -> left);
            }
            return true;
        }
};
```

递归

1、确定递归参数和返回值

比较根节点的两个子树是否是互相翻转的，进而判断这个二叉树是否对称，比较的是左右两个子树，参数就是根节点的左子树头结点和右子树头结点。

返回值自然是bool类型。

2、确定终止条件

要比较两个节点的数值相不相等，首先要把两节点为空的情况搞清楚。

* 左节点为空，右节点不为空
* 左节点不为空，右节点为空
* 左右节点都为空
* 左右节点都不为空但是值不相等   

3、确定单层递归的逻辑

单层递归的逻辑就是两个节点都不为空且节点值相等的情况。

* 比较外侧节点值是否相等：传入左节点的左孩子，右节点的右孩子
* 比较内侧节点值是否相等：传入左节点的右孩子，右节点的左孩子
* 如果左右都对称返回true，有一方不对称就返回false

```c++
class solution{
    public:
    	bool compare(TreeNode* left,TreeNode* right){
            //首先排除空节点的情况
            if(!left && right)
                return false;
            else if(left && !right)
                return false;
            else if(!left && !right)
                return true;
            //排除空节点后，两节点值不相等的情况
            else if((left -> val) != (right -> val))
                return false;
            
            //左子树：左，右子树：右
            bool outside = compare(left -> left,right -> right);
            //左子树：右，右子树：左
            bool inside = compare(left -> right,right -> left);
            //逻辑处理
            bool isSame = outside && inside;
            return isSame;
        }
    	bool isSymmetric(TreeNode* root){
            if(root == NULL)
                return true;
            return compare(root -> left,root -> right);
        }
};
```



### 八、二叉树的最大深度

```c++
//前序遍历
class solution{
    public:
    	int result;//用来存储最大深度
        //函数返回值为void，非int，与计算节点数不同，深度不具有累加性
    	void getDepth(TreeNode* node,int depth){//参数为当前节点，以及当前的深度
            result = depth > result ? depth : result;//通过不断比较深度来更新深度的值
            if(!node->left && !node->right)
                return ;
            if(node->left){//左
                depth ++;//深度加一，因为向下了一层
                getDepth(node->left,depth);
                depth --;//回溯，因为右子树还未进行
            }
            if(node->right){//同理，右
                depth ++;
                getDepth(node->right,depth);
                depth --;
            }
            return ;
        }
    	
    	int maxDepth(TreeNode* root){
            result = 0;
            if(root == nullptr)
                return result;
            getDepth(root,1);
            return result;
        }
}；
```



迭代法中层序遍历最为合适，因为遍历的层数即是二叉树的深度

```c++
class solution{
	public:
    	int maxDepth(TreeNode* root){
            queue<TreeNode*> que;
            int depth = 0;
            if(root != NULL)
                que.push(root);
            while(!que.empty()){
                int size = que.size();
                depth ++;
                for(int i = 0;i < size;i ++){
                    TreeNode* node = que.front();
                    que.pop();
                    if(node -> left)
                        que.push(node -> left);
                    if(node -> right)
                     	que.push(node -> right);
                }
            }
            return depth;
        }
	
};
```

n叉树的最大深度

```c++
class solution{
    public:
    	int maxDepth(TreeNode* root){
            queue<TreeNode*> que;
            int depth = 0;
            if(root != NULL)
                que.push(root);
            while(!que.empty()){
                int size = que.front();
                depth ++;
                for(int i = 0;i < size;i ++){
                    TreeNode* node = que.front();
                    que.pop();
                    for(int j = 0;j < node -> children.size();j ++){
                        if(node -> children[j])
                            que.push(node -> children[j]);
                    }
                }
            }
            return depth;
        }
        
};
```

### 九、二叉树的最小深度

最小深度是指根节点到最近叶子节点的最短路径上的节点个数

```c++
class solution{
    public:
    	int minDepth(TreeNode* root){
        	queue<TreeNode*> que;
            int depth = 0;
            if(root != NULL)
                que.push(root);
            while(!que.empty()){
                int size = que.size();
                depth ++;
                for(int i = 0;i < size;i ++){
                    TreeNode* node = que.front();
                    que.pop();
                    if((!node -> left) && (!node -> right))//左右节点都为空时到达最小深度
                        return depth;
                    if(node -> left)
                        que.push(node -> left);
                    if(node -> right)
                     	que.push(node -> right);
                }
            }
            return depth;
        }
};
```

### 十、完全二叉树的节点个数

将完全二叉树当作普通二叉树看待，依旧使用普通二叉树的遍历方法来计算节点个数

```c++
//递归法
class solution{
    public:
    	int getNodeNums(TreeNode* root){
            if(root == nullptr)
                return 0;
            int leftNodeNums = getNodeNums(root -> left);//左
            int rightNodeNums = getNodeNums(root -> right);//右
            int nodeNums = leftNodeNums + rightNodeNums + 1;//中
            return nodeNums;
        }
    
    	int countNodes(TreeNode* root){
            return getNodeNums(root);
        }
};
```

```c++
//迭代法
class solution{
    public:
    	int countNodes(TreeNode* root){
            queue<TreeNode*> que;
            if(root != NULL)
                que.push(root);
            int result = 0;//记录节点个数
            while(!que.empty()){
                int size = que.size();
                for(int i = 0;i < size;i ++){
                    TreeNode* node = que.front();
                    que.pop();
                    result ++;//每遍历一个节点加一
                    if(node -> left)
                        que.push(node -> left);
                    if(node -> right)
                        que.push(node -> right);
                }
            }
            return result;
        }
};
```



然而，完全二叉树本省具有一些特性使得能够使用一些其它的方法来计算个数

在完全二叉树中，除了最底层的节点可能没有填满之外，其余每层节点数都达到最大值，并且最下面一层节点都集中在该层最左侧的若干区域，若最底层为第h层，则该层节点个数范围为1~2^(h-1)



完全二叉树只有两种情况，情况一：就是满二叉树，情况二：最后一层叶子节点没有满。

对于情况一，可以直接用 2^树深度 - 1 来计算，注意这里根节点深度为1。

对于情况二，分别递归左孩子，和右孩子，递归到某一深度一定会有左孩子或者右孩子为满二叉树，然后依然可以按照情况1来计算。

可以看出如果整个树不是满二叉树，就递归其左右孩子，直到遇到满二叉树为止，用公式计算这个子树（满二叉树）的节点数量。

这里关键在于如何去判断一个左子树或者右子树是不是满二叉树呢？



在完全二叉树中，如果递归向左遍历的深度等于递归向右遍历的深度，那说明就是满二叉树。

```c++
class solution{
    public:
    	int countNodes(TreeNode* root){
            if(root == nullptr)
                return 0;
            TreeNode* left = root -> left;
            TreeNode* right = root -> right;
            int leftDepth = 0;
            int rightDepth = 0;
            while(left){
                left = left -> left;
                leftDepth ++;
            }
            while(right){
                right = right -> right;
                rightDepth ++;
            }
            if(leftDepth == rightDepth)
                return 2^(leftDepth - 1);
            
            return countNodes(root -> left) + countNodes(root -> right) + 1;
                
        }
};
```

### 十一、平衡二叉树

因为求深度可以从上到下去查 所以需要前序遍历（中左右），而高度只能从下到上去查，所以只能后序遍历（左右中）



一颗高度平衡的二叉树定义为：一个二叉树每个节点的左右两棵子树的高度差绝对值不超过1。

根据定义可知，如果以当前节点为传入节点的二叉树已经不是二叉树了，则该二叉树不是平衡二叉树

return -1来标记不符合平衡二叉树的规则。



递归思路：

1、明确递归函数的参数和返回值

参数：当前传入节点。 返回值：以当前传入节点为根节点的树的高度。

那么如何标记左右子树是否差值大于1呢？

如果当前传入节点为根节点的二叉树已经不是二叉平衡树了，还返回高度的话就没有意义了。

所以如果已经不是二叉平衡树了，可以返回-1 来标记已经不符合平衡树的规则了。

2、明确终止条件

递归的过程中依然是遇到空节点了为终止，返回0，表示当前节点为根节点的树高度为0

3、明确单层递归的逻辑

如何判断以当前传入节点为根节点的二叉树是否是平衡二叉树呢？当然是其左子树高度和其右子树高度的差值。

分别求出其左右子树的高度，然后如果差值小于等于1，则返回当前二叉树的高度，否则返回-1，表示已经不是二叉平衡树了。

```c++
//递归
class solution{
    public:
    	int getHeight(TreeNode* node){
            if(node == nullptr)//如果该节点是空指针，则高度为0
                return 0;
            //判断左右子树是否是平衡二叉树，有一个不是则非平衡二叉树
            int leftHeight = getHeight(node->left);
            if(leftHeight == -1)
                return -1;
            int rightHeight = getHeight(node->right);
            if(LeftHeight == -1)
                return -1;
            
            if(abs(leftHeight - rightHeight) > 1){//左右子树高度差不超过1
                return -1;
            }else{
                //取两树高度最大者在加上根节点的高度为整颗平衡二叉树的高度
                return max(leftHeight,rightHeight) + 1;
            }
        }
        
    	bool isBalance(TreeNode* root){
            return getHeight(root) == -1 ? false : true;
        }
};
```

```c++
//迭代
class solution{
    public:
        //node节点的最大深度就是node的高度
    	int getDepth(TreeNode* node){
            stack<TreeNode*> st;
            if(node == nullptr)
                return 0;
            int depth = 0;//记录深度
            int result = 0;//更新最大深度值
            st.push(node);
            while(!st.empty()){
                TreeNode* cur = st.top();
                if(node != NULL){
                    st.pop();
                    st.push(cur);
                    st.push(NULL);
                    depth ++;
                    if(cur->right)
                        st.push(cur->right);
                    if(cur->left)
                        st.push(cur->left);
                }else{
                    st.pop();
                    node = st.top();
                    st.pop();
                    depth --;
                }
                result = result > depth ? result : depth;
            }
            return result;
        }
    	
    	bool isBalance(TreeNode* root){
            stack<TreeNode*> st;
            if(root == NULL)
               return true;
            st.push(root);
            while(!st.empty()){
                TreeNode* node = st.top();
                st.pop();
                if(abs(getDepth(node->left),getDepth(node->right))>1)
                    return false;
                if(node->right)
                    st.push(node->right);
                if(node->left)
                    st.push(node->left);
            }
            return true;
        }
};
```

求深度适合前序遍历，求高度适合后序遍历

### 十二、二叉树的所有路径

```c++
class solution{
    public:
    	void traversal(TreeNode* node,vector<int> path,vector<string> result){
            path.push_back(node->val);
            if(node->left == NULL && node->right == NULL){
                string sPath;
                for(int i = 0;i < path.size() - 1;i++){
                    sPath += to_string(path[i]);
                    sPath += "->";
                }
                sPath += to_string(path[path.size() - 1]);
                result.push_back(sPath);
                return;
            } 
            if(node->left){
                traversal(node->left,path,result);
                path.pop_back();
            }
            if(node->right){
                traversal(node->right,path,result);
                path.pop_back();
            }
        }
    
    	vector<string> binaryTreePaths(TreeNode* root){
            vector<int> path;
            vector<string> result;
            if(root == NULL)
                return result;
            traversal(root,path,result);
            return result;
        }
};
```



### 十三、左叶子之和

1、确定递归函数的参数和返回值

要判断左节点值之和，首先要传入根节点，返回值则为数值之和

2、确定终止条件

如果遍历到空节点，左叶子值为0

```c++
if(root == NULL) return 0;
```

注意，只有当前节点遍历的是父节点，才能判断其子节点是不是左叶子。所以如果当前遍历的节点是叶子节点，那其左叶子也必定为0

```c++
if(root->left == NULL && root->right == NULL) return 0;
```

3、确定单层递归的逻辑

遇到左叶子节点时，记录数值，然后通过递归求取左子树左叶子之和和右子树左叶子之和。相当于整个树的左叶子之和

```c++
//递归法
class solution{
    public:
    	int sumOfLeftLeaves(TreeNode* root){
            if(root == NULL)
                return 0;
            if(root->left == NULL && root->right == NULL)
                return 0;
            
            int LeftValue = sumOfLeftLeaves(root->left);//左
            //当左子树只是一个左叶子节点的时候
            if(root->left && !root->left->left && !root->left->right)
                leftValue = root->left->val;
            
            int rightValue = sumOfLeftLeaves(root->right);//右
            int sum = leftValue + rightValue;//中
            return sum;
        }
};
```

```c++
//迭代法
class solution{
	public:
    	int sumOfLeftLeaves(TreeNode* root){
            if(root == NULL)
                return 0;
            stack<TreeNode*> st;
            st.push(root);
            int result = 0;
            while(!st.empty()){
                TreeNode* node = st.top();
                st.pop();
                if(node->left && !node->left->left && !node->left->right)
                    result += node->left->val;
                if(node->right)
                   st.push(node->right);
                if(node->left)
                   st.push(node->left);
            }
            return result;
        }
};
```



### 十四、找树左下角的值

```c++
//层序遍历
class solution{
    public:
    	int findBottomLeftValue(TreeNode* root){
            queue<TreeNode*> que;
            if(root != NULL)
                que.push(root);
            int result = 0;
            while(!que.empty()){
                int size = que.size();
                for(int i = 0;i < size;i ++){
                    TreeNode* node = que.front();
                    if(i == 0)
                        result = node->val;//每层取第一个节点的值
                    que.pop();
                    if(node->left)
                        que.push(node->left);
                    if(node->right)
                        que.push(node->right);
                }
            }
            return result;//遍历完所有层后result里的值则为树左下角的值
        }
};
```

```c++
//递归法
class solution{
    public:
    	//定义全局变量
    	int maxDepth = INT_MIN;//记录最大深度
    	int result;//记录树左下角的值
    	void traversal(TreeNode* node,int depth){
            if(!node->left && !node->left){
                if(depth > maxDepth){
                    maxDepth = depth;
                    result = node->val;
                }
                return ;
            }
            if(node->left){
                //traversal(node->left,depth + 1);
                depth ++;
                traversal(node->left,depth);
                depth --;
            }
            if(node->right){
                //traversal(node->right,depth + 1);
                depth ++;
                traversal(node->right,depth);
                depth --;
            }
            return ;
        }
    	
    	int findBottomLeftValue(TreeNode* root){
            traversal(root,0);
            return result;
        }
};
```

总结：

1、递归求深度的写法，在**平衡树二叉树**中详细分析了深度应该怎么求，高度应该怎么求

2、递归中隐藏了回溯，在**二叉树的所有路径** 中讲解了究竟哪里使用了回溯，哪里隐藏了回溯

3、层次遍历，在**层序遍历**深度讲解了二叉树层序遍历。



### 十五、路径总和I

给定一个二叉树和目标和，判断该树是否存在根节点到叶子节点的路径，这条路径上的所有节点值相加等于目标和

递归：

1、确定函数返回值和参数

需要一个指针遍历二叉树，同时需要int参数来记录目标和，使用递减的方式，当某条路径上的result递减至0且到叶子节点时，说明该二叉树存在所有节点值相加等于目标和的路径

2、确定终止条件

 当和递减至0且遍历到叶子节点时，存在

```c++
if(result == 0 && !cur->left && !cur->right) return true;
```

当遇到叶子节点未满足条件时，不存在

```c++
if(!cur->left && !cur->right) return false;
```

3、确定单层递归的逻辑

先向左继续判断，不满足再向右继续判断，都不满足则不存在该路径

```c++
if(cur->left){
    if(traversal(cur->left,result - cur->left->val))
        return true;
}
if(cur->right){
    if(traversal(cur->right,result - cur->right->val))
        return true;
}
return false;
```



```c++
//递归法
class solution{
    public:
    	bool traversal(TreeNode* cur,int result){//记录当前节点和目标和
            //当和递减至0且遍历到叶子节点时，存在
            if(result == 0 && !cur->left && !cur->right)
                return true;
            //当遇到叶子节点未满足条件时，不存在
            if(!cur->left && !cur->right)
                return false;
            
            if(cur->left){//左
                if(traversal(cur->left,result - cur->left->val))//包含回溯的逻辑
                    return true;
            }
            if(cur->right){//右
                if(traversal(cur->right,result - cur->right->val))//包含回溯的逻辑
                    return true;
            }
            return false;
            /*将回溯过程体现出来代码可改为：
            if(cur->left){
            	result -= cur->left->val;
            	if(traversal(cur->left,result))
            		return true;
            	result += cur->left->val;
            }
            if(cur->right){
            	result -= cur->right->val;
            	if(traversal(cur->right,result))
            		return true;
            	result += cur->right->val;
            }
            */
        }
    	
    	bool hasPathSum(TreeNode* root,int sum){
            if(root == NULL)
                return false;
            return traversal(root,sum - root->val);
        }
};
```

递归过程隐藏回溯，如：

```c++
traversal(cur->left,result - cur->left->val);
```

把result - cur->left->val直接作为参数传进函数，函数结束，result的数值并没有改变

```c++
//迭代法
class solution{
    public:
    	bool hasPathSum(TreeNode* root,int sum){
            if(root == nullptr)
                return false;
            //此时栈里放的是pair<节点指针，路径数值>	
            stack<pair<TreeNode*,int>> st;
            st.push(pair<TreeNode*,int>(root,root->val));
            while(!st.empty()){
                pair<TreeNode*,int> node = st.top();
                st.pop();
                //如果该节点是叶子节点，同时该路径数值等于sum，返回true
                if(!node.first->left && !node.first->right && sum == node.second)
                    return true;
                //右节点，压入节点的同时，记录路径数值
                if(node.first->right)
                    st.push(pair<TreeNode*,int>(node.first->right,node.second +                           node.first->right->val));
                //左节点，压入节点的同时，记录路径数值
                if(node.first->left)
                    st.push(pair<TreeNode*,int>(node.first->left,node.second +                           node.first->left->val));
            }
            return false;
        }
};
```



### 十六、路径总和II

给定一个二叉树和一个目标和，找到所有从根节点到叶子节点路径总和等于目标和的路径

递归：

```c++
class solution{
    private:
    	vector<vector<int>> result;//记录所有和为sum的路径
    	vector<int> path;//记录单条遍历的路径
        //递归函数不需要返回值，因为要遍历整棵树
    	void traversal(TreeNode* cur,int count){
            //遇到叶子节点且找到和为sum的路径
            if(!cur->left && !cur->right && count == 0){
                result.push_back(path);
                return ;
            }
            
            if(!cur->left && !cur->right)
                return ;
            
            if(cur->left){//左
                path.push_back(cur->left->val);
                traversal(cur->left,count - cur->left->val);
                path.pop_back();
            }
            if(cur->right){//右
                path.push_back(cur->right->val);
                traversal(cur->right,count - cur->right->val);
                path.pop_back();
            }
            /*体现回溯的过程
            if(cur->left){
            	path.push_back(cur->left->val);
            	count -= cur->left->val;
            	traversal(cur->left,count);//递归
            	count += cur->left->val;//回溯
            	path.pop_back();//回溯
            }
            if(cur->right){
            	path.push_back(cur->left->val);
            	count -= cur->right->val;
            	traversal(cur->right,count);//递归
            	count += cur->right->val;//回溯
            	path.pop_back();//回溯
            }
            */
            return ;
        }
    
    public:
    	vector<vector<int>> findPathSum(TreeNode* root,int sum){
            result.clear();
            path.clear();
            if(root == NULL)
                return result;
            path.push_back(root->val);
          	traversal(root,sum - root->val);
            return result;
        }
};
```



### 十七、从中序与后序遍历序列构造二叉树

根据一棵树的中序和后序遍历构造二叉树

```c++
class solution{
    private:
    	TreeNode* traversal(vector<int>& inorder,vector<int>& postorder){
            //第一步
            if(postorder.size() == 0)
                return NULL;
            //后序遍历数组的最后一个元素，就是当前的中间节点
            int rootValue = postorder[postorder.size() - 1];
            TreeNode* root = new TreeNode(rootValue);
            
            if(postorder.size() == 1)
                return root;
            
            for(int delimiterIndex = 0;delimiter < inorder.size();delimiterIndex ++){
                if(inorder[delimiterIndex] == rootValue)
                    break;
            }
            
            vector<int> leftInorder(inorder.begin(),inorder.begin()+delimiterIndex);
            vector<int> rightInorder(inorder.begin()+delimiterIndex+1,inorder.end());
            
            postorder.resize(postorder.size() - 1);
            
            vector<int>                                                                           leftPostorder(postorder.begin(),postorder.begin()+leftInorder.size());
            vector<int>
            rightPostorder(postorder.begin()+leftInorder.size(),postorder.end());
            
            root->left = traversal(leftInorder,leftPostorder);
            root->right = traversal(rightInorder,rightPostorder);
            
            return root;
        }
    
    public:
    	TreeNode* bulidTree(vector<int>& inorder,vector<int>& postorder){
            if(inorder == NULL || postorder == NULL)
                return NULL;
            return traversal(inorder,postorder);
        }
                        
};

```



### 十八、最大二叉树

给定一个不含重复元素的整数数组。最大二叉树定义如下：

1、二叉树的根是数组中的最大元素

2、左子树是通过数组中的最大值左边部分构造出的最大二叉树

3、右子树是通过数组中的最大值左边部分构造出的最大二叉树

给定的数组大小在[1,1000]之间



1、确定递归函数参数和返回值

参数传入的是存放元素的数组，返回该数组构造的二叉树的头节点，返回类型是指向节点的指针

```c++
TreeNode* constructMaxBinaryTree(vector<int>& nums)
```

2、确定终止条件

```c++
TreeNode* node = new TreeNode(0);
if(nums.size() == 1){
    node->val = nums[0];
    return node;
}
```

3、确定单层递归逻辑

分三步：

一、找到数组中最大的值与其对应的下标，最大的值构造根节点，下标用来下一步分割数组

```c++
int maxValue = 0;
int maxValueIndex = 0;
for(int i = 0;i < nums.size();i ++){
    if(nums[i] > maxValue){
        maxValue = nums[i];
        maxValueIndex = i;
    }
}
Treenode* node = new TreeNode(0);
node->val = maxValue;
```

二、最大值所在下标左区间 构造左子树

需要判断maxValueIndex>0,因为至少保证左区间至少有一个数值

```c++
if(maxValueIndex > 0){
    vector<int> leftTree(nums.begin(),nums.begin()+maxValueIndex);
    node->left = constructMaxBinaryTree(leftTree);
}
```

三、最大值所在下标右区间 构造右子树

maxValue<(nums.size()-1),保证右区间至少有一个数值

```c++
if(maxValueIndex < (nums.size() - 1)){
    vector<int> rightTree(nums.begin()+maxValueIndex+1,nums.end());
    node->right = constructMaxBinaryTree(rightTree);
}
```



```c++
class solution{
    public:
    	TreeNode* constructMaxBinaryTree(vector<int>& nums){
            TreeNode* node = new TreeNode(0);//构造中节点
            //当数组只剩下一个元素，即叶子节点
            if(nums.size() == 1){
                node->val = nums[0];
                return node;
            }
            
            //找到数组中的最大值与其下标
            int maxValue = 0;
            int maxValueIndex = 0;
            for(int i = 0;i < nums.size();i ++){
                if(nums[i] > maxValue){
                    maxValue = nums[i];
                    maxValueIndex = i;
                }
            }
            node->val = maxValue;
            
            //确保左右区间至少有一个元素
            if(maxValueIndex > 0){
                vector<int> left(nums.begin(),nums.begin()+maxValueIndex);
                node->left = constructMaxBinaryTree(left);
            }
            if(maxValueIndex < nums.size() - 1){
                vector<int> right(nums.begin()+maxValueIndex+1,nums.end());
                node->right = constructMaxBinaryTree(right);
            }   
            return node;
        }
};
```

以上代码较为冗余，效率不高，每次分割数组后都要新定义vector，但是逻辑描述较为清晰

优化版：

```c++
class solution{
    private:
    	//在左闭右开区间[left,rihgt)，构造二叉树
    	TreeNode* traversal(vector<int>& nums,int left,int right){
            if(left >= right)
                return nullptr;
            //分割点下标：maxValueIndex
            int maxValueIndex = 0;
            for(int i = 0;i < right;i ++){
                if(nums[i] > nums[maxValueIndex])
                    maxValueIndex = i;
            }
            TreeNode* node = new TreeNode(nums[maxValueIndex]);
            
            //左闭右开[left,maxValueIndex)
            node->left = traversal(nums,left,maxValueIndex);
            
            //左闭右开[maxValueIndex+1,right)
            node->right = traversal(nums,maxValueIndex+1,right);
            
            return node;
        }
    public:
    	TreeNode* constructMaxBinaryTree(vector<int>& nums){
            //初始左闭右开区间[0,nums.size())
            return traversal(nums,0,nums.size());
        }
}
```

