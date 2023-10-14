#二分查找总结
#### 个人总结：
二分查找似乎有不止一种写法，看过不少二分查找的代码，不同点在于:
1.右边界初始化 （是数组长度或者是数组长度减一？）
2.循环判断条件 (小于还是小于等于，或者大于还是大于等于？)
3.中位数取法 (向上取整还是向下取整？)
4.左右边界加减问题 (+1? -1? 不变？)

我的常用写法:
```
//对于给定长度为len的一维数组
int lo = 0;
int hi = len-1；
while(lo < hi){
......
}

//循环判断是小于号
//优点：退出必定存在lo等于hi，且lo/hi一定不会越界，只需要判断nums[lo]或者nums[hi]是否满足需求即可
```

中位数的取法有两种，分为上中位数和下中位数，这是为了防止死循环。
左右边界的加减问题（区间划分）需要接合具体的题目来进行思考，思考完后再决定中位数的取法。

情况1.划分 [left, mid] 与 [mid + 1, right] ，mid 被分到左边，对应 int mid = left + (right - left) / 2，即：
```
if(nums[mid]>=target){
   right = mid;
}
else left = mid+1;
```

情况2.划分 [left, mid - 1] 与 [mid, right] ，mid 被分到右边，对应 int mid = left + (right - left + 1) / 2，即：
```
if(nums[mid]<=target){
   left = mid;
}
else right = mid-1;
```
## 注意：用以上的写法，在找目标值时（假设目标值在数组中一定存在），情况1的写法可以表示目标值第一次出现的位置,情况2的写法表示目标值最后一次出现的位置（可通过举例证明），这个性质的体现对解题会有便利性。

## 1.搜索插入位置
```
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        if(nums.size()==0) return 0;
        if (nums[nums.size() - 1] < target) {
            return nums.size();
        }
        int lo = 0;
        int hi = nums.size()-1;
        int mid;
        while(lo < hi){
            mid = lo + (hi-lo)/2;
            if(nums[mid]==target) return mid;
            else if(nums[mid]>target) hi = mid;
            else lo = mid+1;
        }
        return lo;
    }
};
```
## 2.在排序数组中查找元素的第一个和最后一个位置
```
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        vector<int> res(2,-1);
        int a = search(nums, target);
        int b = search(nums, target+1);
        if(a>=nums.size()||nums[a]!=target) return res;
        if(nums[a]==target) res[0]=a;
        if(nums[b]==target) res[1]=b;
        else res[1]=b-1;
        return res;
    }
    int search(vector<int>& nums, int target){
        int lo = 0;
        int hi = nums.size()-1;
        while(lo < hi){
            int mid = lo + (hi-lo)/2;
            if (nums[mid]>=target) hi = mid;
            else lo = mid+1;
        }
        return lo;
    }
};
```
## 3.X的平方根
```
//这里不采用牛顿迭代法
class Solution {
public:
    int mySqrt(int x) {
        int lo = 0;
        int hi = x/2 + 1;
        while(lo < hi){
            int mid = lo + (hi-lo+1)/2;
            if((long long)mid*mid<=x) {
                lo = mid;
            }
            else hi=mid-1;
        }
        return lo;
    }
};
//注：满足lo = mid且hi=mid-1，所以mid = lo + (hi-lo+1)/2
```
## 4.搜索旋转排序数组（无重复元素）
```
//写了两种取中位数的解法

class Solution {
public:
    int search(vector<int>& nums, int target) {
        int len = nums.size();
        if (len == 0) {
            return -1;
        }
        int left = 0;
        int right = len - 1;
        // while(left < right){
        //     int mid = left+(right-left+1)/2;
        //     if(nums[mid]<nums[right]){
        //         if(nums[mid] <= target && target <= nums[right]){
        //             left = mid;
        //         }
        //         else right = mid-1;
        //     }
        //     else{
        //         if(nums[left]<=target && target<=nums[mid-1]){
        //             right = mid-1;
        //         }
        //         else left = mid;
        //     }
        // }
        // if (nums[left] == target) {
        //     return left;
        // }
        // return -1;
        while(left<right){
            int mid = left+(right-left)/2;
            if(nums[mid]>nums[left]){
                if(nums[left]<=target && target<=nums[mid]){
                    right = mid;
                }
                else left = mid+1;
            }
            else{
                if(nums[mid+1]<=target && target<=nums[right]){
                    left = mid + 1;
                }
                else right = mid;
            }
        }
        if (nums[left]==target) return left;
        return -1;
    }
};
```
## 5.搜索旋转排序数组（有重复元素）
```
//在4的基础上增加额外一个判断来缩减区间（中位数取法不同，则这个判断不同）
class Solution {
public:
    bool search(vector<int>& nums, int target) {
        int len = nums.size();
        if (len == 0) {
            return -1;
        }
        int left = 0;
        int right = len - 1;
        while(left < right){
            int mid = left+(right-left+1)/2;
            if(nums[mid]<nums[right]){
                if(nums[mid] <= target && target <= nums[right]){
                    left = mid;
                }
                else right = mid-1;
            }
            else if (nums[mid]>nums[right]){
                if(nums[left]<=target && target<=nums[mid-1]){
                    right = mid-1;
                }
                else left = mid;
            }
            else{
                if(nums[right]==target){
                    return true;
                }
                right--;
            }
        }
        return nums[left] == target;
    }
};
```
## 6.搜索旋转排序数组最小值（无重复元素）
```
class Solution {
public:
    int findMin(vector<int>& nums) {
        int size = nums.size();
        if (size == 0) {
            return 0;
        }

        int left = 0;
        int right = size - 1;

        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] > nums[right]) {
                // [3, 4, 5, 1, 2]，mid 以及 mid 的左边一定不是最小数字
                // 下一轮搜索区间是 [mid + 1, right]
                left = mid + 1;
            }
            else {
                // 此时 numbers[mid] < numbers[right]
                // mid 的右边一定不是最小数字，mid 有可能是，下一轮搜索区间是 [left, mid]
                right = mid;
            }
        }
        return nums[left];
    }
};
```
## 7.搜索旋转排序数组最小值（有重复元素）
```
class Solution {
public:
    int findMin(vector<int>& nums) {
        int left = 0;
        int right = nums.size() - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] < nums[left]) {
                right = mid;
            }
            else if (nums[mid] > nums[right]) {
                left = mid + 1;
            }
            else {
                right -= 1;
            }
        }
        return nums[left];
    }
};
```
## 8.寻找峰值（局部极值）
```
class Solution {
public:
    int findPeakElement(vector<int>& nums) {
        int left = 0,right = nums.size()-1;
        while(left < right){
            int mid = left+(right-left)/2;
            if(nums[mid]>nums[mid+1]) right = mid;
            else left = mid+1;
        }
        return left;
    }
};
```
##  9.搜索二维矩阵
矩阵每行中的整数从左到右按升序排列。每行的第一个整数大于前一行的最后一个整数。
```
//和一维数组搜索完全一样
//这里有元素数量和矩阵下标的转换关系, 即i = mid/col, j = mid%col
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int row = matrix.size();
        if (!row) return false;
        int col = matrix[0].size();
        int left = 0, right = row*col-1;
        while(left < right){
            int mid = left+(right-left)/2;
            if(matrix[mid/col][mid%col]>=target) right=mid;
            else left = mid+1;
        }
        return matrix[left/col][left%col]==target;
    }
};
```
## 10.搜索二维矩阵
矩阵元素排列规则：
每行的元素从左到右升序排列
每列的元素从上到下升序排列
```
//此题解法不像是二分查找，而是利用了矩阵的特性
//从矩阵的右上角开始，向左走是递减，向下走是递增
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int row = matrix.size();
        if(!row) return false;
        int col = matrix[0].size();
        int i = 0,j=col-1;
        while(i<row && j>=0){
            if(matrix[i][j]==target) return true;
            else if(matrix[i][j]>target) j--;
            else i++;
        }
        return false;
    }
};
```
## 11.山脉数组的峰顶索引
符合下列属性的数组 arr 称为 山脉数组 ：
arr.length >= 3
存在 i（0 < i < arr.length - 1）使得：
arr[0] < arr[1] < ... arr[i-1] < arr[i]
arr[i] > arr[i+1] > ... > arr[arr.length - 1]
给你由整数组成的山脉数组 arr ，返回任何满足 arr[0] < arr[1] < ... arr[i - 1] < arr[i] > arr[i + 1] > ... > arr[arr.length - 1] 的下标 i 。

