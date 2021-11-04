# 剑指offer算法题

[toc]

## 二维数组中的查找

> 在一个二维数组中（每个一维数组的长度相同），每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

```java
public class Solution {
    public boolean Find(int target, int [][] array) {
        if(array==null ||array.length==0 || array[0].length == 0){
            return false;
        }
        int m = array.length-1;
        int n = array[0].length-1;
        int i = 0;
        int j = n ;
        while(i<=m&&j>=0){
            if(array[i][j] == target){
                return true;
            }
            else if(array[i][j]>target){
                j--;
            }
            else if(array[i][j]<target){
                i++;
            }
        }
        
        return false;
    }
}
```

## 翻转链表

```java
import java.util.ArrayList;
public class Solution {
    
    private ListNode reverse(ListNode head){
        if(head == null){
            return null;
        }
        ListNode pre = null;
        while(head != null){
            ListNode temp = head.next;
            head.next = pre;
            pre = head;
            head = temp;
        }
        return pre;
    }
    
    public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
       ArrayList<Integer> result = new ArrayList<Integer>();
        if(listNode == null){
            return result;
        }
        ListNode newHead = reverse(listNode);
        while(newHead!=null){
            result.add(newHead.val);
            newHead = newHead.next;
        }
        return result;
    }
}
```

## 根据中序和前序重建二叉树

```java
import java.util.Arrays;
public class Solution {
    public TreeNode reConstructBinaryTree(int [] pre,int [] in) {
        if(pre.length == 0){
            return null;
        }
        int i = 0;
        while(in[i]!=pre[0]){
            i++;
        }
        TreeNode res = new TreeNode(pre[0]);
        res.left = reConstructBinaryTree(Arrays.copyOfRange(pre,1,i+1),Arrays.copyOfRange(in,0,i));
        res.right = reConstructBinaryTree(Arrays.copyOfRange(pre,i+1,pre.length),Arrays.copyOfRange(in,i+1,in.length));
        return res;
    }
}
```

## 两个栈实现一个队

```java
import java.util.Stack;

public class Solution {
    Stack<Integer> stack1 = new Stack<Integer>();
    Stack<Integer> stack2 = new Stack<Integer>();
    
    public void push(int node) {
        stack1.push(node);
    }
    
    public int pop() {
        if(stack2.size()>0){
            return stack2.pop();
        }else{
            while(stack1.size()>0){
                stack2.push(stack1.pop());
            }
            return stack2.pop();
        }
    }
}
```

## 旋转数组的最小数字

>把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。
>输入一个非递减排序的数组的一个旋转，输出旋转数组的最小元素。
>例如数组{3,4,5,1,2}为{1,2,3,4,5}的一个旋转，该数组的最小值为1。
>NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。

```java
链接：https://www.nowcoder.com/questionTerminal/9f3231a991af4f55b95579b44b7a01ba?answerType=1&f=discussion
来源：牛客网

int minNumberInRotateArray(vector<int> rotateArray) {
        if(rotateArray.empty())
            return 0;
 
        int low = 0;
        int high = rotateArray.size() - 1;
        int mid = 0;
 
        while(low < high){
            // 子数组是非递减的数组，10111
            if (rotateArray[low] < rotateArray[high])
                return rotateArray[low];
            mid = low + (high - low) / 2;
            if(rotateArray[mid] > rotateArray[low])
                low = mid + 1;
            else if(rotateArray[mid] < rotateArray[high])
                high = mid;
            else low++;
        }
        return rotateArray[low];
    }
```

## 斐波那契

```java
public class Solution {
    public int Fibonacci(int n) {
        int[] array = new int[40];
        array[0] = 0;
        array[1] = 1;
        if(n == 0){
            return 0;
        }
        if (n==1){
            return 1;
        }
        for(int i = 2;i<=n;i++){
            array[i] = array[i-2] + array[i-1];
        }
        return array[n];
    }
}
```

## 变态跳台阶

> 一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。

```java
public class Solution {
    public int JumpFloorII(int target) {
           if(target == 1){
               return 1;
           }
           if(target == 2){
               return 2;
           }
           return 2 * JumpFloorII(target -1);
    }
}
```

## 二进制中1的个数

```java
public class Solution {
    public int NumberOf1(int n) {
         int count = 0;
        while(n != 0){
            count++;
            n = n&(n-1);
        }
        return count;
    }
}
```

## 重排序数组，奇数在前，偶数在后

> 重要的是相对位置不能变

```java
链接：https://www.nowcoder.com/questionTerminal/beb5aa231adc45b2a5dcc5b62c93f593?answerType=1&f=discussion
来源：牛客网

public class Solution {
  public void reOrderArray(int[] array) {
      int len = array.length;
      if (len <= 1) {
          return;
      }
      int i = 0;
      while (i < len) {
          //如果i所指的元素是奇数，则继续前进
          if (array[i] % 2 == 1) {
              i++;
          } else {
              //当i遇到偶数停下时，j从i的后一位开始走
              int j = i + 1;
              //当j所指的元素也是偶数时，则j向后移动
              while (array[j] % 2 == 0) {
                  //当j移到队尾，则说明i到队尾全是偶数，已满足题目的奇偶分离要求
                  if (j == len - 1) {
                      return;
                  }
                  j++;
              }
              //此时j为奇数，i为偶数，用temp保存array[j]的值
              int temp = array[j];
              //把i到j-1的元素往后移一位
              while (j > i) {
                  array[j] = array[j - 1];
                  j--;
              }
              //把保存在temp中的原第j个元素的值赋给i，此时i就变成奇数了，并进入下个循环
              array[i] = temp;
          }
 
      }
  }
}
```

## 合并链表

```java
public class Solution {
    public ListNode Merge(ListNode list1,ListNode list2) {
        if(list1 == null){
            return list2;
        }
        if(list2 == null){
            return list1;
        }
        
        if(list1.val < list2.val){
            l ist1.next = this.Merge(list1.next,list2);
            return list1;
        }else{
            list2.next = this.Merge(list1,list2.next);
            return list2;
        }
    }
}
```

## 树的子结构

```java
public class Solution {
    // 子树判断
    public boolean HasSubtree(TreeNode root1,TreeNode root2) {
     
        if(root1 == null || root2 == null){
            return false;
        }
       
        if(helper(root1,root2) == true){
            return true;
        }
        return HasSubtree(root1.left,root2) || HasSubtree(root1.right,root2);
    }
    
    // 判断根节点相同的情况下，root1包含root2
    private boolean helper(TreeNode root1,TreeNode root2){
        if(root1 == null && root2 == null){
            return true;
        }
         if(root1 == null){
            return false;
        }
        if(root2 == null){
            return true;
        }
        
        if(root1.val != root2.val){
            return false;
        }
        
        return helper(root1.left,root2.left)&&helper(root1.right,root2.right);
    }
}
```

## 镜像树

```java
public class Solution {
    public void Mirror(TreeNode root) {
        if(root == null){
            return;
        }
        
        TreeNode temp = root.right;
        root.right = root.left;
        root.left = temp;
        Mirror(root.left);
        Mirror(root.right);
    }
}
```

## 顺时针打印矩阵

```java
import java.util.ArrayList;
public class Solution {
    public ArrayList<Integer> printMatrix(int [][] matrix) {
       int mode = 0;
       int up = 0;
       int down = matrix.length-1;
       int left = 0;
       int right = matrix[0].length-1;
       ArrayList<Integer> result = new ArrayList<Integer>();
       while(true){
           if(up > down ||  left>right){
               break;
           }
           // 从左到右
           if(mode%4 == 0){
               for(int i=left;i<=right;i++){
                   result.add(matrix[up][i]);
               }
               up++;
           }
           // 从上到下
           else if(mode%4==1){
               for(int i=up;i<=down;i++){
                   result.add(matrix[i][right]);
               }
               right--;
           }
          // 从右到左
           else if(mode%4==2){
               for(int i=right;i>=left;i--){
                   result.add(matrix[down][i]);
               }
               down--;
           }
           // 从下到上
           else if(mode%4 == 3){
               for(int i=down;i>=up;i--){
                   result.add(matrix[i][left]);
               }
               left++;
           }
           mode++;
       }
       return result;
    }
}
```

