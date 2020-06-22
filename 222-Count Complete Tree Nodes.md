### 222. 完全二叉树的节点个数

给出一个完全二叉树，求出该树的节点个数。

说明：

完全二叉树的定义如下：在完全二叉树中，除了最底层节点可能没填满外，其余每层节点数都达到最大值，并且最下面一层的节点都集中在该层最左边的若干位置。若最底层为第 h 层，则该层包含 1~ 2h 个节点。


```
示例:

输入: 
    1
   / \
  2   3
 / \  /
4  5 6

输出: 6
```

### 题目分析

没有约束的二叉树而言，可以很简单地想到使用下面这个递归的解法，也就是暴力求解。


```
public int countNodes(TreeNode root) {
    if (root == null){
        return 0;
    }
    return countNodes(root.left) + countNodes(root.right) + 1;
}

```

#### 利用完全二叉树特性

满二叉的节点个数怎么计算，如果满二叉树的层数为h，则总节点数为：2^h - 1.


那么我们来对root节点的左右子树进行高度统计，分别记为left和right,有以下两种结果：


- left == right


```
    1
   / \
  2   3
 / \  /
4  5 6

```

这说明，左子树一定是满二叉树，因为节点已经填充到右子树了，左子树必定已经填满了。所以左子树的节点总数我们可以直接得到，是2^left - 1，加上当前这个root节点，则正好是2^left。再对右子树进行递归统计。



- left != right


```
    1
   / \
  2   3
 / 
4 

```


说明此时最后一层不满，但倒数第二层已经满了，可以直接得到右子树的节点个数。同理，右子树节点+root节点，总数为2^right。再对左子树进行递归查找。


```java
class Solution {
    public int countNodes(TreeNode root) {
        if(root == null){
           return 0;
        } 
        int left = countLevel(root.left);
        int right = countLevel(root.right);
        if(left == right){
            // 移位计算2的n次方
            // 左子树是满二叉树
            return countNodes(root.right) + (1<<left)-1+1;
        }else{
            // 肯定是left>right，此时右子树是一棵满二叉树
            // 所以等于递归计算左子树+根结点到右子树的所有节点
            return countNodes(root.left) + (1<<right);
        }
    }

    //非递归方式
    private int countLevel(TreeNode root){
        int level = 0;
        while(root != null){
            level++;
            root = root.left;
        }
        return level;
    }

}
```
