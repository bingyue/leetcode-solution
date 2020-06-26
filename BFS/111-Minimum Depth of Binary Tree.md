### 题目描述

给定一个二叉树，找出其最小深度。

最小深度是从根节点到最近叶子节点的最短路径上的节点数量。

说明: 叶子节点是指没有子节点的节点。

### 题目分析

这个如何处理递归结构？


```
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {

    public int minDepth(TreeNode root) {

        if(root==null){
            return 0;
        }

        if(root.left==null && root.right==null){
            return 1;
        }
        //注意处理单边的情况
        if(root.right==null){
            return 1+minDepth(root.left);
        }

        if(root.left==null){
            return 1+minDepth(root.right);
        }
        return 1+Math.min(minDepth(root.left),minDepth(root.right));
    }
}
```

#### bfs实现

直接套模板，并且二叉树的bfs不需要使用visited，本身扩散的时候不会重复。

> visited的主要作用是防止走回头路，大部分时候都是必须的，但是像一般的二叉树结构，没有子节点到父节点的指针，不会走回头路就不需要visited。


```java
class Solution {
    public int minDepth(TreeNode root) {
        if(root==null) return 0;
        //套模板，bfs，二叉树不需要额外记录访问过的
        Queue<TreeNode> q=new LinkedList<>();// 核心数据结构
        // root 本身就是一层，depth 初始化为 1
        int step=1;
        q.offer(root);// 将起点加入队列
        while(!q.isEmpty()){
            int sz=q.size();
            for(int i=0;i<sz;i++){
                TreeNode cur=q.poll();
                /* 划重点：这里判断是否到达终点 */
                if(cur.left==null && cur.right==null)
                    return step;
                /* 将 cur 的相邻节点加入队列 */
                if(cur.left!=null) q.offer(cur.left);
                if(cur.right!=null) q.offer(cur.right);
            }
            /* 划重点：更新步数在这里 */
            step++;
        }
        return step;
    }
}
```
