   * [1. 两数之和](#1-两数之和)
   * [2. 两数相加](#2-两数相加)
   * [3. 无重复字符的最长字串](#3-无重复字符的最长子串)
   * [4. 寻找两个正序数组的中位数](#4-寻找两个正序数组的中位数)

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
# 3. 无重复字符的最长子串
<B>题目</B>   
给定一个字符串 s ，请你找出其中不含有重复字符的 最长子串 的长度。
示例1：

```text
输入: s = "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```
示例2：

```text
输入: s = "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
```
示例3：

```text
输入: s = "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
```
示例4：

```text
输入: s = ""
输出: 0
```
<B>分析：</B>  
我们以示例1为例，找字符串abcabcbb的最长字串，我们需要从每一个字符开始的，找不包含重复字符的最长子串，那么其中最长的那个字符串即为答案。
对于示例一中的字符串，我们列举出这些结果，其中括号中表示选中的字符以及最长的字符串：
 - 以(a)bcabcbb}(a)bcabcbb开始的最长字符串为(abc)abcbb}(abc)abcbb；
 - 以a(b)cabcbb}a(b)cabcbb开始的最长字符串为a(bca)bcbb}a(bca)bcbb；
 - 以b(c)abcbb}ab(c)abcbb开始的最长字符串为ab(cab)cbb}ab(cab)cbb；
 - 以abc(a)bcbb}abc(a)bcbb 开始的最长字符串为abc(abc)bb}abc(abc)bb；
 - 以abca(b)cbb}abca(b)cbb 开始的最长字符串为abca(bc)bb}abca(bc)bb；
 - 以abcab(c)bb}abcab(c)bb 开始的最长字符串为abcab(cb)b}abcab(cb)b；
 - 以abcabc(b)b}abcabc(b)b 开始的最长字符串为abcabc(b)b}abcabc(b)b；
 - 以abcabcb(b)}abcabcb(b) 开始的最长字符串为abcabcb(b)}abcabcb(b)。
观察下来，我们发现如果我们依次递增地枚举子串的起始位置，那么子串的结束位置也是递增的！
这里的原因在于，假设我们选择字符串中的第k个字符作为起始位置，并且得到了不包含重复字符的最长子串的结束位置为n 
那么当我们选择第k+1个字符作为起始位置时，首先从k+1到n的字符显然是不重复的，并且由于少了原本的第k个字符，
我们可以尝试继续增大n直到右侧出现了重复字符为止。我们可以使用滑动窗口来解决这个问题：
 - 定义两个指针，左指针left:代表枚举字串的初始位置，右指针right:代表不包含重复字符的最长子串的结束位置n
 - 在每一步的操作中将左指针向右移动一格，表示我们开始枚举下一个字符作为起始位置。移动结束后这个子串就对应以左指针开始的，
 - 不包含重复字符的最长字串。我们记录下这个子串的长度。
 - 在枚举结束后，我们找到的最长的子串的长度即为答案。
 - 在上面的流程中，我们还需要使用一种数据结构来判断 是否有重复的字符Java中用HashSet    
<B>Java代码1：时间复杂度：O(n);空间复杂度：O(∣Σ∣)）</B>  
```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
      Set<Character> chars=new HashSet<>();
      //右指针为-1，代表还没移动
      int right=-1,maxLength=0,n=s.length();
      for(int i=0;i<n;i++){
          if(i!=0){
           // 每次遍历下一个字符时,左指针向右移动一格，移除一个字符
           chars.remove(s.charAt(i-1));
          }
          while(right+1<n&&!chars.contains(s.charAt(right+1))){
              chars.add(s.charAt(right+1));
              right++;
          }
          maxLength=Math.max(maxLength,right-i+1);
      }
         return maxLength;
    }
}
```
# 4. 寻找两个正序数组的中位数
<B>题目</B>   
给定两个大小分别为 m 和 n 的正序（从小到大）数组 nums1 和 nums2。请你找出并返回这两个正序数组的 中位数 。
算法的时间复杂度应该为 O(log (m+n)) 。
示例1：
```text
输入：nums1 = [1,3], nums2 = [2]
输出：2.00000
解释：合并数组 = [1,2,3] ，中位数 2
```
示例2：
```text
输入：nums1 = [1,2], nums2 = [3,4]
输出：2.50000
解释：合并数组 = [1,2,3,4] ，中位数 (2 + 3) / 2 = 2.5
```
示例3：
```text
输入：nums1 = [0,0], nums2 = [0,0]
输出：0.00000
```
示例4：
```text
输入：nums1 = [], nums2 = [1]
输出：1.00000。
```
示例5：
```text
输入：nums1 = [2], nums2 = []
输出：2.00000。
```
提示：
```text
nums1.length == m
nums2.length == n
0 <= m <= 1000
0 <= n <= 1000
1 <= m + n <= 2000
-106 <= nums1[i], nums2[i] <= 106
```
<B>分析：</B>  
第一步：分析题目，给定的两个数组是有序的，不为空，同时要求以时间复杂度O(log(m+n))找出中位数。
第二步：不考虑时间复杂度的话，我们可以直接合并两个数组，找出中间数， 如果对时间复杂度的要求有log,
通常都需要用到二分查找，这道题也可以通过二分查找实现。
第三位：结合有序数组和中位数，本题相当于是让用二分法来找中位数。
中位数概念：
中位数（Median）又称中值，统计学中的专有名词，是按顺序排列的一组数据中居于中间位置的数，
代表一个样本、种群或概率分布中的一个数值，<B>其可将数值集合划分为相等的上下两部分</B>。
<B>Java代码：时间复杂度：O(log(min(m,n)));空间复杂度：O(1)</B>  
```java
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        // 保证 m <= n
        if (nums1.length > nums2.length) {
            return findMedianSortedArrays(nums2, nums1);
        }
        int m = nums1.length;
        int n = nums2.length;
        int left = 0, right = m;
        // median1：前一部分的最大值
        // median2：后一部分的最小值
        int median1 = 0, median2 = 0;
        while (left <= right) {
            // 前一部分包含 nums1[0 .. i-1] 和 nums2[0 .. j-1]
            // 后一部分包含 nums1[i .. m-1] 和 nums2[j .. n-1]
            int i = (left + right) / 2;
            int j = (m + n + 1) / 2 - i;
            // nums_im1, nums_i, nums_jm1, nums_j 分别表示 nums1[i-1], nums1[i], nums2[j-1], nums2[j]
            int nums_im1 = (i == 0 ? Integer.MIN_VALUE : nums1[i - 1]);
            int nums_i = (i == m ? Integer.MAX_VALUE : nums1[i]);
            int nums_jm1 = (j == 0 ? Integer.MIN_VALUE : nums2[j - 1]);
            int nums_j = (j == n ? Integer.MAX_VALUE : nums2[j]);
            if (nums_im1 <= nums_j) {
                median1 = Math.max(nums_im1, nums_jm1);
                median2 = Math.min(nums_i, nums_j);
                left = i + 1;
            } else {
                right = i - 1;
            }
        }
        return (m + n) % 2 == 0 ? (median1 + median2) / 2.0 : median1;
    }
}
```




