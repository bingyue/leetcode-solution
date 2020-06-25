### 296.最佳的碰头地点

有一队人（两人或以上）想要在一个地方碰面，他们希望能够最小化他们的总行走距离。

给你一个 2D 网格，其中各个格子内的值要么是 0，要么是 1。

1 表示某个人的家所处的位置。这里，我们将使用 曼哈顿距离 来计算，其中 distance(p1, p2) = |p2.x - p1.x| + |p2.y - p1.y|。


```
示例：

输入: 

1 - 0 - 0 - 0 - 1
|   |   |   |   |
0 - 0 - 0 - 0 - 0
|   |   |   |   |
0 - 0 - 1 - 0 - 0

输出: 6
```


解析: 给定的三个人分别住在(0,0)，(0,4) 和 (2,2):
     (0,2) 是一个最佳的碰面点，其总行走距离为 2 + 2 + 2 = 6，最小，因此返回 6。

### 题目分析

#### 暴力方式，bfs搜索


一种暴力的解法是枚举网格中所有可能的相遇地点。我们从每个点出发做宽度（广度）优先搜索。

在将某个点放进队列时，我们需要记录从相遇点到这个点的曼哈顿距离。同时，我们需要一个额外的数组 visited 记录哪些点已经被访问过了，以避免将一个已经访问过的点重复放入队列。


```
public int minTotalDistance(int[][] grid) {
    int minDistance = Integer.MAX_VALUE;
    for (int row = 0; row < grid.length; row++) {
        for (int col = 0; col < grid[0].length; col++) {
            int distance = search(grid, row, col);
            minDistance = Math.min(distance, minDistance);
        }
    }
    return minDistance;
}

private int search(int[][] grid, int row, int col) {
    Queue<Point> q = new LinkedList<>();
    int m = grid.length;
    int n = grid[0].length;
    boolean[][] visited = new boolean[m][n];
    q.add(new Point(row, col, 0));
    int totalDistance = 0;
    while (!q.isEmpty()) {
        Point point = q.poll();
        int r = point.row;
        int c = point.col;
        int d = point.distance;
        if (r < 0 || c < 0 || r >= m || c >= n || visited[r][c]) {
            continue;
        }
        if (grid[r][c] == 1) {
            totalDistance += d;
        }
        visited[r][c] = true;
        q.add(new Point(r + 1, c, d + 1));
        q.add(new Point(r - 1, c, d + 1));
        q.add(new Point(r, c + 1, d + 1));
        q.add(new Point(r, c - 1, d + 1));
    }
    return totalDistance;
}

public class Point {
    int row;
    int col;
    int distance;
    public Point(int row, int col, int distance) {
        this.row = row;
        this.col = col;
        this.distance = distance;
    }
}

```

时间复杂度： O(m^2n^2)
在 m×n 大小的网格图中，宽度优先搜索最多需m×n 步可以到达任意一个点。所以总时间复杂度是 O(m^2n^2).


空间复杂度： O(mn) 。

visited 表包含 m×n 个元素对应网格图中的每一个点。

#### 直接应用公式计算，暴力


```
public int minTotalDistance(int[][] grid) {
    List<Point> points = getAllPoints(grid);
    int minDistance = Integer.MAX_VALUE;
    for (int row = 0; row < grid.length; row++) {
        for (int col = 0; col < grid[0].length; col++) {
            int distance = calculateDistance(points, row, col);
            minDistance = Math.min(distance, minDistance);
        }
    }
    return minDistance;
}

private int calculateDistance(List<Point> points, int row, int col) {
    int distance = 0;
    for (Point point : points) {
        distance += Math.abs(point.row - row) + Math.abs(point.col - col);
    }
    return distance;
}

private List<Point> getAllPoints(int[][] grid) {
    List<Point> points = new ArrayList<>();
    for (int row = 0; row < grid.length; row++) {
        for (int col = 0; col < grid[0].length; col++) {
            if (grid[row][col] == 1) {
                points.add(new Point(row, col));
            }
        }
    }
    return points;
}

public class Point {
    int row;
    int col;
    public Point(int row, int col) {
        this.row = row;
        this.col = col;
    }
}

```

#### 转换为一维求解


```
public int minTotalDistance(int[][] grid) {
    List<Integer> rows = new ArrayList<>();
    List<Integer> cols = new ArrayList<>();
    for (int row = 0; row < grid.length; row++) {
        for (int col = 0; col < grid[0].length; col++) {
            if (grid[row][col] == 1) {
                rows.add(row);
                cols.add(col);
            }
        }
    }
    int row = rows.get(rows.size() / 2);
    Collections.sort(cols);
    int col = cols.get(cols.size() / 2);
    return minDistance1D(rows, row) + minDistance1D(cols, col);
}

private int minDistance1D(List<Integer> points, int origin) {
    int distance = 0;
    for (int point : points) {
        distance += Math.abs(point - origin);
    }
    return distance;
}

```
