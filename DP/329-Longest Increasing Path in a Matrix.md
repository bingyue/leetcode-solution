### 题目分析


给定一个整数矩阵，找出最长递增路径的长度。

对于每个单元格，你可以往上，下，左，右四个方向移动。 你不能在对角线方向上移动或移动到边界外（即不允许环绕）。


```
示例 1:

输入: nums = 
[
  [9,9,4],
  [6,6,8],
  [2,1,1]
] 
输出: 4 
解释: 最长递增路径为 [1, 2, 6, 9]。
示例 2:

输入: nums = 
[
  [3,4,5],
  [3,2,6],
  [2,2,1]
] 
输出: 4 
解释: 最长递增路径是 [3, 4, 5, 6]。注意不允许在对角线方向上移动。
```

### 题目分析

一个二维数组，让我们求矩阵中最长的递增路径，规定我们只能上下左右行走，不能走斜线或者是超过了边界。


#### 动态规划结合dfs

dp[i][j]=以matrix[i][j]为开始的最长递增路径的长度。

考察 matrix[i][j]和周围元素的关系，进行深度优先搜索检查。

递推数组的计算，需要通过dfs回溯来实现。
这种dfs的方式避免了维护一个visited数组，因为dfs本身就是去记录了访问路径。


```
class Solution {

    int[][] dp;
    public int longestIncreasingPath(int[][] matrix) {
        //针对[]这种输入，一定要先判断，而不是取行和列长度后再检查
        if(matrix.length<=0 || matrix[0].length<=0) return 0;
        int m=matrix.length;
        int n=matrix[0].length;
        dp=new int[m][n];
        int res=0;
        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){
                if(dp[i][j]==0){
                    dp[i][j]=dfs(matrix,i,j);
                }
                res=Math.max(dp[i][j],res);
            }
        }
        return res;
    }

    // 返回matrix[i][j]开始的最长递增子序列的长度
    int[] dr=new int[]{1,0,-1,0};
    int[] dc=new int[]{0,1,0,-1};
    private int dfs(int[][] matrix,int i,int j){
        if(dp[i][j]>0) return dp[i][j];
        //四个方向回溯
        int res=1;
        for(int k=0;k<4;k++){
            int row=i+dr[k];
            int col=j+dc[k];
            if( inArea(matrix,row,col) && matrix[row][col]>matrix[i][j]){
                //不要忘记递推中加一
              res=Math.max(res,dfs(matrix,row,col)+1);
            }
        }
        dp[i][j]=res;
        return res;
    }

    private boolean inArea(int[][] matrix,int i,int j){
        //越界
        if(i>= matrix.length || i<0 || j<0 ||j>= matrix[0].length){
            return false;
        }
        return true;
    }




}
```

#### 拓扑排序

拓扑排序是基于有向无环图的一个算法。
拓扑排序的实现有Kahn 算法和dfs算法两种。
下面是Kahn 算法，每个节点的入度等于四边比它小的节点的个数。

 
```
class Solution {
    public int longestIncreasingPath(int[][] matrix) {
        if (matrix==null||matrix.length==0){
            return 0;
        }        
        int[][] count = new int[matrix.length][matrix[0].length];
        int[][] direction = {{0, 1}, {0, -1}, {-1, 0}, {1, 0}};
        //统计每个点的入度用count数组保存，因为是递增，所以如果在上下左右，每发现一个比当前点小的数，当前点入度+1
        for (int i = 0; i < matrix.length; i++) {
            for (int j = 0; j < matrix[i].length; j++) {
                for (int[] d : direction) {
                    if (longestIncreasingPathVerify(matrix, i + d[0], j + d[1]) && matrix[i + d[0]][j + d[1]] < matrix[i][j]) {
                        count[i][j]++;
                    }
                }
            }
        }
        Deque<int[]> deque = new LinkedList<>();
        //count数组中所有入度为0的点加入队列
        for (int i = 0; i < count.length; i++) {
            for (int j = 0; j < count[i].length; j++) {
                if (count[i][j] == 0) {
                    deque.add(new int[]{i, j});
                }
            }
        }
        int ans = 0;
        while (!deque.isEmpty()) {
            ans++;
            //这个跟课程表I那个题不一样，需要一层一层的出列，而不是一个一个的出，因为课程表那个不关心队列长度
            for (int size = deque.size(); size > 0; size--) {
                int[] poll = deque.poll();
                for (int[] d : direction) {
                    if (longestIncreasingPathVerify(matrix, poll[0] + d[0], poll[1] + d[1]) && matrix[poll[0] + d[0]][poll[1] + d[1]] > matrix[poll[0]][poll[1]]) {
                        if (--count[poll[0] + d[0]][poll[1] + d[1]] == 0) {
                            deque.add(new int[]{poll[0] + d[0], poll[1] + d[1]});
                        }
                    }
                }
            }
        }
        return ans;
    }

    private boolean longestIncreasingPathVerify(int[][] matrix, int i, int j) {
        return i >= 0 && j >= 0 && i < matrix.length && j < matrix[i].length;
    }
}

```
