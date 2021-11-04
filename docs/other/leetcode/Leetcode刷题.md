

## 数组

### 485.最大连续1的个数

#### 题目描述

给定一个二进制数组， 计算其中最大连续1的个数。

示例 1:

输入: [1,1,0,1,1,1]
输出: 3
解释: 开头的两位和最后的三位都是连续1，所以最大连续1的个数是 3.
注意：

输入的数组只包含 0 和1。
输入数组的长度是正整数，且不超过 10,000。

#### 思路

遍历一次，记下连续1的个数。遇到0的时候再比较当前1的个数和已经存储的个数谁更大

**注意**

可能会存在最后结尾的情况是连续1，所以在返回的时候应该还要再比较一次。

#### 代码

```java
class Solution {
    public int findMaxConsecutiveOnes(int[] nums) {
        int res = 0;
        int counter = 0;
        for ( int i = 0; i < nums.length; i++ ) {
            if ( nums[i] == 1) {
                counter++;
            }else {
                res = Math.max(res,counter);
                counter = 0;
            }
        }
        return Math.max(res,counter);
    }
}
```

#### 复杂度

时间：O(n)

空间：O(1)

### 283.移动零

#### 题目描述

给定一个数组 nums，编写一个函数将所有 0 移动到数组的末尾，同时保持非零元素的相对顺序。

示例:

输入: [0,1,0,3,12]
输出: [1,3,12,0,0]
说明:

必须在原数组上操作，不能拷贝额外的数组。
尽量减少操作次数。

#### 方法一:两次遍历

##### 思路

两次遍历。

第一次先遍历，数出非零的个数。并且将非零的个数全部靠前放置。

第二次遍历，剩下的全部填充零即可

##### 代码

```java
class Solution {
    public void moveZeroes(int[] nums) {
        int notZero = 0;
        for ( int i = 0; i < nums.length; i++ ) {
            if ( nums[i] != 0 ) {
                nums[notZero++] = nums[i];
            }
        }
        for ( int i = notZero; i < nums.length; i++ ) {
            nums[i] = 0;
        }
    }
}
```

##### 复杂度

时间复杂度：O(n)

空间复杂度：O(1)

#### 方法二：双指针

##### 思路

一次遍历，相当于是对方法一进行简化

相当于是双指针。

i用于遍历，并将j后的全都置零，j用于记录非零。

##### 代码

```java
class Solution {
    public void moveZeroes(int[] nums) {
        int j = 0;
        for ( int i = 0; i < nums.length; i++ ) {
            if( nums[i] != 0) {
                nums[j] = nums[i];
                if ( i != j ) {
                    nums[i] = 0;
                }
                j++;
            }
        }
    }
}
```

##### 复杂度

时间复杂度：O(n)

空间复杂度：O(1)

### 27.移除元素

#### 题目描述

给你一个数组 nums 和一个值 val，你需要 原地 移除所有数值等于 val 的元素，并返回移除后数组的新长度。

不要使用额外的数组空间，你必须仅使用 O(1) 额外空间并 原地 修改输入数组。

元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

 

示例 1:

给定 nums = [3,2,2,3], val = 3,

函数应该返回新的长度 2, 并且 nums 中的前两个元素均为 2。

你不需要考虑数组中超出新长度后面的元素。
示例 2:

给定 nums = [0,1,2,2,3,0,4,2], val = 2,

函数应该返回新的长度 5, 并且 nums 中的前五个元素为 0, 1, 3, 0, 4。

注意这五个元素可为任意顺序。

你不需要考虑数组中超出新长度后面的元素。

#### 思路：双指针

使用快慢指针。

快指针遍历数组元素，慢指针记录非val元素的数目。

如果快指针遍历时出现了非val元素，那么慢指针所在的索引就要接收快指针的值，并将慢指针数目加一。

#### 代码

```java
class Solution {
    public int removeElement(int[] nums, int val) {
        int counter = 0;
        for ( int i = 0; i < nums.length; i++ ) {
            if ( nums[i] != val ) {
                nums[counter] = nums[i];
                counter++;
            }
        }
        return counter;
    }
}
```

#### 复杂度

时间：O(n)

空间：O(1)

## 链表

### 203.移除链表元素

#### 题目描述

删除链表中等于给定值 ***val\*** 的所有节点。

