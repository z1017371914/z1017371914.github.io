

## 429.N叉树的层序遍历

问题：

>给定一个 N 叉树，返回其节点值的层序遍历。（即从左到右，逐层遍历）。
>
>树的序列化输入是用层序遍历，每组子节点都由 null 值分隔（参见示例）。
>
> 
>
>示例 1：
>
>![image](https://tvax1.sinaimg.cn/large/0085EwgIgy1govygrydawj30lp0dymxk.jpg)
>
>输入：root = [1,null,3,2,4,null,5,6]
>输出：[[1],[3,2,4],[5,6]]

### 思路：BFS

#### 代码

```java
/*
// Definition for a Node.
class Node {
    public int val;
    public List<Node> children;

    public Node() {}

    public Node(int _val) {
        val = _val;
    }

    public Node(int _val, List<Node> _children) {
        val = _val;
        children = _children;
    }
};
*/

class Solution {
    public List<List<Integer>> levelOrder(Node root) {
        if ( root == null ) {
            return new ArrayList<>(){};
        }
        List<List<Integer>> res = new ArrayList<>();
        Queue<Node> queue = new LinkedList<>();
        //根节点入队
        queue.add(root);
        while ( !queue.isEmpty() ) {
            //记录每一层的节点值
            List<Integer> cur = new ArrayList<>();
            //一定要现在记录下当前队列的数目，这时候队列的大小就是这一层的节点数量
            int size = queue.size();
            //容易发生的错误就是，不用现在的size(),而是写成
            //for(int i = 0; i < queue.size(); i++ )
            //这样是不对的，因为在下面的操作中，队列的大小在改变！！！
            for ( int i = 0; i < size; i++ ) {
                //获取当前队头节点，加入到当前层的结果集合中
                Node temp = queue.poll();
                cur.add(temp.val);
                //将该节点的子节点全部入队
                for ( Node child : temp.children ) {
                    queue.add(child);
                }
            }
            res.add(cur);
        }
        return res;
    }
}
```



