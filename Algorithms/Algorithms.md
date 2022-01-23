   * [1. 两数之和](#1-两数之和)
   * [2. 两数相加](#2-两数相加)
   * [3. 无重复字符的最长字串](#3-无重复字符的最长子串)
   * [4. 寻找两个正序数组的中位数](#4-寻找两个正序数组的中位数)
   * [5. 最长回文子串](#5-最长回文子串)
   * [6. Z字形变换](#6-Z字形变换)
   * [7. 整数反转](#7-整数反转)
   * [8. 字符串转换整数](#8-字符串转换整数 (atoi))
   
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
第一步：分析题目，给定的两个数组是有序的，不为空，同时要求以时间复杂度O(log(m+n))找出中位数。<br/>
第二步：不考虑时间复杂度的话，我们可以直接合并两个数组，找出中间数， 如果对时间复杂度的要求有log,
通常都需要用到二分查找，这道题也可以通过二分查找实现。<br/>
第三位：结合有序数组和中位数，本题相当于是让用二分法来找中位数。<br/>
中位数概念：
中位数（Median）又称中值，统计学中的专有名词，是按顺序排列的一组数据中居于中间位置的数，
代表一个样本、种群或概率分布中的一个数值，<B>其可将数值集合划分为相等的上下两部分。<br/>
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
# 5. 最长回文子串
<B>题目</B>   
给你一个字符串 s，找到 s 中最长的回文子串。
示例1：
```text
输入：s = "babad"
输出："bab"
解释："aba" 同样是符合题意的答案。
```
示例2：
```text
输入：s = "cbbd"
输出："bb"
```
示例3：
```text
输入：s = "a"
输出："a"
```
示例4：
```text
输入：s = "ac"
输出："a"
```
提示：
- 1 <= s.length <= 1000
- s 仅由数字和英文字母（大写和/或小写）组成<br/>
<B>分析：</B> <br/>
第一步：分析题目，给定字符串，要求出最长回文字串(字串(substring):原始字符串的一个连续子集;子序列(subsequence):原始字符串的一个子集)，回文字串就代表该子串正反一致。<br/>
第二步：首先想到的是暴力解法，就是求出所有的回文子串，然后选出最长的字串。时间复杂度O(n^3)过高。<br/>
第三步：通过动态规划算法来解决此问题，时间复杂度只有O(n^2)。动态规划算法的思想：<br/>
如果一个字串是回文串，并且长度大于2，那么它首尾的两个字母去除后，它仍然是个回文串。
也就是说对于子串s,只有s[i+1:j-1]是回文串，并且s的第i和j个字母相同时，s[i:j]才会是回文串。<br/>
<B>Java代码1：时间复杂度：O(n^3);空间复杂度：O(1)(只用到常数个临时变量)</B>  
```java
class Solution {
    public String longestPalindrome(String s) {
      int len=s.length();
      if(len<2){
          return s;
      }
      //默认最大长度1
      int begin=0,maxLen=1;
      char[] charArray=s.toCharArray();
      //枚举所有长度严格大于1的子串charArray[i...j]，最后一个字符没必要枚举了
      for(int i=0;i<len-1;i++){
          for(int j=i+1;j<len;j++){
              if(j-i+1>maxLen&&validPalindromic(charArray,i,j)){
                  maxLen=j-i+1;
                  begin=i;
              }
          }
      }
      return s.substring(begin,begin+maxLen);
    }
    /**
    *校验charArray[left...right]是否是回文串
    */
    private boolean validPalindromic(char[] charArray,int left,int right){
        while(left<right){
            if(charArray[left]!=charArray[right]){
                return false;
            }
            left++;
            right--;
        }
        return true;
    }
}
```
<B>Java代码2(动态规划算法)：时间复杂度：O(n^2);空间复杂度：O(n^2)</B>  
上文的所有讨论是建立在子串长度大于2的前提之上的，我们还需要考虑规划的边界条件，即
子串的长度为1或2。对于长度为1的子串，它显然是个回文串；对于长度为2的子串，只要它的两个字母相同，它就是个回文串。
```java
class Solution {
    public String longestPalindrome(String s) {
        int len=s.length();
        if(len<2){
            return s;
        }
        //最大长度默认为1
        int begin=0,maxLen=1;
        // dp[i][j] 表示 s[i..j] 是否是回文串
        boolean[][] dp = new boolean[len][len];
        for(int i=0;i<len;i++){
           dp[i][i]=true;
        }
        char[] charArray=s.toCharArray();
        // 递推开始
        // 先枚举子串长度,长度从2开始枚举
        for(int subStrLen=2;subStrLen<=len;subStrLen++){
            // 枚举左边界，左边界的上限设置可以宽松一些
            for(int i=0;i<len;i++){
                //字串长度subStrLen=j-i+1
                int j=subStrLen+i-1;
                 // 如果右边界越界，就可以退出当前循环
                if (j >= len) {
                    break;
                }
                if(charArray[i]!=charArray[j]){
                    dp[i][j]=false;
                }else{
                    // 相等的情况下
                    // 考虑头尾去掉以后没有字符剩余，或者剩下一个字符的时候，肯定是回文串
                    if (j - i < 3) {
                        dp[i][j] = true;
                    } else {
                        //状态转移
                        dp[i][j] = dp[i + 1][j - 1];
                    }
                }
                if(dp[i][j]&&j-i+1>maxLen){
                    maxLen=j-i+1;
                    begin=i;
                }
            }
        }
        return s.substring(begin,begin+maxLen);
    }
}
```
# 6. Z字形变换
<B>题目</B>   
将一个给定字符串 s 根据给定的行数 numRows ，以从上往下、从左到右进行 Z 字形排列。
比如输入字符串为 "PAYPALISHIRING" 行数为 3 时，排列如下：
```text
P   A   H   N
A P L S I I G
Y   I   R
```
之后，你的输出需要从左往右逐行读取，产生出一个新的字符串，比如："PAHNAPLSIIGYIR"。
请你实现这个将字符串进行指定行数变换的函数：
```text
string convert(string s, int numRows);
```
示例1：
```text
输入：s = "PAYPALISHIRING", numRows = 3
输出："PAHNAPLSIIGYIR"
```
示例2：
```text
输入：s = "PAYPALISHIRING", numRows = 4
输出："PINALSIGYAHRPI"
解释：
P     I    N
A   L S  I G
Y A   H R
P     I
```
示例3：
```text
输入：s = "A", numRows = 1
输出："A"
```
提示：
- 1 <= s.length <= 1000
- s 由英文字母（小写和大写）、',' 和 '.' 组成
- 1 <= numRows <= 1000<br/>
<B>分析：</B>  
第一步：分析题目，这类图形排布的题目首先需要找规律才能解。<br/>
第二步：通过给出的例子我们可以看出规律：枚举字符串从0行开始填充数据，直到numRows-1行。
总共填充了numRows行。每到填充完第0行或者numRows-1行。就开始按行逆序继续填充。
由于最终让输出的是从左到右从上到下的所有字符组成的新字符串，因此我们可以按行计算出每一行的字符都是什么，
然后所有行组合在一起即可。<br/>
第三步：还需要考虑一些特殊情况，如需要输出的行数numRows小于字符长度len的情况(numRows:0,len:1),
因此组合的图形的行数为min(numRows,s.length())。
故：我们可以根据numRows和字符串长度计算出图形的行数，并定义一个boolean变量来决定是否需要
逆序继续填充数据。<br/>
<B>Java代码：时间复杂度：O(n^2);空间复杂度：O(n^2)</B>  
```java
class Solution {
    public String convert(String s, int numRows) {
       if(numRows==1){
           return s;
       }
       //定义一个集合存放每行的字符串
       List<StringBuilder> rows=new ArrayList();
       for(int i=0;i<Math.min(s.length(),numRows);i++){
           rows.add(new StringBuilder());
       }
       int curRow=0;
       //定义变量标记是否逆序
       boolean goingDown=false;
       for(char c:s.toCharArray()){
           rows.get(curRow).append(c);
           //每次到0(首)行和numRows-1(尾)行需要改变方向
           if(curRow==0||curRow==numRows-1){
               goingDown=!goingDown;
           }
           curRow+=goingDown?1:-1;
       }
       StringBuilder ret=new StringBuilder();
       for(StringBuilder sb:rows){
           ret.append(sb);
       }
       return ret.toString();
    }
}
```
# 7. 整数反转
<B>题目</B>   
给你一个 32 位的有符号整数 x ，返回将 x 中的数字部分反转后的结果。<br/>
如果反转后整数超过 32 位的有符号整数的范围 [−231,  231 − 1] ，就返回 0。<br/>
示例1：
```text
输入：x = 123
输出：321
```
示例2：
```text
输入：x = -123
输出：-321
```
示例3：
```text
输入：x = 120
输出：21
```
示例4：
```text
输入：x = 0
输出：0
```
提示：
- -231 <= x <= 231 - 1<br/>
<B>分析：</B> 
这道题我们可以采用数学想法来解，就是把这个数字从个位开始一位位拿出来。然后再一位位推到另一个数字中。<br/>
要在没有辅助栈或数组的帮助下「弹出」和「推入」数字，我们可以使用如下数学方法(在编码的过程中我们还需要判断是否溢出)：<br/>
// 弹出 x 的末尾数字 digit<br/>
digit = x % 10<br/>
x /= 10<br/>
// 将数字 digit 推入 rev 末尾<br/>
rev = rev * 10 + digit<br/>
<B>Java代码：时间复杂度：O(log∣x∣),翻转的次数即x十进制的位数。;空间复杂度：O(1)</B>  
```java
class Solution {
    public int reverse(int x) {
      int rev=0;
      while(x!=0){
          //防止溢出
          if(rev<Integer.MIN_VALUE/10||rev>Integer.MAX_VALUE/10){
              return 0;
          }
          int digit=x%10;
          x/=10;
          rev=rev*10+digit;
      }
      return rev;
    }
}
```
# 8. 字符串转换整数 (atoi)
<B>题目</B>  
请你来实现一个 myAtoi(string s) 函数(解释:Atoi表示ascii to integer)，使其能将字符串转换成一个 32 位有符号整数（类似 C/C++ 中的 atoi 函数）。<br/>
函数 myAtoi(string s) 的算法如下：<br/>
- 读入字符串并丢弃无用的前导空格<br/>
- 检查下一个字符（假设还未到字符末尾）为正还是负号，读取该字符（如果有）。 确定最终结果是负数还是正数。 如果两者都不存在，则假定结果为正。<br/>
- 读入下一个字符，直到到达下一个非数字字符或到达输入的结尾。字符串的其余部分将被忽略。<br/>
- 将前面步骤读入的这些数字转换为整数（即，"123" -> 123， "0032" -> 32）。如果没有读入数字，则整数为 0 。必要时更改符号（从步骤 2 开始）。<br/>
- 如果整数数超过 32 位有符号整数范围[−2^31,2^31 − 1] ，需要截断这个整数，使其保持在这个范围内。具体来说，小于−2^31的整数应该被固定为−2^31 ，大于2^31−1的整数应该被固定为2^31−1 。<br/>
- 返回整数作为最终结果。<br/>
注意：<br/>
- 本题中的空白字符只包括空格字符 ' ' 。<br/>
- 除前导空格或数字后的其余字符串外，请勿忽略 任何其他字符。<br/>
示例1：
```text
输入：s = "42"
输出：42
解释：加粗的字符串为已经读入的字符，插入符号是当前读取的字符。
第 1 步："42"（当前没有读入字符，因为没有前导空格）
第 2 步："42"（当前没有读入字符，因为这里不存在 '-' 或者 '+'）
第 3 步："42"（读入 "42"）
解析得到整数 42 。
由于 "42" 在范围 [-2^31, 2^31 - 1] 内，最终结果为 42 。
```
示例2：
```text
输入：s = "   -42"
输出：-42
解释：
第 1 步："   -42"（读入前导空格，但忽视掉）
第 2 步："   -42"（读入 '-' 字符，所以结果应该是负数）
第 3 步："   -42"（读入 "42"）
解析得到整数 -42 。
由于 "-42" 在范围 [-2^31, 2^31 - 1] 内，最终结果为 -42 。
```
示例3：
```text
输入：s = "4193 with words"
输出：4193
解释：
第 1 步："4193 with words"（当前没有读入字符，因为没有前导空格）
第 2 步："4193 with words"（当前没有读入字符，因为这里不存在 '-' 或者 '+'）
第 3 步："4193 with words"（读入 "4193"；由于下一个字符不是一个数字，所以读入停止）
解析得到整数 4193 。
由于 "4193" 在范围 [-2^31, 2^31 - 1] 内，最终结果为 4193 。
```
示例4：
```text
输入：s = "words and 987"
输出：0
解释：
第 1 步："words and 987"（当前没有读入字符，因为没有前导空格）
第 2 步："words and 987"（当前没有读入字符，因为这里不存在 '-' 或者 '+'）
第 3 步："words and 987"（由于当前字符 'w' 不是一个数字，所以读入停止）
解析得到整数 0 ，因为没有读入任何数字。
由于 0 在范围 [-2^31, 2^31 - 1] 内，最终结果为 0 。
```
示例5：
```text
输入：s = "-91283472332"
输出：-2147483648
解释：
第 1 步："-91283472332"（当前没有读入字符，因为没有前导空格）
第 2 步："-91283472332"（读入 '-' 字符，所以结果应该是负数）
第 3 步："-91283472332"（读入 "91283472332"）
解析得到整数 -91283472332 。
由于 -91283472332 小于范围 [-231, 231 - 1] 的下界，最终结果被截断为 -231 = -2147483648 
```
<B>分析：</B> 
参照上题，本题也是使用数学计算来解决，不过需要注意一下几点：
1、去除首尾空格，取第一位来判断符号。
2、变量字符串时的数字判断，是数字的话才继续计算，不是数字的话直接结束。
3、数字范围的判断
<B>Java代码：时间复杂度：O(n);空间复杂度：O(n)</B>  
```java
class Solution {
    public int myAtoi(String s) {
    //去除字符串首尾空格
     s=s.trim();   
     if(s.length()==0){
         return 0;
     }
     //获取第一个字符
     char firstChar=s.charAt(0);
     if(!Character.isDigit(firstChar)&&firstChar!='+'&&firstChar!='-'){
         return 0;
     }
     //符号标识
      int sign=firstChar=='-'?-1:1;
      //遍历字符串的起始下标
      int i=Character.isDigit(firstChar)?0:1;
      int res=0;
      while(i<s.length()&&Character.isDigit(s.charAt(i))){
          int num=s.charAt(i++)-'0';
         if(res>Integer.MAX_VALUE/10||(res==Integer.MAX_VALUE/10&&num>Integer.MAX_VALUE%10)){
            return Integer.MAX_VALUE;
          }
         if(res<Integer.MIN_VALUE/10||(res == Integer.MIN_VALUE/10&&-num<Integer.MIN_VALUE%10)){
            return Integer.MIN_VALUE;
         }
         res=res*10+sign*num;
      }
      return res;
    }
}
```













