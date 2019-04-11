# LeetCode算法总结

## 两数之和

给定一个整数数组 `nums` 和一个目标值 `target`，请你在该数组中找出和为目标值的那 **两个** 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

思路：

1. 一般思路：遍历两遍，比较得到结果。时间复杂度O(n)，空间复杂度O(1)。
2. 使用哈希表：在进行迭代并将元素插入到表中的同时，我们还会回过头来检查表中是否已经存在当前元素所对应的目标元素。如果它存在，那我们已经找到了对应解，并立即将其返回。

``` java
	public int[] twoSum(int[] nums, int target) {
        for(int i=0;i<nums.length;i++) {
        	for(int j=nums.length-1;j>i;j--) {
        		if((nums[i]+nums[j])==target) {
        			return new int[] {i,j};
        		}
        	}
        }
        throw new IllegalArgumentException("No two sum solution");
    }
```

``` java
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            int complement = target - nums[i];
            if (map.containsKey(complement)) {
                return new int[] { map.get(complement), i };
            }
         map.put(nums[i], i);
        }
        throw new IllegalArgumentException("No two sum solution");
    }
```

## 两数相加

给出两个 **非空** 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 **逆序** 的方式存储的，并且它们的每个节点只能存储 **一位** 数字。

如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。

您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

思路：此题不能转换为数字求，那样会碰到数组溢出的问题。

​	这里模拟相加的过程，计算每一位相加的值和进位，保留进位。

​	通过赋初值，但会初值的下一位可以少写一个判断位。

``` java
	public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
	    ListNode list = new ListNode(0);
	    ListNode p = l1, q = l2, result = list;//不改变传入的数值
	    int carry = 0;
	    while (p != null || q != null) {
	        int x = (p != null) ? p.val : 0;
	        int y = (q != null) ? q.val : 0;
	        int sum = carry + x + y;
	        carry = sum / 10;
	        list.next = new ListNode(sum % 10);
	        list = list.next;
	        if (p != null) p = p.next;
	        if (q != null) q = q.next;
	    }
	    if (carry > 0) {
	    	list.next = new ListNode(carry);
	    }
	    return result.next;
	}
```

## 无重复字符的最长子串

给定一个字符串，请你找出其中不含有重复字符的 **最长子串** 的长度。

思路：通过滑动窗口，存储窗口的长度，并通过比较新进来的字符确认是否要改变窗口。

一般方法：通过HashSet实现

简单方法：通过HashMap实现/通过整数数组来替换HashMap

``` java
//HashSet
public class Solution {
    public int lengthOfLongestSubstring(String s) {
        int n = s.length();
        Set<Character> set = new HashSet<>();
        int ans = 0, i = 0, j = 0;
        while (i < n && j < n) {
            // try to extend the range [i, j]
            if (!set.contains(s.charAt(j))){
                set.add(s.charAt(j++));
                ans = Math.max(ans, j - i);
            }
            else {
                set.remove(s.charAt(i++));
            }
        }
        return ans;
    }
}
```

``` java
//HashMap
public class Solution {
    public int lengthOfLongestSubstring(String s) {
        int n = s.length(), ans = 0;
        Map<Character, Integer> map = new HashMap<>(); // current index of character
        // try to extend the range [i, j]
        for (int j = 0, i = 0; j < n; j++) {
            if (map.containsKey(s.charAt(j))) {
                i = Math.max(map.get(s.charAt(j)), i);
            }
            ans = Math.max(ans, j - i + 1);
            map.put(s.charAt(j), j + 1);
        }
        return ans;
    }
}
//整数数组
public class Solution {
    public int lengthOfLongestSubstring(String s) {
        int n = s.length(), ans = 0;
        int[] index = new int[128]; // current index of character
        // try to extend the range [i, j]
        for (int j = 0, i = 0; j < n; j++) {
            i = Math.max(index[s.charAt(j)], i);
            ans = Math.max(ans, j - i + 1);
            index[s.charAt(j)] = j + 1;
        }
        return ans;
    }
}
```

## 寻找两个有序数组的中位数

给定两个大小为 m 和 n 的有序数组 `nums1` 和 `nums2`。

请你找出这两个有序数组的中位数，并且要求算法的时间复杂度为 O(log(m + n))。

你可以假设 `nums1` 和 `nums2` 不会同时为空。

思路：了解中位数的定义，将两个数组A,B进行划分，使A左+B左=A右+B右，且A左和B左都小于A右和B右，这样结果就是A左B左中的最大值与A右B右最小值的平均值。