**示例:**

```java
输入: 1->2->6->3->4->5->6, val = 6
输出: 1->2->3->4->5
```

#### 思路：哨兵节点

引入两个节点。分别记录当前遍历的节点和前驱节点。

如果当前节点等于该值，那么就删除，并移动当前节点

如果不等于，那就只移动前趋节点和当前节点

##### 哨兵节点

补充：**哨兵节点**广泛应用于**树和链表**中，如伪头、伪尾、标记等，它们是纯功能的，通常不保存任何数据，其主要目的是使链表标准化，如使链表永不为空、永不无头、简化插入和删除。



#### 代码

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode removeElements(ListNode head, int val) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode pre = dummy, current= head;
        while ( current != null ) {
            if ( current.val == val ) pre.next = current.next;
            else pre = current;
            current = current.next;
        }
        return dummy.next;
    }
}
```

#### 复杂度

时间：O(n)

空间：O(1)

### 206.反转链表

#### 题目描述

反转一个单链表。

示例:

输入: 1->2->3->4->5->NULL
输出: 5->4->3->2->1->NULL
进阶:
你可以迭代或递归地反转链表。你能否用两种方法解决这道题？

#### 方法一：双指针迭代

##### 思路：

![9ce26a709147ad9ce6152d604efc1cc19a33dc5d467ed2aae5bc68463fdd2888](https://tvax3.sinaimg.cn/large/0085EwgIgy1gmzv69fmmyg30np0dcgm3.gif)

图片引用[leetcode答主](https://leetcode-cn.com/problems/reverse-linked-list/solution/fan-zhuan-lian-biao-shuang-zhi-zhen-di-gui-yao-mo-/)

##### 代码

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
        ListNode pre = head;
        ListNode current = null;
        while ( pre != null ) {
            ListNode next = pre.next;
            pre.next = current;
            current = pre;
            pre = next;
        }
        return current;
    }
}
```

##### 复杂度

时间：O(n)

空间：O(1)

#### 方法二：递归

##### 思路：

