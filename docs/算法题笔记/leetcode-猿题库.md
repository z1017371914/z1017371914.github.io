# leetcode算法整理

[toc]

## 猿题库

### 最长连续递增序列

```java
class Solution {
    public int findLengthOfLCIS(int[] nums) {
        if(nums == null || nums.length == 0){
            return 0;
        }
        int max = 1;
        int dp[] = new int[nums.length];
        dp[0] = 1;

        for(int i =1;i<nums.length;i++){
            if(nums[i]>nums[i-1]){
                dp[i] = dp[i-1]+1;
            }else{
                dp[i] = 1;
            }
            max = Math.max(max,dp[i]);
        }
        return max;
    }
}
```

### 环形链表

> 判断链表是否有环

```java
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public boolean hasCycle(ListNode head) {
        if(head == null ){
            return false;
        }
        ListNode fast = head;
        ListNode slow = head;
        while(fast!=null && fast.next != null){
            fast = fast.next.next;
            slow = slow.next;
            if(fast == slow){
                return true;
            }
        }
        return false;
    }
}
```

### 字符串解码

> 给定一个经过编码的字符串，返回它解码后的字符串。
>
> 编码规则为: k[encoded_string]，表示其中方括号内部的 encoded_string 正好重复 k 次。注意 k 保证为正整数。
>
> 你可以认为输入字符串总是有效的；输入字符串中没有额外的空格，且输入的方括号总是符合格式要求的。
>
> 此外，你可以认为原始数据不包含数字，所有的数字只表示重复的次数 k ，例如不会出现像 3a 或 2[4] 的输入。
>
> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/decode-string
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

```java
class Solution {
     public String decodeString(String s) {
        Stack<String> stack = new Stack<>();

        for (int i = 0;i<s.length();i++){
            if(s.charAt(i) == ']'){
                String str = "";
                while(stack.size()>0&&!stack.peek().equals("[")){

                    str = stack.pop()+str;
                }
                if(stack.size()>0&&stack.peek().equals("[")){
                    stack.pop();
                }

                String numStr = "";
                while(stack.size()>0&&Character.isDigit(stack.peek().charAt(0))){
                    numStr = stack.pop() + numStr;
                }
                int num = 1;
                if(numStr.length()>0){
                    num = Integer.parseInt(numStr);
                }
                String res = "";
                for (int j = 0;j<num;j++){
                    res += str;
                }

                stack.push(res);
                System.out.println(res);
            }else{
                stack.push(s.substring(i,i+1));
            }
        }
        String res = "";
        while (stack.size()>0) {
            res = stack.pop() + res;
        }
        return res;
    }

}
```

### 三数之和

```java
class Solution {
    public static List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> ans = new ArrayList();
        int len = nums.length;
        if(nums == null || len < 3) return ans;
        Arrays.sort(nums); // 排序
        for (int i = 0; i < len ; i++) {
            if(nums[i] > 0) break; // 如果当前数字大于0，则三数之和一定大于0，所以结束循环
            if(i > 0 && nums[i] == nums[i-1]) continue; // 去重
            int L = i+1;
            int R = len-1;
            while(L < R){
                int sum = nums[i] + nums[L] + nums[R];
                if(sum == 0){
                    ans.add(Arrays.asList(nums[i],nums[L],nums[R]));
                    while (L<R && nums[L] == nums[L+1]) L++; // 去重
                    while (L<R && nums[R] == nums[R-1]) R--; // 去重
                    L++;
                    R--;
                }
                else if (sum < 0) L++;
                else if (sum > 0) R--;
            }
        }        
        return ans;
    }
}
```

### 二叉树的右视图

```java
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
    public List<Integer> rightSideView(TreeNode root) {
        List<Integer> res = new ArrayList<Integer>();
        if(root == null){
            return res;
        }
        List<TreeNode> queue = new ArrayList<>();
        queue.add(root);
        while(queue.size()>0){
            int size = queue.size();
            res.add(queue.get(size - 1).val);
            for(int i = 0;i<size;i++){
                TreeNode temp = queue.remove(0);
                if(temp.left != null){
                    queue.add(temp.left);
                }
                if(temp.right != null){
                    queue.add(temp.right);
                }
            }

        }

        return res;
    }
}
```

### 有效括号

```java
class Solution {
    public boolean isValid(String s) {
        Stack<Character> stack = new Stack<>();
        HashMap<Character,Character> hash = new HashMap<Character,Character>();
        hash.put('}','{');
        hash.put(']','[');
        hash.put(')','(');
        for(int i = 0;i<s.length();i++){
            if(s.charAt(i) == ')'||s.charAt(i) == ']'||s.charAt(i) == '}'){
                if(stack.size()>0&&stack.peek().equals(hash.get(s.charAt(i)))){
                    stack.pop();
                }
                else{
                    return false;
                }
            }else{
                stack.push(s.charAt(i));
            }
        }

        if(stack.size()==0){
            return true;
        }
        return false;
    }
}
```

### LRUCache

```java
class LRUCache {
    private int size = 0;
    private int capacity = 0;
    private HashMap<Integer,ListNode> hash;

    private ListNode dummyHead;
    private ListNode dummyTail;

    private static class ListNode{
        private int key;
        private int value;
        private ListNode pre;
        private ListNode next;
    }



    public LRUCache(int capacity) {
        this.capacity = capacity;
        hash = new HashMap<>();
        dummyHead = new ListNode();
        dummyTail = new ListNode();
        dummyHead.next = dummyTail;
        dummyTail.pre = dummyHead;
    }
    
    public int get(int key) {
        
        if(hash.containsKey(key) == false){
            return -1;
        }
        ListNode node = hash.get(key);
        node.pre.next = node.next;
        node.next.pre = node.pre;

        node.next = dummyHead.next;
        dummyHead.next.pre = node;

        dummyHead.next = node;
        node.pre = dummyHead;
        
        if(size == 1){
             System.out.println(dummyHead.next.value);
        }

        if(size == 2){
             System.out.println(dummyHead.next.value+","+ dummyTail.pre.value);
        }
    
        return node.value;
    }
    
    public void put(int key, int value) {
        if(hash.containsKey(key)){
            hash.get(key).value = value;
            
            this.get(key);
            return ;
        }

        if(size < capacity){
            ListNode temp = new ListNode();
            temp.value = value;
            temp.key = key;

            dummyTail.pre.next = temp;
            temp.pre = dummyTail.pre;
            
            temp.next = dummyTail;
            dummyTail.pre = temp;
            size++;
            
        }else{
          
            dummyTail.pre.value = value;
            hash.remove(dummyTail.pre.key);
           
            dummyTail.pre.key = key; 
        }

        hash.put(key,dummyTail.pre);
        this.get(key);
    }
}

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache obj = new LRUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
```

### 分数排名

```sql
# Write your MySQL query statement below`
select s1.Score,count(distinct(s2.score)) Rank
from
Scores s1,Scores s2
where
s1.score<=s2.score
group by s1.Id
order by Rank
```