## 包含min函数的栈

```java
import java.util.Stack;

public class Solution {
    private Stack <Integer>stack;
    private Stack<Integer> minStack;
    private int curMin;
    {
        stack = new Stack();
        minStack = new Stack();
        curMin = Integer.MAX_VALUE;
    }
 
 
    public void push(int node) {
        stack.push(node);
        if(curMin>node){
            curMin = node;
 
        }
        minStack.push(curMin);
    }
 
    public void pop() {
        stack.pop();
        minStack.pop();
        curMin = minStack.peek();
 
    }
 
    public int top() {
        return stack.peek();
    }
 
    public int min() {
        return curMin;
    }
}
```

## 栈的压入、弹出序列

> 输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否可能为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如序列1,2,3,4,5是某栈的压入顺序，序列4,5,3,2,1是该压栈序列对应的一个弹出序列，但4,3,5,1,2就不可能是该压栈序列的弹出序列。（注意：这两个序列的长度是相等的）

```java
import java.util.ArrayList;
import java.util.Stack;
public class Solution {
    public boolean IsPopOrder(int [] pushA,int [] popA) {
      Stack<Integer> stack = new Stack<Integer>();
      int j = 0;
      for(int i = 0;i<popA.length;i++){
          stack.push(pushA[i]);
          while(stack.size()!=0 && stack.peek()==popA[j]){
              stack.pop();
              j++;
          }
      }
      return stack.size()==0;
    }
}
```

## 从上往下打印二叉树

> 从上往下打印出二叉树的每个节点，同层节点从左至右打印。

```java
import java.util.ArrayList;
/**
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;

    public TreeNode(int val) {
        this.val = val;

    }

}
*/
public class Solution {
    public ArrayList<Integer> PrintFromTopToBottom(TreeNode root) {
        ArrayList<Integer> result = new ArrayList<Integer>();
        ArrayList<TreeNode> queue = new ArrayList<TreeNode>();
        if(root == null){
            return result;
        }
        queue.add(root);
        while(queue.size()!=0){
            TreeNode curNode = queue.remove(0);
            result.add(curNode.val);
            if(curNode.left != null){
                queue.add(curNode.left);
            }
            
            if(curNode.right!= null){
                queue.add(curNode.right);
            }
        }
        return result;
    }
}
```

## 二叉搜索树的后序遍历序列

> 输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。如果是则输出Yes,否则输出No。假设输入的数组的任意两个数字都互不相同。

```java

import java.util.Arrays;
public class Solution {
    
    public boolean Veriff(int [] sequence){
         if(sequence == null || sequence.length == 0){
            return true;
        }
        if(sequence.length == 1){
            return true;
        }
       int i = 0;
       for(i=0;i<sequence.length-1;i++){
           if(sequence[i]>sequence[sequence.length-1]){
                break;
           }
       }
   

       for(int j = i;j<sequence.length-1;j++){
           if(sequence[j]<sequence[sequence.length-1]){
               return false;
           }
       } 
       return Veriff(Arrays.copyOfRange(sequence,0,i))&&Veriff(Arrays.copyOfRange(sequence,i,sequence.length-1));
       
    }
    public boolean VerifySquenceOfBST(int [] sequence) {
  
        if(sequence == null || sequence.length == 0){
            return false;
        }
       return Veriff(sequence);
    }
}
```

## 二叉树中和为某一值的路径

> 输入一颗二叉树的跟节点和一个整数，打印出二叉树中结点值的和为输入整数的所有路径。路径定义为从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。(注意: 在返回值的list中，数组长度大的数组靠前)

```java
import java.util.ArrayList;
/**
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;

    public TreeNode(int val) {
        this.val = val;

    }

}
*/
public class Solution {
  
    ArrayList<ArrayList<Integer>> allList = new ArrayList<ArrayList<Integer>>();

    ArrayList<Integer> list = new ArrayList<Integer>();

    public ArrayList<ArrayList<Integer>> FindPath(TreeNode root, int target) {
        if (root == null) {
            return allList;
        }

        list.add(root.val);
        target -= root.val;

        if (target == 0 && root.left == null && root.right == null) {
            allList.add(new ArrayList<Integer>(list));
            
        }

        FindPath(root.left, target);
        FindPath(root.right, target);

        list.remove(list.size() - 1);


        return allList;
    }

}
```

## 复杂链表的复制

> 输入一个复杂链表（每个节点中有节点值，以及两个指针，一个指向下一个节点，另一个特殊指针指向任意一个节点），返回结果为复制后复杂链表的head。（注意，输出结果中请不要返回参数中的节点引用，否则判题程序会直接返回空）

```java
/*
public class RandomListNode {
    int label;
    RandomListNode next = null;
    RandomListNode random = null;

    RandomListNode(int label) {
        this.label = label;
    }
}
*/
public class Solution {
    public RandomListNode Clone(RandomListNode pHead) {

        if(pHead == null){
            return null;
        }
        //1.new 插入到 old的后面
        RandomListNode temp = pHead;
        while (temp != null){
            RandomListNode pNewNode = new RandomListNode(temp.label);
            RandomListNode temp2 = temp.next;
            temp.next = pNewNode;
            pNewNode.next = temp2;
            temp = temp2;
        }
        //2.给random结点赋值
        temp = pHead;
        while (temp != null){
//            if (temp.next == null){
//                break;
//            }
//
            temp.next.random = temp.random == null?null:temp.random.next;
            temp = temp.next.next;
        }

        //3.拆分出new

     
        temp = pHead;
        RandomListNode pCloneHead = pHead.next;
        while (temp != null){
           RandomListNode pCloneHead2 = temp.next;
           temp.next = pCloneHead2.next;
           pCloneHead2.next = pCloneHead2.next == null?null:pCloneHead2.next.next;
           temp = temp.next;
        }
        return pCloneHead;
    }
}
```

## 二叉搜索树转双向链表



```java
/**
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;

    public TreeNode(int val) {
        this.val = val;

    }

}
*/
public class Solution {
      // 精髓就是这个pre结点，用来记录最后一个访问的结点，然后当前
      TreeNode pre = null;

    public TreeNode Convert(TreeNode pRootOfTree) {
        if(pRootOfTree == null){
            return null;
        }
        Convert(pRootOfTree.right);
        if(pre != null){
            pRootOfTree.right = pre;
            pre.left = pRootOfTree;
        }    
         pre = pRootOfTree;
      
        Convert(pRootOfTree.left);
        return pre;
    }
}
```

## 字符串的全排列

> 思想是：为每一个字符判断是否和后面的字符进行交换，得到所有的结果

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
public class Solution {
         public ArrayList<String> Permutation(String str) {
        ArrayList<String> res = new ArrayList<>();
        if(str.length()==0||str==null)return res;
        char[] arr = str.toCharArray();
//        Arrays.sort(arr);
         
        permutationHelper(res,str.toCharArray(),0);
        Collections.sort(res);
        return res;
    }


    private void permutationHelper(ArrayList<String> res,char[]arr,int start){


        if(start==arr.length){
           res.add(new String(arr));
            return;
        }

        for (int i = start; i <arr.length; i++) {
            if(i!=start && arr[i] == arr[start]){
                continue;
            }
            //交换
            exchange(arr, start, i);
            permutationHelper(res, arr, start + 1);
                //换回
            exchange(arr, start, i);
        }
    }

