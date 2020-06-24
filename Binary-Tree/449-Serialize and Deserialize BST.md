### 449. 序列化和反序列化二叉搜索树

序列化是将数据结构或对象转换为一系列位的过程，以便它可以存储在文件或内存缓冲区中，或通过网络连接链路传输，以便稍后在同一个或另一个计算机环境中重建。

设计一个算法来序列化和反序列化二叉搜索树。 对序列化/反序列化算法的工作方式没有限制。 您只需确保二叉搜索树可以序列化为字符串，并且可以将该字符串反序列化为最初的二叉搜索树。

编码的字符串应尽可能紧凑。

注意：不要使用类成员/全局/静态变量来存储状态。 你的序列化和反序列化算法应该是无状态的。


### 题目分析

如果是一个普通二叉树，需要前序和中序，或者后序和中序两个序列，才能唯一确定二叉树结构，而二叉搜索树，由于各元素之间有左子节点小于父节点再小于右子节点的性质，因此用一个前序排列就可以确定出唯一结构。

二叉搜索树能只够通过前序序列或后序序列构造，是因为以下两个因素：

- 二叉树可以通过前序序列或后序序列和中序序列构造。
- 二叉搜索树的中序序列是递增排序的序列，inorder = sorted(preorder)

说明我们只需要直到了前序序列或后序序列相当于我们也知道了中序序列，可以通过排序获得。



```java
public class Codec {

     // Encodes a tree to a single string.
    //BST的前序遍历结果
    public String serialize(TreeNode root) {
        if (root == null) return "";
        StringBuilder sb = new StringBuilder();
        dfs(root, sb);
        return sb.substring(0, sb.length() - 1);
    }

    private void dfs(TreeNode root, StringBuilder sb) {
        if (root == null) return;
        //拼接当前节点
        sb.append(root.val).append(",");
        dfs(root.left, sb);
        dfs(root.right, sb);
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        if (data == null || data.length() == 0) return null;
        String[] arr = data.split(",");
        return builder(arr, 0, arr.length - 1);
    }

    private TreeNode builder(String[] arr, int lo, int hi) {
        if (lo > hi) return null;
        TreeNode root = new TreeNode(Integer.valueOf(arr[lo]));
        //找到第一个比首元素大的元素位置，也就是右子树的起点
        int index = hi + 1;
        for (int i = lo + 1; i <= hi; i++) {
            if (Integer.valueOf(arr[i]) > root.val) {
                index = i;
                break;
            }
        }
        //递归构建子树
        root.left = builder(arr, lo + 1, index - 1);
        root.right = builder(arr, index, hi);
        return root;
    }
}

```