[参考回答](https://leetcode-cn.com/problems/reverse-linked-list/solution/dong-hua-yan-shi-206-fan-zhuan-lian-biao-by-user74/)

##### 代码

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
        //递归退出条件
        if ( head == null || head.next == null ) return head;
        //递归结果，res取到了链表的尾节点
        ListNode res = reverseList(head.next);
        //当前节点的下一个节点指向它自己
        head.next.next = head;
        //当前节点指向空，防止和上一步中的结果，产生循环指向
        head.next = null;
        //返回尾节点当作反转后的头节点
        return res;
    }
    
}
```

##### 复杂度

时间：O(N)

空间：O(N)

## 栈

### 20.有效的括号

#### 题目描述

给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串 s ，判断字符串是否有效。

有效字符串需满足：

左括号必须用相同类型的右括号闭合。
左括号必须以正确的顺序闭合。


示例 1：

输入：s = "()"
输出：true
示例 2：

输入：s = "()[]{}"
输出：true
示例 3：

输入：s = "(]"
输出：false
示例 4：

输入：s = "([)]"
输出：false
示例 5：

输入：s = "{[]}"
输出：true

#### 思路

使用栈进行存储。

将字符串转成字符数组。对于每个字符数组的元素，每次存入它对应得反方向的符号。当遇到反方向的符号的时候，弹出栈顶元素进行比较。如果不相等或者栈已经没有元素了，那说明并不是满足条件的情况。

最后返回栈是否为空即可。为空那说明全都对应上了。

#### 代码

```java
class Solution {
    public boolean isValid(String s) {
        Stack<Character> stack = new Stack<>();
        for ( char c : s.toCharArray() ) {
            if ( c == '(') {
                stack.push(')');
            } else if ( c == '{') {
                stack.push('}');
            } else if ( c == '[') {
                stack.push(']');
            } else if ( stack.isEmpty() || stack.pop() != c ) {
                return false;
            }
        }
        return stack.isEmpty();
    }
}
```

#### 复杂度

时间：O(n)

空间：O(n)

### 496.下一个更大元素 I

#### 题目描述

给你两个 没有重复元素 的数组 nums1 和 nums2 ，其中nums1 是 nums2 的子集。

请你找出 nums1 中每个元素在 nums2 中的下一个比其大的值。

nums1 中数字 x 的下一个更大元素是指 x 在 nums2 中对应位置的右边的第一个比 x 大的元素。如果不存在，对应位置输出 -1 。

 

示例 1:

输入: nums1 = [4,1,2], nums2 = [1,3,4,2].
输出: [-1,3,-1]
解释:
    对于 num1 中的数字 4 ，你无法在第二个数组中找到下一个更大的数字，因此输出 -1 。
    对于 num1 中的数字 1 ，第二个数组中数字1右边的下一个较大数字是 3 。
    对于 num1 中的数字 2 ，第二个数组中没有下一个更大的数字，因此输出 -1 。
示例 2:

输入: nums1 = [2,4], nums2 = [1,2,3,4].
输出: [3,-1]
解释:
    对于 num1 中的数字 2 ，第二个数组中的下一个较大数字是 3 。
    对于 num1 中的数字 4 ，第二个数组中没有下一个更大的数字，因此输出 -1 。


提示：

1 <= nums1.length <= nums2.length <= 1000
0 <= nums1[i], nums2[i] <= 104
nums1和nums2中所有整数 互不相同
nums1 中的所有整数同样出现在 nums2 中

#### 思路：单调栈+哈希表

设计单调栈和哈希表来实现。

先遍历nums2数组。对于遍历到的元素，每次都和栈顶元素进行对比。如果大于栈顶元素，那么说明他就是栈顶元素的第一个大于栈顶元素的值。这时候就要把遍历到nums[i]和栈顶元素弹出，放入哈希表中：栈顶元素为key，nums2[i]为value。这样，后续我们遍历nums1的时候，只要查一查对应的value即可实现。

注意，在遍历完nums2之后，栈内是有可能还有元素没有被弹出放入哈希表里的，那么这时候说明这些元素其实并没有遇到第一个比他大的数，所以也要将他们放入哈希表中，对应的value为-1.

#### 代码

```java
class Solution {
    public int[] nextGreaterElement(int[] nums1, int[] nums2) {
        Stack<Integer> stack = new Stack<>();
        HashMap<Integer,Integer> hashMap = new HashMap<>();
        int[] res = new int[nums1.length];

        for ( int i = 0; i < nums2.length; i++ ) {
            while ( !stack.isEmpty() && stack.peek() < nums2[i] )
                hashMap.put(stack.pop(),nums2[i]);
            stack.push(nums2[i]);
        }

        while ( !stack.isEmpty() ) {
            hashMap.put(stack.pop(),-1);
        }

        for ( int i = 0; i < nums1.length; i++ ) 
            res[i] = hashMap.get(nums1[i]);
        
        return res;
        
    }
}
```

#### 复杂度

时间：O(m+n)

空间：O(n)

## 哈希表

### 217.存在重复的元素

#### 题目描述

给定一个整数数组，判断是否存在重复元素。

如果存在一值在数组中出现至少两次，函数返回 true 。如果数组中每个元素都不相同，则返回 false 。

 

示例 1:

输入: [1,2,3,1]
输出: true
示例 2:

输入: [1,2,3,4]
输出: false
示例 3:

输入: [1,1,1,3,3,4,3,2,4,2]
输出: true

#### 方法一

##### 思路：排序后比较

##### 代码

```java
class Solution {
    public boolean containsDuplicate(int[] nums) {
        Arrays.sort(nums);
        for ( int i = 0; i < nums.length-1; i++ ) {
            if ( nums[i] == nums[i+1] ) 
                return true;
        }
        return false;
    }
}
```

##### 复杂度

时间：O(N log( N ) )

空间：O(1)

#### 方法二

##### 思路：哈希表

##### 代码

```java
class Solution {
    public boolean containsDuplicate(int[] nums) {
        HashMap<Integer,Integer> map = new HashMap<>();
        for ( int  i = 0; i < nums.length; i++ ) {
            if ( map.containsKey(nums[i])) 
                return true;
            map.put(nums[i],i);
        }
        return false;
        /**
        set也可以，本质上无差别，因为底层实现还是HashMap
        */
        HashSet<Integer> set = new HashSet<>();
        for ( int i = 0; i < nums.length; i++ ) {
            if ( !set.add(nums[i]) )
                return true;
            set.add(nums[i]);
        }
        return false;
    }
}
```

##### 复杂度

时间：O(N)

空间：O(N)

## 集合

### 705.设计哈希集合

#### 题目描述

不使用任何内建的哈希表库设计一个哈希集合

具体地说，你的设计应该包含以下的功能

add(value)：向哈希集合中插入一个值。
contains(value) ：返回哈希集合中是否存在这个值。
remove(value)：将给定值从哈希集合中删除。如果哈希集合中没有这个值，什么也不做。

示例:

MyHashSet hashSet = new MyHashSet();
hashSet.add(1);         
hashSet.add(2);         
hashSet.contains(1);    // 返回 true
hashSet.contains(3);    // 返回 false (未找到)
hashSet.add(2);          
hashSet.contains(2);    // 返回 true
hashSet.remove(2);          
hashSet.contains(2);    // 返回  false (已经被删除)

注意：

所有的值都在 [0, 1000000]的范围内。
操作的总数目在[1, 10000]范围内。
不要使用内建的哈希集合库。

#### 方法一

##### 思路：数组+链表

定义哈希值函数通过取模769得到。为什么是769？因为和它互质的数很多，理论上缺的质数越大，哈希冲突就越少，但是这个值也决定了数组的长度，太长的话空间复杂度也会增加，权衡之下取769比较合适。

基本思路就是**拉链法**

用数组来存，每个数组元素是哈希桶，桶内是用链表来实现响应的增删查找。

需要注意的是集合，那么增加的时候不是无脑的增加，而是需要先判断是否已经存在改值，才能增加。其他操作比较简单，看看代码就能懂了

##### 代码

```java
class MyHashSet {
    private int selfMod = 769;
    private Bucket[] bucketArray = new Bucket[selfMod];
    /** Initialize your data structure here. */
    public MyHashSet() {
        for ( int i = 0 ; i < selfMod; i++ ) 
            bucketArray[i] = new Bucket();
    }
    
