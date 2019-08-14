# **LeetCode**算法总结1

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

## 买卖股票的最佳时机

### 买卖股票的最佳时机——1

给定一个数组，它的第 *i* 个元素是一支给定股票第 *i* 天的价格。

如果你最多只允许完成一笔交易（即买入和卖出一支股票），设计一个算法来计算你所能获取的最大利润。

注意你不能在买入股票前卖出股票。

思路：这一题即求出从前到后的最大差值，这并不是简单求其最大值和最小值，做法是使用一次遍历，在遍历过程中动态的改变最大的利润和最小的买入时机，当遍历完一遍既可以的到正确的结果。

``` java
	public int maxProfit(int[] prices) {
        if(prices==null||prices.length==0)
            return 0;
        int min = prices[0];
        int profit = 0;
        for (int i = 0; i < prices.length; i++) {
            profit = Math.max(prices[i]-min,profit);
            min = Math.min(min,prices[i]);
        }
        return profit;  
   	}
```

### 买卖股票的最佳时机——2

给定一个数组，它的第 *i* 个元素是一支给定股票第 *i* 天的价格。

设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。

**注意：**你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

#### 方法一：

峰谷法：

假设给定的数组为：

[7, 1, 5, 3, 6, 4].

如果我们在图表上绘制给定数组中的数字，我们将会得到：

​	![Profit Graph](C:\Users\12084\Desktop\数据结构算法总结\maxprofit.PNG)

由图可知，最大的利润一定为A+B，而不是C，因此这一题和上题有很大的不同。

``` java
    public int maxProfit(int[] prices) {
        int i = 0;
        int valley = prices[0];
        int peak = prices[0];
        int maxprofit = 0;
        while (i < prices.length - 1) {
            while (i < prices.length - 1 && prices[i] >= prices[i + 1])
                i++;
            valley = prices[i];
            while (i < prices.length - 1 && prices[i] <= prices[i + 1])
                i++;
            peak = prices[i];
            maxprofit += peak - valley;
        }
        return maxprofit;
    }
```

#### 方法二：

该解决方案遵循 [方法一]的本身使用的逻辑，但有一些轻微的变化。在这种情况下，我们可以简单地继续在斜坡上爬升并持续增加从连续交易中获得的利润，而不是在谷之后寻找每个峰值。最后，我们将有效地使用峰值和谷值，但我们不需要跟踪峰值和谷值对应的成本以及最大利润，但我们可以直接继续增加加数组的连续数字之间的差值，如果第二个数字大于第一个数字，我们获得的总和将是最大利润。这种方法将简化解决方案。 这个例子可以更清楚地展现上述情况：

[1, 7, 2, 3, 6, 7, 6, 7]

与此数组对应的图形是：

![Profit Graph](C:\Users\12084\Desktop\数据结构算法总结\maxprofit_2.PNG)

从上图中，我们可以观察到 A+B+C*A*+*B*+*C* 的和等于差值 D*D* 所对应的连续峰和谷的高度之差。

``` java
class Solution {
    public int maxProfit(int[] prices) {
        int maxprofit = 0;
        for (int i = 1; i < prices.length; i++) {
            if (prices[i] > prices[i - 1])
                maxprofit += prices[i] - prices[i - 1];
        }
        return maxprofit;
    }
}
```

### 买股票的最佳时机——3

给定一个数组，它的第 *i* 个元素是一支给定的股票在第 *i* 天的价格。

设计一个算法来计算你所能获取的最大利润。你最多可以完成 *两笔* 交易。

注意：这里相比较上面最多只能完成两笔交易

```java
class Solution {
    public int maxProfit(int[] prices) {
        /**
        对于任意一天考虑四个变量:
        fstBuy: 在该天第一次买入股票可获得的最大收益 
        fstSell: 在该天第一次卖出股票可获得的最大收益
        secBuy: 在该天第二次买入股票可获得的最大收益
        secSell: 在该天第二次卖出股票可获得的最大收益
        分别对四个变量进行相应的更新, 最后secSell就是最大
        收益值(secSell >= fstSell)
        **/
        int fstBuy = Integer.MIN_VALUE, fstSell = 0;
        int secBuy = Integer.MIN_VALUE, secSell = 0;
        for(int p : prices) {
            fstBuy = Math.max(fstBuy, -p);
            fstSell = Math.max(fstSell, fstBuy + p);
            secBuy = Math.max(secBuy, fstSell - p);
            secSell = Math.max(secSell, secBuy + p); 
        }
        return secSell;
    }
}
```

## K个一组翻转链表

给出一个链表，每 *k* 个节点一组进行翻转，并返回翻转后的链表。

*k* 是一个正整数，它的值小于或等于链表的长度。如果节点总数不是 *k* 的整数倍，那么将最后剩余节点保持原有顺序。

思路：使用递归

``` java
class Solution {
   public ListNode reverseKGroup(ListNode head, int k) {
        if(head==null||k==0||k==1){
            return head;
        }
        ListNode start = null;
        ListNode temp = head;
        ListNode end = null;

        int i = 0,j=0;
        while(temp.next!=null){
            i++;
            temp = temp.next;
        }

        temp = head;
        if(i>=k-1){
            while(j<k){
                end = temp.next;
                temp.next = start;
                start = temp;
                temp = end;
                j++;
            }
            head.next = reverseKGroup(temp,k);
            return start;
        }else {
            return head;
        }


    }
}
```

思路2：使用循环，分段翻转+虚节点



## 可怜的小猪

假设有 `n` 只水桶，猪饮水中毒后会在 `m` 分钟内死亡，你需要多少猪（`x`）就能在 `p` 分钟内找出 “**有毒**” 水桶？这 `n` 只水桶里有且仅有一只有毒的桶。

 注意，通过建立矩阵明白两只猪可以找出两个p/m+1的坐标，两只可以依次得到一个坐标

``` java
class Solution {
    public int poorPigs(int buckets, int minutesToDie, int minutesToTest) {
        int times = minutesToTest/minutesToDie+1;
        int i = 0;
        int number =1;
        while(number<buckets){
            number *= times;
            i++;
        }
        return i;
    }
}
```



## Z字形变换

将一个给定字符串根据给定的行数，以从上往下、从左到右进行 Z 字形排列。

比如输入字符串为 `"LEETCODEISHIRING"` 行数为 3 时，排列如下：

```
L   C   I   R
E T O E S I I G
E   D   H   N
```

之后，你的输出需要从左往右逐行读取，产生出一个新的字符串，比如：`"LCIRETOESIIGEDHN"`。

思路：分两类进行截取操作

``` java
class Solution {
    public String convert(String s, int numRows) {
        if(s==null||s.trim().equals("")||numRows==1){
            return s;
        }
        StringBuilder input = new StringBuilder(s);
        StringBuilder result = new StringBuilder();

        for (int i = 0; i < numRows; i++) {
            if(i==0||i==numRows-1){
                for (int j = i; j < s.length(); j+=(2*numRows-2)){
                    result.append(input.charAt(j));
                }
            }else{
                for (int j = i; j < s.length(); j+=(2*numRows-2)) {
                    result.append(input.charAt(j));
                    int x = j+2*numRows-2-2*i;
                    if(x<s.length()){
                        result.append(input.charAt(x));
                    }
                }
            }
        }
        return result.toString();
    }
}
```