    private void exchange(char[] arr,int pos1,int pos2){
        char temp = arr[pos1];
        arr[pos1] = arr[pos2];
        arr[pos2] = temp;
    }
}
```

##  数组中出现次数超过一半的数字

>采用阵地攻守的思想：
>第一个数字作为第一个士兵，守阵地；count = 1；
>遇到相同元素，count++;
>遇到不相同元素，即为敌人，同归于尽,count--；当遇到count为0的情况，又以新的i值作为守阵地的士兵，继续下去，到最后还留在阵地上的士兵，有可能是主元素。
>再加一次循环，记录这个士兵的个数看是否大于数组一般即可。

```java
public class Solution {
    public int MoreThanHalfNum_Solution(int [] array) {
        int length=array.length;
        if(array==null||length<=0){
            return 0;
        }
        
        int result=array[0];
        int times=1;
        for(int i=1;i<length;i++){
            if(times==0){
                result=array[i];
                times=1;
            }else{
                if(array[i]==result){
                    times++;
                }else{
                    times--;
                }
            }
        }
        
        times=0;
        for(int i=0;i<length;i++){
            if(result==array[i]){
                times++;
            }
       }
            
       if(times*2<length){
           result=0;
       }
       return result;
    }
}
```

## 最小的K个数

```java
import java.util.ArrayList;
public class Solution {
 public ArrayList<Integer> GetLeastNumbers_Solution(int [] input, int k) {
        
         if(k>input.length || k == 0){
            return new ArrayList<Integer>();
        }
        MyMaxHeap myMaxHeap = new MyMaxHeap(k);
        for (int i = 0; i < input.length; i++) {
            myMaxHeap.add(input[i]);
        }
        ArrayList<Integer> res = new ArrayList<Integer>();
        
        for (int i = 0; i < k; i++) {
            res.add(myMaxHeap.arr[i]);    
        }
        
        return res;
    }

    private static class MyMaxHeap{
        private int[] arr;
        private int size;
        public MyMaxHeap(int k){
            arr = new int[k];
            size = 0;
        }

        public void add(int number){
            if(size == 0){
                arr[0] = number;
                size++;
            }

            else if(size == arr.length && number < arr[0]){
                arr[0] = number;
                shiftDown(0);
            }

            else if(size< arr.length){
                arr[size] = number;
                shiftUp(size);
                size++;
            }

        }

        public void shiftDown(int position1){
            int position = position1;
            int temp = arr[position];

            while(2*position+1<size){

                int max = 2*position + 1;


                if(max+1<size && arr[max+1] > arr[max]){
                    max = max+1;
                }


                if(arr[max] <= arr[position]){
                    break;
                }
                swap(max,position);
                position = max;
            }
        }

        public void shiftUp(int position){
            int temp = arr[position];
            while ((position-1)/2 >= 0 && arr[position] > arr[(position-1)/2]){
                swap(position,(position-1)/2);
                position = (position-1)/2;
            }

        }
        private void swap(int i,int j){
            int temp = arr[i];
            arr[i] = arr[j];
            arr[j] = temp;
        }
    }
}
```

## 连续子数组的最大和

```java
public class Solution {
    public int FindGreatestSumOfSubArray(int[] array) {
           int max = Integer.MIN_VALUE;
        
        int dp[]=new int[array.length];
        
        dp[0] = array[0];
        max= dp[0];
        for (int i = 1; i <array.length ; i++) {
            dp[i] = (dp[i-1]+array[i])>array[i]?(dp[i-1]+array[i]):array[i];
            max = dp[i]>max?dp[i]:max;
        }
        return max;
    }
}
```

## 整数中1出现的次数

> 重点：（1）11 算2 而不是算1，所以要把各个位是1的情况加起来
>
> ​           （2）考虑三种情况  数字为0，1，大于等于2

```java
public class Solution {
    public int NumberOf1Between1AndN_Solution(int n) {
    
        int count=0;
        int i=1;
        for(i=1;i<=n;i*=10)
        {
            //i表示当前分析的是哪一个数位
            int a = n/i;
            int b = n%i;
            count = count+(a+8)/10*i+ (a%10==1?1:0)*(b+1);
        }
        return count;
    
    }
}
```

## 把数组排成最小的数

```java
import java.util.ArrayList;

import java.util.List;
import java.util.stream.Collectors;
import java.util.stream.IntStream;
public class Solution {
    public String PrintMinNumber(int [] numbers) {
     List<Integer> ls= IntStream.of(numbers).boxed().collect(Collectors.toList());
        ls.sort((a,b)->(a+""+b).compareTo(b+""+a));
        
        String res="";
        for (int i = 0; i < ls.size(); i++) {
            res = res+ls.get(i);
        }
        return res;
    }
}
```

## 丑数

```java
public class Solution {
  public int GetUglyNumber_Solution(int index) {
        if(index == 0){
            return 0;
        }
        int []uglyArr = new int[index];
        uglyArr[0] = 1;
        int p2 = 0;
        int p3 = 0;
        int p5 = 0;
        for (int i = 1; i < index; i++) {
            int curUgly = myMin(uglyArr[p2]*2,uglyArr[p3]*3,uglyArr[p5]*5);
            uglyArr[i] = curUgly;
            if (uglyArr[p2]*2 == curUgly){
                p2++;
            }
            
           if(uglyArr[p3]*3 == curUgly){
                p3++;
            }
           if(uglyArr[p5]*5 == curUgly){
                p5++;
            }
            
        }
        return uglyArr[index-1];
    }

    private int myMin(int a,int b,int c){
        int min;
        min = a<b?a:b;
        min = c<min?c:min;
        return min;
    }
}
```

##  数组中的逆序对

> 在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组,求出这个数组中的逆序对的总数P。并将P对1000000007取模的结果输出。 即输出P%1000000007

```java
/*
*归并排序的思想，最后求得的逆序数进行取摸 % 1000000007
*/
public class Solution {
   public int InversePairs(int [] array) {
            if(array==null || array.length<=0){
                return 0;
            }
            int pairsNum=mergeSort(array,0,array.length-1);
            return pairsNum;
        }
         
        public int mergeSort(int[] array,int left,int right){
            if(left==right){
                return 0;  
            }
            int mid=(left+right)/2;
            int leftNum=mergeSort(array,left,mid);
            int rightNum=mergeSort(array,mid+1,right);
            return (Sort(array,left,mid,right)+leftNum+rightNum)%1000000007;
        }
         
        public int Sort(int[] array,int left,int middle,int right){
            int current1=middle;
            int current2=right;
            int current3=right-left;
            int temp[]=new int[right-left+1];
            int pairsnum=0;
            while(current1>=left && current2>=middle+1){
                if(array[current1]>array[current2]){
                    temp[current3--]=array[current1--];
                    pairsnum+=(current2-middle);     //这个地方是current2-middle！！！！
                    if(pairsnum>1000000007)//数值过大求余
                    {
                        pairsnum%=1000000007;
                    }
                }else{
                    temp[current3--]=array[current2--];
                }
            }
            while(current1>=left){
                temp[current3--]=array[current1--];
            }
            while(current2>=middle+1){
                temp[current3--]=array[current2--];
            }
            //将临时数组赋值给原数组
            int i=0;
            while(left<=right){
                array[left++]=temp[i++];
            }
            return pairsnum;
        }
}
```

## 两个链表公共结点

> 两个个指针分别遍历一个链表，当到尾部的话就换另一个链表遍历
>
> 巧妙之处在于  不会有死循环，时间复杂度最多为 o(m+n)

```java
class Solution {
public:
    ListNode* FindFirstCommonNode( ListNode *pHead1, ListNode *pHead2) {
        ListNode *p1 = pHead1;
        ListNode *p2 = pHead2;
        while(p1!=p2){
            p1 = (p1==NULL ? pHead2 : p1->next);
            p2 = (p2==NULL ? pHead1 : p2->next);
        }
        return p1;
    }
};
```

##  数字在排序数组中出现的次数

```java
public class Solution {
   public int GetNumberOfK(int [] array , int k) {
        
        if(array==null || array.length == 0){
            return 0;
        }
        int  first = getFirstK(array,k);
        if(first == -1){
            return 0;
        }
        int last = getLastK(array,k);
        return last-first + 1;
    }

