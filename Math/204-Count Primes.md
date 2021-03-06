统计所有小于非负整数 n 的质数的数量。

示例:

```
输入: 10
输出: 4
解释: 小于 10 的质数一共有 4 个, 它们是 2, 3, 5, 7 。
```

### 题目分析


==质数又称素数。一个大于1的自然数，除了1和它自身外，不能被其他自然数整除的数叫做质数==。

如何判断整数 n 是否是素数?

厄拉多塞筛法。

如求10之内的质数，首先列出2~N-1的所有数，如果当前数为质数，则其倍数就是质数，如

- 第一个质数为2，在2上画圈，其倍数4/6/8不是质数，划掉4/6/8，继续遍历
- 下一个质数为3，在3上画圈，其倍数6/9不是质数，划掉6/9，继续遍历
- 下一个质数为5，在5上画圈，没有倍数，继续遍历
- 下一个质数为7，在7上画圈，没有倍数，继续遍历。
- 最后再次遍历整个数组，画圈的数字就是质数，即2,3,5,7

转换为代码就是如果需要求<n的所有质数个数，则创建一个长度为n的整数数组，所有元素值变为1，1表示对应的索引值为质数，0表示对应的索引值为非质数。从2开始遍历，如果当前数字值为1，则获取其所有倍数，将元素值变为0（标记为非质数）。遍历完成后再次遍历数组，从2开始，记录元素为1的个数，即为对应的质数个数。



```java
class Solution {
    public int countPrimes(int n) {
        boolean[] isPrim = new boolean[n];
        // 默认都设置为是质数
        Arrays.fill(isPrim, true);
        for (int i = 2; i * i < n; i++) {
            //如果当前数为质数，那么它的倍数不是质数
            if (isPrim[i]) {
                for (int j = i * i; j < n; j += i)
                    isPrim[j] = false;
            }
        }
        int count = 0;
        // 遍历一遍
        //0和1不是质数，注意要从2开始!!
        for (int i = 2; i < n; i++) {
            if (isPrim[i]) count++;
        }
        return count;
    }
}

```

#### 参考代码二

```
public int countPrimes(int n) {
    int[] nums = new int[n];
    for (int i = 0; i < n; i++) {
        nums[i] = 1;
    }
    for (int i = 2; i < n; i++) {
        //如果当前数为质数
        if (nums[i] == 1) {
            //将对应数的倍数变为0
            for (int j = 2; i * j < n; j++) {
                nums[i * j] = 0;
            }
        }
    }
    //遍历数组，统计值为1的元素个数
    int res = 0;
    for (int i = 2; i < n; i++) {
        if (nums[i] == 1)
            res++;
    }
    return res;
}

```
