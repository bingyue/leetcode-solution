## 题目描述

假设按照升序排序的数组在预先未知的某个点上进行了旋转。

( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。

搜索一个给定的目标值，如果数组中存在这个目标值，则返回它的索引，否则返回 -1 。

你可以假设数组中不存在重复的元素。
你的算法时间复杂度必须是 O(log n) 级别。


```
示例 1:

输入: nums = [4,5,6,7,0,1,2], target = 0
输出: 4

```

### 题目解答

#### 一、直接求解，复杂度为O(n)


```
 public int search(int[] nums, int target) {

        if(nums==null || nums.length==0)
          return -1;
          
        for(int i=0;i<nums.length;i++){
            if(nums[i]==target){
                return i;
            }
        }
        return -1;
    }
```
#### 二分查找

这道题让在旋转数组中搜索一个给定值，若存在返回坐标，若不存在返回 -1。我们还是考虑二分搜索法，但是这道题的难点在于不知道原数组在哪旋转。

二分搜索法的关键在于获得了中间数后，判断下面要搜索左半段还是右半段，观察上面红色的数字都是升序的，可以得出出规律，如果中间的数小于最右边的数，则右半段是有序的，若中间数大于最右边数，则左半段是有序的，我们只要在有序的半段里用首尾两个数组来判断目标值是否在这一区域内，这样就可以确定保留哪半边了。


![image](http://note.youdao.com/yws/res/15693/4DBC5F27E24A4D3E8E54F9CC5C86D427)

如果左端点小于中点，那么左半边有序，否则，右半边有序。

![image](http://note.youdao.com/yws/res/15696/17A588087096408FB1C56EDDDD2A36A5)


```java
//整个旋转数组分为两部分一定有一部分有序，那么通过判断左边还是右边有序分为两种情况。
    //  然后再判断向左走还是向右走
    class Solution {
     public int search(int[] A, int target) {
        int left = 0, right = A.length - 1;
        int mid;
        while (left <= right) {
            mid = left + (right - left) / 2;
            if (target == A[mid]) {
                return mid;
            }
            if (A[mid] < A[A.length - 1]) {//右边有序
                if (target > A[mid] && target <= A[A.length - 1]) {
                    left = mid + 1;
                } else {
                    right = mid - 1;
                }
            } else {//左边有序
                //  这个A[0] <= target有什么用呢，需要保证这个target大于左边界
                // 也就是在这个边界里，上边也一样
                if (target < A[mid] && A[0] <= target) {
                    right = mid - 1;
                } else {
                    left = mid + 1;
                }
            }
        }
        //  找不到，返回-1
        return -1;
    }
}
```
