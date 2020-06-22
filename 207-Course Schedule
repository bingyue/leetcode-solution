你这个学期必须选修 numCourse 门课程，记为 0 到 numCourse-1 。

在选修某些课程之前需要一些先修课程。 例如，想要学习课程 0 ，你需要先完成课程 1 ，我们用一个匹配来表示他们：[0,1]

给定课程总量以及它们的先决条件，请你判断是否可能完成所有课程的学习？


```
示例 1:

输入: 2, [[1,0]] 
输出: true
解释: 总共有 2 门课程。学习课程 1 之前，你需要完成课程 0。所以这是可能的。
示例 2:

输入: 2, [[1,0],[0,1]]
输出: false
解释: 总共有 2 门课程。学习课程 1 之前，你需要先完成​课程 0；并且学习课程 0 之前，你还应先完成课程 1。这是不可能的。
```

提示：

输入的先决条件是由 边缘列表 表示的图形，而不是 邻接矩阵 。详情请参见图的表示法。
你可以假定输入的先决条件中没有重复的边。
1 <= numCourses <= 10^5

### 题目分析

本题可约化为： 课程安排图是否是 有向无环图(DAG)。即课程间规定了前置条件，但不能构成任何环路，否则课程前置条件将不成立。

思路是通过 拓扑排序 判断此课程安排图是否是 有向无环图(DAG) 。 拓扑排序原理： 对 DAG 的顶点进行排序，使得对每一条有向边 (u, v)(u,v)，均有 uu（在排序记录中）比 vv 先出现。亦可理解为对某点 vv 而言，只有当 vv 的所有源点均出现了，vv 才能出现。

通过课程前置条件列表 prerequisites 可以得到课程安排图的 邻接表 adjacency，以降低算法时间复杂度，以下两种方法都会用到邻接表。

#### 方法一 拓扑排序

拓扑排序的方法必须掌握。

```
class Solution {
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        // 入度表
        int[] indegrees=new int[numCourses];
        // 邻接表
        List<List<Integer>> adjacency=new ArrayList();
        // 用于拓扑排序的队列
        Queue<Integer> queue=new LinkedList();
        //初始化入度表
        for(int i=0;i<numCourses;i++){
            adjacency.add(new ArrayList());
        }

        // 初始化数据
        for(int[] pair:prerequisites){
            // pair就是[0,1] 这样的一维二元素数组
            // 设置入度表
            indegrees[pair[0]]+=1;
            //邻接表就是取到上一个
            adjacency.get(pair[1]).add(pair[0]);
        }

        for(int i = 0; i < numCourses; i++){
            // 获取入度为0的数据
            // 加入队列中
            if(indegrees[i] == 0) queue.add(i);
        }

        // BFS TopSort
        // 广度优先，拓扑排序
        while(!queue.isEmpty()) {
            int pre = queue.poll();
            numCourses--;
            for(int cur : adjacency.get(pre)){
                //前边加加，这里减减
                if(--indegrees[cur] == 0) 
                  queue.add(cur);
            }
        }
        return numCourses == 0;
    }
}
```

#### 方法二 dfs实现拓扑排序

借助一个标志列表 flags，用于判断每个节点 i （课程）的状态：

- 未被 DFS 访问：i == 0；
- 已被其他节点启动的 DFS 访问：i == -1；
- 已被当前节点启动的 DFS 访问：i == 1。


对 numCourses 个节点依次执行 DFS，判断每个节点起步 DFS 是否存在环，若存在环直接返回 FalseFalse。DFS 流程；
- 终止条件：
- 当 flag[i] == -1，说明当前访问节点已被其他节点启动的 DFS 访问，无需再重复搜索，直接返回 TrueTrue。
- 当 flag[i] == 1，说明在本轮 DFS 搜索中节点 i 被第 22 次访问，即 课程安排图有环 ，直接返回 FalseFalse。
- 将当前访问节点 i 对应 flag[i] 置 11，即标记其被本轮 DFS 访问过；
- 递归访问当前节点 i 的所有邻接节点 j，当发现环直接返回 FalseFalse；
- 当前节点所有邻接节点已被遍历，并没有发现环，则将当前节点 flag 置为 -1−1 并返回 TrueTrue。
- 若整个图 DFS 结束并未发现环，返回 TrueTrue。




```
class Solution {
      public boolean canFinish(int numCourses, int[][] prerequisites) {
        List<List<Integer>> adjacency = new ArrayList<>();
        for (int i = 0; i < numCourses; i++)
            adjacency.add(new ArrayList<>());
        int[] flags = new int[numCourses];
        for (int[] cp : prerequisites)
            adjacency.get(cp[1]).add(cp[0]);
        for (int i = 0; i < numCourses; i++)
            if (!dfs(adjacency, flags, i)) return false;
        return true;
    }

    private boolean dfs(List<List<Integer>> adjacency, int[] flags, int i) {
        if (flags[i] == 1)
            return false;
        if (flags[i] == -1)
            return true;
        flags[i] = 1;
        for (Integer j : adjacency.get(i))
            if (!dfs(adjacency, flags, j))
                return false;
        flags[i] = -1;
        return true;
    }
}
```
