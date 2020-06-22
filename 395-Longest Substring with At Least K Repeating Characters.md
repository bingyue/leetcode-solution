### 395. 至少有K个重复字符的最长子串

找到给定字符串（由小写字符组成）中的最长子串 T ， 要求 T 中的每一字符出现次数都不少于 k 。输出 T 的长度。


```
示例 1:

输入:
s = "aaabb", k = 3

输出:
3

最长子串为 "aaa" ，其中 'a' 重复了 3 次。
示例 2:

输入:
s = "ababbc", k = 2

输出:
5

最长子串为 "ababb" ，其中 'a' 重复了 2 次， 'b' 重复了 3 次。
```


### 题目分析

注意，这道题目没有要求连续，怎么理解呢，参考输入示例2、

#### 结合哈希表，分治处理思路

先用hash表统计s中每个字符出现的次数，显然如果字符c出现的次数小于k，c必然不在最长子串里面，根据这个特性可以将原始s分割成多个子串递归地求解问题。


```java
class Solution {
    public int longestSubstring(String s, int k) {

        //corner case
        if(s.length()<k ) return 0;
        if(k<=1) return s.length();

        HashMap<Character,Integer> map=new HashMap();
        for(char ch:s.toCharArray()){
            map.put(ch,map.getOrDefault(ch,0)+1);
        }

        int i=0;
        while(i<s.length()){
            char ch=s.charAt(i);
            if(map.get(ch)<k){
                //可能出现连续的重复字符，优化
                while(i+1<s.length() && s.charAt(i+1)==ch){
                    i++;
                }
                return Math.max(longestSubstring(s.substring(0,i),k),longestSubstring(s.substring(i+1),k));
            }
            i++;
        }
        return s.length();
    }
}
```