    private int getFirstK(int[]array,int k){
        int left = 0;
        int right = array.length - 1;

        while ((right - left)>1){
            int mid = left + (right-left)/2;

            if (array[mid] > k){
                right = mid;
            }
            else if(array[mid] <k){
                left = mid;
            }
            else if(array[mid] == k){
                right = mid;
            }
        }
        if(array[left]==k){
            return left;
        }
        if(array[right] == k){
            return right;
        }
        return -1;
    }
    private int getLastK(int[]array,int k){
        int left = 0;
        int right = array.length - 1;

        while ((right - left)>1){
            int mid = left + (right-left)/2;

            if (array[mid] > k){
                right = mid;
            }
            else if(array[mid] <k){
                left = mid;
            }
            else if(array[mid] == k){
                left = mid;
            }
        }
        if(array[right]==k){
            return right;
        }
        if(array[left] == k){
            return left;
        }
        return -1;
    }
}
```

## 二叉树的深度

```java
public class Solution {
    public int TreeDepth(TreeNode root) {
        if(root == null){
            return 0;
        }
        return 1 + (TreeDepth(root.left)>TreeDepth(root.right)?TreeDepth(root.left):TreeDepth(root.right));
    }
}
```

## 平衡二叉树

```java
public class Solution {
    public boolean IsBalanced_Solution(TreeNode root) {
        if(root == null){
            return true;
        }
        int deepLeft = TreeDepth(root.left);
        int deepRight = TreeDepth(root.right);
        
        if(deepLeft - deepRight>1 || deepLeft - deepRight<-1){
            return false;
        }
        return IsBalanced_Solution(root.left)&&IsBalanced_Solution(root.right);
        
    }
    public int TreeDepth(TreeNode root) {
        if(root == null){
            return 0;
        }
     
        
        return 1 + (TreeDepth(root.left)>TreeDepth(root.right)?TreeDepth(root.left):TreeDepth(root.right));
    }
}
```

## 数组中只出现一次的数字

> 可以用位运算实现，如果将所有所有数字相异或，则最后的结果肯定是那两个只出现一次的数字异或
>  的结果，所以根据异或的结果1所在的最低位，把数字分成两半，每一半里都还有只出现一次的数据和成对出现的数据,这样继续对每一半相异或则可以分别求出两个只出现一次的数字。

```java
//num1,num2分别为长度为1的数组。传出参数
//将num1[0],num2[0]设置为返回结果
public class Solution {
    public void FindNumsAppearOnce(int [] array,int num1[] , int num2[]) {
        if(array.length < 2) return ;
        int myxor = 0;
        int flag = 1;
        for(int i = 0 ; i < array.length; ++ i )
            myxor ^= array[i];
        while((myxor & flag) == 0) flag <<= 1;
       // num1[0] = myxor;
        //num2[0] = myxor;
        for(int i = 0; i < array.length; ++ i ){
            if((flag & array[i]) == 0) num2[0]^= array[i];
            else num1[0]^= array[i];
        }     
    }
}
```

## 和为S的连续正数序列

> 滑动窗口法

```java
import java.util.ArrayList;

import java.util.ArrayList;
public class Solution {
    public ArrayList<ArrayList<Integer> > FindContinuousSequence(int sum) {
        //存放结果
        ArrayList<ArrayList<Integer> > result = new ArrayList<>();
        //两个起点，相当于动态窗口的两边，根据其窗口内的值的和来确定窗口的位置和大小
        int plow = 1,phigh = 2;
        while(phigh > plow){
            //由于是连续的，差为1的一个序列，那么求和公式是(a0+an)*n/2
            int cur = (phigh + plow) * (phigh - plow + 1) / 2;
            //相等，那么就将窗口范围的所有数添加进结果集
            if(cur == sum){
                ArrayList<Integer> list = new ArrayList<>();
                for(int i=plow;i<=phigh;i++){
                    list.add(i);
                }
                result.add(list);
                plow++;
            //如果当前窗口内的值之和小于sum，那么右边窗口右移一下
            }else if(cur < sum){
                phigh++;
            }else{
            //如果当前窗口内的值之和大于sum，那么左边窗口右移一下
                plow++;
            }
        }
        return result;
    }
}
```

## 和为S的两个数字

```java
import java.util.ArrayList;
public class Solution {
    public ArrayList<Integer> FindNumbersWithSum(int [] array,int sum) {
        ArrayList<Integer> list = new ArrayList<Integer>();
        if (array == null || array.length < 2) {
            return list;
        }
        int i=0,j=array.length-1;
        while(i<j){
            if(array[i]+array[j]==sum){
            list.add(array[i]);
            list.add(array[j]);
                return list;
           }else if(array[i]+array[j]>sum){
                j--;
            }else{
                i++;
            }
             
        }
        return list;
    }
}
```

## 扑克牌顺子

> LL今天心情特别好,因为他去买了一副扑克牌,发现里面居然有2个大王,2个小王(一副牌原本是54张^_^)...他随机从中抽出了5张牌,想测测自己的手气,看看能不能抽到顺子,如果抽到的话,他决定去买体育彩票,嘿嘿！！“红心A,黑桃3,小王,大王,方片5”,“Oh My God!”不是顺子.....LL不高兴了,他想了想,决定大\小 王可以看成任何数字,并且A看作1,J为11,Q为12,K为13。上面的5张牌就可以变成“1,2,3,4,5”(大小王分别看作2和4),“So Lucky!”。LL决定去买体育彩票啦。 现在,要求你使用这幅牌模拟上面的过程,然后告诉我们LL的运气如何， 如果牌能组成顺子就输出true，否则就输出false。为了方便起见,你可以认为大小王是0。

```java
// 没有相同的数字，最大值和最小值的差小于5就可以 ！！！

public boolean isContinuous(int [] numbers) {
int[]d = new int[14]; 
d[0] = -5; 
int len = numbers.length; 
int max = -1; 
int min = 14; 
for(int i =0;i<len;i++){
    d[numbers[i]]++; 
    f(numbers[i] == 0){
        continue; 
    }
    if(d[numbers[i]]>1){
        return false; 
    }
    if(numbers[i] >max){
        max = numbers[i]; 
    } if(numbers[i] <min){
        min = numbers[i]; 
    }
 
}
if(max -min<5){
    return true; 
}
    return false;
}
```

## 约瑟夫环问题

> f(n,m) 为有n个人的最后结果
>
> f(n-1,m)为有n-1个人的最后结果
>
> 如果让结果相同，<b>让f(n-1,m)的所有元素的序号加m就可以了</b>

```java
链接：https://www.nowcoder.com/questionTerminal/11b018d042444d4d9ca4914c7b84a968
来源：牛客网

