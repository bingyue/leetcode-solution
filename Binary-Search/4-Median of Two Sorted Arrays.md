### 题目描述
有两个大小分别为m和n的有序数组A和B。

给定两个大小为 m 和 n 的有序数组 nums1 和 nums2。

请你找出这两个有序数组的中位数，并且要求算法的时间复杂度为 O(log(m + n))。

你可以假设 nums1 和 nums2 不会同时为空。


```
示例 1:

nums1 = [1, 3]
nums2 = [2]

则中位数是 2.0
示例 2:

nums1 = [1, 2]
nums2 = [3, 4]

则中位数是 (2 + 3)/2 = 2.5
```

### 题目解答

中位数（Median）是按顺序排列的一组数据中居于中间位置的数对于有限的数集，可以通过把所有观察值高低排序后找出正中间的一个作为中位数。

#### 一、暴力求解

![image](http://note.youdao.com/yws/res/30879/21B44DCE67D647378DFC6078BFE1B0C1)

##### ++时间复杂度是O(n*logn),也就是O((m+n)*log(m+n))


#### 二、使用归并排序

简单粗暴，先将两个数组合并，两个有序数组的合并也是归并排序中的一部分。然后根据奇数，还是偶数，返回中位数。

##### ++时间复杂度是O(m+n)++

```
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {

          //边界情况
        if(nums1.length==0){
            return getMedian(nums2);
        }else if(nums2.length==0){
            return getMedian(nums1);
        }
        //合并数组
        int[] nums=merge(nums1,nums2);
        //求解
        return getMedian(nums);
    }

    //获得数组中位数
    public double getMedian(int[] nums){
        int len=nums.length;

        // 判断偶数的位运算怎么写来着?
        // (len&1)==0
        if(len%2==0){//偶数
            //返回double，java会自动提升类型转换
            //这里有一个技巧，/2.0就可以进行类型提升
            return (nums[len/2]+nums[len/2-1])/2.0;
        }else{//奇数
            //注意啊，下标从0开始的，比如[1,2,3]，返回nums[1]
            return nums[len/2];
        }
    }

     //合并两个有序数组
    public int[] merge(int[] nums1, int[] nums2){
        //双指针，从尾部开始
        int p=nums1.length-1;
        int q=nums2.length-1;
        
        // 再加上新数组的下标指针
        int index= nums1.length+nums2.length-1;
        
        //合并后的新数组
        // 开辟一个新数组
        int[] nums=new int[index+1];
        while(p>=0 && q>=0){
            if(nums1[p]<nums2[q]){
                nums[index]=nums2[q];
                index--;
                q--;
            }else{
                nums[index]=nums1[p];
                index--;
                p--;
            }
        }
        if(p>=0){
            while(p>=0){
                nums[index]=nums1[p];
                index--;
                p--;
            }
        }

        if(q>=0){
            while(q>=0){
            nums[index]=nums2[q];
            index--;
            q--;
            }
        }
        return nums;
    }

}
```


#### 三、二分查找

![image](http://note.youdao.com/yws/res/30890/2D769E300C02415BB0D5CB7A41BE48EE)

![image](http://note.youdao.com/yws/res/30892/98C7F8476D764D9680E6CDAE79A02922)

![image](http://note.youdao.com/yws/res/30894/D3EB1917C369405B9F6303752482D91B)

![image](http://note.youdao.com/yws/res/30896/84CE8E5C5071498D95BFAFF2F09F6228)


#### 代码实现

```java
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int leftLength = nums1.length;
        int rightLength = nums2.length;
        // 为了保证第一个数组比第二个数组小(或者相等)
        if (leftLength > rightLength) {
            return findMedianSortedArrays(nums2, nums1);
        }
        // 分割线左边的所有元素需要满足的个数 m + (n - m + 1) / 2;
        // 两个数组长度之和为偶数时，当在长度之和上+1时，由于整除是向下取整，所以不会改变结果
        // 两个数组长度之和为奇数时，按照分割线的左边比右边多一个元素的要求，此时在长度之和上+1，就会被2整除，会在原来的数
        //的基础上+1，于是多出来的那个1就是左边比右边多出来的一个元素
        int totalLeft = (leftLength + rightLength + 1) / 2;
        // 在 nums1 的区间 [0, leftLength] 里查找恰当的分割线，
        // 使得 nums1[i - 1] <= nums2[j] && nums2[j - 1] <= nums1[i]
        int left = 0;
        int right = leftLength;
        // nums1[i - 1] <= nums2[j]
        //  此处要求第一个数组中分割线的左边的值 不大于(小于等于) 第二个数组中分割线的右边的值
        // nums2[j - 1] <= nums1[i]
        //  此处要求第二个数组中分割线的左边的值 不大于(小于等于) 第一个数组中分割线的右边的值
        // 循环条件结束的条件为指针重合，即分割线已找到
        while (left < right) {
            // 二分查找，此处为取第一个数组中左右指针下标的中位数，决定起始位置
            // 此处+1首先是为了不出现死循环，即left永远小于right的情况
            // left和right最小差距是1，此时下面的计算结果如果不加1会出现i一直=left的情况，而+1之后i才会=right
            // 于是在left=i的时候可以破坏循环条件，其次下标+1还会保证下标不会越界，因为+1之后向上取整，保证了
            // i不会取到0值，即i-1不会小于0
            // 此时i也代表着在一个数组中左边的元素的个数
            int i = left + (right - left + 1) / 2;
            // 第一个数组中左边的元素个数确定后，用左边元素的总和-第一个数组中元素的总和=第二个元素中左边的元素的总和
            // 此时j就是第二个元素中左边的元素的个数
            int j = totalLeft - i;
            // 此处用了nums1[i - 1] <= nums2[j]的取反，当第一个数组中分割线的左边的值大于第二个数组中分割线的右边的值
            // 说明又指针应该左移，即-1
            if (nums1[i - 1] > nums2[j]) {
                // 下一轮搜索的区间 [left, i - 1]
                right = i - 1;
                // 此时说明条件满足，应当将左指针右移到i的位置，至于为什么是右移，请看i的定义
            } else {
                // 下一轮搜索的区间 [i, right]
                left = i;
            }
        }
        // 退出循环时left一定等于right，所以此时等于left和right都可以
        // 为什么left一定不会大于right?因为left=i。
        // 此时i代表分割线在第一个数组中所在的位置
        // nums1[i]为第一个数组中分割线右边的第一个值
        // nums[i-1]即第一个数组中分割线左边的第一个值
        int i = left;
        // 此时j代表分割线在第二个数组中的位置
        // nums2[j]为第一个数组中分割线右边的第一个值
        // nums2[j-1]即第一个数组中分割线左边的第一个值
        int j = totalLeft - i;
        // 当i=0时，说明第一个数组分割线左边没有值，为了不影响
        // nums1[i - 1] <= nums2[j] 和 Math.max(nums1LeftMax, nums2LeftMax)
        // 的判断，所以将它设置为int的最小值
        int nums1LeftMax = i == 0 ? Integer.MIN_VALUE : nums1[i - 1];
        // 等i=第一个数组的长度时，说明第一个数组分割线右边没有值，为了不影响
        // nums2[j - 1] <= nums1[i] 和 Math.min(nums1RightMin, nums2RightMin)
        // 的判断，所以将它设置为int的最大值
        int nums1RightMin = i == leftLength ? Integer.MAX_VALUE : nums1[i];
        // 当j=0时，说明第二个数组分割线左边没有值，为了不影响
        // nums2[j - 1] <= nums1[i] 和 Math.max(nums1LeftMax, nums2LeftMax)
        // 的判断，所以将它设置为int的最小值
        int nums2LeftMax = j == 0 ? Integer.MIN_VALUE : nums2[j - 1];
        // 等j=第二个数组的长度时，说明第二个数组分割线右边没有值，为了不影响
        // nums1[i - 1] <= nums2[j] 和 Math.min(nums1RightMin, nums2RightMin)
        // 的判断，所以将它设置为int的最大值
        int nums2RightMin = j == rightLength ? Integer.MAX_VALUE : nums2[j];
        // 如果两个数组的长度之和为奇数，直接返回两个数组在分割线左边的最大值即可
        if (((leftLength + rightLength) % 2) == 1) {
            return Math.max(nums1LeftMax, nums2LeftMax);
        } else {
            // 如果两个数组的长度之和为偶数，返回的是两个数组在左边的最大值和两个数组在右边的最小值的和的二分之一
            // 此处不能被向下取整，所以要强制转换为double类型
            return (double) ((Math.max(nums1LeftMax, nums2LeftMax) + Math.min(nums1RightMin, nums2RightMin))) / 2;
        }
    }

```



### 四、转化为求有序数组的第k个元素

如果观察值有偶数个，通常取最中间的两个数值的平均数作为中位数。

> 为了简化代码，不分情况讨论，使用一个小 trick，分别找第 (m+n+1) / 2 个，
和 (m+n+2) / 2 个，然后求其平均值即可，这对奇偶数均适用。
> 若 m+n 为奇数的话，
那么其实 (m+n+1) / 2 和 (m+n+2) / 2 的值相等，
相当于两个相同的数字相加再除以2，还是其本身。




需要定义一个函数来在两个有序数组中找到第K个元素，下面重点来看如何实现找到第K个元素。首先，为了避免拷贝产生新的数组从而增加时间复杂度，使用两个变量i和j分别来标记数组 nums1 和 nums2 的起始位置。然后来处理一些 corner cases，比如当某一个数组的起始位置大于等于其数组长度时，说明其所有数字均已经被淘汰了，相当于一个空数组了，那么实际上就变成了在另一个数组中找数字，直接就可以找出来了。还有就是如果 K=1 的话，只要比较 nums1 和 nums2 的起始位置i和j上的数字就可以了。

难点就在于一般的情况怎么处理？因为需要在两个有序数组中找到第K个元素，为了加快搜索的速度，可以使用二分法，那么对谁二分呢，数组么？

其实要对K二分，意思是需要分别在 nums1 和 nums2 中查找第 K/2 个元素，注意这里由于两个数组的长度不定，所以有可能某个数组没有第 K/2 个数字，所以需要先 check 一下，数组中到底存不存在第 K/2 个数字，如果存在就取出来，否则就赋值上一个整型最大值（目的是要在 nums1 或者 nums2 中先淘汰 K/2 个较小的数字，判断的依据就是看 midVal1 和 midVal2 谁更小，但如果某个数组的个数都不到 K/2 个，自然无法淘汰，所以将其对应的 midVal 值设为整型最大值，以保证其不会被淘汰），若某个数组没有第 K/2 个数字，则淘汰另一个数组的前 K/2 个数字即可。


举个例子来说吧，比如 nums1 = {3}，nums2 = {2, 4, 5, 6, 7}，K=4，要找两个数组混合中第4个数字，则分别在 nums1 和 nums2 中找第2个数字，而 nums1 中只有一个数字，不存在第二个数字，则 nums2 中的前2个数字可以直接跳过，为啥呢，因为要求的是整个混合数组的第4个数字，不管 nums1 中的那个数字是大是小，第4个数字绝不会出现在 nums2 的前两个数字中，所以可以直接跳过。


```
public class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int m = nums1.length, n = nums2.length, left = (m + n + 1) / 2, right = (m + n + 2) / 2;
        return (findKth(nums1, 0, nums2, 0, left) + findKth(nums1, 0, nums2, 0, right)) / 2.0;
    }
    int findKth(int[] nums1, int i, int[] nums2, int j, int k) {
        if (i >= nums1.length) return nums2[j + k - 1];
        if (j >= nums2.length) return nums1[i + k - 1];
        if (k == 1) return Math.min(nums1[i], nums2[j]);
        int midVal1 = (i + k / 2 - 1 < nums1.length) ? nums1[i + k / 2 - 1] : Integer.MAX_VALUE;
        int midVal2 = (j + k / 2 - 1 < nums2.length) ? nums2[j + k / 2 - 1] : Integer.MAX_VALUE;
        if (midVal1 < midVal2) {
            return findKth(nums1, i + k / 2, nums2, j, k - k / 2);
        } else {
            return findKth(nums1, i, nums2, j + k / 2, k - k / 2);
        }
    }
}
```



### 相关题目

[88.Merge Sorted Array 合并两个有序数组](https://leetcode-cn.com/problems/merge-sorted-array/)


