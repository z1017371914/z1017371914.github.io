# Hotstar

[toc]



## [895. 最大频率栈](https://leetcode-cn.com/problems/maximum-frequency-stack/)

> 一个map    key是key,value是频率  ，用来记录所有元素的频率
>
> 一个priorityQueue                              用来弹出栈顶

```java
class FreqStack {

        private int index;
        private Map<Integer,Integer> map;
        private PriorityQueue<int[]> queue;

        public FreqStack() {
            queue=new PriorityQueue<>(new Comparator<int[]>() {
                @Override
                public int compare(int[] o1, int[] o2) {
                    if(o1[1]==o2[1])
                    F{
                        return o2[2]-o1[2];
                    }
                    return o2[1]-o1[1];
                }
            });
            map=new HashMap<>();
            index=0;
        }

        public void push(int x) {
            int cnt=map.getOrDefault(x,0)+1;
            map.put(x,cnt);
            queue.add(new int[]{x,cnt,index++});
        }

        public int pop() {
            int[] poll = queue.poll();
            map.put(poll[0],poll[1]-1);
            return poll[0];
        }
    }
```



## [加油站](https://leetcode-cn.com/problems/gas-station/solution/jia-you-zhan-by-leetcode-solution/)

> 我们首先检查第 00 个加油站，并试图判断能否环绕一周；如果不能，就从第一个无法到达的加油站开始继续检查。

```java
class Solution {
    public int canCompleteCircuit(int[] gas, int[] cost) {
        int n = gas.length;
        int i = 0;
        while (i < n) {
            int sumOfGas = 0, sumOfCost = 0;
            int cnt = 0;
            while (cnt < n) {
                int j = (i + cnt) % n;
                sumOfGas += gas[j];
                sumOfCost += cost[j];
                if (sumOfCost > sumOfGas) {
                    break;
                }
                cnt++;
            }
            if (cnt == n) {
                return i;
            } else {
                i = i + cnt + 1;
            }
        }
        return -1;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/gas-station/solution/jia-you-zhan-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 5 最长回文子串

```java
class Solution {
    public String longestPalindrome(String s) {
        String newStr = addStr(s);
        int start = 0;
        int end = 0;
        int len = 0;

        for(int i=0;i<newStr.length();i++){
            int left = i-1;
            int right = i+1;
            int curLen = 1;
            while(left>=0 && right < newStr.length()&& newStr.charAt(left) == newStr.charAt(right)){
                left--;
                right++;
            }
            left++;
            right--;
            if(right-left > len){
                len = right - left;
                start = left;
                end = right;
            }

        }
        return newStr.substring(start,end+1).replace("#","");
    }