public class Joseph {
    /*
     * 编号为(0~n-1)
     */
    public int getResult(int n, int m) {
        if (n < 0 || m < 0) {
            return -1;
        }
        int last = 0;
        for(int i=2;i<=n;++i){
            last = (last+m)%i;
        }
        // 因为实际编号为(1~n)
        return (last+1);
    }
}
```

## 求1+2+...+n

> 求1+2+3+...+n，要求不能使用乘除法、for、while、if、else、switch、case等关键字及条件判断语句（A?B:C）。

```java
public class Solution {
    public int Sum_Solution(int n) {
        if(n==1){
            return 1;
        }
        return n + Sum_Solution(n-1);
    }
}
```



## 不用加减乘除做加法

```java
public class Solution {
    public int Add(int num1,int num2) {
        int sum,carry;
        sum = num1 ^ num2;
        carry = (num1&num2)<<1;
        num1= sum;
        num2 = carry;
        while(carry!=0){
             sum = num1 ^ num2;
             carry = (num1&num2)<<1;
             num1= sum;
             num2 = carry;
        }
        return sum;
    }
}
```

## 字符串转整数

```java
public class Solution {
    public int StrToInt(String str) {
          if(str == null || str.length() == 0)return 0;
       int start;
       int tag ;
       if(str.charAt(0)=='+'){
           tag = 1;
           start = 1;
       }
       else if (str.charAt(0)=='-') {
        tag = 0;
        start =1;
    }else {
        tag = 1;
        start = 0;
    }
       long result = 0;
       for(int i=start;i<str.length();i++){
           char tmp = str.charAt(i);
           if(tmp >= '0'&& tmp<='9'){
               result = result*10 +(tmp-'0');
               if(tag == 1 && result>Integer.MAX_VALUE)
                   throw new RuntimeException("上溢出");
               if(tag == 0 && result<Integer.MIN_VALUE)
                   throw new RuntimeException("下溢出");
           }
           else {
            return 0;
        }
       }
        
        if(tag == 0)
            return (int) (-1*result);
        else {
            return (int) result;
        }
    }
}
```

```java
class Solution {
    public int myAtoi(String str) {
        long rev = 0;
        char[] charList = str.toCharArray();
        int length = str.length();
        int zf = 1;
        int i = 0;
        int pop = 0;
        for(;  i < length; i++){
            if(charList[i] == ' '){
                continue;
            }else{
                if(charList[i] == '-'){
                    i++;
                    zf = -1;
                    break;
                }
                if(charList[i] == '+'){
                    i++;
                    break;
                }
                if(charList[i] <'0' || charList[i] >'9'){
                    return 0;
                }else{
                    break;
                }
            }
        }
        if(i == length){
            return 0;
        }
      
        for(;i < length; i++){
            if(charList[i] <'0' || charList[i] >'9'){
                rev = rev * zf;
                if(zf==-1 && rev<Integer.MIN_VALUE){
                    return Integer.MIN_VALUE;
                }
                if(zf==1 && rev > Integer.MAX_VALUE){
                    return Integer.MAX_VALUE;
                }
                return (int)rev;
            }
       
            rev = rev * 10 + (long)(charList[i] - '0') ;
            if(zf==-1 && rev<Integer.MIN_VALUE){
            return Integer.MIN_VALUE;
        }
        if(zf==1 && rev > Integer.MAX_VALUE){
            return Integer.MAX_VALUE;
        }
        }
        rev = rev*zf;
        System.out.println("rev:"+rev);
        if(zf==-1 && rev<Integer.MIN_VALUE){
            return Integer.MIN_VALUE;
        }
        if(zf==1 && rev > Integer.MAX_VALUE){
            return Integer.MAX_VALUE;
        }
        return (int)rev;
    }
}
```



## 数组中重复的数字

> 在一个长度为n的数组里的所有数字都在0到n-1的范围内。 数组中某些数字是重复的，但不知道有几个数字是重复的。也不知道每个数字重复几次。请找出数组中任意一个重复的数字。 例如，如果输入长度为7的数组{2,3,1,0,2,5,3}，那么对应的输出是第一个重复的数字2。

```java
public class Solution {
    // Parameters:
    //    numbers:     an array of integers
    //    length:      the length of array numbers
    //    duplication: (Output) the duplicated number in the array number,length of duplication array is 1,so using duplication[0] = ? in implementation;
    //                  Here duplication like pointor in C/C++, duplication[0] equal *duplication in C/C++
    //    这里要特别注意~返回任意重复的一个，赋值duplication[0]
    // Return value:       true if the input is valid, and there are some duplications in the array number
    //                     otherwise false
    public boolean duplicate(int numbers[],int length,int [] duplication) {
        int temp;
        if(length<=1)
            return false;
        for(int i=0;i<length;i++) {
            while(numbers[i]!=i) {
                if(numbers[numbers[i]]!=numbers[i]) {
                    temp=numbers[numbers[i]];
                    
                    numbers[numbers[i]]=numbers[i];
                    numbers[i]=temp;
                }
                else {
                    duplication[0]=numbers[i];
                    return true;
                }    
            }               
        }
       return false;
     
    }
}
```

## 构建乘积数组

> 给定一个数组A[0,1,...,n-1],请构建一个数组B[0,1,...,n-1],其中B中的元素B[i]=A[0]*A[1]*...*A[i-1]*A[i+1]*...*A[n-1]。不能使用除法。

```java
//链接：https://www.nowcoder.com/questionTerminal/94a4d381a68b47b7a8bed86f2975db46?answerType=1&f=discussion

public class Solution {
    public int[] multiply(int[] A) {
        if (A == null || A.length <= 0) {
            return null;
        }
 
        int[] C = new int[A.length];
        C[0] = 1;
        for (int i = 1; i < C.length; i++) {
            C[i] = C[i - 1] * A[i - 1];
        }
 
        int[] D = new int[A.length];
        D[D.length - 1] = 1;
        for (int i = D.length - 2; i >= 0; i--) {
            D[i] = D[i + 1] * A[i + 1];
        }
 
        int[] result = new int[A.length];
        for (int i = 0; i < result.length; i++) {
            result[i] = C[i] * D[i];
        }
        return result;
    }
 
}
```

## 正则表达式匹配

> 请实现一个函数用来匹配包括'.'和'*'的正则表达式。模式中的字符'.'表示任意一个字符，而'*'表示它前面的字符可以出现任意次（包含0次）。 在本题中，匹配是指字符串的所有字符匹配整个模式。例如，字符串"aaa"与模式"a.a"和"ab*ac*a"匹配，但是与"aa.a"和"ab*a"均不匹配

```java
public class Solution {
    public boolean match(char[] str, char[] pattern) {
        if(str==null&&pattern == null){
            return true;
        }

        if(str != null && pattern == null){
            return false;
        }

        return match(str,0,pattern,0);
    }

    public boolean match(char[] str, int index1,char[] pattern,int index2) {

        if(index1 >= str.length && index2 >= pattern.length){
            return true;
        }

        if (index2 >= pattern.length){
            return false;
        }

        if((index2+1)<pattern.length&&pattern[index2+1] == '*' ){
            if( index1 < str.length &&(pattern[index2] == '.' || pattern[index2] == str[index1] )){
                  // 不用*  用一个* 用多个*
                return  match(str,index1,pattern,index2+2)||
                        match(str,index1+1,pattern,index2+2)||
                        match(str,index1+1,pattern,index2);
            }
            return match(str,index1,pattern,index2+2);

        }

        if(index1<str.length&&(str[index1] == pattern[index2]||pattern[index2] == '.')){
            return match(str,index1+1,pattern,index2+1);
        }

        return false;

    }
}
```

## 表示值的字符串

> 请实现一个函数用来判断字符串是否表示数值（包括整数和小数）。例如，字符串"+100","5e2","-123","3.1416"和"-1E-16"都表示数值。 但是"12e","1a3.14","1.2.3","+-5"和"12e+4.3"都不是。

```java
bool isNumeric(char* string)
{
    if(string == NULL || *string == '\0')
        return false;
    int index = 0;
    //处理第一个字符是否'*'或者'-'
    if(string[index] == '+' || string[index] == '-')
        ++index;
    //一个数只能有一个小数点
    bool douhao = true;
    //'e'或者'E'把一个数分成两部分，如果字符串中有'e'或者'E'，那么可能有第二部分
    bool secondPart = false;
    while(string[index] != '\0' )
    {
        char c = string[index];
        if(c == '.')
        {
            //只有一个逗号
            if(douhao)
            {
                ++index;
                douhao = false;
            }
            //有两个逗号则不是数字
            else
            {
                return false;
            }
        }
        //是数字，则进行下一位判断
        else if(isdigit(c))
        {
            ++index;
        }
        //是'e'或者'E',那么进行第二部分判断
        else if(c == 'e' || c == 'E')
        {
            ++index;
            secondPart = true;
            break;
        }
        //非逗号，非数字，非'e'或者'E'，则不是数字，返回false
        else
        {
            return false;
        }
    }
    //第二部分判断
    if(secondPart)
    {
        //处理第一个字符是否'*'或者'-'
        if(string[index] == '+' || string[index] == '-')
            ++index;
        //看是否还有下一位
        if(string[index] == '\0')
            return false;
        while(string[index] != '\0')
        {
            if(!isdigit(string[index]))
                return false;
            ++index;
        }
    }
    return true;
}
```

## 字符流中第一个不重复的字符

> 请实现一个函数用来找出字符流中第一个只出现一次的字符。例如，当从字符流中只读出前两个字符"go"时，第一个只出现一次的字符是"g"。当从该字符流中读出前六个字符“google"时，第一个只出现一次的字符是"l"。

```java
链接：https://www.nowcoder.com/questionTerminal/00de97733b8e4f97a3fb5c680ee10720?f=discussion
来源：牛客网

