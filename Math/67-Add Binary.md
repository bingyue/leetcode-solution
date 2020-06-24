### 题目描述

给你两个二进制字符串，返回它们的和（用二进制表示）。

输入为 非空 字符串且只包含数字 1 和 0。

```
示例 1:

输入: a = "11", b = "1"
输出: "100"
```

### 题目分析


#### 转换为十进制，再转换回去

使用内置函数的简单方法：

- 将 a 和 b 转换为十进制整数。
- 求和。
- 将求和结果转换为二进制整数。


```
public String addBinary(String a, String b) {
    return Integer.toBinaryString(Integer.parseInt(a, 2) + Integer.parseInt(b, 2));
  }

```
该方法受输入字符串 a 和 b 的长度限制。字符串长度太大时，不能将其转换为 Integer，Long 或者 BigInteger 类型。

#### 每个位进行计算，经典的进位carry方法

官方实现：

```
class Solution {
    public String addBinary(String a, String b) {

        StringBuilder ans = new StringBuilder();
        // 从低位开始计算
        int lenA = a.length()-1;
        int lenB = b.length()-1;
        int carry = 0;
        while (lenA >=0 || lenB >= 0) {
            //判断是否超出
            int valA = lenA<0 ? 0 : a.charAt(lenA)-'0';
            int valB = lenB<0 ? 0 : b.charAt(lenB)-'0';
            //取余计算当前数值
            int sum = (valA + valB + carry)%2;
            ans.append(sum);
            //取商计算进位
            carry = (valA + valB + carry)/2;
            lenA--;
            lenB--;
        }

        ans.append(carry==0 ? "" : carry);
        //因为字符串是从低位开始的，必须reverse一下
        return  ans.reverse().toString();
    }
}

```

简洁的一个实现：


```
class Solution {
    public String addBinary(String a, String b) {
        StringBuilder ans = new StringBuilder();
        int lenA = a.length()-1;
        int lenB = b.length()-1;
        int carry = 0;
        while (lenA >=0 || lenB >= 0) {
            //判断是否超出
            int valA = lenA<0 ? 0 : a.charAt(lenA)-'0';
            int valB = lenB<0 ? 0 : b.charAt(lenB)-'0';
            //取余计算当前数值
            int sum = (valA + valB + carry)%2;
            ans.append(sum);
            //取商计算进位
            carry = (valA + valB + carry)/2;
            lenA--;
            lenB--;
        }

        ans.append(carry==0 ? "" : carry);
        //因为字符串是从低位开始的，必须reverse一下
        return  ans.reverse().toString();
    }
}
```
