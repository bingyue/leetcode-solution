### 268. 丢失的数字

### 题目描述

给定一个包含 0, 1, 2, ..., n 中 n 个数的序列，找出 0 .. n 中没有出现在序列中的那个数。


```
示例 1:

输入: [3,0,1]
输出: 2
示例 2:

输入: [9,6,4,2,3,5,7,0,1]
输出: 8
```

说明:
你的算法应具有线性时间复杂度。你能否仅使用额外常数空间来实现?


### 题目分析

#### 数字异或方式

和以前的一道题（只出现一次的数字）有异曲同工之处。异或操作（^）是一种很好的方式，不用考虑sum越界问题。

举个例子：


```
0 ^ 4 = 4
4 ^ 4 = 0
```

那么，就可以不用求和，直接使用异或运算^进行 抵消，剩下的数字就是缺失的了。



```
class Solution {
    public int missingNumber(int[] nums) {

        //为什么初始化成这个，就是初始化成n
        int res=nums.length;
        for (int i = 0; i < nums.length; i++) {
            res^=nums[i];
            res^=i;
        }
        return res;
    }
}
```


#### 数学方式，高斯求和


```
class Solution {
    public int missingNumber(int[] nums) {
        int sum = 0;
        for(int i = 0; i < nums.length; i++){
            sum += nums[i];
        }
        // 高斯求和=（首项+末项）*项数/2
        // 1+2+3..+100=(1+100)+(2+99)..(50+51)=101*50
        return nums.length * (nums.length + 1) / 2 - sum;
    }
}
```


#### 哈希表方式


```
class Solution {
    public int missingNumber(int[] nums) {

        Set<Integer> set = new HashSet<>();
        for(int i = 0; i < nums.length; i++) set.add(nums[i]);   
        for(int i = 0; i <= nums.length; i++)
            if(!set.contains(i)) return i;
        return -1;
    }
}
```



#### 排序实现,时间复杂度为排序的nlogn

- 正常的包含0，1，2，3中4个数的数列是，[0,1,2,3] 应该满足nums[i]=i
- 如果只包含3个数，有一个没出现，比如[0,1,2] [1,2,3] [0,1,3]
- 排序以后，如果0不在首位，那么就缺了0
- 如果末尾的数字不等于数组长度，那么就是缺了末尾的数字
- 如果是在中间的数字缺失，就从0开始遍历一遍，如果不满足nums[i] == nums[i-1] + 1，那么就是缺了nums[i-1] + 1这个值

```
class Solution {
    public int missingNumber(int[] nums) {
        Arrays.sort(nums);

        // 判断 n 是否出现在末位
        if (nums[nums.length-1] != nums.length) {
            return nums.length;
        }
        // 判断 0 是否出现在首位
        else if (nums[0] != 0) {
            return 0;
        }

        // 此时缺失的数字一定在 (0, n) 中
        for (int i = 1; i < nums.length; i++) {
            int expectedNum = nums[i-1] + 1;
            if (nums[i] != expectedNum) {
                return expectedNum;
            }
        }

        // 未缺失任何数字（保证函数有返回值）
        return -1;
    }
}
```
