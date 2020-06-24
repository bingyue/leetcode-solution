### 365. 水壶问题

有两个容量分别为 x升 和 y升 的水壶以及无限多的水。请判断能否通过使用这两个水壶，从而可以得到恰好 z升 的水？

如果可以，最后请用以上水壶中的一或两个来盛放取得的 z升 水。

你允许：

- 装满任意一个水壶
- 清空任意一个水壶
- 从一个水壶向另外一个水壶倒水，直到装满或者倒空

```
示例 1: (From the famous "Die Hard" example)

输入: x = 3, y = 5, z = 4
输出: True
示例 2:

输入: x = 2, y = 6, z = 5
输出: False
```

###  题目分析

问题可以转花为找到m和n使得mx + ny = k，其中m，n可以为正数或负数。

对于这类问题，数学上可以证明只要x，y的最大公约数能整除k，就存在一对m，n实现mx + ny = k。 因此问题就转化为寻找x,y的最大公约数是否能整除k。


#### 最大公约数方法


```
class Solution {
    public boolean canMeasureWater(int x, int y, int z) {
     // 在一个瓶子为空的情况，另一个瓶子只能等于水的容量，不然没法搞
        if (x == 0 || y == 0) {
            if (x == z || y == z) return true;
            return false;
        }
        // 特判：x+y<z时一定是false！！！ 仔细品一品
        if (x + y < z) return false;
        // 辗转相除法 
        int tmp = gcd(x, y);
        return z % tmp == 0;
    }


    // 1071. 字符串的最大公因子
    private int gcd(int x, int y) {
        if (y == 0) return x;
        return gcd(y, x % y);
    }
}   
```