import java.util.LinkedHashMap;
import java.util.Map;
 
public class Solution {
    private Map<Character, Integer> map = new LinkedHashMap<>();
     
    //Insert one char from stringstream
    public void Insert(char ch) {
        if (map.containsKey(ch)) {
            map.put(ch, map.get(ch) + 1);
        } else {
            map.put(ch, 0);
        }
    }
     
    //return the first appearence once char in current stringstream
    public char FirstAppearingOnce() {
        for (Map.Entry<Character, Integer> set : map.entrySet()) {
            if (set.getValue() == 0) {
                return set.getKey();
            }
        }
        return '#';
    }
}
```

## 链表中环的入口

> 给一个链表，若其中包含环，请找出该链表的环的入口结点，否则，输出null。

```java
//链接：https://www.nowcoder.com/questionTerminal/253d2c59ec3e4bc68da16833f79a38e4?f=discussion
//来源：牛客网

public class 链表中环的入口结点 {
    //找到一快一满指针相遇处的节点，相遇的节点一定是在环中
    public static ListNode meetingNode(ListNode head) {
        if(head==null)
            return null;
         
        ListNode slow = head.next;
        if(slow==null)
            return null;
         
        ListNode fast = slow.next;
        while (slow != null && fast != null) {
            if(slow==fast){
                return fast;
            }
            slow=slow.next;
            fast=fast.next;
             
            if(fast!=slow){
                fast=fast.next;
            }
        }
        return null;
    }
    public ListNode EntryNodeOfLoop(ListNode pHead) {
        ListNode meetingNode=meetingNode(pHead);
        if(meetingNode==null)
            return null;
//      得到环中的节点个数
        int nodesInLoop=1;
        ListNode p1=meetingNode;
        while(p1.next!=meetingNode){
            p1=p1.next;
            ++nodesInLoop;
        }
//      移动p1
        p1=pHead;
        for(int i=0;i<nodesInLoop;i++){
            p1=p1.next;
        }
//      移动p1，p2
        ListNode p2=pHead;
        while(p1!=p2){
            p1=p1.next;
            p2=p2.next;
        }
        return p1;
    }
}
```

## 删除链表中重复的结点

> 在一个排序的链表中，存在重复的结点，请删除该链表中重复的结点，重复的结点不保留，返回链表头指针。 例如，链表1->2->3->3->4->4->5 处理后为 1->2->5

```java
//链接：https://www.nowcoder.com/questionTerminal/fc533c45b73a41b0b44ccba763f866ef?answerType=1&f=discussion
//来源：牛客网

/*
 public class ListNode {
    int val;
    ListNode next = null;

    ListNode(int val) {
        this.val = val;
    }
}
*/
//用一个dummy头结点，寻找不是重复元素的结点
public class Solution {
      public ListNode deleteDuplication(ListNode pHead)
    {
        ListNode dummyNode = new ListNode(0);
        dummyNode.next = pHead;
        ListNode tail = dummyNode;
        ListNode pCur = dummyNode.next;

        while(pCur != null){
            if(pCur.next != null && pCur.next.val == pCur.val ){
                int val = pCur.val;
                pCur = pCur.next;
                while(pCur != null &&pCur.val == val){
                    pCur = pCur.next;
                }
            }else{
                System.out.println(pCur.val);
                tail.next = pCur;
                tail = tail.next;
                pCur = pCur.next;
            }
        }
        tail.next = null;
        return dummyNode.next;
    }
}
}
```

## 二叉树的下一个结点

> 给定一个二叉树和其中的一个结点，请找出中序遍历顺序的下一个结点并且返回。注意，树中的结点不仅包含左右子结点，同时包含指向父结点的指针。

```java
public class Solution {
    public TreeLinkNode GetNext(TreeLinkNode pNode)
    {
      //1. 有右子树
      //2. 无右子树 迭代寻找该结点的父节点，直到该结点是父节点的左子结点
      if(pNode.right != null){
          TreeLinkNode node = pNode.right;
          while(node.left != null){
              node = node.left;
          }
          return node;

      }else{

        while(pNode.next!=null){
            TreeLinkNode proot=pNode.next;
            if(proot.left==pNode)
                return proot;
            pNode=pNode.next;
        }
        return null;
      }
        
    }
}
```

## 对称的二叉树

> 请实现一个函数，用来判断一颗二叉树是不是对称的。注意，如果一个二叉树同此二叉树的镜像是同样的，定义其为对称的。

```java
/*
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;

    public TreeNode(int val) {
        this.val = val;

    }

}
*/
public class Solution {
    // 判断两个二叉树是否对称
    public boolean jude(TreeNode node1, TreeNode node2) {
        if (node1 == null && node2 == null) {
            return true;
        } else if (node1 == null || node2 == null) {
            return false;
        }
        if (node1.val != node2.val) {
            return false;
        } else {
            return jude(node1.left, node2.right) && jude(node1.right, node2.left);
        }
    }
    public boolean isSymmetrical(TreeNode pRoot) {
        return pRoot==null || jude(pRoot.left, pRoot.right);
    }
}
```

## 按之字形打印二叉树

> 请实现一个函数按照之字形打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右至左的顺序打印，第三行按照从左到右的顺序打印，其他行以此类推。

## 把二叉树打印成多行

```java
import java.util.ArrayList;


/*
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;

    public TreeNode(int val) {
        this.val = val;

    }

}
*/
public class Solution {
    ArrayList<ArrayList<Integer> > Print(TreeNode pRoot) {
        ArrayList<ArrayList<Integer>> result = new ArrayList<ArrayList<Integer>>();
        if(pRoot == null ){
            return result;
        }
        ArrayList<TreeNode> queue = new ArrayList<TreeNode>();
        queue.add(pRoot);
        while(queue.size()>0){
            int size = queue.size();
            ArrayList<Integer> temp = new ArrayList<Integer>();
            for(int i = 0;i<size;i++){
                TreeNode node = queue.remove(0);
                temp.add(node.val);
                if(node.left != null){
                    queue.add(node.left);
                }
                if(node.right != null){
                    queue.add(node.right);
                }
            }
            result.add(temp);
        }
        return result;
    }
    
}
```

## 二叉搜索树的第K个结点

```java
TreeNode KthNode(TreeNode root, int k){
        if(root==null||k==0)
            return null;
        Stack<TreeNode> stack = new Stack<TreeNode>();
        int count = 0;
        TreeNode node = root;
        do{
            if(node!=null){
                stack.push(node);
                node = node.left;
            }else{
                node = stack.pop();
                count++;
                if(count==k)
                    return node;
                node = node.right;
            }
        }while(node!=null||!stack.isEmpty());
        return null;
    }
```

## 数据流中的中位数

> 如何得到一个数据流中的中位数？如果从数据流中读出奇数个数值，那么中位数就是所有数值排序之后位于中间的数值。如果从数据流中读出偶数个数值，那么中位数就是所有数值排序之后中间两个数的平均值。我们使用Insert()方法读取数据流，使用GetMedian()方法获取当前读取数据的中位数。

```java
链接：https://www.nowcoder.com/questionTerminal/9be0172896bd43948f8a32fb954e1be1?f=discussion
来源：牛客网

