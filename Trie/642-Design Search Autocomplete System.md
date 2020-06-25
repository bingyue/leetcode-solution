### 642. 设计搜索自动补全系统

为搜索引擎设计一个搜索自动补全系统。用户会输入一条语句（最少包含一个字母，以特殊字符 '#' 结尾）。除 '#' 以外用户输入的每个字符，返回历史中热度前三并以当前输入部分为前缀的句子。下面是详细规则：

- 一条句子的热度定义为历史上用户输入这个句子的总次数。
- 返回前三的句子需要按照热度从高到低排序（第一个是最热门的）。如果有多条热度相同的句子，请按照 ASCII 码的顺序输出（ASCII 码越小排名越前）。
- 如果满足条件的句子个数少于 3，将它们全部输出。
- 如果输入了特殊字符，意味着句子结束了，请返回一个空集合。
你的工作是实现以下功能：

构造函数：

AutocompleteSystem(String[] sentences, int[] times): 这是构造函数，输入的是历史数据。 Sentences 是之前输入过的所有句子，Times 是每条句子输入的次数，你的系统需要记录这些历史信息。

现在，用户输入一条新的句子，下面的函数会提供用户输入的下一个字符：

List<String> input(char c): 其中 c 是用户输入的下一个字符。字符只会是小写英文字母（'a' 到 'z' ），空格（' '）和特殊字符（'#'）。输出历史热度前三的具有相同前缀的句子。


```
样例 ：
操作 ： AutocompleteSystem(["i love you", "island","ironman", "i love leetcode"], [5,3,2,2])
系统记录下所有的句子和出现的次数：
"i love you" : 5 次
"island" : 3 次
"ironman" : 2 次
"i love leetcode" : 2 次
现在，用户开始新的键入：


输入 ： input('i')
输出 ： ["i love you", "island","i love leetcode"]
解释 ：
有四个句子含有前缀 "i"。其中 "ironman" 和 "i love leetcode" 有相同的热度，由于 ' ' 的 ASCII 码是 32 而 'r' 的 ASCII 码是 114，所以 "i love leetcode" 在 "ironman" 前面。同时我们只输出前三的句子，所以 "ironman" 被舍弃。

输入 ： input(' ')
输出 ： ["i love you","i love leetcode"]
解释:
只有两个句子含有前缀 "i "。

输入 ： input('a')
输出 ： []
解释 ：
没有句子有前缀 "i a"。

输入 ： input('#')
输出 ： []
解释 ：

用户输入结束，"i a" 被存到系统中，后面的输入被认为是下一次搜索。
```
注释 ：

输入的句子以字母开头，以 '#' 结尾，两个字母之间最多只会出现一个空格。
即将搜索的句子总数不会超过 100。每条句子的长度（包括已经搜索的和即将搜索的）也不会超过 100。
即使只有一个字母，输出的时候请使用双引号而不是单引号。
请记住清零 AutocompleteSystem 类中的变量，因为静态变量、类变量会在多组测试数据中保存之前结果。详情请看这里。


### 题目分析


#### 代码实现一


单词查找树常用于存储字典中的所有单词。数的每一个层表示字符串对应位置上的字符。从词根开始到一个叶节点，就是一个单词。

修改单词查找树的结构，为每个单词增加访问次数标记 timestimes，它存储在对应单词的叶节点。


