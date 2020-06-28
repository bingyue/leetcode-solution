### 题目描述

数字 n 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 有效的 括号组合。

```
示例：

输入：n = 3
输出：[
       "((()))",
       "(()())",
       "(())()",
       "()(())",
       "()()()"
     ]

```


### 题目解答

在 LeetCode 中有关括号的题共有七道，除了这一道的另外六道是 Score of Parentheses，Valid Parenthesis String， Remove Invalid Parentheses，Different Ways to Add Parentheses，Valid Parentheses 和 Longest Valid Parentheses。


这道题给定一个数字n，让生成共有n个括号的所有正确的形式，对于这种列出所有结果的题首先还是考虑用递归 Recursion 来解，由于字符串只有左括号和右括号两种字符，而且最终结果必定是左括号3个，右括号3个，所以这里定义两个变量 left 和 right 分别表示剩余左右括号的个数，如果在某次递归时，左括号的个数大于右括号的个数，说明此时生成的字符串中右括号的个数大于左括号的个数，即会出现 ')(' 这样的非法串，所以这种情况直接返回，不继续处理。

如果 left 和 right 都为0，则说明此时生成的字符串已有3个左括号和3个右括号，且字符串合法，则存入结果中后返回。如果以上两种情况都不满足，若此时 left 大于0，则调用递归函数，注意参数的更新，若 right 大于0，则调用递归函数，同样要更新参数。


#### 参考代码一


```
class Solution {
     public List<String> generateParenthesis(int n) {
        List<String> ans = new ArrayList();
        backtrack(ans, new StringBuilder(), 0, 0, n);
        return ans;
    }

    public void backtrack(List<String> ans, StringBuilder cur, int open, int close, int max){
        if (cur.length() == max * 2) {
            ans.add(cur.toString());
            return;
        }

        if (open < max) {
            cur.append('(');
            backtrack(ans, cur, open+1, close, max);
            cur.deleteCharAt(cur.length() - 1);
        }
        if (close < open) {
            cur.append(')');
            backtrack(ans, cur, open, close+1, max);
            //回溯法
            cur.deleteCharAt(cur.length() - 1);
        }
    }

}
```


#### 参考代码二，使用String
注意String在每次递归的过程中，没有改变变量的值，如果使用StringBuilder，需要同步删除。


```
 public class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> res = new ArrayList<String>();
        helper(n, n, "", res);
        return res;
    }
    void helper(int left, int right, String out, List<String> res) {
    
        //注意left>right的这个分支
        //因为在处理是首先进行left--，所以left一定<=right
        if (left < 0 || right < 0 || left > right) 
        return;
       
        if (left == 0 && right == 0) {
            res.add(out);
            return;
        }
        
        //处理左括号
        //注意一个写法，下面的写法会导致调用完成以后再+1，--left也不行，会改变left的值，递归本身就更新left了
        // helper(left--,right,out + "(",ret);
        helper(left - 1, right, out + "(", res);
        helper(left, right - 1, out + ")", res);
    }
}

```

#### 参考代码三

```

public class Solution {
    ArrayList<String> r = new ArrayList<String>();
    public ArrayList<String> generateParenthesis(int n) {
        //保证左边‘（’的数量始终大于等于右边的‘）’数量，可以考虑回溯法
        ArrayList<String> s = new ArrayList<String>();
        gp("",0,0,n);
        return r;
    }
    private void gp(String s,int left,int right,int n){
        if(right == n){
            r.add(s);
        }
        if(left < n){
            gp(s+"(",left+1,right,n);
        }
        // 递归过程中 左括号x的个数必须大于等于右括号个数
        if(left > right){
            gp(s+")",left,right+1,n);
        }
    }
}
```