import java.util.PriorityQueue;
import java.util.Comparator;
public class Solution {
    //小顶堆
    private PriorityQueue<Integer> minHeap = new PriorityQueue<Integer>();
     
    //大顶堆
    private PriorityQueue<Integer> maxHeap = new PriorityQueue<Integer>(15, new Comparator<Integer>() {
        @Override
        public int compare(Integer o1, Integer o2) {
            return o2 - o1;
        }
    });
     
    //记录偶数个还是奇数个
    int count = 0;
    //每次插入小顶堆的是当前大顶堆中最大的数
    //每次插入大顶堆的是当前小顶堆中最小的数
    //这样保证小顶堆中的数永远大于等于大顶堆中的数
    //中位数就可以方便地从两者的根结点中获取了
    public void Insert(Integer num) {
        //个数为偶数的话，则先插入到大顶堆，然后将大顶堆中最大的数插入小顶堆中
        if(count % 2 == 0){
            maxHeap.offer(num);
            int max = maxHeap.poll();
            minHeap.offer(max);
        }else{
            //个数为奇数的话，则先插入到小顶堆，然后将小顶堆中最小的数插入大顶堆中
            minHeap.offer(num);
            int min = minHeap.poll();
            maxHeap.offer(min);
        }
        count++;
    }
    public Double GetMedian() {
        //当前为偶数个，则取小顶堆和大顶堆的堆顶元素求平均
        if(count % 2 == 0){
            return new Double(minHeap.peek() + maxHeap.peek())/2;
        }else{
            //当前为奇数个，则直接从小顶堆中取元素即可
            return new Double(minHeap.peek());
        }
    }
}
```

## 滑动窗口的最大值

> 给定一个数组和滑动窗口的大小，找出所有滑动窗口里数值的最大值。例如，如果输入数组{2,3,4,2,6,2,5,1}及滑动窗口的大小3，那么一共存在6个滑动窗口，他们的最大值分别为{4,4,6,6,6,5}； 针对数组{2,3,4,2,6,2,5,1}的滑动窗口有以下6个： {[2,3,4],2,6,2,5,1}， {2,[3,4,2],6,2,5,1}， {2,3,[4,2,6],2,5,1}， {2,3,4,[2,6,2],5,1}， {2,3,4,2,[6,2,5],1}， {2,3,4,2,6,[2,5,1]}。

```java
链接：https://www.nowcoder.com/questionTerminal/1624bc35a45c42c0bc17d17fa0cba788?f=discussion
来源：牛客网
//思路：把可能成为最大值数字的下标放入双端队列deque，从而减少遍历次数。首先，所有在没有查看后面数字的情况下，任何一个节点都有可能成为某个状态的滑动窗口的最大值，因此，数组中任何一个元素的下标都会入队。关键在于出队，以下两种情况下，该下标对应的数字不会是窗口的最大值需要出队：(1)该下标已经在窗口之外，比如窗口长度为3，下标5入队，那么最大值只可能在下标3,4,5中出现，队列中如果有下标2则需要出队；(2)后一个元素大于前面的元素，那么前面的元素出对，比如目前队列中有下标3、4，data[3] = 50,data[4]=40，下标5入队，但data[5] = 70，则队列中的3，4都需要出队。

作者：ryderchan
链接：https://www.jianshu.com/p/95e4e819761b
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
import java.util.*;
public class Solution {
    public ArrayList<Integer> maxInWindows(int [] num, int size) {
        if (num == null || num.length == 0 || size <= 0 || num.length < size) {
            return new ArrayList<Integer>();
        }
        ArrayList<Integer> result = new ArrayList<>();
        //双端队列，用来记录每个窗口的最大值下标
        LinkedList<Integer> qmax = new LinkedList<>();
        int index = 0;
        for (int i = 0; i < num.length; i++) {
            while (!qmax.isEmpty() && num[qmax.peekLast()] < num[i]) {
                qmax.pollLast();
            }
            qmax.addLast(i);
            //判断队首元素是否过期  过期元素最多有1个，这里挺重要！！！
            if (qmax.peekFirst() == i - size) {
                qmax.pollFirst();
            }
            //向result列表中加入元素
            if (i >= size - 1) {
                result.add(num[qmax.peekFirst()]);
            }
        }
        return result;
    }
}
```

## 矩阵中的路径

> 请设计一个函数，用来判断在一个矩阵中是否存在一条包含某字符串所有字符的路径。路径可以从矩阵中的任意一个格子开始，每一步可以在矩阵中向左，向右，向上，向下移动一个格子。如果一条路径经过了矩阵中的某一个格子，则该路径不能再进入该格子。 例如 a b c e s f c s a d e e 矩阵中包含一条字符串"bcced"的路径，但是矩阵中不包含"abcb"路径，因为字符串的第一个字符b占据了矩阵中的第一行第二个格子之后，路径不能再次进入该格子。

```java
链接：https://www.nowcoder.com/questionTerminal/c61c6999eecb4b8f88a98f66b273a3cc?f=discussion
来源：牛客网

public class Solution {
    public boolean hasPath(char[] matrix, int rows, int cols, char[] str) {
        int flag[] = new int[matrix.length];
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (helper(matrix, rows, cols, i, j, str, 0, flag))
                    return true;
            }
        }
        return false;
    }
 
    private boolean helper(char[] matrix, int rows, int cols, int i, int j, char[] str, int k, int[] flag) {
        int index = i * cols + j;
        if (i < 0 || i >= rows || j < 0 || j >= cols || matrix[index] != str[k] || flag[index] == 1)
            return false;
        if(k == str.length - 1) return true;
        flag[index] = 1;
        if (helper(matrix, rows, cols, i - 1, j, str, k + 1, flag)
                || helper(matrix, rows, cols, i + 1, j, str, k + 1, flag)
                || helper(matrix, rows, cols, i, j - 1, str, k + 1, flag)
                || helper(matrix, rows, cols, i, j + 1, str, k + 1, flag)) {
            return true;
        }
        flag[index] = 0;
        return false;
    }
}

```

## 机器人的运动范围

> 地上有一个m行和n列的方格。一个机器人从坐标0,0的格子开始移动，每一次只能向左，右，上，下四个方向移动一格，但是不能进入行坐标和列坐标的数位之和大于k的格子。 例如，当k为18时，机器人能够进入方格（35,37），因为3+5+3+7 = 18。但是，它不能进入方格（35,38），因为3+5+3+8 = 19。请问该机器人能够达到多少个格子？

```java
//链接：https://www.nowcoder.com/questionTerminal/6e5207314b5241fb83f2329e89fdecc8?f=discussion
来源：牛客网

public class Solution {
 
    public int movingCount(int threshold, int rows, int cols) {
        int flag[][] = new int[rows][cols]; //记录是否已经走过
        return helper(0, 0, rows, cols, flag, threshold);
    }
 
    private int helper(int i, int j, int rows, int cols, int[][] flag, int threshold) {
        if (i < 0 || i >= rows || j < 0 || j >= cols || numSum(i) + numSum(j)  > threshold || flag[i][j] == 1) return 0;    
        flag[i][j] = 1;
        return helper(i - 1, j, rows, cols, flag, threshold)
            + helper(i + 1, j, rows, cols, flag, threshold)
            + helper(i, j - 1, rows, cols, flag, threshold)
            + helper(i, j + 1, rows, cols, flag, threshold)
            + 1;
    }
 
    private int numSum(int i) {
        int sum = 0;
        do{
            sum += i%10;
        }while((i = i/10) > 0);
        return sum;
    }
}
```



## 减绳子  数学问题

> 给你一根长度为n的绳子，请把绳子剪成整数长的m段（m、n都是整数，n>1并且m>1），每段绳子的长度记为k[0],k[1],...,k[m]。请问k[0]xk[1]x...xk[m]可能的最大乘积是多少？例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。

```java

