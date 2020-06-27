在二维空间中有许多球形的气球。对于每个气球，提供的输入是水平方向上，气球直径的开始和结束坐标。由于它是水平的，所以y坐标并不重要，因此只要知道开始和结束的x坐标就足够了。开始坐标总是小于结束坐标。平面内最多存在104个气球。

一支弓箭可以沿着x轴从不同点完全垂直地射出。在坐标x处射出一支箭，若有一个气球的直径的开始和结束坐标为 xstart，xend， 且满足  xstart ≤ x ≤ xend，则该气球会被引爆。可以射出的弓箭的数量没有限制。 弓箭一旦被射出之后，可以无限地前进。我们想找到使得所有气球全部被引爆，所需的弓箭的最小数量。


```
Example:

输入:
[[10,16], [2,8], [1,6], [7,12]]

输出:
2

解释:
对于该样例，我们可以在x = 6（射爆[2,8],[1,6]两个气球）和 x = 11（射爆另外两个气球）。
```

### 题目分析

这个问题和区间调度算法一模一样！如果最多有 n 个不重叠的区间，那么就至少需要 n 个箭头穿透所有区间：

![image](http://note.youdao.com/yws/res/30649/C02377DFF340487DABE607A49EC248D9)

只是有一点不一样，在 intervalSchedule 算法中，如果两个区间的边界触碰，不算重叠；而按照这道题目的描述，箭头如果碰到气球的边界气球也会爆炸，所以说相当于区间的边界触碰也算重叠：

![image](http://note.youdao.com/yws/res/30648/7A89F2D6120E482AB0BC3602A4E0E603)

所以只要将之前的算法稍作修改，就是这道题目的答案。

先回顾下之前的模板:



```java
/**
 * 区间调度问题
 */
public class IntervalSchedule {

    //求最多有几个区间不相交
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


上面的模板里，相交的点不认为重合，改动一下：

```
class Solution {
    public int findMinArrowShots(int[][] points) {

        if (points.length == 0) return 0;
        Arrays.sort(points, new Comparator<int[]>() {
            @Override
            public int compare(int[] o1, int[] o2) {
                return o1[1] - o2[1];
            }
        });

        // 至少有一个区间不相交
        int count = 1;
        // 排序后，第一个区间就是 x
        int x_end = points[0][1];
        for (int[] interval : points) {
            int start = interval[0];
            //改动条件
            if (start > x_end) {  //发现一个不相交的，加一
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