```
public class AutocompleteSystem {
    class Node {
        Node(String st, int t) {
            sentence = st;
            times = t;
        }
        String sentence;
        int times;
    }
    class Trie {
        int times;
        Trie[] branches = new Trie[27];
    }
    public int int_(char c) {
        return c == ' ' ? 26 : c - 'a';
    }
    public void insert(Trie t, String s, int times) {
        for (int i = 0; i < s.length(); i++) {
            if (t.branches[int_(s.charAt(i))] == null)
                t.branches[int_(s.charAt(i))] = new Trie();
            t = t.branches[int_(s.charAt(i))];
        }
        t.times += times;
    }
    public List < Node > lookup(Trie t, String s) {
        List < Node > list = new ArrayList < > ();
        for (int i = 0; i < s.length(); i++) {
            if (t.branches[int_(s.charAt(i))] == null)
                return new ArrayList < Node > ();
            t = t.branches[int_(s.charAt(i))];
        }
        traverse(s, t, list);
        return list;
    }
    public void traverse(String s, Trie t, List < Node > list) {
        if (t.times > 0)
            list.add(new Node(s, t.times));
        for (char i = 'a'; i <= 'z'; i++) {
            if (t.branches[i - 'a'] != null)
                traverse(s + i, t.branches[i - 'a'], list);
        }
        if (t.branches[26] != null)
            traverse(s + ' ', t.branches[26], list);
    }
    Trie root;
    public AutocompleteSystem(String[] sentences, int[] times) {
        root = new Trie();
        for (int i = 0; i < sentences.length; i++) {
            insert(root, sentences[i], times[i]);
        }
    }
    String cur_sent = "";
    public List < String > input(char c) {
        List < String > res = new ArrayList < > ();
        if (c == '#') { 
            insert(root, cur_sent, 1);
            cur_sent = "";
        } else {
            cur_sent += c;
            List < Node > list = lookup(root, cur_sent);
            Collections.sort(list, (a, b) -> a.times == b.times ? a.sentence.compareTo(b.sentence) : b.times - a.times);
            for (int i = 0; i < Math.min(3, list.size()); i++)
                res.add(list.get(i).sentence);
        }
        return res;
    }
} 

```


#### 代码实现二,前缀树加优先队列

前缀树（字典树）的基本知识不多讲。这里说明一下需要注意的地方。

- 前缀树为27叉树，每个节点的子节点记录在长度为27的数组中。0-25对应a-z（index = char - 'a'）。26对应空格。
- 如果节点为句子终结节点，其中的属性sentence会记录整个句子（方便自动补全时返回结果），times会记录相应热度（之前搜索的次数）。
- 由于在搜索过程中，每次只会输入一个字符，所以前缀树对象中包含一个当前节点对象current，作为之前搜索到的节点。current被初始化为root。每次新来一个字符，该属性都会更改为输入字符对应的子节点。如果遇到终结符#，current重置为root。
- 每次搜索以当前已输入部分作为前缀的句子时，会把当前节点作为参数调用递归函数searchSentence。其返回值保存在pq这一优先队列中。该优先队列存放的是树节点，通过自定义比较函数比较节点的排名（先比较times，相等则比较sentence）。当优先队列存放元素超过3个时，弹出顶部的元素，也就是排名最靠后的节点。所以最多返回3个排名最靠前的节点对应的句子。


```java
class AutocompleteSystem {
    private Trie trie;
    public AutocompleteSystem(String[] sentences, int[] times) {
        trie = new Trie();
        int n = sentences.length;
        for (int i = 0; i < n; ++i)
            trie.insert(sentences[i], times[i]);
    }
    
    public List<String> input(char c) {
        return trie.searchChar(c);
    }

    class Trie {
    private TrieNode root;
    private TrieNode current;           // 在插入过程中的当前节点，插完后重置为root
    private PriorityQueue<TrieNode> pq; // 小顶堆，自定义比较函数，用来保存排在前3的句子
    private StringBuilder sb;           // 单个字符输入时，保存已输入字符
    
    public Trie() {
        root = new TrieNode();
        current = root;
        Comparator<TrieNode> comparator = new Comparator<TrieNode>() {
            public int compare(TrieNode node1, TrieNode node2) {
                if (node1.times != node2.times)
                    return node1.times - node2.times;
                return node2.sentence.compareTo(node1.sentence);
            }
        };
        pq = new PriorityQueue(4, comparator);
        sb = new StringBuilder();
    }
    
    public void insert(String sentence, int times) {
        int n = sentence.length();
        for (int i = 0; i < n; ++i)
            insertNewChar(sentence.charAt(i));
        current.sentence = sentence;
        current.times += times;
        current = root;
    }
    
    // 插入新字符，并更新current
    // 之前前缀没有该字符，返回true
    private boolean insertNewChar(char c) {
        int charNo = c == ' ' ? 26 : c - 'a';
        if (current.children[charNo] != null) {
            current = current.children[charNo];
            return false;
        }
        current.children[charNo] = new TrieNode();
        current = current.children[charNo];
        return true;
    }
    
    // 在当前节点中搜索字符，如果为终止符，则结束该句搜索过程
    // 否则，如果插入字符之前没有该前缀，则返回空列表
    // 否则，从该字符所在的点开始，搜索所有句子，并找出其中3个最大的
    public List<String> searchChar(char c) {
        List<String> result = new LinkedList();
        if (c == '#') {
            current.sentence = sb.toString();
            ++current.times;
            current = root;
            sb.delete(0, sb.length());
            return result;
        }
        
        sb.append(c);
        
        if (insertNewChar(c))
            return result;
        
        searchSentence(current);
        while (pq.peek() != null)
            result.add(0, pq.poll().sentence);
        
        return result;
    }
    
    // 从node点开始向下寻找句子，保存在优先队列里，超过3个弹出
    private void searchSentence(TrieNode node) {
        if (node.sentence != null) {
            pq.offer(node);
            if (pq.size() > 3)
                pq.poll();
        }
        for (TrieNode child: node.children) {
            if (child != null)
                searchSentence(child);
        }
    }
}



    class TrieNode {
    public TrieNode[] children;
    public String sentence;     // 如果该点为句子终点，保存整个句子
    public int times;           // 如果该点为句子终点，记录句子热度
    
    public TrieNode() {
        times = 0;
        children = new TrieNode[27];
        sentence = null;
    }
    
    }
}


```

