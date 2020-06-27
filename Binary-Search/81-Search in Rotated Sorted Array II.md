### 题目描述

假设按照升序排序的数组在预先未知的某个点上进行了旋转。

( 例如，数组 [0,0,1,2,2,5,6] 可能变为 [2,5,6,0,0,1,2] )。

编写一个函数来判断给定的目标值是否存在于数组中。若存在返回 true，否则返回 false。


```
示例 1:

输入: nums = [2,5,6,0,0,1,2], target = 0
输出: true
```

这是 搜索旋转排序数组 的延伸题目，本题中的 nums  可能包含重复元素。
这会影响到程序的时间复杂度吗？会有怎样的影响，为什么？


### 题目分析


```
class Solution {
    public boolean search(int[] nums, int target) {        
        int left = 0, right = nums.length - 1;
        while (left <= right) {
               int mid = left+(right -left) / 2;
                //其实是非常关键的
                if (target == nums[mid]) {
                    return true;
                }
                
                //用二分来优化的一种方式，只要更新了左右断点，就是逼近!
                if (nums[left] == nums[mid] && nums[mid] == nums[right]) {
                    // 既然是不满足的，就跳过
                    left++;
                    right--;
                }else if(nums[left]<=nums[mid] ) {//左边有序，二分搜索左边
                   if (nums[left] <= target && target < nums[mid]) {
                        right = mid - 1;
                    } else {
                        left = mid + 1;
                    }

                } else {//右边有序，二分搜索右边
                    if (nums[mid]<target && target <= nums[right]) {
                        left = mid + 1;
                    } else {
                        right = mid - 1;
                    }
                }
            }
            return false;
        }
}
```
