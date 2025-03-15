---
title: array
date: 2023-8-19 22:19:00
update: 2023-8-19 22:19:00
description: 数组专题
cover: https://images7.alphacoders.com/132/thumbbig-1320332.webp
tags: 
    - 数据结构 
    - 数组
---

数组是存放在连续内存空间上的相同类型数据的集合

vector底层实现是array，严格讲vector是容器而不是数组

数组元素不能删除，只能覆盖

c++中二维数组在地址空间上是连续的



### 一、二分查找

给定一个 n 个元素有序的（升序）整型数组 nums 和一个目标值 target  ，写一个函数搜索 nums 中的 target，如果目标值存在返回下标，否则返回 -1。

第一种写法：（左闭右闭）

```c++
class solution{
    public:
    	int search(vector<int>& nums,int target){
            int left = 0;
            int right = nums.size() - 1;
            while(left <= right){//左闭右闭区间，允许left=right
                int middle = (left + right) / 2;
                if(nums[middle] > target){//target在左区间，[left,middle-1]
                    right = middle - 1;
                }else if(nums[middle] < target){//target在右区间,[middle+1,right]
                    left = middle + 1;
                }else{//target在middle处
                    return middle;
                }
            }
            return -1;//目标值不存在
        }
};
```

第二种写法：（左闭右开）

```c++
class solution{
    public:
    	int search(vector<int>& nums,int target){
            int left = 0;
            int right = nums.size() - 1;
            while(left < right){
                int middle = (left + right) / 2;
                if(nums[middle] > target){
                    right = middle;//唯一不同之处在于target位于[left,middle)
                }else if(nums[middle] < target){//[middle+1,right)
                    left = middle + 1; 
                }else{
                    return middle;
                }
            }
            return -1;
        }
};
```

区间的定义就是不变量，那么在循环中坚持根据查找区间的定义来做边界处理，就是循环不变量规则。



### 二、移除元素

给你一个数组 nums 和一个值 val，你需要 原地 移除所有数值等于 val 的元素，并返回移除后数组的新长度。

暴力解法：

```c++
class solution{
    public:
    	int removeElement(vector<int>& nums,int val){
            int size = nums.size();
            for(int i = 0;i < size;i ++){//遍历数组
                if(nums[i] == val){
                    for(int j = i;j < size - 1;j ++){//执行移动操作
                        nums[j] = nums[j + 1];//后面元素覆盖前一个元素
                    }
                }
                i --;//i下标后所有元素向前移动一位，i先减再加保持位置不变
                size --;//数组大小减一
            }
            return size;
        }
};
```

###  双指针法

通过一个快指针和一个慢指针在一个for循环下完成两个for循环的工作

* 快指针：寻找新数组的元素，新数组就是不包含目标值的数组
* 慢指针：指向更新 新数组下标的位置

```c++
class solution{
    public:
    	int removeElement(vector<int>& nums,int val){
            int slowIndex = 0;
            for(int fastIndex = 0;fastIndex < nums.size();fastIndex ++){
                if(nums[fastIndex] != val){//快指针指向新数组值
                    nums[slowIndex] = nums[fastIndex];//将值赋给慢指针所指下标
                    slowIndex ++;//慢指针向右移动一位
                }
            }
            return slowIndex;//删除完毕后慢指针下标所指值正好为新数组大小
        }
};
```



### 三、有序数组的平方

给你一个按 非递减顺序 排序的整数数组 nums，返回 每个数字的平方 组成的新数组，要求也按 非递减顺序 排序。

示例 1： 输入：nums = [-4,-1,0,3,10] 输出：[0,1,9,16,100] 解释：平方后，数组变为 [16,1,0,9,100]，排序后，数组变为 [0,1,9,16,100]

示例 2： 输入：nums = [-7,-3,2,3,11] 输出：[4,9,9,49,121]

暴力解法：(每个数平方之后排序)

```c++
class solution{
    public:
    	vector<int> sortedSquare(vector<int>& nums){
            for(int i = 0;i < nums.size();i ++){
                nums[i] *= nums[i];
            }
            sort(nums.begin(),nums.end());
            return nums;
        }
};
```

