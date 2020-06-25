给定一个会议时间安排的数组，每个会议时间都会包括开始和结束的时间 [[s1,e1],[s2,e2],...] (si < ei)，请你判断一个人是否能够参加这里面的全部会议。


```
示例 1:

输入: [[0,30],[5,10],[15,20]]
输出: false
示例 2:

输入: [[7,10],[2,4]]
输出: true
```

### 题目分析

对会议开始时间进行排序。


```java
class Solution {
    public boolean canAttendMeetings(int[][] intervals) {
        //注意泛型的写法
        Arrays.sort(intervals,new Comparator<int[]>(){
            @Override
            public int compare(int[] o1, int[] o2) {
                // 前边减去后边，如果>=0不用调整。<0调整位置
                return o1[0]-o2[0];
            }
        });

        for(int i=0;i<intervals.length-1;i++){
            if(intervals[i][1] > intervals[i + 1][0]){
                return false;
            }
        }
        return true;
    }
}
```