    public int selfHash(int key) {
        return ( key % selfMod);
    }
    public void add(int key) {
        bucketArray[selfHash(key)].insert(key);
    }
    
    public void remove(int key) {
        bucketArray[selfHash(key)].delete(key);
    }
    
    /** Returns true if this set contains the specified element */
    public boolean contains(int key) {
        return bucketArray[selfHash(key)].exit(key);
    }
}

class Bucket {
    private LinkedList<Integer> container = new LinkedList<>();
    public Bucket() {

    }

    public void insert(Integer key) {
        if ( container.indexOf(key) == -1) 
            container.add(key);
    }

    public void delete(Integer key) {
        container.remove(key);
    }

    public boolean exit(Integer key) {
        return container.indexOf(key) == -1 ? false: true;
    } 
}
/**
 * Your MyHashSet object will be instantiated and called as such:
 * MyHashSet obj = new MyHashSet();
 * obj.add(key);
 * obj.remove(key);
 * boolean param_3 = obj.contains(key);
 */
```

##### 复杂度

时间复杂度：O(N/k),N表示插入的值，k表示哈希取模数的大小。

空间复杂度：O(K+N),k表示哈希取模数的大小，N表示已经在set中的数量

#### 方法二(待完善)

##### 思路：数组+二叉搜索数

方法一的插入操作，需要遍历链表来查询是否已经存在，如果冲突很严重，那么时间复杂度就是O(N)

使用二叉搜索树能把搜索的时间复杂度降到O(log N).

##### 代码

```java

```

##### 复杂度

时间：

空间：

## 树

### 树的遍历

#### 前序遍历

**根节点-**>左子树->右子树

##### 方法一：递归实现

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> res = new LinkedList<>();
        recurPre(root,res);
        return res;
    }

    public void recurPre(TreeNode root , List<Integer> list) {
        if ( root == null ) return;
        list.add(root.val);
        recurPre(root.left,list);
        recurPre(root.right,list);
    }
}
```

###### 复杂度

时间：O( N )

空间：O( N ) 递归使用了栈