#### 双指针法

```c++
class solution{
    public:
    	vector<int> sortSquare(vector<int>& nums){
            int size = nums.size();
            vector<int> result(size,0);
            int k = size - 1;
            
            for(int i = 0,j = size - 1;i <= j;){
                if(nums[i] * nums[i] < nums[j] * nums[j]){
                    result[k --] = nums[j] * nums[j];
                    j --;
                }else{
                    result[k --] = nums[i] * nums[i];
                    i ++;
                }
            }
            return result;
        }
};
```



### 四、长度最小的子数组

给定一个含有 n 个正整数的数组和一个正整数 s ，找出该数组中满足其和 ≥ s 的长度最小的 连续 子数组，并返回其长度。如果不存在符合条件的子数组，返回 0。

暴力解法：

两层for循环，找到符合条件的子序列

```c++
class solution{
    public:
    	minSubArrayLen(vector<int>& nums,int s){
            int size = nums.size();
            int result = INT_MAX;//记录最终结果
           	int subLen = 0;//记录子数组的长度
            int sum = 0;//记录子数组和
            for(int i = 0;i < size;i ++){//子数组初始位置i
                sum = 0;//新子数组和要清零
                for(int j = i;j < size;j ++){//子数组终止位置j
                    sum += nums[j];
                    if(sum >= s){
                        subLen = j - i + 1;//子数组长度终止-初始+1
                        result = result < subLen ? result : subLen;//保持结果长度最小
                        break;
                    }
                }
            }
            return result == INT_MAX ? 0 : result;//result未被赋值则返回0
        }
};
```

#### 滑动窗口

所谓滑动窗口，**就是不断的调节子序列的起始位置和终止位置，从而得出我们要想的结果**。

窗口就是 满足其和 ≥ s 的长度最小的 连续 子数组。

窗口的起始位置如何移动：如果当前窗口的值大于s了，窗口就要向前移动了（也就是该缩小了）。

窗口的结束位置如何移动：窗口的结束位置就是遍历数组的指针，也就是for循环里的索引。



动态调整子数组的初始位置：

```c++
while(sum >= s){
	subLen = j - i + 1;//子数组长度
    result = result < subLen ? result : subLen;
    sum -= nums[i ++];//不断变更i（子数组的初始位置）
}
```

```c++
class solution{
    public:
    	int minSubArrayLen(vector<int>& nums,int s){
            int result = INT_MAX;
            int subLen = 0;
            int sum = 0;
            for(int i = 0,j = 0;j < nums.size();j ++){
                sum += nums[j];//和<s，向新数组加元素
                while(sum >= s){
                    subLen = j - i + 1;//子数组长度
                    result = result < subLen ? result : subLen;
                    sum -= nums[i ++];//不断变更i（子数组的初始位置）
                }
            }
            return result == INT_MAX ? 0 : result;
        }
};
```



### 螺旋矩阵

给定一个正整数 n，生成一个包含 1 到 n^2 所有元素，且元素按顺时针顺序螺旋排列的正方形矩阵。

示例:

输入: 3 输出: [ [ 1, 2, 3 ], [ 8, 9, 4 ], [ 7, 6, 5 ] ]

遵循循环不变量原则

```c++
class solution{
    public:
    	vector<vector<int>> generateMatrix(int n){
            vector<vector<int>> res(n,vector<int>(n,0));
            int startx = 0,starty = 0;
            int loop = n / 2;
            int mid = n / 2;
            int count = 1;
            int offset = 1;
            int i,j;
            while(loop --){
                for(j = starty;j < n - offset;j ++){
                    res[startx][j] = count ++;
                }
                
                for(i = startx;i < n - offset;i ++){
                    res[i][j] = count ++;
                }
                
                for(;j > starty;j --){
                    res[i][j] = count ++;
                }
                
                for(;i > startx;i --){
                    res[i][j] = count ++;
                }
                
                startx ++;
                starty ++;
                offset ++;
            }
            if(n % 2 == 1){
                res[mid][mid] = n * n;
            }
            return res;
        }
};
```

{% note default simple %}binary-tree{% endnote %}



