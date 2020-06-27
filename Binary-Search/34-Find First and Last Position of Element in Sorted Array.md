### 题目描述

给定一个按照升序排列的整数数组 nums，和一个目标值 target。找出给定目标值在数组中的开始位置和结束位置。

你的算法时间复杂度必须是 O(log n) 级别。

如果数组中不存在目标值，返回 [-1, -1]。



```
示例 1:

输入: nums = [5,7,7,8,8,10], target = 8
输出: [3,4]
示例 2:

输入: nums = [5,7,7,8,8,10], target = 6
输出: [-1,-1]
```

### 题目分析


#### 直接求解

先看下遍历求解的实现：

```
public int[] searchRange(int[] nums, int target) {

        int[] ret=new int[]{-1,-1};

         if(nums==null || nums.length==0){
            return ret;
        }

        for(int i=0;i<nums.length;i++){
            if(nums[i]==target){
                ret[0]=i;
                break;
            }
        }

        for(int i=nums.length-1;i>=0;i--){
            if(nums[i]==target){
                ret[1]=i;
                break;
            }
        }

        return ret;
    }
```



#### 二分时间复杂度是logn级别


```java
class Solution {
     public int[] searchRange(int[] nums, int target) {
        int len = nums.length;
        if (len == 0) {
            return new int[]{-1, -1};
        }
        int leftIndex = gegLeft(nums, target);
        if (leftIndex == -1) {
            return new int[]{-1, -1};
        }
        int rightIndex = getRight(nums, target);
        return new int[]{leftIndex, rightIndex};
    }

    private int gegLeft(int[] nums, int target) {
        int res=-1;
        int left = 0;
        int right = nums.length - 1;
        while (left <= right) {
            int mid = (left + right) >>> 1;
            // 小于一定不是解
            if (nums[mid] < target) {
                // 下一轮搜索区间是 [mid + 1, right]
                left = mid + 1;
            } else if (nums[mid] > target){
                right = mid-1;
            }else{
                right = mid-1;
                res=mid;
            }
        }
        
        return res;
    }

    private int getRight(int[] nums, int target) {
        int res=-1;
        int left = 0;
        int right = nums.length - 1;
        while (left <= right) {
            int mid = (left + right) >>> 1;
            // 小于一定不是解
            if (nums[mid] < target) {
                // 下一轮搜索区间是 [mid + 1, right]
                left = mid + 1;
            } else if (nums[mid] > target){
                right = mid-1;
            }else{
                left = mid + 1;
                res=mid;
            }
        }
        return res;
    }
}

```

#### 二分查找代码实现二


```
class Solution {
    public int[] searchRange(int[] nums, int target) {
        if(nums==null || nums.length==0)
            return new int[]{-1,-1};

        int left=searchLeft(nums,target);
        int right=searchRight(nums,target);
        return new int[]{left,right};
    }

    //搜索左半边
    public int searchLeft(int[] nums, int target) {
        int left=0;
        int right=nums.length-1;

        //为什么要+1？不够模板啊
        while(left+1<right){
            int mid=left+(right-left)/2;
            //如果=继续往左边找
            //如果>继续往左边找
            if(nums[mid]>=target){
                right=mid;
            //如果<继续往右边找
            }else{
                left=mid;
            }
        }

        if(nums[left]==target){
            return left; 
        }else if(nums[right]==target){
            return right; 
        }else{
            return -1;
        }

    }

    //搜索右半边
    public int searchRight(int[] nums, int target) {

        int left=0;
        int right=nums.length-1;
        while(left+1<right){
            int mid=left+(right-left)/2;
            //如果=继续往右边找
            //如果<继续往右边找
            if(nums[mid]<=target){
                left=mid;
            //如果>继续往左边找
            }else{
                right=mid;
            }
        }

        if(nums[right]==target){
            return right; 
        }else if(nums[left]==target){
            return left; 
        }else{
            return -1;
        }
    }
}
```