    private String addStr(String s){
        StringBuilder sb = new StringBuilder();
        sb.append("#");
        for(int i = 0;i<s.length();i++){
            sb.append(s.substring(i,i+1));
            sb.append("#");
        }
        return sb.toString();

    }
}
```

## [44 通配符](https://leetcode-cn.com/problems/wildcard-matching/)

```java
class Solution {
    // 状态 dp[i][j] : 表示 s 的前 i 个字符和 p 的前 j 个字符是否匹配 (true 的话表示匹配)
    // 状态转移方程：
    //      1. 当 s[i] == p[j]，或者 p[j] == ? 那么 dp[i][j] = dp[i - 1][j - 1];
    //      2. 当 p[j] == * 那么 dp[i][j] = dp[i][j - 1] || dp[i - 1][j]    其中：
    //      dp[i][j - 1] 表示 * 代表的是空字符，例如 ab, ab*
    //      dp[i - 1][j] 表示 * 代表的是非空字符，例如 abcd, ab*
    // 初始化：
    //      1. dp[0][0] 表示什么都没有，其值为 true
    //      2. 第一行 dp[0][j]，换句话说，s 为空，与 p 匹配，所以只要 p 开始为 * 才为 true
    //      3. 第一列 dp[i][0]，当然全部为 false
    public boolean isMatch(String s, String p) {
        int m = s.length();
        int n = p.length();

        // 状态 dp[i][j] : 表示 s 的前 i 个字符和 p 的前 j 个字符是否匹配
        boolean[][] dp = new boolean[m + 1][n + 1];

        // 初始化
        dp[0][0] = true;
        for (int i = 1; i <= n; i++) {
            dp[0][i] = dp[0][i - 1] && p.charAt(i - 1) == '*';
        }

        // 状态转移
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (s.charAt(i - 1) == p.charAt(j - 1) || p.charAt(j - 1) == '?') {
                    dp[i][j] = dp[i - 1][j - 1];
                } else if (p.charAt(j - 1) == '*') {
                    dp[i][j] = dp[i][j - 1] || dp[i - 1][j];
                }
            }
        }
        
        // 返回结果
        return dp[m][n];

    }
}
```

## 55 [合并区间](https://leetcode-cn.com/problems/merge-intervals/)

```java
class Solution {
    public int[][] merge(int[][] intervals) {
        Arrays.sort(intervals,(a,b)-> a[0]-b[0]);
        LinkedList<int[]> list = new  LinkedList<>();
        for(int i=0;i<intervals.length;i++){
            if(list.size()>0){
                if(list.peekLast()[1] < intervals[i][0]){
                    list.add(intervals[i]);
                }else{
                    list.peekLast()[1] = Math.max(list.peekLast()[1],intervals[i][1]);
                }

            }else{
                list.add(intervals[i]);
            }
        }
        return list.toArray(new int[list.size()][2]);
    }
}
```

##  [146 LRU缓存机制](https://leetcode-cn.com/problems/lru-cache/)

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

##  [198 打家劫舍](https://leetcode-cn.com/problems/house-robber/)

```java
class Solution {
    public int rob(int[] nums) {
        int []dp = new int[nums.length];
        if(nums == null || nums.length == 0){
            return 0;
        }
        for(int i = 0;i< nums.length;i++){
            if(i==0){
                dp[0] = nums[i];
                continue;
            }
            if(i==1){
                dp[i] = Math.max(nums[0],nums[1]);
                continue;
            }
            dp[i] = Math.max(dp[i-1],dp[i-2]+nums[i]);
        }
        return dp[nums.length-1];
    }
}
```

## 反转链表  递归实现

```java
class Solution {
    public ListNode reverseList(ListNode head) {
       if(head == null || head.next == null){
           return head;
       }
       ListNode newTail = head.next;
       ListNode newHead = reverseList(head.next);
       newTail.next = head;
       head.next = null;
       return newHead;
    }
}
```

##  [最短回文串](https://leetcode-cn.com/problems/shortest-palindrome/)

```

```

##  [回文链表](https://leetcode-cn.com/problems/palindrome-linked-list/)

```java
    public boolean isPalindrome(ListNode head) {
        if(head == null || head.next == null) {
            return true;
        }
        ListNode slow = head, fast = head;
        ListNode pre = head, prepre = null;
        while(fast != null && fast.next != null) {
            pre = slow;
            slow = slow.next;
            fast = fast.next.next;
            pre.next = prepre;
            prepre = pre;
        }
        if(fast != null) {
            slow = slow.next;
        }
        while(pre != null && slow != null) {
            if(pre.val != slow.val) {
                return false;
            }
            pre = pre.next;
            slow = slow.next;
        }
        return true;
    }
```

## [滑动窗口最大值](https://leetcode-cn.com/problems/sliding-window-maximum/)

> 单调队列

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        int n = nums.length;
        Deque<Integer> deque = new LinkedList<Integer>();
        for (int i = 0; i < k; ++i) {
            while (!deque.isEmpty() && nums[i] >= nums[deque.peekLast()]) {
                deque.pollLast();
            }
            deque.offerLast(i);
        }

        int[] ans = new int[n - k + 1];
        ans[0] = nums[deque.peekFirst()];
        for (int i = k; i < n; ++i) {
            while (!deque.isEmpty() && nums[i] >= nums[deque.peekLast()]) {
                deque.pollLast();
            }
            deque.offerLast(i);
            while (deque.peekFirst() <= i - k) {
                deque.pollFirst();
            }
            ans[i - k + 1] = nums[deque.peekFirst()];
        }
        return ans;
    }
}
```



## [会议室问题](https://leetcode-cn.com/problems/meeting-rooms-ii/)

```java
class Solution {
    public int minMeetingRooms(int[][] intervals) {
        if(intervals == null || intervals.length == 0) return 0; 
        int[] start = new int[intervals.length];
        int[] end = new int[intervals.length];
        for(int i=0;i<intervals.length;i++){
            start[i] = intervals[i][0];
            end[i] = intervals[i][1];
        }
        Arrays.sort(start);
        Arrays.sort(end);
        
        int rooms=0, activeMeeting = 0;
        int i=0,j=0;
        while(i<intervals.length && j<intervals.length){
            if(start[i]<end[j]){
                activeMeeting++;
                i++;
            }else{
                activeMeeting--;
                j++;
         }
            rooms = Math.max(rooms,activeMeeting);
        }
        return rooms;
        
    }
}
```