```
//给的数组满足先上升后下降的特性，所以一定存在峰顶元素
//所以判断条件是A[mid]<A[mid+1]
class Solution {
public:
    int peakIndexInMountainArray(vector<int>& A) {
        int left = 0;
        int right = A.size()-1;
        while(left<right){
            int mid = left+(right-left)/2;
            if(A[mid]<A[mid+1]) left = mid+1;
            else right = mid;
        }
        return left;
    }
};
```
## 12.山脉数组查找目标值
给你一个 山脉数组 mountainArr，请你返回能够使得 mountainArr.get(index) 等于 target 最小 的下标 index 值。
如果不存在这样的下标 index，就请返回 -1。
何为山脉数组？如果数组 A 是一个山脉数组的话，那它满足如下条件：
首先，A.length >= 3
其次，在 0 < i < A.length - 1 条件下，存在 i 使得：
A[0] < A[1] < ... A[i-1] < A[i]
A[i] > A[i+1] > ... > A[A.length - 1]
你将 不能直接访问该山脉数组，必须通过 MountainArray 接口来获取数据：
MountainArray.get(k) - 会返回数组中索引为k 的元素（下标从 0 开始）
MountainArray.length() - 会返回该数组的长度
注意：
对 MountainArray.get 发起超过 100 次调用的提交将被视为错误答案
```
//比较简单，根据11，先找到数组的峰顶元素
//峰顶元素把数组划分为两个部分，先搜索前半部分，找到了就不用去后半部分搜索了
//如果前半部分没有，再去后半部分搜索
class Solution {
public:
    int findInMountainArray(int target, MountainArray &mountainArr) {
        int left = 0, right = mountainArr.length()-1;
        int top = findTop(mountainArr,left, right);
        int res = findAesc(mountainArr, left, top, target);
        if (res != -1) {
            return res;
        }
        return findDesc(mountainArr, top+1, right, target);

    }
    int findTop(MountainArray &mountainArr, int left, int right){
        while(left<right){
            int mid = left+(right-left)/2;
            if(mountainArr.get(mid)<mountainArr.get(mid+1)) left = mid+1;
            else right = mid;
        }
        return left;
    }
    int findAesc(MountainArray &mountainArr, int left, int right, int target)
    {
        while(left<right){
            int mid = left+(right-left)/2;
            if(mountainArr.get(mid)<target) left = mid+1;
            else right = mid;
        }
        if(mountainArr.get(left)==target) return left;
        return -1;
    }

    int findDesc(MountainArray &mountainArr, int left, int right, int target)
    {
        while(left<right){
            int mid = left+(right-left)/2;
            if(mountainArr.get(mid)>target) left = mid+1;
            else right = mid;
        }
        if(mountainArr.get(left)==target) return left;
        return -1;
    }
};

```
