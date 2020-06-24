### 题目描述

给定一个仅包含数字 2-9 的字符串，返回所有它能表示的字母组合。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。


```
示例:

输入："23"
输出：["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
```

说明:
尽管上面的答案是按字典序排列的，但是你可以任意选择答案输出的顺序。


###  题目解答

需要建立一个字典，用来保存每个数字所代表的字符串，然后还需要一个变量 level，记录当前生成的字符串的字符个数，实现套路和上述那些题十分类似。

字典可以使用数组，下标对应非常方便。


```
class Solution {
    //初始化字典
    //0和1下标也一起初始化
    String[] dict=new String[]{"","","abc","def","ghi","jkl","mno","pqrs","tuv","wxyz"};
    public List<String> letterCombinations(String digits) {

        //边界条件
        if(digits==null || digits.length()==0){
            return new ArrayList();
        }

        //结果集
        List<String> ret=new ArrayList();
        
        int index=0;
        StringBuilder curr=new StringBuilder();
        build(digits,index,curr,ret);
        return ret;
    }

     //digits 输入的字符串
     //index 本次操作的位置
     //curr 中间变量保存
     //ret 结果集
     private void build(String digits,int index,StringBuilder curr ,List<String> ret){
         //回溯的终止条件
         //不继续处理，已经递归完最后一个数字了
         if(index==digits.length()){
             ret.add(curr.toString());
             //记得return
             return;
         }

         //首先找到数字
         //使用-'0'方式转换为int
         int num=digits.charAt(index)-'0';

         //开始处理
         for(int i=0;i<dict[num].length();i++){
             curr.append(dict[num].charAt(i));
             build(digits,index+1,curr,ret);
             //移除，到底是删除哪里的呢？
             //删除的是最后一个字符，也就是DFS一次遍历到底后回溯
             curr.deleteCharAt(curr.length()-1);
         }
     }
}
```