## [300 最长递增子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/)

```java
public class Solution {
    public int lengthOfLIS(int[] nums) {
        int len = nums.length;
        if (len <= 1) {
            return len;
        }

        // tail 数组的定义：长度为 i + 1 的上升子序列的末尾最小是几
        int[] tail = new int[len];
        // 遍历第 1 个数，直接放在有序数组 tail 的开头
        tail[0] = nums[0];
        // end 表示有序数组 tail 的最后一个已经赋值元素的索引
        int end = 0;

        for (int i = 1; i < len; i++) {
            // 【逻辑 1】比 tail 数组实际有效的末尾的那个元素还大
            if (nums[i] > tail[end]) {
                // 直接添加在那个元素的后面，所以 end 先加 1
                end++;
                tail[end] = nums[i];
            } else {
                // 使用二分查找法，在有序数组 tail 中
                // 找到第 1 个大于等于 nums[i] 的元素，尝试让那个元素更小
                int left = 0;
                int right = end;
                while (left < right) {
                    // 选左中位数不是偶然，而是有原因的，原因请见 LeetCode 第 35 题题解
                    // int mid = left + (right - left) / 2;
                    int mid = left + ((right - left) >>> 1);
                    if (tail[mid] < nums[i]) {
                        // 中位数肯定不是要找的数，把它写在分支的前面
                        left = mid + 1;
                    } else {
                        right = mid;
                    }
                }
                // 走到这里是因为 【逻辑 1】 的反面，因此一定能找到第 1 个大于等于 nums[i] 的元素
                // 因此，无需再单独判断
                tail[left] = nums[i];
            }
            // 调试方法
            // printArray(nums[i], tail);
        }
        // 此时 end 是有序数组 tail 最后一个元素的索引
        // 题目要求返回的是长度，因此 +1 后返回
        end++;
        return end;
    }

    // 调试方法，以观察是否运行正确
    private void printArray(int num, int[] tail) {
        System.out.print("当前数字：" + num);
        System.out.print("\t当前 tail 数组：");
        int len = tail.length;
        for (int i = 0; i < len; i++) {
            if (tail[i] == 0) {
                break;
            }
            System.out.print(tail[i] + ", ");
        }
        System.out.println();
    }

    public static void main(String[] args) {
        int[] nums = new int[]{3, 5, 6, 2, 5, 4, 19, 5, 6, 7, 12};
        Solution solution = new Solution8();
        int lengthOfLIS = solution8.lengthOfLIS(nums);
        System.out.println("最长上升子序列的长度：" + lengthOfLIS);
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/longest-increasing-subsequence/solution/dong-tai-gui-hua-er-fen-cha-zhao-tan-xin-suan-fa-p/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## [零钱兑换](https://leetcode-cn.com/problems/coin-change/solution/322-ling-qian-dui-huan-by-leetcode-solution/)

```java
public class Solution {
    public int coinChange(int[] coins, int amount) {
        int max = amount + 1;
        int[] dp = new int[amount + 1];
        Arrays.fill(dp, max);
        dp[0] = 0;
        for (int i = 1; i <= amount; i++) {
            for (int j = 0; j < coins.length; j++) {
                if (coins[j] <= i) {
                    dp[i] = Math.min(dp[i], dp[i - coins[j]] + 1);
                }
            }
        }
        return dp[amount] > amount ? -1 : dp[amount];
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/coin-change/solution/322-ling-qian-dui-huan-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 496 [下一个更大元素1](https://leetcode-cn.com/problems/next-greater-element-i/)

> 单调递减栈

```java
import java.util.ArrayDeque;
import java.util.Arrays;
import java.util.Deque;
import java.util.HashMap;
import java.util.Map;
import java.util.Stack;

public class Solution {

    public int[] nextGreaterElement(int[] nums1, int[] nums2) {
        int len1 = nums1.length;
        int len2 = nums2.length;

        Deque<Integer> stack = new ArrayDeque<>();
        Map<Integer, Integer> map = new HashMap<>();
        // 先处理 nums2，把对应关系存入哈希表
        for (int i = 0; i < len2; i++) {
            while (!stack.isEmpty() && stack.peekLast() < nums2[i]) {
                map.put(stack.removeLast(), nums2[i]);
            }
            stack.addLast(nums2[i]);
        }

        // 遍历 nums1 得到结果集
        int[] res = new int[len1];
        for (int i = 0; i < len1; i++) {
            res[i] = map.getOrDefault(nums1[i], -1);
        }
        return res;
    }
}

作者：LeetCode
链接：https://leetcode-cn.com/problems/next-greater-element-i/solution/xia-yi-ge-geng-da-yuan-su-i-by-leetcode/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

##  [连续差相同的数字](https://leetcode-cn.com/problems/numbers-with-same-consecutive-differences/)

```java
class Solution {
    List<Integer> res = new ArrayList<>();
    public int[] numsSameConsecDiff(int n, int k) {
        //先初始化开头第一个字符，进入递归
        for (int i = 1; i <= 9; i++) {
            //如果i+k或者i-k都不是有效的字符，说明当前i不能作为数字的开头
            if (i + k > 9 && i - k < 0)
                continue;
            //进入递归
            DFS(new StringBuilder(String.valueOf(i)), n, k);
        }
        int[] nums = new int[res.size()];
        for (int i = 0; i < nums.length; i++) {
            nums[i] = res.get(i);
        }
        return nums;
    }

    public void DFS(StringBuilder str, int n, int k) {
        //长度到了就进入结果
        if (str.length() == n) {
            res.add(Integer.parseInt(str.toString()));
            return;
        }
        //获取上一个数字是什么
        int num = str.charAt(str.length() - 1) - '0';
        //num+k 可以加入到str中，并且开始递归
        if (num + k < 10) {
            str.append(String.valueOf(num + k));
            DFS(str, n, k);
            str.deleteCharAt(str.length() - 1);
        }
        //num-k 可以加入到str中并且开始递归
        if (num - k >= 0 && k != 0) {
            str.append(String.valueOf(num - k));
            DFS(str, n, k);
            str.deleteCharAt(str.length() - 1);
        }
    }
}

作者：zhangqiang-9
链接：https://leetcode-cn.com/problems/numbers-with-same-consecutive-differences/solution/dfs-jian-dan-ming-liao-by-zhangqiang-9-zwn9/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

##  [加油站](https://leetcode-cn.com/problems/gas-station/)

```java
class Solution {
public:
    int canCompleteCircuit(vector<int>& gas, vector<int>& cost) {
        int n = gas.size();
        int i = 0;
        while (i < n) {
            int sumOfGas = 0, sumOfCost = 0;
            int cnt = 0;
            while (cnt < n) {
                int j = (i + cnt) % n;
                sumOfGas += gas[j];
                sumOfCost += cost[j];
                if (sumOfCost > sumOfGas) {
                    break;
                }
                cnt++;
            }
            if (cnt == n) {
                return i;
            } else {
                i = i + cnt + 1;
            }
        }
        return -1;
    }
};

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/gas-station/solution/jia-you-zhan-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



## 分发糖果

```java
class Solution {
    public int candy(int[] ratings) {

        if(ratings == null || ratings.length ==  0){
            return 0;
        }
        int sum = ratings.length;
        int[] arr = new int[ratings.length];
        for(int i=1;i<ratings.length;i++){
            if(ratings[i]>ratings[i-1]){
                arr[i] = arr[i-1] + 1;
            }
        }
        

        for(int i=ratings.length-2;i>=0;i--){
            if(ratings[i] > ratings[i+1]){
                arr[i] = Math.max(arr[i],arr[i+1] + 1);
            }
        }
        for(int i =0;i<arr.length;i++){
            System.out.print(arr[i]+1+" ");
            sum  += arr[i];
        }
        return sum;
    }
}
```

## [单词接龙](https://leetcode-cn.com/problems/word-ladder/) （待解决）

## 424 [替换后的最长重复字符](https://leetcode-cn.com/problems/longest-repeating-character-replacement/)

```java
class Solution {
    public int characterReplacement(String s, int k) {
        int[] num = new int[26];
        int n = s.length();
        int maxn = 0;
        int left = 0, right = 0;
        int result = -1;
        while (right < n) {
            num[s.charAt(right) - 'A']++;
            maxn = Math.max(maxn, num[s.charAt(right) - 'A']);
            if (right - left + 1 - maxn > k) {
                num[s.charAt(left) - 'A']--;
                left++;
            }
            result = Math.max(result,right - left + 1);
            right++;
        }
        return result;
    }
}
```

##  841 钥匙和房间

##  671 二叉树中第二小的节点

```java
class Solution {
    int ans;
    int rootvalue;

    public int findSecondMinimumValue(TreeNode root) {
        ans = -1;
        rootvalue = root.val;
        dfs(root);
        return ans;
    }

    public void dfs(TreeNode node) {
        if (node == null) {
            return;
        }
       
        if (node.val > rootvalue && ans != -1) {
            ans =  Math.min(node.val,ans);
        }else if(node.val > rootvalue){
            ans = node.val;
        }
        dfs(node.left);
        dfs(node.right);
    }
}

```



## [1569. 将子数组重新排序得到同一个二叉查找树的方案数](https://leetcode-cn.com/problems/number-of-ways-to-reorder-array-to-get-same-bst/)

## [1675. 数组的最小偏移量](https://leetcode-cn.com/problems/minimize-deviation-in-array/)

## [50. Pow(x, n)](https://leetcode-cn.com/problems/powx-n/)

```java
class Solution {
    public double myPow(double x, int n) {
        if(n==0){
            return 1;
        }
        if(n == 1){
            return x;
        }
        if(n==-1){
            return 1.0/x;
        }
        double half = myPow(x,n/2);
        if(n%2 == 0){
            return half*half;
        }else{
            return half*half*myPow(x,n%2);
        }
    }
}
```

## [1024. 视频拼接](https://leetcode-cn.com/problems/video-stitching/)

```java
class Solution {
    public int videoStitching(int[][] clips, int time) {
        int[] dp = new int[time + 1];
        Arrays.fill(dp, Integer.MAX_VALUE - 1);
        dp[0] = 0;
        for (int i = 1; i <= time; i++) {
            for (int[] clip : clips) {
                if (clip[0] < i && i <= clip[1]) {
                    dp[i] = Math.min(dp[i], dp[clip[0]] + 1);
                }
            }
        }
        return dp[time] == Integer.MAX_VALUE - 1 ? -1 : dp[time];
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/video-stitching/solution/shi-pin-pin-jie-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## [741. 摘樱桃](https://leetcode-cn.com/problems/cherry-pickup/)  不会



## [560. 和为 K 的子数组](https://leetcode-cn.com/problems/subarray-sum-equals-k/)

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        /**
        扫描一遍数组, 使用map记录出现同样的和的次数, 对每个i计算累计和sum并判断map内是否有sum-k
        **/
        Map<Integer, Integer> map = new HashMap<>();
        map.put(0, 1);
        int sum = 0, ret = 0;

        for(int i = 0; i < nums.length; ++i) {
            sum += nums[i];
            if(map.containsKey(sum-k))
                ret += map.get(sum-k);
            map.put(sum, map.getOrDefault(sum, 0)+1);
        }
        
        return ret;
    }
}
```

## [36. 有效的数独](https://leetcode-cn.com/problems/valid-sudoku/)

```java
class Solution {
    public boolean isValidSudoku(char[][] board) {
        // 记录某行，某位数字是否已经被摆放
        boolean[][] row = new boolean[9][9];
        // 记录某列，某位数字是否已经被摆放
        boolean[][] col = new boolean[9][9];
        // 记录某 3x3 宫格内，某位数字是否已经被摆放
        boolean[][] block = new boolean[9][9];

        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) {
                if (board[i][j] != '.') {
                    int num = board[i][j] - '1';
                    int blockIndex = i / 3 * 3 + j / 3;
                    if (row[i][num] || col[j][num] || block[blockIndex][num]) {
                        return false;
                    } else {
                        row[i][num] = true;
                        col[j][num] = true;
                        block[blockIndex][num] = true;
                    }
                }
            }
        }
        return true;
    }
}
```

## [37. 解数独](https://leetcode-cn.com/problems/sudoku-solver/)

```java
class Solution {
    public void solveSudoku(char[][] board) {
        /**
         * 记录某行，某位数字是否已经被摆放
         */
        boolean[][] row = new boolean[9][9];
        /**
         * 记录某列，某位数字是否已经被摆放
         */
        boolean[][] col = new boolean[9][9];
        /**
         * 记录某 3x3 宫格内，某位数字是否已经被摆放
         */
        boolean[][] block = new boolean[9][9];

        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) {
                if (board[i][j] != '.') {
                    int num = board[i][j] - '1';
                    row[i][num] = true;
                    col[j][num] = true;
                    // blockIndex = i / 3 * 3 + j / 3，取整
                    block[i / 3 * 3 + j / 3][num] = true;
                }
            }
        }
        dfs(board, row, col, block, 0, 0);
    }

    private boolean dfs(char[][] board, boolean[][] row, boolean[][] col, boolean[][] block, int i, int j) {
        // 找寻空位置
        while (board[i][j] != '.') {
            if (++j >= 9) {
                i++;
                j = 0;
            }
            if (i >= 9) {
                return true;
            }
        }
        for (int num = 0; num < 9; num++) {
            int blockIndex = i / 3 * 3 + j / 3;
            if (!row[i][num] && !col[j][num] && !block[blockIndex][num]) {
                // 递归
                board[i][j] = (char) ('1' + num);
                row[i][num] = true;
                col[j][num] = true;
                block[blockIndex][num] = true;
                if (dfs(board, row, col, block, i, j)) {
                    return true;
                } else {
                    // 回溯
                    row[i][num] = false;
                    col[j][num] = false;
                    block[blockIndex][num] = false;
                    board[i][j] = '.';
                }
            }
        }
        return false;
    }

    private void printBoard(char[][] board) {
        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) {
                System.out.print(board[i][j] + " ");
            }
            System.out.println();
        }
    }

    public static void main(String[] args) {
        char[][] board = new char[][]{
                {'5', '3', '.', '.', '7', '.', '.', '.', '.'},
                {'6', '.', '.', '1', '9', '5', '.', '.', '.'},
                {'.', '9', '8', '.', '.', '.', '.', '6', '.'},
                {'8', '.', '.', '.', '6', '.', '.', '.', '3'},
                {'4', '.', '.', '8', '.', '3', '.', '.', '1'},
                {'7', '.', '.', '.', '2', '.', '.', '.', '6'},
                {'.', '6', '.', '.', '.', '.', '2', '8', '.'},
                {'.', '.', '.', '4', '1', '9', '.', '.', '5'},
                {'.', '.', '.', '.', '8', '.', '.', '7', '9'}
        };
        Solution solution = new Solution();
        solution.printBoard(board);
        solution.solveSudoku(board);
        solution.printBoard(board);
    }
}
```

## [移位字符串分组](https://leetcode-cn.com/problems/group-shifted-strings/)

```java
class Solution {
    public List<List<String>> groupStrings(String[] strings) {
        Map<String, List<String>> map = new HashMap();
        for (int i = 0; i < strings.length; i++) {
            String hash = hash(strings[i]);
            List<String> list = map.computeIfAbsent(hash, a -> new ArrayList());
            list.add(strings[i]);
        }
        return new ArrayList(map.values());
    }

