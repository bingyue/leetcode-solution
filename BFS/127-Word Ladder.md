### 127. 单词接龙

### 题目描述

给定两个单词（beginWord 和 endWord）和一个字典，找到从 beginWord 到 endWord 的最短转换序列的长度。转换需遵循如下规则：

1. 每次转换只能改变一个字母。
1. 转换过程中的中间单词必须是字典中的单词。

说明:

- 如果不存在这样的转换序列，返回 0。
- 所有单词具有相同的长度。
- 所有单词只由小写字母组成。
- 字典中不存在重复的单词。
- 你可以假设 beginWord 和 endWord 是非空的，且二者不相同。



```
示例 1:

输入:
beginWord = "hit",
endWord = "cog",
wordList = ["hot","dot","dog","lot","log","cog"]

输出: 5

解释: 一个最短转换序列是 "hit" -> "hot" -> "dot" -> "dog" -> "cog",
     返回它的长度 5。
示例 2:

输入:
beginWord = "hit"
endWord = "cog"
wordList = ["hot","dot","dog","lot","log"]

输出: 0

解释: endWord "cog" 不在字典中，所以无法进行转换。


```

### 题目分析

#### 广度优先搜索

我们将问题抽象在一个无向无权图中，每个单词作为节点，差距只有一个字母的两个单词之间连一条边。问题变成找到从起点到终点的最短路径，如果存在的话。因此可以使用广度优先搜索方法。

![image](http://note.youdao.com/yws/res/29669/8515685E94764C5085C1A286395523BA)


```java
class Solution {
    public int ladderLength(String beginWord, String endWord, List<String> wordList) {

        //corner case
         if (!wordList.contains(endWord)) {
            return 0;
        }
        Queue<String> queue = new LinkedList<>();
        boolean[] visited = new boolean[wordList.size()];
        int depth = 0;
        int idx = wordList.indexOf(beginWord);
        if (idx != -1) {
            visited[idx] = true;
        }
        queue.offer(beginWord);
        while (!queue.isEmpty()) {
            int size = queue.size();
            depth ++;
            for(int i=0;i<size;i++){
                String start = queue.poll();
                for (int j = 0; j < wordList.size(); ++j) {
                    if (visited[j]) {
                        continue;
                    }
                    String tmp = wordList.get(j);
                    if (!convert(start, tmp)) {
                        continue;
                    }
                    if (tmp.equals(endWord)) {
                        return depth + 1;
                    }
                    // 加入队列
                    queue.offer(tmp);
                    visited[j] = true;
                }
            }
        }
        return 0;
    }

    private boolean convert(String s1, String s2) {
        int count = 0;
        for (int i = 0; i < s1.length(); ++i) {
            if (s1.charAt(i) != s2.charAt(i)) {
                count++;
            }
            //判断count++以后的是否大于1，这个顺序还挺重要
            if (count > 1) return false;

        }
        return true;
    }
}
```

#### 双端进行的广度优先搜索

这个具体实现参考相关题解吧。

