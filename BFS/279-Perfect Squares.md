### 279. 完全平方数

给定正整数 n，找到若干个完全平方数（比如 1, 4, 9, 16, ...）使得它们的和等于 n。你需要让组成和的完全平方数的个数最少。


```
示例 1:

输入: n = 12
输出: 3 
解释: 12 = 4 + 4 + 4.
```

### 题目描述

这道题目可以使用动态规划或者bfs广度优先的方式来解决。

#### 动态规划

```

首先初始化长度为n+1的数组dp，每个位置都为0
如果n为0，则结果为0。

因为1也是完全平方数，所以这个问题是可解的。

对数组进行遍历，下标为i，每次都将当前数字先更新为最大的结果，即dp[i]=i，比如i=4，最坏结果为4=1+1+1+1即为4个数字

动态转移方程为：dp[i] = MIN(dp[i], dp[i - j * j] + 1)，i表示当前数字，j表示所有小于i的可选，并且需要满足 j*j<i,j*j表示平方数

时间复杂度：O(n*sqrt(n))，sqrt为平方根
```

二层循环里的j * j 才是真正状态转移的关键。

```
class Solution {
     public int numSquares(int n) {
         // java 默认初始化值都为0
        int[] dp = new int[n + 1]; 
        for (int i = 1; i <= n; i++) {
            // 1 也是完全平方数
            // 最坏的情况就是每次+1
            dp[i] = i; 
            //这个循环里的j * j 才是真正状态转移的关键
            for (int j = 1; i - j * j >= 0; j++) { 
                dp[i] = Math.min(dp[i], dp[i - j * j] + 1); // 动态转移方程
            }
        }
        return dp[n];
    }
}

```

上面的循环体是优化后的，下面的也可以

```

for (int i = 1; i <= n; i++) {
            // 1 也是完全平方数
            // 最坏的情况就是每次+1
            dp[i]=i;
            for(int j=1;j<i;j++){
                if(i-j*j>=0){
                  dp[i]=Math.min(dp[i],dp[i-j*j]+1);
                }
            }
        }
```



#### 使用队列来求解无权图中的最短路径

这个题目可以构造一个有向无权图，然后通过计算求图中从n到0的最短路径。

![image](http://cdn.ctofun.com/leetcode279.jpg)

如果两个数字x-y相差一个完全平方数，那么连接一条边。

![image](http://cdn.ctofun.com/leetcode279-1.jpg)

比如图中的8，可以拆解为4+1+1+1+1,就是从8到0的路径是5，也可以拆解为4+4,也就是2，很明显返回2

##### bfs的模板


```
while queue 不空：
    cur = queue.pop()
    for 节点 in cur的所有相邻节点：
        if 该节点有效且未访问过：
            queue.push(该节点)
```


##### 代码实现


```java
class Solution {
    public int numSquares(int n) {
        Queue<Integer> queue = new LinkedList();
        // 创建 set 来存放非重复的元素
        Set<Integer> visited = new HashSet();
        queue.add(n);
        // 定义 level 记录完全平方数的个数
        int level = 0;
        while (!queue.isEmpty()) {
            int len = queue.size();
             // 每次有元素入队就代表还有剩余子平方数
            level++;
            for (int i = 0; i < len; i++) {
                // 从 1 开始取，每次拿平方数来比较
                int curr = queue.poll();
                for (int j = 1; j * j <= curr; j++) {
                    // 用当前结点减去平方数 1,4,9...
                    int next = curr - j * j;
                    // 找完所有的平方数即可返回
                    if (next == 0) return level;
                    // 如果 set 里面没有存放当前元素，则可以入队,入 set
                    if (!visited.contains(next)) {
                        queue.add(next);
                        visited.add(next);
                    }
                }
            }
        }
        return 0;
    }
}
```

写完这个以后，我们再对比下二叉树的层序遍历写法，回忆一下:



```
public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res=new ArrayList();

        if(root==null)
          return res;

        LinkedList<TreeNode> queue=new LinkedList();
        //先将头结点入队列
        // 正常队列对应的方法是offer添加，poll返回并删除第一个，peek返回第一个不删除
        queue.offer(root);
        while(!queue.isEmpty()){
            List<Integer> level=new ArrayList();
            //记录开始遍历前该层的长度
            int size=queue.size();
            //不能在for循环中比较，因为queue动态增加
            for(int i=0;i<size;i++){
                TreeNode node=queue.poll();
                // 取出当前节点
                level.add(node.val);
                //左右节点依次入队
                if(node.left!=null){
                  queue.offer(node.left);
                }
                if(node.right!=null){
                  queue.offer(node.right);
                }
            }
            // 完成一层遍历
            res.add(level);
        }

        return res;

    }
```