    public String hash(String val) {
        char[] chars = val.toCharArray();
        for (int i = 1; i < chars.length; i++) {
            chars[i] =  (char)('a' + (chars[i] - chars[0] + 26) % 26);
        }
        chars[0] = 'a';
        return new String(chars);
    }
}
```

## 任务调度器

```java
 /**
     * 解题思路：
     * 1、将任务按类型分组，正好A-Z用一个int[26]保存任务类型个数
     * 2、对数组进行排序，优先排列个数（count）最大的任务，
     *      如题得到的时间至少为 retCount =（count-1）* (n+1) + 1 ==> A->X->X->A->X->X->A(X为其他任务或者待命)
     * 3、再排序下一个任务，如果下一个任务B个数和最大任务数一致，
     *      则retCount++ ==> A->B->X->A->B->X->A->B
     * 4、如果空位都插满之后还有任务，那就随便在这些间隔里面插入就可以，因为间隔长度肯定会大于n，在这种情况下就是任务的总数是最小所需时间
     *
     * @param tasks
     * @param n
     * @return
     */
    public int leastInterval(char[] tasks, int n) {
        if (tasks.length <= 1 || n < 1) return tasks.length;
        //步骤1
        int[] counts = new int[26];
        for (int i = 0; i < tasks.length; i++) {
            counts[tasks[i] - 'A']++;
        }
        //步骤2
        Arrays.sort(counts);
        int maxCount = counts[25];
        int retCount = (maxCount - 1) * (n + 1) + 1;
        int i = 24;
        //步骤3
        while (i >= 0 && counts[i] == maxCount) {
            retCount++;
            i--;
        }
        //步骤4
        return Math.max(retCount, tasks.length);
    }