## 整数反转

给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。

**示例 1:**

```
输入: 123
输出: 321
```

 **示例 2:**

```
输入: -123
输出: -321
```

**示例 3:**

```
输入: 120
输出: 21
```

注意边界的判定！

``` java
class Solution {
    public int reverse(int x) {
        int ans = 0;
        while (x != 0) {
            int pop = x % 10;
            if (ans > Integer.MAX_VALUE / 10 || (ans == Integer.MAX_VALUE / 10 && pop > 7)) 
                return 0;
            if (ans < Integer.MIN_VALUE / 10 || (ans == Integer.MIN_VALUE / 10 && pop < -8)) 
                return 0;
            ans = ans * 10 + pop;
            x /= 10;
        }
        return ans;
    }
}
```



## 字符串转整数

请你来实现一个 atoi 函数，使其能将字符串转换成整数。

首先，该函数会根据需要丢弃无用的开头空格字符，直到寻找到第一个非空格的字符为止。

当我们寻找到的第一个非空字符为正或者负号时，则将该符号与之后面尽可能多的连续数字组合起来，作为该整数的正负号；假如第一个非空字符是数字，则直接将其与之后连续的数字字符组合起来，形成整数。

该字符串除了有效的整数部分之后也可能会存在多余的字符，这些字符可以被忽略，它们对于函数不应该造成影响。

注意：假如该字符串中的第一个非空格字符不是一个有效整数字符、字符串为空或字符串仅包含空白字符时，则你的函数不需要进行转换。

在任何情况下，若函数不能进行有效的转换时，请返回 0。

注意边界的判定和返回值的截取：

``` java
class Solution {
    public int myAtoi(String str) {
        if(str==null||str.trim().equals("")){
            return 0;
        }
        char input[] = str.trim().toCharArray();
        int start = 0;
        int end = 0;
        boolean tag = true;
        if(input[0]=='+'){
            start=1;
        }else if(input[0]=='-'){
            start=1;
            tag=false;
        }
        end=start;
        int result=0;
        for (int i = start; i < input.length; i++) {
            if(input[i]>'9'||input[i]<'0') {
                return tag?result:result*-1;
            }else if(end==i){
                //sum用于判断累加之后是否超出界限，超出界限会出现由正转负，可由此判断
                int sum=result*10+(input[i]-'0');

                if((sum-(input[i]-'0'))/10!=result||(sum<0&&result>0)) {
                    return tag?2147483647:-2147483648;
                }
                //得到结果
                result = result*10+(input[i]-'0');
                end++;
            }
        }
        return tag?result:result*-1;
    }
}
```



## 反转字符串

编写一个函数，其作用是将输入的字符串反转过来。输入字符串以字符数组 `char[]` 的形式给出。

不要给另外的数组分配额外的空间，你必须**原地修改输入数组**、使用 O(1) 的额外空间解决这一问题。