public class Solution {
    public int cutRope(int target) {
        //数学问题
        if(target <= 2){
            return 1;
        }
        if(target == 3){
            return 2;
        }
        int  mod = target % 3;
        if(mod == 0){
            return (int)Math.pow(3,target/3);
        }else if( mod == 1){
            return (int)(4 * Math.pow(3,target/3 -1));
        }else{
            return (int)(2 * Math.pow(3,target/3));
        }
    }
}
```


##  有向图判断有环

> 有向图判断有环用 拓扑排序， 一个数组记录入度，一个hashset数组 记录有几个边，把入度是0的边加入到队列里
>
> 无向图判断有环用 并查集

```java
import java.util.HashSet;
import java.util.LinkedList;
import java.util.Queue;

public class Solution {

    public boolean canFinish(int numCourses, int[][] prerequisites) {
        if (numCourses <= 0) {
            return false;
        }

        // 特判
        int pLen = prerequisites.length;
        if (pLen == 0) {
            return true;
        }

        int[] inDegree = new int[numCourses];
        HashSet<Integer>[] adj = new HashSet[numCourses];
        for (int i = 0; i < numCourses; i++) {
            adj[i] = new HashSet<>();
        }

        for (int[] p : prerequisites) {
            inDegree[p[0]]++;
            adj[p[1]].add(p[0]);
        }

        Queue<Integer> queue = new LinkedList<>();

        // 首先加入入度为 0 的结点
        for (int i = 0; i < numCourses; i++) {
            if (inDegree[i] == 0) {
                queue.add(i);
            }
        }

        // 记录已经出队的课程数量
        int cnt = 0;
        while (!queue.isEmpty()) {
            Integer top = queue.poll();
            cnt += 1;
            // 遍历当前出队结点的所有后继结点
            for (int successor : adj[top]) {
                inDegree[successor]--;
                if (inDegree[successor] == 0) {
                    queue.add(successor);
                }
            }
        }
        return cnt == numCourses;
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/course-schedule/solution/tuo-bu-pai-xu-by-liweiwei1419/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## [406. 根据身高重建队列](https://leetcode-cn.com/problems/queue-reconstruction-by-height/)

```java
class Solution {
   public int[][] reconstructQueue(int[][] people) {
        if (0 == people.length || 0 == people[0].length)
            return new int[0][0];
         //按照身高降序 K升序排序 
        Arrays.sort(people, new Comparator<int[]>() {
            @Override   
            public int compare(int[] o1, int[] o2) {
                return o1[0] == o2[0] ? o1[1] - o2[1] : o2[0] - o1[0];
            }
        });
        List<int[]> list = new ArrayList<>();
        //K值定义为 排在h前面且身高大于或等于h的人数 
        //因为从身高降序开始插入，此时所有人身高都大于等于h
        //因此K值即为需要插入的位置
        for (int[] i : people) {
            list.add(i[1], i);
        }
        return list.toArray(new int[list.size()][]);




       
```

## k个一组翻转链表

```java
class Solution {
  public ListNode reverseKGroup(ListNode head, int k) {
    ListNode dummy = new ListNode(0);
    dummy.next = head;

    ListNode pre = dummy;
    ListNode end = dummy;

    while (end.next != null) {
        for (int i = 0; i < k && end != null; i++) end = end.next;
        if (end == null) break;
        ListNode start = pre.next;
        ListNode next = end.next;
        end.next = null;
        pre.next = reverse(start);
        start.next = next;
        pre = start;

        end = pre;
    }
    return dummy.next;
}

private ListNode reverse(ListNode head) {
    ListNode pre = null;
    ListNode curr = head;
    while (curr != null) {
        ListNode next = curr.next;
        curr.next = pre;
        pre = curr;
        curr = next;
    }
    return pre;
}
 
}
```



## LRU 缓存机制

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
        
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

## 链表排序 快排

```java
public ListNode sortList(ListNode head) {
    //采用快速排序
   quickSort(head, null);
   return head;
}
public static void quickSort(ListNode head, ListNode end) {
    if (head != end) {
        ListNode node = partion(head, end);
        quickSort(head, node);
        quickSort(node.next, end);
    }
}

public static ListNode partion(ListNode head, ListNode end) {
    ListNode p1 = head, p2 = head.next;

    //走到末尾才停
    while (p2 != end) {

        //大于key值时，p1向前走一步，交换p1与p2的值
        if (p2.val < head.val) {
            p1 = p1.next;

            int temp = p1.val;
            p1.val = p2.val;
            p2.val = temp;
        }
        p2 = p2.next;
    }

    //当有序时，不交换p1和key值
    if (p1 != head) {
        int temp = p1.val;
        p1.val = head.val;
        head.val = temp;
    }
    return p1;
}
```

## 单链表归并

```sql
public ListNode sortList(ListNode head) {
    //采用归并排序
    if (head == null || head.next == null) {
        return head;
    }
    //获取中间结点
    ListNode mid = getMid(head);
    ListNode right = mid.next;
    mid.next = null;
    //合并
    return mergeSort(sortList(head), sortList(right));
}

/**
 * 获取链表的中间结点,偶数时取中间第一个
 *
 * @param head
 * @return
 */
private ListNode getMid(ListNode head) {
    if (head == null || head.next == null) {
        return head;
    }
    //快慢指针
    ListNode slow = head, quick = head;
    //快2步，慢一步
    while (quick.next != null && quick.next.next != null) {
        slow = slow.next;
        quick = quick.next.next;
    }
    return slow;
}

/**
 *
 * 归并两个有序的链表
 *
 * @param head1
 * @param head2
 * @return
 */
private ListNode mergeSort(ListNode head1, ListNode head2) {
    ListNode p1 = head1, p2 = head2, head;
   //得到头节点的指向
    if (head1.val < head2.val) {
        head = head1;
        p1 = p1.next;
    } else {
        head = head2;
        p2 = p2.next;
    }

    ListNode p = head;
    //比较链表中的值
    while (p1 != null && p2 != null) {

        if (p1.val <= p2.val) {
            p.next = p1;
            p1 = p1.next;
            p = p.next;
        } else {
            p.next = p2;
            p2 = p2.next;
            p = p.next;
        }
    }
    //第二条链表空了
    if (p1 != null) {
        p.next = p1;
    }
    //第一条链表空了
    if (p2 != null) {
        p.next = p2;
    }
    return head;
}

```



## [leetcode 84 柱状图中最大的矩形](https://leetcode-cn.com/problems/largest-rectangle-in-histogram/)

> 记录一下单调递增栈，如果当前遍历的节点比栈顶节点小，那么就找到了以当前栈顶为 左端点和高，并且找到的右端点为 当前遍历的i -1

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        // 这里为了代码简便，在柱体数组的头和尾加了两个高度为 0 的柱体。
        int[] tmp = new int[heights.length + 2];
        System.arraycopy(heights, 0, tmp, 1, heights.length); 
        
        Deque<Integer> stack = new ArrayDeque<>();
        int area = 0;
        for (int i = 0; i < tmp.length; i++) {
            // 对栈中柱体来说，栈中的下一个柱体就是其「左边第一个小于自身的柱体」；
            // 若当前柱体 i 的高度小于栈顶柱体的高度，说明 i 是栈顶柱体的「右边第一个小于栈顶柱体的柱体」。
            // 因此以栈顶柱体为高的矩形的左右宽度边界就确定了，可以计算面积🌶️ ～
            while (!stack.isEmpty() && tmp[i] < tmp[stack.peek()]) {
                int h = tmp[stack.pop()];
                area = Math.max(area, (i - stack.peek() - 1) * h);   
            }
            stack.push(i);
        }

        return area;
    }
}
```

## [718. 最长重复子数组](https://leetcode-cn.com/problems/maximum-length-of-repeated-subarray/)

```

```