##### 方法二：迭代实现

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> res  = new LinkedList<>();
        Deque<TreeNode> stack = new LinkedList<>();
        TreeNode cur = root;
        while ( !stack.isEmpty() || cur != null) {
            while ( cur != null ) {
                res.add(cur.val);
                stack.push(cur);
                cur = cur.left;
            }
            cur = stack.pop();
            cur = cur.right;
        }
        return res;
    }
}
```

###### 复杂度

时间：O(N)

空间：平均情况下为 O*(log* n*)，最坏情况下树呈现链状，为 O(n)*

##### 方法三：Morris 遍历

#### 中序遍历

左子树->**根节点**->右子树

##### 方法一：递归实现

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        recurMedium(root,res);
        return res;
    }

    public void recurMedium(TreeNode root, List<Integer> list) {
        if ( root == null ) return;
        recurMedium(root.left,list);
        list.add(root.val);
        recurMedium(root.right,list);
    }
}
```

###### 复杂度

时间：O( N )

空间：O( N ) 递归使用了栈

##### 方法二：迭代实现

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        Deque<TreeNode> stack = new ArrayDeque<>();
        while ( !stack.isEmpty() || root != null ) {
            while ( root != null ) {
                stack.push( root);
                root = root.left;
            }
            root = stack.pop();
            res.add(root.val);
            root = root.right;
        } 
        return res;
    }
}
```

###### 复杂度

时间：O(N)

空间：平均情况下为 O*(log* n*)，最坏情况下树呈现链状，为 O(n)*

##### 方法三：Morris 遍历





#### 后序遍历

左子树->右子树->**根节点** 

##### 方法一：递归实现

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        recurBack(root,res);
        return res;
    }

    public void recurBack( TreeNode root, List<Integer> list) {
        if ( root == null ) return;
        recurBack(root.left,list);
        recurBack(root.right,list);
        list.add(root.val);
    }
}
```

###### 复杂度

时间：O( N )

空间：O( N ) 递归使用了栈

##### 方法二：迭代实现

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        Deque<TreeNode> stack = new ArrayDeque<>();
        TreeNode prev = null;
        while ( !stack.isEmpty() || root != null ) {
            while ( root != null ) {
                stack.push(root);
                root = root.left;
            }
            root = stack.pop();
            if ( root.right == null || root .right == prev) {
                res.add(root.val);
                prev =root;
                root = null;
            } else {
                stack.push(root);
                root = root.right;
            }
        }
        return res;
    }
}
```

###### 复杂度

时间：O(N)

空间：平均情况下为 O*(log* n*)，最坏情况下树呈现链状，为 O(n)*

##### 方法三：Morris 遍历

## 技巧题

### 389.找不同

#### 题目描述

给定两个字符串 s 和 t，它们只包含小写字母。

字符串 t 由字符串 s 随机重排，然后在随机位置添加一个字母。

请找出在 t 中被添加的字母。

 

示例 1：

输入：s = "abcd", t = "abcde"
输出："e"
解释：'e' 是那个被添加的字母。
示例 2：

输入：s = "", t = "y"
输出："y"
示例 3：

输入：s = "a", t = "aa"
输出："a"
示例 4：

输入：s = "ae", t = "aea"
输出："a"

#### 方法一

##### 思路：计数后相减

##### 代码

```java
class Solution {
    public char findTheDifference(String s, String t) {
        int res = 0;
        for ( int i = 0; i < s.length(); i++ ) 
            res ^= s.charAt(i);
        for ( int i = 0; i < t.length(); i++ ) 
            res ^= t.charAt(i);
        return (char)res;
    }   
}