你可以假设数组中的所有字符都是 [ASCII](https://baike.baidu.com/item/ASCII) 码表中的可打印字符。

 ``` java
class Solution {
    public void reverseString(char[] s) {
        swap(0, s.length-1, s);
    }
    
    public void swap(int start, int end, char[] s) {
        if(start >= end){
            return;
        }
        
        char temp = s[start];
        s[start] = s[end];
        s[end] = temp;
        swap(start+1, end-1, s);
    }
}
 ```



## 两两交换链表中的节点

给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。

**你不能只是单纯的改变节点内部的值**，而是需要实际的进行节点交换。

注意：需要更改的是链表内部的引用不能写 head = head.next; 

``` java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode swapPairs(ListNode head) {
        if(head==null||head.next==null){
            return head;
        }
        ListNode next = head.next;
        ListNode node = head.next.next;
        next.next = head;
        head.next = swapPairs(node);
        return next;
    }
}
```



## 杨辉三角

给定一个非负整数 *numRows，*生成杨辉三角的前 *numRows* 行。

![img](https://upload.wikimedia.org/wikipedia/commons/0/0d/PascalTriangleAnimated2.gif)

在杨辉三角中，每个数是它左上方和右上方的数的和。

递归写法：

``` java
class Solution {
  List<List<Integer>> result = new ArrayList<List<Integer>>();
    public List<List<Integer>> generate(int numRows) {
        if(numRows==0){
            return result;
        }
        if(numRows==1){
            List<Integer> l = new ArrayList<>();
            l.add(1);
            result.add(l);
            return result;
        }
        if(numRows==2){
            List<Integer> l1 = new ArrayList<>();
            l1.add(1);
            List<Integer> l2 = new ArrayList<>();
            l2.add(1);
            l2.add(1);
            result.add(l1);
            result.add(l2);
            return result;
        }
        List<Integer> l = new ArrayList<>();
        List<List<Integer>> pre = generate(numRows-1);
        for (int i = 0; i < numRows; i++) {
            if(i==0||i==numRows-1){
                l.add(1);
            }else {
                List<Integer> p = pre.get(numRows-2);
                int temp = p.get(i-1)+p.get(i);
                l.add(temp);
            }
        }
        pre.add(l);
        return pre;
    }
}
```

非递归写法：

#### 动态规划

**思路**

如果能够知道一行杨辉三角，我们就可以根据每对相邻的值轻松地计算出它的下一行。

``` java
class Solution {
    public List<List<Integer>> generate(int numRows) {
        List<List<Integer>> triangle = new ArrayList<List<Integer>>();

        // First base case; if user requests zero rows, they get zero rows.
        if (numRows == 0) {
            return triangle;
        }

        // Second base case; first row is always [1].
        triangle.add(new ArrayList<>());
        triangle.get(0).add(1);

        for (int rowNum = 1; rowNum < numRows; rowNum++) {
            List<Integer> row = new ArrayList<>();
            List<Integer> prevRow = triangle.get(rowNum-1);

            // The first row element is always 1.
            row.add(1);

            // Each triangle element (other than the first and last of each row)
            // is equal to the sum of the elements above-and-to-the-left and
            // above-and-to-the-right.
            for (int j = 1; j < rowNum; j++) {
                row.add(prevRow.get(j-1) + prevRow.get(j));
            }

            // The last row element is always 1.
            row.add(1);

            triangle.add(row);
        }

        return triangle;
    }
}
```



## 杨辉三角2

给定一个非负索引 *k*，其中 *k* ≤ 33，返回杨辉三角的第 *k* 行。

![img](https://upload.wikimedia.org/wikipedia/commons/0/0d/PascalTriangleAnimated2.gif)

在杨辉三角中，每个数是它左上方和右上方的数的和。

``` java
class Solution {
    public List<Integer> getRow(int rowIndex) {
        if(rowIndex==0){
            List<Integer> l = new ArrayList<>();
            l.add(1);
            return l;
        }
        if(rowIndex==1){
            List<Integer> l = new ArrayList<>();
            l.add(1);
            l.add(1);
            return l;
        }
        List<Integer> pre = getRow(rowIndex-1);
        List<Integer> result = new ArrayList<>();
        for (int i = 0; i <= rowIndex; i++) {
            if(i==0||i==rowIndex){
                result.add(1);
            }else {
                result.add(pre.get(i)+pre.get(i-1));
            }
        }
        return result;
    }
}
```

##  反转链表

反转一个单链表。

**示例:**

```
输入: 1->2->3->4->5->NULL
输出: 5->4->3->2->1->NULL
```

**递归写法：**

递归版本稍微复杂一些，其关键在于反向工作。假设列表的其余部分已经被反转，现在我该如何反转它前面的部分？假设列表为：n1 → … → nk-1 → nk → nk+1 → … → nm → Ø

若从节点 nk+1 到 nm 已经被反转，而我们正处于 nk。

n1 → … → nk-1 → **nk** → nk+1 ← … ← nm

我们希望 nk+1 的下一个节点指向 nk。

所以，

nk.next.next = nk;

要小心的是 n1 的下一个必须指向 Ø 。如果你忽略了这一点，你的链表中可能会产生循环。如果使用大小为 2 的链表测试代码，则可能会捕获此错误。

``` java
public ListNode reverseList(ListNode head) {
    if (head == null || head.next == null) return head;
    ListNode p = reverseList(head.next);
    head.next.next = head;
    head.next = null;
    return p;
}
```

**迭代写法：**

假设存在链表 `1 → 2 → 3 → Ø`，我们想要把它改成 `Ø ← 1 ← 2 ← 3`。

在遍历列表时，将当前节点的 `next` 指针改为指向前一个元素。由于节点没有引用其上一个节点，因此必须事先存储其前一个元素。在更改引用之前，还需要另一个指针来存储下一个节点。不要忘记在最后返回新的头引用！

``` java
public ListNode reverseList(ListNode head) {
    ListNode prev = null;
    ListNode curr = head;
    while (curr != null) {
        ListNode nextTemp = curr.next;
        curr.next = prev;
        prev = curr;
        curr = nextTemp;
    }
    return prev;
}
```

##  Pow(x, n)

实现 [pow(*x*, *n*)](https://www.cplusplus.com/reference/valarray/pow/) ，即计算 x 的 n 次幂函数。

思路：直接用递归或者循环一个一个乘在N较大的时候很可能导致栈溢出或者运行事件过长，因此可以选择通过快速幂的算法来进行求解。

**递归：**

```java
class Solution {
    private double fastPow(double x, long n) {
        if (n == 0) {
            return 1.0;
        }
        //折半查询
        double half = fastPow(x, n / 2);
        if (n % 2 == 0) {
            return half * half;
        } else {
            return half * half * x;
        }
    }
    public double myPow(double x, int n) {
        long N = n;
        //正负处理
        if (N < 0) {
            x = 1 / x;
            N = -N;
        }

        return fastPow(x, N);
    }
};
```

**迭代快速幂算法**

``` java
class Solution {
    public double myPow(double x, int n) {
        long N = n;
        if (N < 0) {
            x = 1 / x;
            N = -N;
        }
        double ans = 1;
        double current_product = x;
        for (long i = N; i > 0; i /= 2) {
            //奇次幂
            if ((i % 2) == 1) {
                ans = ans * current_product;
            }
            //偶次幂
            current_product = current_product * current_product;
        }
        return ans;
    }
};
```



## 合并两个有序链表

将两个有序链表合并为一个新的有序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

**示例：**

```
输入：1->2->4, 1->3->4
输出：1->1->2->3->4->4
```

方法一：递归

``` java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) {
            return l2;
        }
        else if (l2 == null) {
            return l1;
        }
        else if (l1.val < l2.val) {
            l1.next = mergeTwoLists(l1.next, l2);
            return l1;
        }
        else {
            l2.next = mergeTwoLists(l1, l2.next);
            return l2;
        }

    }
}
```

方法二：迭代

``` java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        // maintain an unchanging reference to node ahead of the return node.
        ListNode prehead = new ListNode(-1);

        ListNode prev = prehead;
        while (l1 != null && l2 != null) {
            if (l1.val <= l2.val) {
                prev.next = l1;
                l1 = l1.next;
            } else {
                prev.next = l2;
                l2 = l2.next;
            }
            prev = prev.next;
        }

        // exactly one of l1 and l2 can be non-null at this point, so connect
        // the non-null list to the end of the merged list.
        prev.next = l1 == null ? l2 : l1;

        return prehead.next;
    }
}
```



## 第K个语法符号

在第一行我们写上一个 `0`。接下来的每一行，将前一行中的`0`替换为`01`，`1`替换为`10`。

给定行数 `N` 和序数 `K`，返回第 `N` 行中第 `K`个字符。（`K`从1开始）


**例子:**

```
输入: N = 1, K = 1
输出: 0

输入: N = 2, K = 1
输出: 0

输入: N = 2, K = 2
输出: 1

输入: N = 4, K = 5
输出: 1

解释:
第一行: 0
第二行: 01
第三行: 0110
第四行: 01101001
```

思路：这题如果单纯使用暴力法来递归或者迭代，字符串的长度可能有 10 亿左右，因为每一行的长度都是前一行的两倍，所以这种方法不够高效。

**方法一：递归（父变体）**

**思路和算法**

因为生成每一行只需要前一行的信息，所以我们可以考虑解析前一行的位来输出答案。



![Diagram of digits with relationship to their parent](https://leetcode-cn.com/explore/orignial/card/recursion-i/260/conclusion/Figures/779/parent.png)



来看这个例子，如果我们中间有一行是 `"0110"`，那么就会生成 `"01101001"` 作为它的下一行，也就是说第一位 `"0"` 生成下一行中的第一个 `"01"`，第二位 `"1"` 生成下一行中的 `"10"`，接着 `"1"` 又生成了 `"10"`，而最后的 `"0"` 将会生成最后的 `"01"`。



![Diagram of digits through repeated function calls](https://leetcode-cn.com/explore/orignial/card/recursion-i/260/conclusion/Figures/779/link.png)



一般而言，第 `K` 位的父位应该是第 `(K+1) / 2` 位。**如果父位是 `0`，那么这一位就是 `1 - (K%2)`。如果父位是 `1`，那么这一位就是 `K%2`。**

``` java
class Solution {
    public int kthGrammar(int N, int K) {
        if (N == 1) return 0;
        return (~K & 1) ^ kthGrammar(N-1, (K+1)/2);
    }
}
```

**方法三：递归（翻转变体）**

**思路和算法**

就像在 *方法二* 中那样，我们可以尝试按它前面的位来写出这一位。

如果我们写出该序列中的几行，就可以发现：后半部分总是与前半部分相反，也就是说：`'0'` 变成 `'1'` 而 `'1'`变成 `'0'`。

我们可以用归纳法来验证这一推断。其关键思想是，如果字符串 X*X* 生成 Y*Y*，那么翻转后的字符串 X&#x27;*X*′ 将会生成 Y&#x27;*Y*′。

这就引出了下面的算法思想：如果 `K` 在后半部分，那么我们可以将 `K -= (1 << N-2)` 设为前半部分，然后翻转得到最终答案。

``` java
class Solution {
    public int kthGrammar(int N, int K) {
        if (N == 1) return 0;
        if (K <= 1 << N-2)
            return kthGrammar(N-1, K);
        return kthGrammar(N-1, K - (1 << N-2)) ^ 1;
    }
}
```



## 不同的二叉搜索树 II

给定一个整数 *n*，生成所有由 1 ... *n* 为节点所组成的**二叉搜索树**。

**示例:**

```
输入: 3
输出:
[
  [1,null,3,2],
  [3,2,null,1],
  [3,1,null,null,2],
  [2,1,3],
  [1,null,2,null,3]
]
解释:
以上的输出对应以下 5 种不同结构的二叉搜索树：

   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3
```

**方法：递归**

首先，让我们来数一数需要构建多少棵树。 在[不同的二叉搜索树](https://leetcode-cn.com/problems/unique-binary-search-trees/)中，我们了解到，需要构建的二叉搜索树的数量实际上满足[卡特兰数](https://baike.baidu.com/item/%E5%8D%A1%E7%89%B9%E5%85%B0%E6%95%B0)。

我们将会顺着上一篇文章的思路继续，不过这一次我们不需要计数，而是实际构造树。

**算法**

从序列 `1 ..n` 取出数字 `i` 并以它作为当前树的根节点。 那么就有 `i - 1` 个元素可以用来构造左子树，而另外的 `n - i` 个元素可以用于构造右子树。最后我们将会得到 `G(i - 1)` 棵不同的左子树，以及 `G(n - i)` 棵不同的右子树，其中 `G` 为卡特兰数。

![BST](https://leetcode-cn.com/articles/Figures/96/96_BST.png)

现在，我们将会在序列 `1 ... i - 1` 上重复前面的步骤来构造所有的左子树，之后对序列 `i + 1 ... n` 也这样做以得到所有的右子树。

这么一来，我们就得到了根节点 `i` 和两个可能的左右子树列表。 最后一步是遍历两个列表，将左右子树和根节点链接起来。

``` java
class Solution {
  public LinkedList<TreeNode> generate_trees(int start, int end) {
    LinkedList<TreeNode> all_trees = new LinkedList<TreeNode>();
    if (start > end) {
      all_trees.add(null);
      return all_trees;
    }

    // pick up a root
    for (int i = start; i <= end; i++) {
      // all possible left subtrees if i is choosen to be a root
      LinkedList<TreeNode> left_trees = generate_trees(start, i - 1);

      // all possible right subtrees if i is choosen to be a root
      LinkedList<TreeNode> right_trees = generate_trees(i + 1, end);

      // connect left and right trees to the root i
      for (TreeNode l : left_trees) {
        for (TreeNode r : right_trees) {
          TreeNode current_tree = new TreeNode(i);
          current_tree.left = l;
          current_tree.right = r;
          all_trees.add(current_tree);
        }
      }
    }
    return all_trees;
  }

  public List<TreeNode> generateTrees(int n) {
    if (n == 0) {
      return new LinkedList<TreeNode>();
    }
    return generate_trees(1, n);
  }
}
```



## 最小栈

设计一个支持 push，pop，top 操作，并能在常数时间内检索到最小元素的栈。

push(x) -- 将元素 x 推入栈中。
pop() -- 删除栈顶的元素。
top() -- 获取栈顶元素。
getMin() -- 检索栈中的最小元素。
示例:

MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin();   --> 返回 -3.
minStack.pop();
minStack.top();      --> 返回 0.
minStack.getMin();   --> 返回 -2.

**思路分析：**
在代码实现的时候有两种方式：

1、辅助栈和数据栈同步

特点：编码简单，不用考虑一些边界情况，就有一点不好：辅助栈可能会存一些“不必要”的元素。

```java
package Test;

import java.util.Stack;

public class MinStack {

    // 数据栈
    private Stack<Integer> data;
    // 辅助栈
    private Stack<Integer> helper;

    /**
     * initialize your data structure here.
     */
    public MinStack() {
        data = new Stack<>();
        helper = new Stack<>();
    }

    // 思路 1：数据栈和辅助栈在任何时候都同步

    public void push(int x) {
        // 数据栈和辅助栈一定会增加元素
        data.add(x);
        if (helper.isEmpty() || helper.peek() >= x) {
            helper.add(x);
        } else {
            helper.add(helper.peek());
        }
    }

    public void pop() {
        // 两个栈都得 pop
        if (!data.isEmpty()) {
            helper.pop();
            data.pop();
        }
    }

    public int top() {
        if(!data.isEmpty()){
            return data.peek();
        }
        throw new RuntimeException("栈中元素为空，此操作非法");
    }

    public int getMin() {
        if(!helper.isEmpty()){
            return helper.peek();
        }
        throw new RuntimeException("栈中元素为空，此操作非法");
    }
}
```



## 有效的括号

给定一个只包括 `'('`，`')'`，`'{'`，`'}'`，`'['`，`']'` 的字符串，判断字符串是否有效。

有效字符串需满足：

1. 左括号必须用相同类型的右括号闭合。
2. 左括号必须以正确的顺序闭合。

注意空字符串可被认为是有效字符串。

思路：借用栈来实现

``` java
class Solution {

  // Hash table that takes care of the mappings.
  private HashMap<Character, Character> mappings;

  // Initialize hash map with mappings. This simply makes the code easier to read.
  public Solution() {
    this.mappings = new HashMap<Character, Character>();
    this.mappings.put(')', '(');
    this.mappings.put('}', '{');
    this.mappings.put(']', '[');
  }

  public boolean isValid(String s) {

    // Initialize a stack to be used in the algorithm.
    Stack<Character> stack = new Stack<Character>();

    for (int i = 0; i < s.length(); i++) {
      char c = s.charAt(i);

      // If the current character is a closing bracket.
      if (this.mappings.containsKey(c)) {

        // Get the top element of the stack. If the stack is empty, set a dummy value of '#'
        char topElement = stack.empty() ? '#' : stack.pop();

        // If the mapping for this bracket doesn't match the stack's top element, return false.
        if (topElement != this.mappings.get(c)) {
          return false;
        }
      } else {
        // If it was an opening bracket, push to the stack.
        stack.push(c);
      }
    }

    // If the stack still contains elements, then it is an invalid expression.
    return stack.isEmpty();
  }
}
```




## 每日温度

根据每日 气温 列表，请重新生成一个列表，对应位置的输入是你需要再等待多久温度才会升高超过该日的天数。如果之后都不会升高，请在该位置用 0 来代替。

例如，给定一个列表 temperatures = [73, 74, 75, 71, 69, 72, 76, 73]，你的输出应该是 [1, 1, 4, 2, 1, 1, 0, 0]。

提示：气温 列表长度的范围是 [1, 30000]。每个气温的值的均为华氏度，都是在 [30, 100] 范围内的整数。



**思路：**

逆序遍历，减少遍历次数

如下图所示，绿色部分区域会给多次遍历，如果我们能减少这部分区域的遍历次数，就能整体提高运算效率。

![image.png](D:\java\md_pictures\leetCode\63f890bd5ecec9b4a34d4cddf066643b14150f8714c10968d288902da231de07-image.png)


如果我们先从计算右边，那么我们计算过的位置就不需要重复计算，如图所示：

![image.png](D:\java\md_pictures\leetCode\0f16daf6fde5475d72cbb6e9efec1d66409590141b861c2cf62fd87394211a82-image.png)

当前我们需要计算 7575 位置，然后向右遍历到 7171，因为我们已经计算好了 7171 位置对应的值为 22，那么我们就可以直接跳 22 为在进行比较，利用了已经有的结果，减少了遍历的次数。

``` java
class Solution {
    public int[] dailyTemperatures(int[] T) {
        int length = T.length;
        int[] result = new int[length];

        //从右向左遍历
        for (int i = length - 2; i >= 0; i--) {
        // j+= result[j]是利用已经有的结果进行跳跃
            for (int j = i + 1; j < length; j+= result[j]) {
                if (T[j] > T[i]) {
                    result[i] = j - i;
                    break;
               } else if (result[j] == 0) { //遇到0表示后面不会有更大的值，那当然当前值就应该也为0
                    result[i] = 0;
                    break;
                }
            }
        }

        return result;
    }
}
```



## 逆波兰表达式求值

根据[逆波兰表示法](https://baike.baidu.com/item/%E9%80%86%E6%B3%A2%E5%85%B0%E5%BC%8F/128437)，求表达式的值。

有效的运算符包括 `+`, `-`, `*`, `/` 。每个运算对象可以是整数，也可以是另一个逆波兰表达式。

**说明：**

- 整数除法只保留整数部分。
- 给定逆波兰表达式总是有效的。换句话说，表达式总会得出有效数值且不存在除数为 0 的情况。

**示例 1：**

```
输入: ["2", "1", "+", "3", "*"]
输出: 9
解释: ((2 + 1) * 3) = 9
```

**示例 2：**

```
输入: ["4", "13", "5", "/", "+"]
输出: 6
解释: (4 + (13 / 5)) = 6
```

**示例 3：**

```
输入: ["10", "6", "9", "3", "+", "-11", "*", "/", "*", "17", "+", "5", "+"]
输出: 22
解释: 
  ((10 * (6 / ((9 + 3) * -11))) + 17) + 5
= ((10 * (6 / (12 * -11))) + 17) + 5
= ((10 * (6 / -132)) + 17) + 5
= ((10 * 0) + 17) + 5
= (0 + 17) + 5
= 17 + 5
= 22
```

思路：这一题只要了解逆波兰式的原理，利用栈的特性就能够很容易的解出来。

``` java
class Solution {
        public int evalRPN(String[] tokens) {
        Stack<Integer> stack = new Stack<>();
        for (String s: tokens) {
            switch (s){
                case "+":
                    stack.push(stack.pop() + stack.pop());
                    break;
                case "-":
                    stack.push(- stack.pop() + stack.pop());
                    break;
                case "*":
                    stack.push(stack.pop() * stack.pop());
                    break;
                case "/":
                    Integer second = stack.pop();
                    Integer first = stack.pop();
                    stack.push(first / second);
                    break;
                default:
                    stack.push(Integer.valueOf(s));
                    break;
            }
        }
        return stack.pop();
    }
}
```

## 岛屿数量

给定一个由 `'1'`（陆地）和 `'0'`（水）组成的的二维网格，计算岛屿的数量。一个岛被水包围，并且它是通过水平方向或垂直方向上相邻的陆地连接而成的。你可以假设网格的四个边均被水包围。

**示例 1:**

```
输入:
11110
11010
11000
00000

输出: 1
```

**示例 2:**

```
输入:
11000
11000
00100
00011

输出: 3
```

**方法一：深度优先搜索**

``` java
class Solution {
    Queue<Integer> queue = new LinkedList<>();
    Set<Integer> used = new HashSet<>();
    public int numIslands(char[][] grid) {
        if(grid==null||grid.length==0){
            return 0;
        }
        int L = grid.length;
        int K = grid[0].length;
        int result = 0;

        for (int i = 0; i < L; i++) {
            for (int j = 0; j < K; j++) {
                if(grid[i][j]=='1'&&!used.contains(j+K*i)){
                    checkSingle(i,j,L,K,grid);
                    result++;
                }
            }
        }
        return result;
    }


    public void checkSingle(int i,int j,int L,int K,char[][] grid){

        if(i<0||i==L||j<0||j==K|| grid[i][j] != '1'){
            return;
        }

        if(grid[i][j]=='1'&&!used.contains(j+K*i)){
            queue.offer(j+K*i);
            used.add(j+K*i);
            checkSingle(i-1,j,L,K,grid);
            checkSingle(i+1,j,L,K,grid);
            checkSingle(i,j-1,L,K,grid);
            checkSingle(i,j+1,L,K,grid);
        }
    }
}
```

**方法二：广度优先搜索**

``` java
class Solution {
  public int numIslands(char[][] grid) {
    if (grid == null || grid.length == 0) {
      return 0;
    }

    int nr = grid.length;
    int nc = grid[0].length;
    int num_islands = 0;

    for (int r = 0; r < nr; ++r) {
      for (int c = 0; c < nc; ++c) {
        if (grid[r][c] == '1') {
          ++num_islands;
          grid[r][c] = '0'; // mark as visited
          Queue<Integer> neighbors = new LinkedList<>();
          neighbors.add(r * nc + c);
          while (!neighbors.isEmpty()) {
            int id = neighbors.remove();
            int row = id / nc;
            int col = id % nc;
            if (row - 1 >= 0 && grid[row-1][col] == '1') {
              neighbors.add((row-1) * nc + col);
              grid[row-1][col] = '0';
            }
            if (row + 1 < nr && grid[row+1][col] == '1') {
              neighbors.add((row+1) * nc + col);
              grid[row+1][col] = '0';
            }
            if (col - 1 >= 0 && grid[row][col-1] == '1') {
              neighbors.add(row * nc + col-1);
              grid[row][col-1] = '0';
            }
            if (col + 1 < nc && grid[row][col+1] == '1') {
              neighbors.add(row * nc + col+1);
              grid[row][col+1] = '0';
            }
          }
        }
      }
    }

    return num_islands;
  }
}
```

## 打开转盘锁

你有一个带有四个圆形拨轮的转盘锁。每个拨轮都有10个数字： `'0', '1', '2', '3', '4', '5', '6', '7', '8', '9'` 。每个拨轮可以自由旋转：例如把 `'9'` 变为  `'0'`，`'0'`变为 `'9'` 。每次旋转都只能旋转一个拨轮的一位数字。

锁的初始数字为 `'0000'` ，一个代表四个拨轮的数字的字符串。

列表 `deadends` 包含了一组死亡数字，一旦拨轮的数字和列表里的任何一个元素相同，这个锁将会被永久锁定，无法再被旋转。

字符串 `target` 代表可以解锁的数字，你需要给出最小的旋转次数，如果无论如何不能解锁，返回 -1。

**示例 1:**

```
输入：deadends = ["0201","0101","0102","1212","2002"], target = "0202"
输出：6
解释：
可能的移动序列为 "0000" -> "1000" -> "1100" -> "1200" -> "1201" -> "1202" -> "0202"。
注意 "0000" -> "0001" -> "0002" -> "0102" -> "0202" 这样的序列是不能解锁的，
因为当拨动到 "0102" 时这个锁就会被锁定。
```

**示例 2:**

```
输入: deadends = ["8888"], target = "0009"
输出：1
解释：
把最后一位反向旋转一次即可 "0000" -> "0009"。
```

**示例 3:**

```
输入: deadends = ["8887","8889","8878","8898","8788","8988","7888","9888"], target = "8888"
输出：-1
解释：
无法旋转到目标数字且不被锁定。
```

**示例 4:**

```
输入: deadends = ["0000"], target = "8888"
输出：-1
```

**思路：**用一个队列保存要遍历的数字，从根节点 ‘0000’ 出发，将下一个遍历节点加到队列中，注意两点：

遍历过的节点放到死亡数字中，避免后续再遇到
遇到给定的数字，直接返回当前深度，由于是一层一层往下遍历，最先遇到的节点肯定是路径最短的

``` java
class Solution {
    public int openLock(String[] deadends, String target) {
        char[] pos = {'0','1','2','3','4','5','6','7','8','9'};
        Set<String> deadSet = new HashSet<>(Arrays.asList(deadends));
        LinkedList<String> list = new LinkedList<>();
        if(target==null||target.length() == 0 || deadSet.contains("0000")){
            return -1;
        }
        list.add("0000");
        int result = 0;
        while(!list.isEmpty()){
            int size = list.size();
            while(size>0){
                String str = list.removeFirst();
                size--;
                if(str.equals(target)){
                    return result;
                }
                if(!deadSet.contains(str)){
                    deadSet.add(str);
                    list.addAll(getNextList(str, pos));
                }
            }
            result++;
        }
        return -1;
    }

    private List<String> getNextList(String str, char[] pos) {
        List<String> list = new ArrayList<>();
        char[] chars = str.toCharArray();
        for (int i = 0; i < chars.length; i++) {
            int x = chars[i]-'0';
            char[] newChar = chars.clone();
            newChar[i] = pos[(x+1)%10];
            list.add(new String(newChar));
            newChar[i] = pos[(x+9)%10];
            list.add(new String(newChar));
        }
        return list;
    }

}
```



## 完全平方数

给定正整数 *n*，找到若干个完全平方数（比如 `1, 4, 9, 16, ...`）使得它们的和等于 *n*。你需要让组成和的完全平方数的个数最少。

**示例 1:**

```
输入: n = 12
输出: 3 
解释: 12 = 4 + 4 + 4.
```

**示例 2:**

```
输入: n = 13
输出: 2
解释: 13 = 4 + 9.
```

``` java
class Solution {
    public int numSquares(int n) {
        LinkedList<Integer> list = new LinkedList<>();
        boolean record[] = new boolean[n];
        int result = 0;
        list.add(n);
        while (!list.isEmpty()){
            int size = list.size();
            while(size>0){
                int val = list.removeFirst();
                size--;
                // 每一层的广度遍历
                for (int i = 1;; i++) {
                    int nextVal = val - i * i;
                    // 说明已到最大平方数
                    if (nextVal < 0)
                        break;

                    // 由于是广度遍历，所以当遍历到0时，肯定是最短路径
                    if(nextVal == 0)
                        return ++result;

                    // 当再次出现时没有必要加入，因为在该节点的路径长度肯定不小于第一次出现的路径长
                    if(!record[nextVal]){
                        list.add(nextVal);
                        record[nextVal] = true;   //提升效率的关键
                    }
                }
            }
            result++;
        }
        return -1;
    }

}
```



## 克隆图

给定无向[**连通**](https://baike.baidu.com/item/%E8%BF%9E%E9%80%9A%E5%9B%BE/6460995?fr=aladdin)图中一个节点的引用，返回该图的[**深拷贝**](https://baike.baidu.com/item/%E6%B7%B1%E6%8B%B7%E8%B4%9D/22785317?fr=aladdin)（克隆）。图中的每个节点都包含它的值 `val`（`Int`） 和其邻居的列表（`list[Node]`）。

**示例：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2019/02/23/113_sample.png)

```
输入：
{"$id":"1","neighbors":[{"$id":"2","neighbors":[{"$ref":"1"},{"$id":"3","neighbors":[{"$ref":"2"},{"$id":"4","neighbors":[{"$ref":"3"},{"$ref":"1"}],"val":4}],"val":3}],"val":2},{"$ref":"4"}],"val":1}

解释：
节点 1 的值是 1，它有两个邻居：节点 2 和 4 。
节点 2 的值是 2，它有两个邻居：节点 1 和 3 。
节点 3 的值是 3，它有两个邻居：节点 2 和 4 。
节点 4 的值是 4，它有两个邻居：节点 1 和 3 。
```

**提示：**

1. 节点数介于 1 到 100 之间。
2. 无向图是一个[简单图](https://baike.baidu.com/item/%E7%AE%80%E5%8D%95%E5%9B%BE/1680528?fr=aladdin)，这意味着图中没有重复的边，也没有自环。
3. 由于图是无向的，如果节点 *p* 是节点 *q* 的邻居，那么节点 *q* 也必须是节点 *p* 的邻居。
4. 必须将**给定节点的拷贝**作为对克隆图的引用返回。

**1.深度优先遍历**

``` java
	public Node cloneGraph(Node node) {
        Map<Node, Node> lookup = new HashMap<>();
        return dfs(node, lookup);
    }

    private Node dfs(Node node, Map<Node,Node> lookup) {
        if (node == null) return null;
        if (lookup.containsKey(node)) return lookup.get(node);
        Node clone = new Node(node.val, new ArrayList<>());
        lookup.put(node, clone);
        for (Node n : node.neighbors)clone.neighbors.add(dfs(n,lookup));
        return clone;
    }
```

**2.广度优先遍历**

``` java
	public Node cloneGraph(Node node) {
        if (node == null) return null;
        Map<Node, Node> lookup = new HashMap<>();
        Node clone = new Node(node.val, new ArrayList<>());
        lookup.put(node, clone);
        Deque<Node> queue = new LinkedList<>();
        queue.offer(node);
        while (!queue.isEmpty()) {
            Node tmp = queue.poll();
            for (Node n : tmp.neighbors) {
                if (!lookup.containsKey(n)) {
                    lookup.put(n, new Node(n.val, new ArrayList<>()));
                    queue.offer(n);
                }
                lookup.get(tmp).neighbors.add(lookup.get(n));
            }
        }
        return clone;
    }
```



## 目标和

给定一个非负整数数组，a1, a2, ..., an, 和一个目标数，S。现在你有两个符号 `+` 和 `-`。对于数组中的任意一个整数，你都可以从 `+` 或 `-`中选择一个符号添加在前面。

返回可以使最终数组和为目标数 S 的所有添加符号的方法数。

**示例 1:**

```
输入: nums: [1, 1, 1, 1, 1], S: 3
输出: 5
解释: 

-1+1+1+1+1 = 3
+1-1+1+1+1 = 3
+1+1-1+1+1 = 3
+1+1+1-1+1 = 3
+1+1+1+1-1 = 3

一共有5种方法让最终目标和为3。
```

**1.暴力递归**

``` java
class Solution {
        public int findTargetSumWays(int[] nums, int S) {
        if(nums==null||nums.length==0){
            return 0;
        }
        int sum = 0;
        for(int num : nums){
            sum += num;
        }
        if(sum < S || (S+sum)%2 != 0){
            return 0;
        }
        int L = nums.length;
        int count=0;
        int result = funTargetSumWays(nums,S,0,L,count);
        return result;
    }

    private int funTargetSumWays(int[] nums, int s, int i, int l,int count) {
        if(i==l-1){
            if((count+nums[i])==s&&(count-nums[i])==s){
                return 2;
            }else if((count-nums[i])==s){
                return 1;
            }else if((count+nums[i])==s){
                return 1;
            }else {
                return 0;
            }
        }
        return funTargetSumWays(nums,s,i+1,l,(count+nums[i]))+
            funTargetSumWays(nums,s,i+1,l,(count-nums[i]));

    }
}
```

**2.动态规划**

动态规划转移方程：

当我们用之前若干个数有c种方法凑出s的时候 拿出下一个数n 那么我们就有c种方法凑出s-n和s+n

``` java
class Solution {
     public static int findTargetSumWays(int[] nums, int S) {
        int sum = 0;
        for (int num : nums) {
            sum += num;
        }
        if (sum < S || (sum + S) % 2 == 1) {
            return 0;
        }
        int w = (sum + S) / 2;
        int[] dp = new int[w + 1];
        dp[0] = 1;
        for (int num : nums) {
            for (int j = w; j >= num; j--) {
                dp[j] += dp[j - num];
            }
        }
        return dp[w];
    }
}
```



## 二叉树的中序遍历

给定一个二叉树，返回它的*中序* 遍历。

**示例:**

```
输入: [1,null,2,3]
   1
    \
     2
    /
   3

输出: [1,3,2]
```

1. **递归算法**

``` java
	List<Integer> inorder = new ArrayList<>();
    public List<Integer> inorderTraversal(Solution.TreeNode root) {
        if(root!=null){
            inorderTraversal(root.left);
            inorder.add(root.val);
            inorderTraversal(root.right);
        }
        return inorder;
    }
```

2. **基于栈的迭代方法**

``` java
public class Solution {
    public List < Integer > inorderTraversal(TreeNode root) {
        List < Integer > res = new ArrayList < > ();
        Stack < TreeNode > stack = new Stack < > ();
        TreeNode curr = root;
        while (curr != null || !stack.isEmpty()) {
            while (curr != null) {
                stack.push(curr);
                curr = curr.left;
            }
            curr = stack.pop();
            res.add(curr.val);
            curr = curr.right;
        }
        return res;
    }
}
```

## **用栈实现队列**

使用栈实现队列的下列操作：

- push(x) -- 将一个元素放入队列的尾部。
- pop() -- 从队列首部移除元素。
- peek() -- 返回队列首部的元素。
- empty() -- 返回队列是否为空。

**示例:**

```
MyQueue queue = new MyQueue();

queue.push(1);
queue.push(2);  
queue.peek();  // 返回 1
queue.pop();   // 返回 1
queue.empty(); // 返回 false
```

class MyQueue {

    Stack<Integer> s1;
    Stack<Integer> s2;
    int front;
    
    /** Initialize your data structure here. */
    public MyQueue() {
        s1 = new Stack<>();
        s2 = new Stack<>();
    }
    
    /** Push element x to the back of queue. */
    public void push(int x) {
        if (s1.empty())
            front = x;
        s1.push(x);
    }


    /** Removes the element from in front of queue and returns that element. */
    public int pop() {
        if (s2.isEmpty()) {
            while (!s1.isEmpty())
                s2.push(s1.pop());
        }
        return s2.pop();
    }


    /** Get the front element. */
    public int peek() {
        if (!s2.isEmpty()) {
            return s2.peek();
        }
        return front;
    }
    
    /** Returns whether the queue is empty. */
    public boolean empty() {
        return s1.isEmpty()&&s2.isEmpty();
    }
}



## 字符串解码

给定一个经过编码的字符串，返回它解码后的字符串。

编码规则为: `k[encoded_string]`，表示其中方括号内部的 *encoded_string* 正好重复 *k* 次。注意 *k* 保证为正整数。

你可以认为输入字符串总是有效的；输入字符串中没有额外的空格，且输入的方括号总是符合格式要求的。

此外，你可以认为原始数据不包含数字，所有的数字只表示重复的次数 *k* ，例如不会出现像 `3a` 或 `2[4]` 的输入。

**示例:**

```
s = "3[a]2[bc]", 返回 "aaabcbc".
s = "3[a2[c]]", 返回 "accaccacc".
s = "2[abc]3[cd]ef", 返回 "abcabccdcdcdef".
```

``` java
class Solution {
    public String decodeString(String s) {
        StringBuilder res = new StringBuilder();
        Stack<Integer> numStack = new Stack<>();
        Stack<String> strStack = new Stack<>();
        String tempStr = null;
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (s.charAt(i) == ']') {
                String str = strStack.pop();
                int num = numStack.pop();
                String nowStr = repeatStr(str, num);
                if (!numStack.isEmpty()) {
                    StringBuilder  builder = new StringBuilder();
                    builder.append(strStack.peek());
                    builder.append(nowStr);
                    int m = i + 1;
                    while (s.charAt(m) != ']' && !('0' < s.charAt(m) && '9' >= s.charAt(m))) {
                        m++;
                    }
                    builder.append(s.substring(i + 1, m));
                    strStack.set(strStack.size() - 1, builder.toString());
                    i = m - 1;
                } else {
                    tempStr = null;
                    res.append(nowStr);
                }
            } else if ('0' <= c && '9' >= c) {
                int m = i + 1;
                while ('0' <= s.charAt(m) && '9' >= s.charAt(m)) {
                    m++;
                }
                numStack.push(Integer.parseInt(s.substring(i, m)));
                i = m - 1;
                int k =  i + 2;
                while (s.charAt(k) != ']' && !('0' <= s.charAt(k) && '9' >= s.charAt(k)))  {
                    k++;
                }
                strStack.push(s.substring(i+2, k));
                i = k - 1;
            } else if (numStack.isEmpty()) {
                res.append(s.charAt(i));
            }
        }
        return res.toString();
    }

    private String repeatStr(String str, int num) {
        StringBuilder sb = new StringBuilder();
        if (num <= 0) {
            return "";
        }
        for (int i = 0; i < num; i++) {
            sb.append(str);
        }
        return sb.toString();
    }
}
```





## 图像渲染

有一幅以二维整数数组表示的图画，每一个整数表示该图画的像素值大小，数值在 0 到 65535 之间。

给你一个坐标 `(sr, sc)` 表示图像渲染开始的像素值（行 ，列）和一个新的颜色值 `newColor`，让你重新上色这幅图像。

为了完成上色工作，从初始坐标开始，记录初始坐标的上下左右四个方向上像素值与初始坐标相同的相连像素点，接着再记录这四个方向上符合条件的像素点与他们对应四个方向上像素值与初始坐标相同的相连像素点，……，重复该过程。将所有有记录的像素点的颜色值改为新的颜色值。

最后返回经过上色渲染后的图像。

**示例 1:**

```
输入: 
image = [[1,1,1],[1,1,0],[1,0,1]]
sr = 1, sc = 1, newColor = 2
输出: [[2,2,2],[2,2,0],[2,0,1]]
解析: 
在图像的正中间，(坐标(sr,sc)=(1,1)),
在路径上所有符合条件的像素点的颜色都被更改成2。
注意，右下角的像素没有更改为2，
因为它不是在上下左右四个方向上与初始点相连的像素点。
```

**深度优先遍历DFS**

``` java
class Solution {
    public int[][] floodFill(int[][] image, int sr, int sc, int newColor) {
        if(image==null||image.length==0){
            return image;
        }
        int oldColor = image[sr][sc];
        if(oldColor==newColor){
            return image;
        }
        int L = image.length;
        int K = image[0].length;
        expansion(image,sr,sc,L,K,oldColor,newColor);
        return image;
    }

    private void expansion(int[][] image, int i, int j,int L,int K, int oldColor, int newColor) {
        if(i<0||i==L||j<0||j==K|| image[i][j] != oldColor){
            return;
        }

        if(image[i][j]==oldColor){
            image[i][j]=newColor;
            expansion(image,i-1,j,L,K,oldColor,newColor);
            expansion(image,i+1,j,L,K,oldColor,newColor);
            expansion(image,i,j-1,L,K,oldColor,newColor);
            expansion(image,i,j+1,L,K,oldColor,newColor);
        }
    }
}
```



## 01 矩阵



给定一个由 0 和 1 组成的矩阵，找出每个元素到最近的 0 的距离。

两个相邻元素间的距离为 1 。

**示例 1:** 
输入:

```
0 0 0
0 1 0
0 0 0
```

输出:

```
0 0 0
0 1 0
0 0 0
```

**示例 2:** 
输入:

```
0 0 0
0 1 0
1 1 1
```

输出:

```
0 0 0
0 1 0
1 2 1
```

1. **深度优先遍历**

必须要考虑递归的深度，减少递归的次数才能AC

``` java
class Solution {
    private int row;
    private int col;
    private int[][] vector = new int[][]{{0, 1}, {0, -1}, {1, 0}, {-1, 0}};
    /**
     * DFS（递归实现，优化版，可 AC）
     */
    public int[][] updateMatrix(int[][] matrix) {
        row = matrix.length;
        col = matrix[0].length;
        for (int i = 0; i < row; i++) {
            for (int j = 0; j < col; j++) {
                // 优化：如果元素在 0 附近，保留元素值 1，不在 0 附近，初始化为一个较大值
                if (matrix[i][j] == 1
                        && !((i > 0 && matrix[i - 1][j] == 0)
                        || (i < row - 1 && matrix[i + 1][j] == 0)
                        || (j > 0 && matrix[i][j - 1] == 0)
                        || (j < col - 1 && matrix[i][j + 1] == 0))) {
                    matrix[i][j] = row + col;
                }
            }
        }
        for (int i = 0; i < matrix.length; i++) {
            for (int j = 0; j < matrix[0].length; j++) {
                // 优化：将元素值为 1 的点作为深搜起点，降低递归深度
                if (matrix[i][j] == 1) {
                    updateMatrix(matrix, i, j);
                }
            }
        }
        return matrix;
    }

    /**
     * DFS 递归函数
     */
    private void updateMatrix(int[][] matrix, int r, int c) {
        // 搜索上下左右四个方向
        for (int[] v : vector) {
            int nr = r + v[0], nc = c + v[1];
            if (nr >= 0 && nr < row
                    && nc >= 0 && nc < col
                    && matrix[nr][nc] > matrix[r][c] + 1) {
                matrix[nr][nc] = matrix[r][c] + 1;
                updateMatrix(matrix, nr, nc);
            }
        }
    }
}
```

2. **广度优先遍历**

首先，既然是使用广度优先算法，自然想到了借助队列实现。需要进行第一次遍历，初始化队列，找出数组中所有值为0的坐标，并将其保存至队列中，若值非0，则赋予最大值10001（因为数组最多有10000个元素）。当该次遍历结束后，数组中原来为0的值保持不变，原来为1的值被改为最大值10001，队列中保存有所有原来值为0的坐标，这是第一批次的坐标节点。

然后，对队列中的节点按批次进行处理，遍历队列中的节点，进行处理，处理方法为：再次遍历该节点的上下左右四个合法的邻居节点，若邻居节点的值为最大值10001，则说明该邻居节点未曾处理，将其添加到队列中，等待下一批次处理，并计算出四个邻居节点和自身节点的最小值min，最后进行赋值。

当队列为空时，全部节点遍历完毕，得出最终答案。

``` java
class Solution {
    
    private int[][] neighborVals = {{-1,0}, {1,0}, {0,-1}, {0,1}};
    
    private Queue<int[]> queue = new LinkedList<>();
    
    public int[][] updateMatrix(int[][] matrix) {
        for(int i = 0; i < matrix.length; i++){
            for(int j = 0; j < matrix[0].length; j++){
                if(matrix[i][j] == 0)
                    queue.offer(new int[]{i, j});
                else 
                    matrix[i][j] = 10001;
            }
        }
        while(!queue.isEmpty()){
            int len = queue.size();
            while(len > 0){
                len--;
                int[] temp = queue.poll();
                neighbor(matrix, temp[0], temp[1]);
            }
        }
        return matrix;
    }
    
    // 处理邻居节点
    private void neighbor(int[][] matrix, int x, int y){
        int min = matrix[x][y];
        // 先通过遍历该节点的邻居节点，找到最小邻居值并将未使用过的邻居节点添加至队列中
        for(int[] neighborVal : neighborVals){
            int xx = x + neighborVal[0];
            int yy = y + neighborVal[1];
            if(xx >= 0 && xx < matrix.length && yy >= 0 && yy < matrix[0].length){
                min = Math.min(matrix[xx][yy]+1, min);
                if(matrix[xx][yy] == 10001)
                    queue.offer(new int[]{xx, yy});
            }
        }
        matrix[x][y] = min;
    }
}
```

3. **动态规划**

``` java
class Solution {
    public int[][] updateMatrix(int[][] matrix) {
        int row = matrix.length;
        int col = 0;
        if(row > 0){
            col = matrix[0].length;
        }
        for(int i = 0 ; i < row ;i++){
            for(int j = 0 ; j < col;j++){
                if(matrix[i][j] == 1){
                     dfsleft(matrix,row,col,i,j);
                }
            }
        }
        for(int i = row-1; i >=0 ;i--){
            for(int j = col-1 ; j >=0;j--){
                if(matrix[i][j] > 0){
                     dfsright(matrix,row,col,i,j);
                }
            }
        }
        return matrix;
    }
    private static void dfsleft(int[][] matrix,int row,int col,int x,int y){
        int min = row+col;
        if(x-1 >= 0){
            min = Math.min(matrix[x-1][y]+1,min);
        } 
        if(y-1 >= 0){
            min = Math.min(matrix[x][y-1]+1,min);
        }
        matrix[x][y] = min;
    }
    private static void dfsright(int[][] matrix,int row,
                                 int col,int x,int y){
        int min = matrix[x][y];
        if(x+1 < row){
            min = Math.min(matrix[x+1][y]+1,min);
        } 
        if(y+1 < col){
            min = Math.min(matrix[x][y+1]+1,min);
        }
        matrix[x][y] = min;
    }
}
```

