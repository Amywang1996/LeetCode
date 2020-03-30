   * [1. 两数之和](#1-两数之和)
   * [2. 两数相加](#2-两数相加)

# 1. 两数之和

<B>题目</B>   
给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

```text
给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```

<B>分析：</B>  
第一步：目标是从nums中找到两个相加后等于target的两个数的下标,题目中不能重复利用数组中相同的元素，我这样理解当target为8、nums=[4,4]时，nums[0]+nums[1]是不同的元素，num[0]+num[0]是相同的元素。  
第二步：首先想到的是双重for循环遍历数组中所有的两数相加等于target的情况，当然，必须排除两个下标相同的元素相加等于target的情况。这样就确定了内循环的起点下标必须是外循环下标加1。  
第三步：通过第二步，我们得到的算法时间复杂度为O(n^2)。我们继续分析有没有时间复杂度更低的算法，我们现在是通过值找下标，那么可以遍历nums，把nums中的值作为HashMap的key,下标作为value,然后遍历nums数组然后用target减去nums[下标]得到另一个数，通过这个值找到map的value即为另一个数的下标，时间复杂度为O(n)  
<B>Java代码1：双层for循环（时间复杂度：O(n^2);空间复杂度：O(1)）</B>  

```java
class Solution {
 public int[] twoSum(int[] nums, int target) {
   for(int i=0;i<nums.length;i++){
      for(int j=i+1;j<nums.length;j++){
            if(target==nums[i]+nums[j]){
            return new int[]{i,j};
            }
      }
   }
      throw new IllegalArgumentException("No two sum solution");      
 }
}
```

<B>Java代码2：二遍哈希表（时间复杂度：O(n);空间复杂度：O(n)）</B>  

```java
class Solution {
   public int[] twoSum(int[] nums, int target) {
      Map<Integer,Integer> map = new HashMap<Integer,Integer>();
      for(int i=0;i<nums.length;i++){
         map.put(nums[i],i);
      }
      for(int i=0;i<nums.length;i++){
         int anotherNum=target-nums[i];
         //map.get(anotherNum)!=i,保证不利用数组中相同的元素
         if(map.containsKey(anotherNum)&&map.get(anotherNum)!=i){
               return new int[]{i,map.get(anotherNum)};
         }
      }
         throw new IllegalArgumentException("No two sum solution");      
   }
}
```

<B>Java代码3：一遍哈希表（时间复杂度：O(n);空间复杂度：O(n)）</B>  

```java
class Solution {
   public int[] twoSum(int[] nums, int target) {
      Map<Integer,Integer> map = new HashMap<Integer,Integer>();
      for(int i=0;i<nums.length;i++){
          int complement = target - nums[i];
            if (map.containsKey(complement)) {
                return new int[] { map.get(complement), i };
            }
         map.put(nums[i],i);
      }
      throw new IllegalArgumentException("No two sum solution");      
   }
}
```

# 2. 两数相加

<B>题目</B>   
给出两个 非空 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 逆序 的方式存储的，并且它们的每个节点只能存储 一位 数字。

如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。

您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

示例：

```text
输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807
```

<B>分析：</B>  
第一步：从题目我们知道两个数分别逆序存放在两个链表中，我们所要求的是两个数的和所存到的新的链表。  
第二步：由于逆序存放，两个链表的头结点存放的都是个位，往后依次类推。  
第三步：遍历两个链表，并把每个节点的值相加，由于可能存在进位问题，我们需要定义个中间变量来存储这个进位，由于极限情况下9+9+1(1为进位)，因此这个中间变量最大为1。  
第四步：由于从头结点开始计算，可以定义一个哑结点来简化对头结点是否为空的特殊处理(哑结点是链表中的一个概念，大致意思就是在一个链表的前边添加一个节点，存放位置是数据结点之前，头结点之后。好处就是加入哑结点之后就可以使所有数据结点都有前驱结点，这样就会方便执行链表的一些操作。哑节点的使用可以对代码起到简化作用（省略当函数的入口参数为空时的判断）默认是nul)。  
第五步：最后对中间变量进行判断，如果等于1，则需要新创建一个值为1的节点，把当前节点的尾指针指向这个新的节点。  
<B>Java代码：假设m和n分别表示l1和l2的长度，则此算法最多重复max(m,n),故时间复杂度为：O(max(m,n)),空间复杂度为：O(max(m,n)),新列表长度最多为max(m,n)+1</B>  

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
        ListNode dummyHead = new ListNode(0);
        ListNode p=l1,q=l2,curr=dummyHead;
        int carry=0;
        while(p!=null||q!=null){
            int x=(p!=null)?p.val:0;
            int y=(q!=null)?q.val:0;
            int sum = carry+x+y;
            carry = sum/10;
            curr.next = new ListNode(sum%10);
            curr=curr.next;
            if(p!=null)p=p.next;
            if(q!=null)q=q.next;
        }
        if(carry>0){
            curr.next=new ListNode(carry);
        }
        return dummyHead.next;
    }
  
}
```
