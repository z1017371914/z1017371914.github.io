# leetcode-头条

## 两数之和

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        
        HashMap<Integer,Integer> map = new HashMap<>();
        for(int i=0;i<nums.length;i++){
            if(map.containsKey(target - nums[i])){
                return new int[]{i,map.get(target-nums[i])};
            }
            map.put(nums[i],i);
        }
        return null;
    }
}
```

## 两数相加

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode dummyNode = new ListNode(0);
        ListNode tail = dummyNode;
        int jinwei = 0;
        while(l1 != null && l2 != null){
            int number = l1.val + l2.val + jinwei;
            ListNode temp = new ListNode(number%10);
            jinwei = number / 10;
            tail.next = temp;
            tail = temp;
            l1 = l1.next;
            l2 = l2.next;
        }
        if(l2 != null){
            l1 = l2;
        }
        while(l1 != null){
             int number = l1.val + jinwei;
            ListNode temp = new ListNode(number%10);
            jinwei = number / 10;
            tail.next = temp;
            tail = temp;
            l1 = l1.next;
        }

        if(jinwei > 0){
            ListNode temp = new ListNode(jinwei);
            tail.next = temp;
            tail = temp;
        }
        tail.next = null;
        return dummyNode.next;
    }
}
```

## 无重复字符的最长子串

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        if(s == null || s.length() == 0){
            return 0;
        }
        int[] array = new int[256];
        for(int i=0;i<256;i++){
            array[i] = -1;
        }
        int start = -1;
        int maxLen = 0;
        for(int i = 0;i<s.length();i++){
            if(array[s.charAt(i)] > start){
                start = array[s.charAt(i)];
            }
            maxLen = Math.max(maxLen,i-start);
            array[s.charAt(i)] = i;
        }
        return maxLen;
    }
}
```

## 最长回文串

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

## 反转链表

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode reverseList(ListNode head) {
        if(head == null){
            return null;
        }
        ListNode cur = null;
        ListNode next = head;
        while(next != null){
            ListNode temp = next.next;
            next.next = cur;
            cur = next;
            next = temp;
        }
        return cur;
    }
}
```

## 接雨水

```java
class Solution {
    public int trap(int[] height) {
        if(height == null || height.length == 0){
            return 0;
        }
        int[] left = new int[height.length];
        int[] right = new int[height.length];
        int max = 0;
        for(int i = 0;i<height.length;i++){
            left[i] = max;
            max = Math.max(max,height[i]);
        }
        max = 0;
        for(int i = height.length-1;i>=0;i--){
            right[i] = max;
            max = Math.max(max,height[i]);
        }
        int sum = 0;
        for(int i = 1;i<height.length-1;i++){
            sum += Math.max(0,Math.min(left[i],right[i])-height[i]);
        }
        return sum;
    }
}
```

## 寻找两个有序数组的中位数

```java
 import java.util.Arrays;
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int length = nums1.length + nums2.length;

        if(length%2 == 1){
            return findMedianSortedArrays( nums1,nums2, length/2 + 1);
        }
        return findMedianSortedArrays(nums1,nums2,length/2 + 1)/2 +
                findMedianSortedArrays(nums1,nums2,length/2)/2 ;


    }
    /// k是第k个数 下标 是k-1
    private double findMedianSortedArrays(int[] nums1,int[] nums2,int k){
        
        if(nums1.length > nums2.length){
            int[] temp = nums1;
            nums1 = nums2;
            nums2 = temp;
        }
        if(nums1.length == 0){
            return nums2[k-1];
        }
        if(k == 1){
            return Math.min(nums1[0],nums2[0]);
        }
    
        int k1 = Math.min(nums1.length,k/2);

       
        if(nums1[k1-1] < nums2[k/2 -1]){
            return findMedianSortedArrays(Arrays.copyOfRange(nums1,k1,nums1.length),nums2,k-k1);
            
        }
        return findMedianSortedArrays(nums1,Arrays.copyOfRange(nums2,k/2,nums2.length),k-k/2);

    }
}
```

## 字典序的第K小数字

> 这是一个十叉树，但不是满的，要找到当前节点下所有的节点数目,总数目是一层一层的找的。
>
> 如果

```java
class Solution {
    public int findKthNumber(int n, int k) {
        int cur = 1;
        while(k>1){
            long step = 0;
            long last = cur+1;
            long first = cur;
            while(first <= n){
                step += (long)Math.min(n+1-first,last-first);
                first*=10;
                last *=10;
            }

            if(step < k){
                ++cur;
                k-=step;
            }else{
                k--;
                cur*=10;
            }

        }
        return cur;
    }
}
```