#### 代码实现三

```
class AutocompleteSystem {
    // 字典树
    private final Trie root;
    // 记录前一结点
    private Trie pre;
    // 记录历史字符串
    private StringBuffer sb;

    public AutocompleteSystem(String[] sentences, int[] times) {
        this.root = new Trie(3);
        this.pre = root;
        sb = new StringBuffer();

        for (int i = 0; i < sentences.length; i++) {
            root.insert(sentences[i], times[i]);
        }
    }

    public List<String> input(char c) {
        List<String> res = new LinkedList<>();
        // 终止
        if (c == '#') {
            // 更新历史记录
            saveHistory(sb.toString());
            // 清空状态
            pre = root;
            sb = new StringBuffer();
            return res;
        }

        // 记录历史记录
        sb.append(c);

        // 更新结点
        if (pre != null) pre = c == ' ' ? pre.children[26] : pre.children[c - 'a'];
        // 搜索其后所有值
        if (pre != null) pre.search(res);

        return res;
    }

    private void saveHistory(String s) {
        root.insert(s, 1);
    }
}

class Trie {
    // 只有结尾结点才有的属性
    boolean isEnding;
    int count;
    String s;

    Trie[] children = new Trie[27];
    // 最小堆保存最大的k个
    int k;
    PriorityQueue<Trie> queue;

    Trie(int k) {
        this.k = k;
        // 最小堆
        this.queue = new PriorityQueue<>((a, b) -> a.count == b.count ? b.s.compareTo(a.s) : a.count - b.count);
        // 最后一位存放空格
        this.children = new Trie[27];
    }

    public void insert(String word, int val) {
        if (word == null || word.isEmpty()) return;

        Trie temp = this;
        // 记录路径上的结点
        List<Trie> path = new LinkedList<>();
        for (char c : word.toCharArray()) {
            int idx = c == ' ' ? 26 : (c - 'a');
            if (temp.children[idx] == null) temp.children[idx] = new Trie(k);

            temp = temp.children[idx];
            path.add(temp);
        }
        // 结尾字符标记及计数更新
        temp.isEnding = true;
        temp.count += val;
        temp.s = word;

        // 关联终止节点到路径上每个结点
        for (Trie cur : path) {
            // 移除老的值
            if (cur.queue.contains(temp)) cur.queue.remove(temp);
            // 加入新的值
            cur.queue.add(temp);
            // 大于k个，将最小的弹出
            while (cur.queue.size() > k) cur.queue.poll();
        }
    }

    public void search(List<String> res) {
        List<Trie> temp = new ArrayList<>();
        // 加入堆中元素
        while (!queue.isEmpty()) {
            Trie trie = queue.poll();
            temp.add(trie);
            res.add(trie.s);
        }
        queue.addAll(temp);
        Collections.reverse(res);
    }
}
```