``` java
class Solution {
    public double findMedianSortedArrays(int[] A, int[] B) {
	        int m = A.length;
	        int n = B.length;
	        if (m > n) { // to ensure m<=n
	            int[] temp = A; A = B; B = temp;
	            int tmp = m; m = n; n = tmp;
	        }
	        int iMin = 0, iMax = m, halfLen = (m + n + 1) / 2;
	        while (iMin <= iMax) {
	            int i = (iMin + iMax) / 2;
	            int j = halfLen - i;
	            if (i < iMax && B[j-1] > A[i]){
	                iMin = i + 1; // i is too small
	            }
	            else if (i > iMin && A[i-1] > B[j]) {
	                iMax = i - 1; // i is too big
	            }
	            else { // i is perfect
	                int maxLeft = 0;
	                if (i == 0) { maxLeft = B[j-1]; }
	                else if (j == 0) { maxLeft = A[i-1]; }
	                else { maxLeft = Math.max(A[i-1], B[j-1]); }
	                if ( (m + n) % 2 == 1 ) { return maxLeft; }

	                int minRight = 0;
	                if (i == m) { minRight = B[j]; }
	                else if (j == n) { minRight = A[i]; }
	                else { minRight = Math.min(B[j], A[i]); }

	                return (maxLeft + minRight) / 2.0;
	            }
	        }
	        return 0.0;
	    }
}
```

## 最长回文子串

给定一个字符串 `s`，找到 `s` 中最长的回文子串。可以假设 `s` 的最大长度为 1000。

思路：

1.倒序检测，将字符串反转，依次找到两个字符串中最长的相同字符串，并判断是否为回文，不是则检测下一个。

2.中心扩展，利用回文的特征从n个点的2n-1个中心的位置进行扩展，并记录最长的部分。

``` java
//中心扩展法
class Solution {
    public String longestPalindrome(String s) {
    	if(s==null||s.length()==0)
    		return s;
        int start=0,end=0;
        for(int i=0;i<s.length();i++) {
        	int len1 = Palindrome(s,i,i);
        	int len2 = Palindrome(s,i,i+1);
        	int len = len1>len2?len1:len2;
        	if(len>end-start) {
        		start=i-(len-1)/2;
        		end=i+len/2;
        	}
        }
        return s.substring(start, end+1);
    }
	private int Palindrome(String s, int left, int right) {
		while(left>=0&&right<s.length()&&s.charAt(left)==s.charAt(right)) {
			left--;
			right++;
		}
		return right-left-1;	
	}
}
```

## 编辑距离

给定两个单词 *word1* 和 *word2*，计算出将 *word1* 转换成 *word2* 所使用的最少操作数 。

你可以对一个单词进行如下三种操作：

1. 插入一个字符
2. 删除一个字符
3. 替换一个字符

思路i：动态规划——设word1的长度为A，word2的长度为B，创建一个arrs数组来存储从word1的i位置开始变换成为从word2的j位置开始的后续字符串所需要的最少操作数，最终需要得到的就是i=0且j=0的位置需要的最小操作数。

``` java
public class Test {
	int[][] min;
    // 最少操作数
    // 如果当前字符不等，则有三种选择，前面插入、删除当前，或者替换当前，
    // 如果相等，则继续比较
    public int minDistance(String word1, String word2) {
        if(word1 == null && word2 == null){
            return 0;
        }
        if(word1.length() == 0 || word2.length() == 0){
            return word1.length() ==0 ? word2.length() : word1.length();
        }
        min = new int[word1.length()][word2.length()];   
        for(int i = 0; i < word1.length(); i ++){
            for(int j = 0; j < word2.length(); j ++){
                min[i][j] = -1;
            }
        }
        return minDistance(word1.toCharArray(),word2.toCharArray(),0,0);
    }
    
    public int minDistance(char[] word1,char[] word2,int index1,int index2){
        if(index1 == word1.length || index2 == word2.length){
            return index1 == word1.length ? word2.length - index2 : word1.length - index1;
        }
        if(word1[index1] == word2[index2]){
            return minDistance(word1,word2,index1+1,index2+1);
        }
        if(min[index1][index2] != -1){
            return min[index1][index2];
        }
        int insert = minDistance(word1,word2,index1,index2+1);
        int delete = minDistance(word1,word2,index1+1,index2);
        int replace = minDistance(word1,word2,index1+1,index2+1);
        min[index1][index2] = Math.min(insert,Math.min(delete,replace)) + 1;//这里加的1是在执行上面三个操作之一时进行的一次动作
        return min[index1][index2];
    }
    
    public static void main(String[] args) {
		Test test = new Test();
		System.out.println(test.minDistance("hors", "ros"));
		System.out.println(test.min[0][0]);
	}
}
```

