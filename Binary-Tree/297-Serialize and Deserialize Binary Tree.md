### 题目描述

序列化是将一个数据结构或者对象转换为连续的比特位的操作，进而可以将转换后的数据存储在一个文件或者内存中，同时也可以通过网络传输到另一个计算机环境，采取相反方式重构得到原数据。

请设计一个算法来实现二叉树的序列化与反序列化。这里不限定你的序列 / 反序列化算法执行逻辑，你只需要保证一个二叉树可以被序列化为一个字符串并且将这个字符串反序列化为原始的树结构。

示例: 

```
你可以将以下二叉树：

    1
   / \
  2   3
     / \
    4   5

序列化为 "[1,2,3,null,null,4,5]"
```
提示: 这与 LeetCode 目前使用的方式一致，详情请参阅 LeetCode 序列化二叉树的格式。你并非必须采取这种方式，你也可以采用其他的方法解决这个问题。

说明: 不要使用类的成员 / 全局 / 静态变量来存储状态，你的序列化和反序列化算法应该是无状态的。

### 题目分析


序列化使用层序遍历的方式，按行打印，空节点记录null。

反序列化时还原为队列，开始继续还原。

先说说空节点序列化的问题，以下面的树为例：


```
                                             1
                                            / \
                                           2   3
                                              / \
                                             4   5
                                                / 
                                               6

```

比如和节点6同层的节点2的子节点都是空且深度差超过1，这时候我们无需序列化和和节点6同深度的属于2的空节点。原则上来说是需要的，但是会增加操作难度，另外，不影响我们重建二叉树，我们就不需要额外去考虑这些空节点，我们只需要知道2的子节点为空，那么再往下的子节点都是空的，因此空节点只需要且有必要序列化一层，代表该树该分叉到头了，我们最终的序列结果应该是相当于把树加了一层空节点，假如我们从树的下面仰视，看到的都是最后一层的空节点，像下面这种：


```
                         1
                                            /    \
                                           2      3
                                          / \    / \
                                        nul nul 4  nul
                                               / \
                                             nul nul

```

序列化：例子给的序列化过程是二叉树的层序遍历(广度优先)过程，那我也用层序遍历吧，实际上，你可以采用任意方式，比如深度优先的方式，不会层序遍历可以做一做102题二叉树的层序遍历，我们利用队列依次将每一层的节点压入和弹出，保证了层序，压入过程压入该层节点的所有子节点，弹出过程弹出该层所有节点，因为这里只需要序列化，我们不必知道节点是哪一层的，所以只需要清空队列即可。


反序列化：反序列化的过程同样需要队列，这个过程是我们模拟序列化的过程，然后依次将val值给我们构建的二叉树节点。


```

public class Codec {

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        if(root == null) return "[]";
        StringBuilder res = new StringBuilder("[");
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        // queue可以存储null
        while(!queue.isEmpty()) {
            TreeNode node = queue.poll();
            if(node != null) {
                res.append(node.val + ",");
                queue.add(node.left);
                queue.add(node.right);
            }
            else res.append("null,");
        }
        // 删除最后的,
        res.deleteCharAt(res.length() - 1);
        res.append("]");
        return res.toString();
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        if(data.equals("[]")) return null;
        // 去除两边的括号，转换为字符串数组
        // 从1到data.length() - 1，左闭右开区间
        String[] vals = data.substring(1, data.length() - 1).split(",");
        // 层序遍历第一个就是头结点
        TreeNode root = new TreeNode(Integer.parseInt(vals[0]));
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        // 字符串数组的下标
        int i = 1;
        while(!queue.isEmpty()) {
            TreeNode node = queue.poll();
            if(!vals[i].equals("null")) {
                node.left = new TreeNode(Integer.parseInt(vals[i]));
                queue.add(node.left);
            }
            i++;
            if(!vals[i].equals("null")) {
                node.right = new TreeNode(Integer.parseInt(vals[i]));
                queue.add(node.right);
            }
            i++;
        }
        return root;
    }
}

```