class Solution {
    public char findTheDifference(String s, String t) {
        int[] counter = new int[26];
        for ( int i = 0; i < s.length(); i++ ) 
            counter[ s.charAt(i) - 'a']++;
         for ( int i = 0; i < t.length(); i++ ) {
             counter[ t.charAt(i) - 'a']--;
             if ( counter[t.charAt(i) - 'a'] < 0) 
             return t.charAt(i);
         }
        return ' ';   
    }
}
```

##### 复杂度

时间：O(N）

空间：O(1)

#### 方法二

##### 思路：ASCII码相减

##### 代码

```java
class Solution {
    public char findTheDifference(String s, String t) {
        int counter1 = 0, counter2 = 0, res = 0;
        for ( int i = 0; i < s.length(); i++ ) 
            counter1 += s.charAt(i);

        for ( int i = 0; i < t.length(); i++ ) 
            counter2 += t.charAt(i); 
        res = counter2 - counter1;
        return (char)res;
    }
}
```

##### 复杂度

时间：O(N)

空间：O(1)

#### 方法三

##### 思路：取异或(很重要的解法)

![image](https://tva1.sinaimg.cn/large/0085EwgIgy1gn10ufq6grj30nt06oaai.jpg)

既然两个字符串中只有一个字符是不一样的。通过异或的性质我们可以知道，偶数的字符最终都会被异或成0，那么奇数个数的字符一定会存在，而且他和0异或是它本身

##### 代码

```java
class Solution {
    public char findTheDifference(String s, String t) {
        int res = 0;
        for ( int i = 0; i < s.length(); i++ ) 
            res ^= s.charAt(i);
        
        for ( int i = 0; i < t.length(); i++ ) 
            res ^= t.charAt(i);
        return (char)res;
    }
}
```

##### 复杂度

时间：O(N)

空间：O(1)

## 双指针

### 881.救生艇

#### 题目描述

第 i 个人的体重为 people[i]，每艘船可以承载的最大重量为 limit。

每艘船最多可同时载两人，但条件是这些人的重量之和最多为 limit。

返回载到每一个人所需的最小船数。(保证每个人都能被船载)。

 

示例 1：

输入：people = [1,2], limit = 3
输出：1
解释：1 艘船载 (1, 2)
示例 2：

输入：people = [3,2,2,1], limit = 3
输出：3
解释：3 艘船分别载 (1, 2), (2) 和 (3)
示例 3：

输入：people = [3,5,3,4], limit = 5
输出：4
解释：4 艘船分别载 (3), (3), (4), (5)

提示：

1 <= people.length <= 50000
1 <= people[i] <= limit <= 30000



#### 思路:排序+双指针

先排序，后双指针

#### 代码

```java
class Solution {
    public int numRescueBoats(int[] people, int limit) {
        Arrays.sort(people);
        int left = 0, right = people.length -1;
        int res = 0;
        //双指针，指向已排序的数组的前和后。分配策略：最重的肯定要分配一艘船。如果最轻的也能一起配对就加上
        while ( left <= right ) {
            res++;
            if ( people[left] + people[right] <= limit ) left++;
            right--; 
        } 
        return res;
    }
}
```

#### 复杂度

时间：O(n log(n) )

空间：O(1)

### 141.环形链表

#### 题目描述

给定一个链表，判断链表中是否有环。

如果链表中有某个节点，可以通过连续跟踪 next 指针再次到达，则链表中存在环。 为了表示给定链表中的环，我们使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。注意：pos 不作为参数进行传递，仅仅是为了标识链表的实际情况。

如果链表中存在环，则返回 true 。 否则，返回 false 。





#### 思路:快慢指针



#### 代码

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
        if ( head == null ) return false;
        ListNode slow = head;
        ListNode fast = head; 
        //判断快指针即可，不用判断慢指针。如果有环快指针一定走不出去；如果无环那慢指针也是无法达到尾部就能得出结果了。
        while ( fast != null && fast.next != null ) { 
            fast = fast.next.next;
            slow = slow.next;
            if ( fast == slow ) return true;
        } 
        return false;
    }
}
```

#### 复杂度