```

## 寻找重复数字

> 给定一个包含 n + 1 个整数的数组 nums ，其数字都在 1 到 n 之间（包括 1 和 n），可知至少存在一个重复的整数。
>
> 假设 nums 只有 一个重复的整数 ，找出 这个重复的数 。
>
> 你设计的解决方案必须不修改数组 nums 且只用常量级 O(1) 的额外空间。
>
> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/find-the-duplicate-number
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

```java
class Solution {
    public int findDuplicate(int[] nums) {
        /**
        快慢指针思想, fast 和 slow 是指针, nums[slow] 表示取指针对应的元素
        注意 nums 数组中的数字都是在 1 到 n 之间的(在数组中进行游走不会越界),
        因为有重复数字的出现, 所以这个游走必然是成环的, 环的入口就是重复的元素, 
        即按照寻找链表环入口的思路来做
        **/
        int fast = 0, slow = 0;
        while(true) {
            fast = nums[nums[fast]];
            slow = nums[slow];
            if(slow == fast) {
                fast = 0;
                while(nums[slow] != nums[fast]) {
                    fast = nums[fast];
                    slow = nums[slow];
                }
                return nums[slow];
            }
        }
    }
}
```

## 85[最大矩形](https://leetcode-cn.com/problems/maximal-rectangle/)

## 84 最大矩形

> 单调递增栈

```java
 /**
     * 84. 柱状图中最大的矩形
     */
    public int largestRectangleArea(int[] heights) {
        int res = 0;
        Deque<Integer> stack = new ArrayDeque<>();
        int[] new_heights = new int[heights.length + 2];
        for (int i = 1; i < heights.length + 1; i++) {
            new_heights[i] = heights[i - 1];
        }
        ;
        for (int i = 0; i < new_heights.length; i++) {
            while (!stack.isEmpty() && new_heights[stack.peek()] > new_heights[i]) {
                int cur = stack.pop();
                res = Math.max(res, (i - stack.peek() - 1) * new_heights[cur]);
            }
            stack.push(i);
        }
        return res;
    }
