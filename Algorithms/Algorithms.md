   * [1. 两数之和](#1-两数之和)

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