时间：O(n）

空间：O(1)

## 二分法

### 74.搜索二维矩阵

#### 题目描述

编写一个高效的算法来判断 m x n 矩阵中，是否存在一个目标值。该矩阵具有如下特性：

每行中的整数从左到右按升序排列。
每行的第一个整数大于前一行的最后一个整数。



#### 思路:二分法

将矩阵看成一维数组即可

#### 代码

```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        //看作是一维数组，对下标进行响应的处理即可
        int left = 0;
        int right = matrix.length * matrix[0].length -1;
        while ( left <= right ) {
            int mid = left + (right - left)/2;
            int midElem = matrix[mid / matrix[0].length][mid % matrix[0].length];
            if ( midElem == target ) return true;
            else if ( midElem > target ) right = mid -1;
            else left = mid +1;
        }
        return false;
    }
}
```

#### 复杂度

时间：O( log(n) )

空间：O(1)

## 回溯

### 22.括号生成

#### 题目描述

数字 n 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 有效的 括号组合。

示例 1：

输入：n = 3
输出：["((()))","(()())","(())()","()(())","()()()"]
示例 2：

输入：n = 1
输出：["()"]


提示：

1 <= n <= 8



#### 思路:DFS+剪枝

使用深度优先搜索算法+回溯剪枝。

无外乎有这些情况：

1.剩余的左括号数大于剩余右括号数，那么就可以知道无论如何都是错误的，这种情况是不允许的，需要剪枝。

2.剩余的左括号大于0，添加左括号

3.剩余的右括号数大于0，添加右括号

4.左右剩余均是0，返回结果

#### 代码

```java
class Solution {
    public List<String> generateParenthesis(int n) {
        if ( n == 0 ) return null;
        List<String> res = new ArrayList<>();
        dfs("",n,n,res);
        return res;
    }

    public void dfs(String cur, int leftRest, int rightRest, List<String> res) {
        if ( leftRest == 0 && rightRest == 0 ) {
            res.add(cur);
            return;
        }

        if ( leftRest > rightRest ) return;//剪枝
        if ( leftRest > 0 ) dfs( cur+"(", leftRest - 1, rightRest, res);
        if ( rightRest > 0 ) dfs( cur+")", leftRest, rightRest - 1, res );
    }
}
```

#### 复杂度

时间：O(n)

空间：O(n)

## BFS

### 102.二叉树的层序遍历

#### 题目描述

给你一个二叉树，请你返回其按 层序遍历 得到的节点值。 （即逐层地，从左到右访问所有节点）。

 

示例：
二叉树：[3,9,20,null,null,15,7]

![image](https://tva2.sinaimg.cn/large/0085EwgIgy1gnii46wxw8j303w04ajr5.jpg)

返回其层序遍历结果：

[
  [3],
  [9,20],
  [15,7]

]



#### 思路:BFS+队列

使用队列实现。

首先获取每一层的长度，注意一定要选下来而不是用queue.size()来获取，因为队列长度是会变化的。



#### 代码

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
    public List<List<Integer>> levelOrder(TreeNode root) {
        if ( root == null ) return new ArrayList<List<Integer>>();
        List<List<Integer>> res = new ArrayList<>();
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);

        while ( queue.size() > 0 ) {
            int currentLen = queue.size();//获取该层的长度
            List<Integer> floor = new ArrayList<>();
            for ( int i = 0; i < currentLen; i++ ) {
                TreeNode cur = queue.poll();
                floor.add(cur.val);
                if ( cur.left != null ) queue.add(cur.left);
                if ( cur.right != null ) queue.add(cur.right);
            }
            res.add(floor);
        }
        return res;
    }
}
```

#### 复杂度

时间：O(n)

空间：O(n)

### 107.二叉树的层序遍历II

#### 题目描述

给定一个二叉树，返回其节点值自底向上的层序遍历。 （即按从叶子节点所在层到根节点所在的层，逐层从左向右遍历）

例如：
给定二叉树 [3,9,20,null,null,15,7]

![image](https://tva1.sinaimg.cn/large/0085EwgIgy1gniimrvmgvj30dy0b474a.jpg)

返回其自底向上的层序遍历为：

[
  [15,7],
  [9,20],
  [3]
]



#### 思路:BFS+队列

和上一步思路一模一样。

关键在于结果的处理。使用LinkedList的addFirst()函数可以完美解决



#### 代码

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
    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        if ( root == null ) return new LinkedList<>();
        LinkedList<List<Integer>> res = new LinkedList<>();//关键点，使用LinkedList，就可以使用addFirst函数了
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);

        while ( queue.size() > 0 ) {
            List<Integer> floor = new ArrayList<>();
            int currentLen = queue.size();
            for ( int i = 0; i < currentLen; i++ ) {
                TreeNode cur = queue.poll();
                floor.add(cur.val);
                if ( cur.left != null ) queue.add(cur.left);
                if ( cur.right != null) queue.add(cur.right);
            }
            res.addFirst(floor);
        }
        return res;
    }
}
```

#### 复杂度

时间：O(n)

空间：O(n)

## DFS

### 78.子集

#### 题目描述

给你一个整数数组 nums ，数组中的元素 互不相同 。返回该数组所有可能的子集（幂集）。

解集 不能 包含重复的子集。你可以按 任意顺序 返回解集。

示例 1：

