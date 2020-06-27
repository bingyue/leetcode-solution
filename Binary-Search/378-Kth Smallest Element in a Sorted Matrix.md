给定一个 n x n 矩阵，其中每行和每列元素均按升序排序，找到矩阵中第 k 小的元素。
请注意，它是排序后的第 k 小元素，而不是第 k 个不同的元素。

 


```
示例：

matrix = [
   [ 1,  5,  9],
   [10, 11, 13],
   [12, 13, 15]
],
k = 8,

返回 13。
```

 

提示：
你可以假设 k 的值永远是有效的，1 ≤ k ≤ n2 。

### 题目分析

#### 按照行进行归并排序


```java
class Solution {
       public int kthSmallest(int[][] matrix, int k) {
        int[] tmp = matrix[0];
        for(int i=1;i<matrix.length;i++){
            tmp = mergeSort(tmp,matrix[i]);
        }
        return tmp[k-1];
    }
    public int[] mergeSort(int[] a,int[] b){//两个有序数组归并排序
        int[] merge = new int[a.length+b.length];
        int i=0,j=0,k=0;
        while(i<a.length&&j<b.length){
            if(a[i]<b[j]){
                merge[k]=a[i];
                i++;
            }else{
                merge[k]=b[j];
                j++;
            }
                k++;
        }
        while(i<a.length){
            merge[k]=a[i];
            i++;
            k++;
        }
        while(j<b.length){
            merge[k]=b[j];
            j++;
            k++;
        }
        return merge;
    }
}


```


#### 解决静态的第k小，维护一个为k的大顶堆

涉及到第k小或者大的元素，都可以使用优先队列


- 要找第k小的元素，一种最常规的做法就是使用优先队列。
- 找第k小的元素，就保留k个最小的元素，其中最大的那个就是答案，所以可以使用最大优先队列。
- 遍历矩阵中的元素，将元素添加到队列中，如果队列中元素数目MaxPQ.size() > k，就将堆点最大的元素弹出。
- 遍历结束后弹出堆顶元素，就是最小的k个元素中最大的，即第k小的元素。


```java
class Solution {
       public int kthSmallest(int[][] matrix, int k) {
        // 使用最大堆就可以
        PriorityQueue<Integer> pq = new PriorityQueue<>(new Comparator<Integer>(){
            @Override
            public int compare(Integer o1, Integer o2) {
                return o2-o1;
            }

        });
        for (int[] row : matrix) {
            for (int num : row) {
                //优化下
                pq.add(num);
                if(pq.size()>k){
                    pq.remove();
                }

            }
        }
        //此时堆顶的就是第k小的
        return pq.remove();
    }
}


```


### 二分查找


```
1.找出二维矩阵中最小的数left，最大的数right，那么第k小的数必定在left~right之间
2.mid=(left+right) / 2；在二维矩阵中寻找小于等于mid的元素个数count
3.若这个count小于k，表明第k小的数在右半部分且不包含mid，即left=mid+1, right=right，又保证了第k小的数在left~right之间
4.若这个count大于k，表明第k小的数在左半部分且可能包含mid，即left=left, right=mid，又保证了第k小的数在left~right之间
5.因为每次循环中都保证了第k小的数在left~right之间，当left==right时，第k小的数即被找出，等于right
```

#### 代码实现


```java
class Solution {
       public int kthSmallest(int[][] matrix, int k) {
        int row = matrix.length;
        int col = matrix[0].length;
        int left = matrix[0][0];
        int right = matrix[row - 1][col - 1];
        while (left < right) {
            // 每次循环都保证第K小的数在start~end之间，当start==end，第k小的数就是start
            int mid = (left + right) / 2;
            // 找二维矩阵中<=mid的元素总个数
            int count = findNotBiggerThanMid(matrix, mid, row, col);
            if (count < k) {
                // 第k小的数在右半部分，且不包含mid
                left = mid + 1;
            } else {
                // 第k小的数在左半部分，可能包含mid
                right = mid;
            }
        }
        return right;
    }

    private int findNotBiggerThanMid(int[][] matrix, int mid, int row, int col) {
        // 以列为单位找，找到每一列最后一个<=mid的数即知道每一列有多少个数<=mid
        int i = row - 1;
        int j = 0;
        int count = 0;
        while (i >= 0 && j < col) {
            if (matrix[i][j] <= mid) {
                // 第j列有i+1个元素<=mid
                count += i + 1;
                j++;
            } else {
                // 第j列目前的数大于mid，需要继续在当前列往上找
                i--;
            }
        }
        return count;
    }
}


```
