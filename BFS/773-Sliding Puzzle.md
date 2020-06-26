在一个 2 x 3 的板上（board）有 5 块砖瓦，用数字 1~5 来表示, 以及一块空缺用 0 来表示.

一次移动定义为选择 0 与一个相邻的数字（上下左右）进行交换.

最终当板 board 的结果是 [[1,2,3],[4,5,0]] 谜板被解开。

给出一个谜板的初始状态，返回最少可以通过多少次移动解开谜板，如果不能解开谜板，则返回 -1 。


```
示例：

输入：board = [[1,2,3],[4,0,5]]
输出：1
解释：交换 0 和 5 ，1 步完成
输入：board = [[1,2,3],[5,4,0]]
输出：-1
解释：没有办法完成谜板
输入：board = [[4,1,2],[5,0,3]]
输出：5
解释：
最少完成谜板的最少移动次数是 5 ，
一种移动路径:
尚未移动: [[4,1,2],[5,0,3]]
移动 1 次: [[4,1,2],[0,5,3]]
移动 2 次: [[0,1,2],[4,5,3]]
移动 3 次: [[1,0,2],[4,5,3]]
移动 4 次: [[1,2,0],[4,5,3]]
移动 5 次: [[1,2,3],[4,5,0]]
输入：board = [[3,2,4],[1,5,0]]
输出：14
```

提示：

board 是一个如上所述的 2 x 3 的数组.
board[i][j] 是一个 [0, 1, 2, 3, 4, 5] 的排列.


### 题目分析

#### 这道题目可以对标752，打开转盘锁，解题技巧是这样的，把二维的矩阵转换成一行一行开始遍历的一维的字符串，这样就和转盘锁完全一致了。



比如说输入的二维数组board = [[4,1,2],[5,0,3]]，算法应该返回 5：

![image](https://note.youdao.com/src/8B034F92B18D46D0A5D5BC6316ECA8C3)

如果输入的是board = [[1,2,3],[4,0,5]]，则算法返回 -1，因为这种局面下无论如何都不能赢得游戏。

#### BFS 算法并不只是一个寻路算法，而是一种暴力搜索算法，只要涉及暴力穷举的问题，BFS 就可以用，而且可以最快地找到答案。

我们的问题就转化成了：如何穷举出board当前局面下可能衍生出的所有局面？这就简单了，看数字 0 的位置呗，和上下左右的数字进行交换就行了：

![image](https://note.youdao.com/src/0C5B11EDC56A4470B78B7C19DAAF5173)

这样其实就是一个 BFS 问题，每次先找到数字 0，然后和周围的数字进行交换，形成新的局面加入队列…… 当第一次到达target时，就得到了赢得游戏的最少步数。


#### 二维数组的压缩，这里是一个非常的技巧，要记住

我们这里的board仅仅是 2x3 的二维数组，所以可以压缩成一个一维字符串。其中比较有技巧性的点在于，二维数组有「上下左右」的概念，压缩成一维后，如何得到某一个索引上下左右的索引？

很简单，我们只要手动写出来这个映射就行了：


```
vector<vector<int>> neighbor = {
    { 1, 3 },
    { 0, 4, 2 },
    { 1, 5 },
    { 0, 4 },
    { 3, 1, 5 },
    { 4, 2 }
};

```


这个含义就是，在一维字符串中，索引i在二维数组中的的相邻索引为neighbor[i]，：

![image](https://note.youdao.com/src/75673C96AA4D4BA99A992881151C9C9C)

至此，我们就把这个问题完全转化成标准的 BFS 问题.

#### C++代码实现


```
int slidingPuzzle(vector<vector<int>>& board) {
    int m = 2, n = 3;
    string start = "";
    string target = "123450";
    // 将 2x3 的数组转化成字符串
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            start.push_back(board[i][j] + '0');
        }
    }
    // 记录一维字符串的相邻索引
    vector<vector<int>> neighbor = {
        { 1, 3 },
        { 0, 4, 2 },
        { 1, 5 },
        { 0, 4 },
        { 3, 1, 5 },
        { 4, 2 }
    };

    /******* BFS 算法框架开始 *******/
    queue<string> q;
    unordered_set<string> visited;
    q.push(start);
    visited.insert(start);

    int step = 0;
    while (!q.empty()) {
        int sz = q.size();
        for (int i = 0; i < sz; i++) {
            string cur = q.front(); q.pop();
            // 判断是否达到目标局面
            if (target == cur) {
                return step;
            }
            // 找到数字 0 的索引
            int idx = 0;
            for (; cur[idx] != '0'; idx++);
            // 将数字 0 和相邻的数字交换位置
            for (int adj : neighbor[idx]) {
                string new_board = cur;
                swap(new_board[adj], new_board[idx]);
                // 防止走回头路
                if (!visited.count(new_board)) {
                    q.push(new_board);
                    visited.insert(new_board);
                }
            }
        }
        step++;
    }
    return -1;
    /******* BFS 算法框架结束 *******/
}
```