输入：nums = [1,2,3]
输出：[[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
示例 2：

输入：nums = [0]
输出：[[],[0]]


提示：

1 <= nums.length <= 10
-10 <= nums[i] <= 10
nums 中的所有元素 互不相同

#### 方法一

##### 思路:二进制

将数组看成是N个二进制数，那么取值范围就是0 ~ (2^n)-1

依次遍历这个范围的每个数。在每次遍历中，需要对数组元素进行选择。将这个数和每一位取与，将结果添加到临时的数组中。

##### 代码

```java
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        List< List<Integer> > res = new ArrayList<>();
        List<Integer> current = new ArrayList<>();
        //将数组看成n位二进制数，0代表不使用，1代表使用
        for ( int counter = 0; counter < ( 1 << nums.length); counter++ ) {
            current.clear();
            for ( int i = 0; i < nums.length; i++ ) {
                if ( (counter & (1 << i)) != 0) current.add( nums[i] );
            }
            res.add( new ArrayList<Integer>(current));
        } 
        return res;
    }
}
```

##### 复杂度

时间：O(N x 2^N)

空间：O( N)

#### 方法二

##### 思路: DFS



##### 代码

```java

```

##### 复杂度

时间：O(N x 2^N)

空间：O( N)

### 938.二叉搜索树的范围和

#### 题目描述

给定二叉搜索树的根结点 `root`，返回值位于范围 *`[low, high]`* 之间的所有结点的值的和。

![image](https://tvax2.sinaimg.cn/large/0085EwgIgy1gnidwbgb1jj30f208dmxk.jpg)

```c
输入：root = [10,5,15,3,7,null,18], low = 7, high = 15
输出：32
```



#### 思路:DFS

#### 代码

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public int rangeSumBST(TreeNode root, int low, int high) {
        //深度优先搜索。遍历二叉搜索树，利用其特性进行筛选即可
        if ( root == null ) return 0;//递归退出条件
        if ( root.val < low ) return rangeSumBST(root.right, low, high);//当前节点比最小值还要小，则剪掉左边的子树，从右子树中寻找
        if ( root.val > high ) return rangeSumBST(root.left, low, high);//当前节点比最大值还要大，则剪掉右边的子树，从左子树中寻找
        return root.val + rangeSumBST(root.left,low,high) + rangeSumBST(root.right,low,high);//满足条件，在最大值和最小值之间，那么是无法确定的，需要对左右子树都有继续DFS
    }
}
```

#### 复杂度

时间：O(n)

空间：O(h)，h为树的高度

### 200.岛屿数量

#### 题目描述

给你一个由 '1'（陆地）和 '0'（水）组成的的二维网格，请你计算网格中岛屿的数量。

岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。

此外，你可以假设该网格的四条边均被水包围。



示例 1：

输入：grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
输出：1
示例 2：

输入：grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
输出：3


提示：

m == grid.length
n == grid[i].length
1 <= m, n <= 300
grid[i][j] 的值为 '0' 或 '1'

#### 思路:DFS

深度优先搜索陆地，对于每一个陆地，找到其上下左右的陆地，然后置为0，防止后续重复查找。

#### 代码

```java
class Solution {
    public int numIslands(char[][] grid) {
        int res = 0;
        for ( int i = 0; i < grid.length; i++ ) {
            for ( int j = 0; j < grid[0].length; j++ ) {
                if ( grid[i][j] == '1') {
                    dfs( grid, i ,j);
                    res++;
                }
            }
        }
        return res;
    }

    public void dfs(char[][] grid,int i, int j) {
        if ( i< 0 || j < 0 || i >= grid.length || j >= grid[0].length || grid[i][j] == '0') return;
        grid[i][j] = '0';//置为0，防止重复查找
        dfs(grid, i + 1, j);
        dfs(grid, i - 1, j);
        dfs(grid, i, j - 1);
        dfs(grid, i, j + 1);
    }
}
```

#### 复杂度

时间：O(MN)

空间：O(MN)

## 查找算法

### 二分查找

```java
class Solution {
    public int search(int[] nums, int target) {
        int left = 0, right = nums.length -1, mid = 0;
        while ( left <= right ) {
            mid = left + (right- left)/2;
            if ( nums[mid] == target ) return mid;
            else if ( nums[mid] < target ) left = mid + 1;
            else right = mid -1;
        }
        return -1;
    }
}
```

