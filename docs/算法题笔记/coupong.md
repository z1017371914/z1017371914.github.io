# Coupang

[toc]

## [268. 丢失的数字](https://leetcode-cn.com/problems/missing-number/)

> 下标和值出现的会抵消掉，比求和相减还要简单

```c++
int missingNumber(vector<int>& nums) {
    int res = nums.size();
    for (int i = 0; i < nums.size(); ++i){
        res ^= nums[i];
        res ^= i;
    }
    return res;
}
```

## [227. 基本计算器 II](https://leetcode-cn.com/problems/basic-calculator-ii/)

> 就是先把所有数压栈，然后结果就是把所有的数相加，好处是没有 加减乘除的顺序了

```java
class Solution {
    public int calculate(String s) {
        // 保存上一个符号，初始为 +
        char sign = '+';
        Stack<Integer> numStack = new Stack<>();
        // 保存当前数字，如：12是两个字符，需要进位累加
        int num = 0;
        int result = 0;
        for(int i = 0; i < s.length(); i++){
            char cur = s.charAt(i);
            if(cur >= '0'){
                // 记录当前数字。先减，防溢出
                num = num*10 - '0' + cur;
            }
            if((cur < '0' && cur !=' ' )|| i == s.length()-1){
                // 判断上一个符号是什么
                switch(sign){
                    // 当前符号前的数字直接压栈
                    case '+': numStack.push(num);break;
                    // 当前符号前的数字取反压栈
                    case '-': numStack.push(-num);break;
                    // 数字栈栈顶数字出栈，与当前符号前的数字相乘，结果值压栈
                    case '*': numStack.push(numStack.pop()*num);break;
                    // 数字栈栈顶数字出栈，除于当前符号前的数字，结果值压栈
                    case '/': numStack.push(numStack.pop()/num);break;
                }
                // 记录当前符号
                sign = cur;
                // 数字清零
                num = 0;
            }
        }
        // 将栈内剩余数字累加，即为结果
        while(!numStack.isEmpty()){
            result += numStack.pop();
        }
        return result;
    }
}
```

## [776. 拆分二叉搜索树](https://leetcode-cn.com/problems/split-bst/)

```java
class Solution {
    public TreeNode[] splitBST(TreeNode root, int target) {
        if(root == null){
            return new TreeNode[]{null,null};
        }
        
        // 
        if(root.val <= target){
            TreeNode[] res = splitBST(root.right,target);
            root.right = res[0];
            res[0] = root;
            return res;
        }else{
            TreeNode[] res = splitBST(root.left,target);
            root.left = res[1];
            res[1] = root;
            return res;

        }
    }
}
```

## [1296. 划分数组为连续数字的集合](https://leetcode-cn.com/problems/divide-array-in-sets-of-k-consecutive-numbers/)

```java
class Solution {
    public boolean isPossibleDivide(int[] nums, int k) {
        TreeMap<Integer,Integer> map = new TreeMap<>();
        for(int num : nums){
            map.put(num,map.getOrDefault(num,0) + 1);
        }
        while(map.size() > 0){
            int first = map.firstKey();
            for(int i = first;i < first + k;i++){
                if(!map.containsKey(i)) return false;
                map.put(i,map.get(i) - 1);
                if(map.get(i) == 0){
                    map.remove(i);
                }
            }
        }
        return true;
    }
}
```

## [1254. 统计封闭岛屿的数目](https://leetcode-cn.com/problems/number-of-closed-islands/)

```java
class Solution {
   int[][] dir = new int[][]{{1,0},{-1,0},{0,-1},{0,1}};
    boolean[][] visited;
    public int closedIsland(int[][] grid) {
        int row = grid.length;
        int col = grid[0].length;
        visited = new boolean[row][col];
        int count = 0;
        for(int i = 0; i < row; i++){
            for (int j = 0; j < col; j++){
                if(grid[i][j] == 0 && !visited[i][j]) {
                    if (dfsFindLand(grid, col, row, i, j)) count++;
                }
            }
        }
        return count;
    }

    private boolean dfsFindLand(int[][] grid, int col, int row, int curRow, int curCol){
        if(curRow >= row || curRow < 0) return false;
        if(curCol >= col || curCol < 0) return false;
        if(grid[curRow][curCol] == 1 ||visited[curRow][curCol]) return true;
        visited[curRow][curCol] = true;
        boolean flag = true;
        for(int[] direction : dir){
            if(!dfsFindLand(grid, col, row, curRow + direction[0], curCol + direction[1]))
                flag = false;
        }
        return flag;
    }
}
```

## [322. 零钱兑换](https://leetcode-cn.com/problems/coin-change/)

```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        int[] dp = new int[amount + 1];
        Arrays.fill(dp, Integer.MAX_VALUE);
        dp[0] = 0;
        for(int i = 1;i<=amount;i++){
            for(int j = 0;j<coins.length;j++){
                if(i-coins[j] >= 0  && dp[i-coins[j]] != Integer.MAX_VALUE ){
                    dp[i] =  Math.min(dp[i], 1 + dp[i-coins[j]]);
                }
               
            }
            System.out.println(dp[i]);
        }
        return dp[amount] == Integer.MAX_VALUE? -1:dp[amount];
    }
}
```

## [114. 二叉树展开为链表](https://leetcode-cn.com/problems/flatten-binary-tree-to-linked-list/)

```java
class Solution {
    TreeNode last;
    public void flatten(TreeNode root) {
        if(root == null){
            return ;
        }

        flatten(root.right);
        flatten(root.left);
        root.right = last;
        root.left = null;
        last = root;
    }
}
```

## [886. 可能的二分法](https://leetcode-cn.com/problems/possible-bipartition/)

> 这题主要是考察并查集，想象成一棵一棵的树
>
> 后面的N个数组 用查找 不能在一起的接点
>
> 前面的N个数组 并查集用来查找 在同一个集合的节点。  这种算法叫做  种类并查集

```java
class Solution {
    int N;  //共多少人
    int[] father;   //并查集
    public boolean possibleBipartition(int _N, int[][] dl) {
        N = _N+1;   
        father = new int[N*2+5];    //开两倍的数组
        for(int i = 1; i < N*2+5; i++) //初始化并查集
            father[i] = i;
        for(int i = 0; i < dl.length; i++){
            int x = find(dl[i][0]); //查找自己的根节点
            int y = find(dl[i][1]);
            int a = find(dl[i][0] + N); //查找自己不喜欢的人的根节点
            int b = find(dl[i][1] + N); 
            if(x == y) return false; //发现这俩人已经在一组
            else{
                father[a] = y;  //将不喜欢的人合并
                father[b] = x;
            }
        }
        return true;
    }
    private int find(int x){ //寻找根节点
        if(x != father[x])  ui
            father[x] = find(father[x]); //路径压缩
        return father[x];
    }
}

作者：gyb-3
链接：https://leetcode-cn.com/problems/possible-bipartition/solution/ke-neng-de-er-fen-fa-chong-lei-bing-cha-ji-by-gyb-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