```

## 212 单词搜索2

```java
class Solution {
    int[][] dirs = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};

    public List<String> findWords(char[][] board, String[] words) {
        Trie trie = new Trie();
        for (String word : words) {
            trie.insert(word);
        }

        Set<String> ans = new HashSet<String>();
        for (int i = 0; i < board.length; ++i) {
            for (int j = 0; j < board[0].length; ++j) {
                dfs(board, trie, i, j, ans);
            }
        }

        return new ArrayList<String>(ans);
    }

    public void dfs(char[][] board, Trie now, int i1, int j1, Set<String> ans) {
        if (!now.children.containsKey(board[i1][j1])) {
            return;
        }
        char ch = board[i1][j1];
        now = now.children.get(ch);
        if (!"".equals(now.word)) {
            ans.add(now.word);
        }

        board[i1][j1] = '#';
        for (int[] dir : dirs) {
            int i2 = i1 + dir[0], j2 = j1 + dir[1];
            if (i2 >= 0 && i2 < board.length && j2 >= 0 && j2 < board[0].length) {
                dfs(board, now, i2, j2, ans);
            }
        }
        board[i1][j1] = ch;
    }
}

class Trie {
    String word;
    Map<Character, Trie> children;
    boolean isWord;

    public Trie() {
        this.word = "";
        this.children = new HashMap<Character, Trie>();
    }

    public void insert(String word) {
        Trie cur = this;
        for (int i = 0; i < word.length(); ++i) {
            char c = word.charAt(i);
            if (!cur.children.containsKey(c)) {
                cur.children.put(c, new Trie());
            }
            cur = cur.children.get(c);
        }
        cur.word = word;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/word-search-ii/solution/dan-ci-sou-suo-ii-by-leetcode-solution-7494/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## [1014. 最佳观光组合](https://leetcode-cn.com/problems/best-sightseeing-pair/)

```java
class Solution {
    public int maxScoreSightseeingPair(int[] A) {
        
        int left = A[0], res = Integer.MIN_VALUE;
        for (int j = 1; j < A.length; j++) {
            
            res = Math.max(res, left + A[j] - j);
            left = Math.max(left, A[j] + j);
        }
        return res;
    }
}
```

