### 652.寻找重复的子树

给定一棵二叉树，返回所有重复的子树。对于同一类的重复子树，你只需要返回其中任意一棵的根结点即可。

两棵树重复是指它们具有相同的结构以及相同的结点值。


```
示例 1：

        1
       / \
      2   3
     /   / \
    4   2   4
       /
      4
下面是两个重复的子树：

      2
     /
    4
和

    4
因此，你需要以列表的形式返回上述重复子树的根结点。
```

### 题目分析

最开始想了一个暴力的方式，遍历二叉树返回一个列表，然后两两节点之间进行比较，判断是否是相同的二叉树，非常暴力，并且还要额外处理重复节点只返回一个的问题，继续优化使用哈希表，发现和题解很像了。

#### 使用哈希表存储序列化字段

使用哈希表来存储对应的序列化的String,如果出现重复的string，将当前节点返回到结果中。

1. DFS将所有二叉树的子树都展开为string。
1. 用string做为Hash key，TreeNode做为Hash data。
1. 输出Hash中重复项。

##### 先序遍历，代码实现一


```java
class Solution {
    public List<TreeNode> findDuplicateSubtrees(TreeNode root) {
        List<TreeNode> res = new ArrayList<>();
        HashMap<String, Integer> map = new HashMap<>();
        if (root == null) return res;
        saveRoute(root, res, map);
        return res;
    }

    //递归获取每个子树的路径，保存于Map中
    private String saveRoute(TreeNode node, List<TreeNode> res, HashMap<String, Integer> map) {
        if (node == null) return "";
        //自底向上获取每个节点的序列化值
        String route = node.val + "," + saveRoute(node.left, res, map) + "," + saveRoute(node.right, res, map);
        //将结果放入map，判断是否有相同子树
        //避免出现多次相同子树
        if (map.get(route) != null && map.get(route) == 1) {
            res.add(node);
        }
        map.put(route, map.getOrDefault(route, 0) + 1);
        return route;
    }
}
```


##### 中序遍历方式代码


```
public LinkedList<TreeNode> result = new LinkedList<>();
    public HashMap<String,Integer> map = new HashMap<>();
    public List<TreeNode> findDuplicateSubtrees(TreeNode root) {
        if(root == null){
            return result;
        }
        serialize(root);
        return result;
    }

    public String serialize(TreeNode root){
        if(root == null){
            return "null";
        }
        //序列化以当前节点为根节点的二叉树
        String str = serialize(root.left) + ","+ root.val + ","+ serialize(root.right);
        //使用一个HashMap来记录所有的子树的序列
        map.put(str,map.getOrDefault(str,0)+1);
        //当其value为2时，则表示出现了重复结构，将这个子树的根节点放入到结果当中。
        if(map.get(str) == 2){
            result.add(root);
        }
        return str;
    }


```


