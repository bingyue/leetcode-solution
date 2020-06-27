给定一个区间的集合，找到需要移除区间的最小数量，使剩余区间互不重叠。

注意:

- 可以认为区间的终点总是大于它的起点。
- 区间 [1,2] 和 [2,3] 的边界相互“接触”，但没有相互重叠。


```
示例 1:

输入: [ [1,2], [2,3], [3,4], [1,3] ]

输出: 1

解释: 移除 [1,3] 后，剩下的区间没有重叠。
示例 2:

输入: [ [1,2], [1,2], [1,2] ]

输出: 2

解释: 你需要移除两个 [1,2] 来使剩下的区间没有重叠。

```

### 题目分析

贪心算法，转化成计算最多能组成的不重叠区间个数，然后用区间总个数减去不重叠区间的个数。


```
class Solution {
    public int eraseOverlapIntervals(int[][] intervals) {
        int n=intervals.length;
        //[最多有x个区间不会重叠]之外的，也就是答案，需要移除的
        return n-intervalSchedule(intervals);
    }

    //求最多有几个区间不会重叠
    public int intervalSchedule(int[][] intvs) {
        if (intvs.length == 0) return 0;
        Arrays.sort(intvs, new Comparator<int[]>() {
            @Override
            public int compare(int[] o1, int[] o2) {
                return o1[1] - o2[1];
            }
        });

        // 至少有一个区间不相交
        int count = 1;
        // 排序后，第一个区间就是 x
        int x_end = intvs[0][1];
        for (int[] interval : intvs) {
            int start = interval[0];
            if (start >= x_end) {  //发现一个不相交的，加一
                // 找到下一个选择的区间了
                count++;
                //更新下一个x的值
                x_end = interval[1];
            }
        }
        return count;
    }
}
```
