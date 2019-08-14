# LeetCode 算法总结2

## 钥匙和房间

有 `N` 个房间，开始时你位于 `0` 号房间。每个房间有不同的号码：`0，1，2，...，N-1`，并且房间里可能有一些钥匙能使你进入下一个房间。

在形式上，对于每个房间 `i` 都有一个钥匙列表 `rooms[i]`，每个钥匙 `rooms[i][j]` 由 `[0,1，...，N-1]` 中的一个整数表示，其中 `N = rooms.length`。 钥匙 `rooms[i][j] = v`可以打开编号为 `v` 的房间。

最初，除 `0` 号房间外的其余所有房间都被锁住。

你可以自由地在房间之间来回走动。

如果能进入每个房间返回 `true`，否则返回 `false`。

**示例 1：**

```
输入: [[1],[2],[3],[]]
输出: true
解释:  
我们从 0 号房间开始，拿到钥匙 1。
之后我们去 1 号房间，拿到钥匙 2。
然后我们去 2 号房间，拿到钥匙 3。
最后我们去了 3 号房间。
由于我们能够进入每个房间，我们返回 true。
```

**示例 2：**

```
输入：[[1,3],[3,0,1],[2],[0]]
输出：false
解释：我们不能进入 2 号房间。
```

**广度优先遍历**

``` java
class Solution {
    public boolean canVisitAllRooms(List<List<Integer>> rooms) {
        LinkedList<Integer> list = new LinkedList<>();
        boolean[] visited = new boolean[rooms.size()];
        if(rooms==null||rooms.size()==0){
            return true;
        }
        visited[0] = true;
        int count = 1;
        list.addAll(rooms.get(0));
        while(!list.isEmpty()){
            int room = list.poll();
            if(!visited[room]){
                visited[room] = true;
                count++;
                list.addAll(rooms.get(room));
            }
        }
        return count==rooms.size();
    }
}
```



## 寻找数组的中心索引

给定一个整数类型的数组 `nums`，请编写一个能够返回数组**“中心索引”**的方法。

我们是这样定义数组**中心索引**的：数组中心索引的左侧所有元素相加的和等于右侧所有元素相加的和。

如果数组不存在中心索引，那么我们应该返回 -1。如果数组有多个中心索引，那么我们应该返回最靠近左边的那一个。

**示例 1:**

```
输入: 
nums = [1, 7, 3, 6, 5, 6]
输出: 3
解释: 
索引3 (nums[3] = 6) 的左侧数之和(1 + 7 + 3 = 11)，与右侧数之和(5 + 6 = 11)相等。
同时, 3 也是第一个符合要求的中心索引。
```

**示例 2:**

```
输入: 
nums = [1, 2, 3]
输出: -1
解释: 
数组中不存在满足此条件的中心索引。
```

``` java
class Solution {
    public int pivotIndex(int[] nums) {
        int sum = 0;
        for (int a : nums
        ) {
            sum += a;
        }
        int left = 0;
        int right = sum;
        for (int i = 0; i < nums.length; i++) {
            if (left==right-nums[i]){
                return i;
            }
            left+=nums[i];
            right -= nums[i];
        }
        return -1;
    }
}
```

## 至少是其他数字两倍的最大数

在一个给定的数组`nums`中，总是存在一个最大元素 。

查找数组中的最大元素是否至少是数组中每个其他数字的两倍。

如果是，则返回最大元素的索引，否则返回-1。

**示例 1:**

```
输入: nums = [3, 6, 1, 0]
输出: 1
解释: 6是最大的整数, 对于数组中的其他整数,
6大于数组中其他元素的两倍。6的索引是1, 所以我们返回1.
```

**示例 2:**

```
输入: nums = [1, 2, 3, 4]
输出: -1
解释: 4没有超过3的两倍大, 所以我们返回 -1.
```

**一次遍历，得到最大次大**

``` java
class Solution {
    public int dominantIndex(int[] nums) {
        if(nums==null||nums.length==0){
            return -1;
        }
        if(nums.length==1){
            return 0;
        }
        int max1;
        int max2;
        int index;
        if(nums[0]>=nums[1]){
            max1=nums[0];
            max2=nums[1];
            index=0;
        }else {
            max1=nums[1];
            max2=nums[0];
            index=1;
        }
        
        for (int i = 2; i < nums.length; i++) {
            int temp = max1;
            max2 = Math.max(max2,nums[i]);
            if(max2>=max1){
                max1=max2;
                max2=temp;
                index=i;
            }
        }
        if(max1>=max2*2){
            return index;
        }
        return -1;
    }
}
```



## 加一

给定一个由**整数**组成的**非空**数组所表示的非负整数，在该数的基础上加一。

最高位数字存放在数组的首位， 数组中每个元素只存储**单个**数字。

你可以假设除了整数 0 之外，这个整数不会以零开头。

**示例 1:**

```
输入: [1,2,3]
输出: [1,2,4]
解释: 输入数组表示数字 123。
```

**示例 2:**

```
输入: [4,3,2,1]
输出: [4,3,2,2]
解释: 输入数组表示数字 4321。
```

``` java
class Solution {
    public int[] plusOne(int[] digits) {
        if(digits==null||digits.length==0){
            return digits;
        }
        for (int i = digits.length-1; i >=0; i--) {
            if(digits[i]>=0&&digits[i]<=8){
                digits[i]++;
                return digits;
            }else{
                digits[i]=0;
            }
        }
        int[] result = new int[digits.length+1];
        result[0]=1;
        for (int i = 1; i < digits.length+1 ; i++) {
            result[i]=digits[i-1];
        }
        return result;
    }
}
```



## 对角线遍历

给定一个含有 M x N 个元素的矩阵（M 行，N 列），请以对角线遍历的顺序返回这个矩阵中的所有元素，对角线遍历如下图所示。

 

**示例:**

```
输入:
[
 [ 1, 2, 3 ],
 [ 4, 5, 6 ],
 [ 7, 8, 9 ]
]

输出:  [1,2,4,7,5,3,6,8,9]
```

**简单思路：**

``` java
public int[] findDiagonalOrder(int[][] matrix) {
        if(matrix==null||matrix.length==0){
            return new int[0];
        }
        int M = matrix.length;
        int N = matrix[0].length;
        int[] result = new int[M*N];
        int x = 0;

        for (int i = 0; i < M+N-1; i++) {
            if(i%2==1){
                int a=0;
                int b=i;
                while (a<=M+N-1&&b>=0){
                    if(b<=N-1&&a<=M-1){
                        result[x] = matrix[a][b];
                        x++;
                    }
                    a++;
                    b--;
                }
            }else {
                int a=i;
                int b=0;
                while (a>=0&&b<=M+N-1){
                    if(b<=N-1&&a<=M-1){
                        result[x] = matrix[a][b];
                        x++;
                    }
                    a--;
                    b++;
                }
            }
        }
        return result;
        
    }
```

**复杂思路：**

``` java
class Solution {
    public int[] findDiagonalOrder(int[][] matrix) {
        if (null == matrix) return null;
        if (matrix.length == 0) return new int[0];
        if (matrix.length == 1) return matrix[0];

        int a = matrix[0].length;
        int b = matrix.length;
        int size = a + b - 1;
        int[] result = new int[a * b];
        int index = 0;
        boolean up = false;
        result[0] = matrix[0][0];
        index = 1;
        for (int i = 1; i < size; i++) {
            if (i <= a - 1) {
                if (!up) {
                    for (int j = 0; j <= i && j < b; j++) {
                        result[index++] = matrix[j][i - j];
                    }
                    up = !up;
                } else {
                    for (int j = Math.min(i, b - 1); j >= 0; j--) {
                        result[index++] = matrix[j][i - j];
                    }

                    up = !up;
                }
            }

            if (i > a - 1) {
                int temp = i + 1 - a;
                if (!up) {
                    for (int j = temp; j <= i && j < b; j++) {
                        result[index++] = matrix[j][i - j];
                    }
                    up = !up;
                } else {
                    for (int j = Math.min(i, b - 1); j >= temp; j--) {
                        result[index++] = matrix[j][i - j];

                    }
                    up = !up;
                }
            }
        }

        return result;
    }
}
```


## 螺旋矩阵

给定一个包含 *m* x *n* 个元素的矩阵（*m* 行, *n* 列），请按照顺时针螺旋顺序，返回矩阵中的所有元素。

**示例 1:**

```
输入:
[
 [ 1, 2, 3 ],
 [ 4, 5, 6 ],
 [ 7, 8, 9 ]
]
输出: [1,2,3,6,9,8,7,4,5]
```

**示例 2:**

```
输入:
[
  [1, 2, 3, 4],
  [5, 6, 7, 8],
  [9,10,11,12]
]
输出: [1,2,3,4,8,12,11,10,9,5,6,7]
```

``` java
class Solution {
    List<Integer> result = new ArrayList<>();
    public List<Integer> spiralOrder(int[][] matrix) {
        if(matrix==null||matrix.length==0){
            return result;
        }
        int M = matrix.length;
        int N = matrix[0].length;
        spiralMatrix(0,0,M-1,N-1,matrix);
        return result;
    }

    private void spiralMatrix(int i, int j, int m, int n,int[][] matrix) {
        if(i==m&&j==n){
            result.add(matrix[i][j]);
            return;
        }
        if(i==m){
            for (int b = j; b <= n; b++) {
                result.add(matrix[i][b]);
            }
            return;
        }
        if(j==n){
            for (int a = i; a <= m; a++) {
                result.add(matrix[a][n]);
            }
            return;
        }
        if(i>m||j>n){
            return;
        }
        for (int b = j; b <= n; b++) {
            result.add(matrix[i][b]);
        }
        for (int a = i+1; a <= m; a++) {
            result.add(matrix[a][n]);
        }
        for (int b = n-1; b >= j; b--) {
            result.add(matrix[m][b]);
        }
        for (int a = m-1; a > i; a--) {
            result.add(matrix[a][j]);
        }
        spiralMatrix(i+1,j+1,m-1,n-1,matrix);

    }
}
```



## 二进制求和

给定两个二进制字符串，返回他们的和（用二进制表示）。

输入为**非空**字符串且只包含数字 `1` 和 `0`。

**示例 1:**

```
输入: a = "11", b = "1"
输出: "100"
```

**示例 2:**

```
输入: a = "1010", b = "1011"
输出: "10101"
```

``` java
class Solution {
    public String addBinary(String a, String b) {
        StringBuilder ans = new StringBuilder();
        int ca = 0;
        for(int i = a.length() - 1, j = b.length() - 1;i >= 0 || j >= 0; i--, j--) {
            int sum = ca;
            sum += i >= 0 ? a.charAt(i) - '0' : 0;
            sum += j >= 0 ? b.charAt(j) - '0' : 0;
            ans.append(sum % 2);
            ca = sum / 2;
        }
        ans.append(ca == 1 ? ca : "");
        return ans.reverse().toString();
    }
}
```



## 实现 strStr()

实现 [strStr()](https://baike.baidu.com/item/strstr/811469) 函数。

给定一个 haystack 字符串和一个 needle 字符串，在 haystack 字符串中找出 needle 字符串出现的第一个位置 (从0开始)。如果不存在，则返回  **-1**。

**示例 1:**

```
输入: haystack = "hello", needle = "ll"
输出: 2
```

**示例 2:**

```
输入: haystack = "aaaaa", needle = "bba"
输出: -1
```

``` java
class Solution {
    public int strStr(String haystack, String needle) {
        if(needle==null||haystack.equals(needle)||needle.equals("")){
            return 0;
        }
        char[] hay = haystack.toCharArray();
        char[] need = needle.toCharArray();
        boolean result;
        for (int i = 0; i < hay.length-need.length+1; i++) {
            if(hay[i]==need[0]){
                result=true;
                for (int j = 0; j < need.length; j++) {
                    if(hay[i+j]!=need[j])
                        result=false;
                }
                if(result){
                    return i;
                }
            }
        }
        return -1;
    }
}
```



## 最长公共前缀

编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 `""`。

**示例 1:**

```
输入: ["flower","flow","flight"]
输出: "fl"
```

**示例 2:**

```
输入: ["dog","racecar","car"]
输出: ""
解释: 输入不存在公共前缀。
```

``` java
public String longestCommonPrefix(String[] strs) {
        if(strs==null||strs.length==0){
            return "";
        }
        if(strs.length==1){
            return strs[0];
        }
        StringBuilder result = new StringBuilder();
        int min = strs[0].length();
        for (int i = 0; i < strs.length; i++) {
            min  = Math.min(min,strs[i].length());
        }
        for (int i = 0; i < min; i++) {
            if(i>=strs[0].length()){
                break;
            }
            char x = strs[0].charAt(i);
            boolean test = true;
            for (int j = 0; j < strs.length; j++) {
                if(strs[j].charAt(i)!=x){
                    test=false;
                    break;
                }
            }
            if(test){
                result.append(x);
            }else {
                break;
            }
        }
        return result.toString();
    }
```



##  数组拆分 I

给定长度为 **2n** 的数组, 你的任务是将这些数分成 **n** 对, 例如 (a1, b1), (a2, b2), ..., (an, bn) ，使得从1 到 n 的 min(ai, bi) 总和最大。

**示例 1:**

```
输入: [1,4,3,2]

输出: 4
解释: n 等于 2, 最大总和为 4 = min(1, 2) + min(3, 4).
```

``` java
class Solution {
    public int arrayPairSum(int[] nums) {
        Arrays.sort(nums);
        int result = 0;
        for (int i = 0; i < nums.length; i+=2) {
            result+=nums[i];
        }
        return result;
    }
}
```



## 两数之和 II - 输入有序数组

给定一个已按照**升序排列** 的有序数组，找到两个数使得它们相加之和等于目标数。

函数应该返回这两个下标值 index1 和 index2，其中 index1 必须小于 index2*。*

**说明:**

- 返回的下标值（index1 和 index2）不是从零开始的。
- 你可以假设每个输入只对应唯一的答案，而且你不可以重复使用相同的元素。

**示例:**

```
输入: numbers = [2, 7, 11, 15], target = 9
输出: [1,2]
解释: 2 与 7 之和等于目标数 9 。因此 index1 = 1, index2 = 2 。
```

``` java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        if(numbers==null||numbers.length<=1){
            return new int[2];
        }
        int index1 = 0;
        int index2 = numbers.length-1;
        for (int i = index1; i < index2; i++) {
            if((numbers[i]+numbers[index2])==target){
                index1=i;
                break;
            }else if((numbers[i]+numbers[index2])>target){
                while ((numbers[i]+numbers[index2])>target){
                    index2--;
                }
                if((numbers[i]+numbers[index2])==target) {
                    index1=i;
                    break;
                }
            }

        }
        return new int[]{index1+1,index2+1};
    }
}
```



## 移除元素

给定一个数组 *nums* 和一个值 *val*，你需要**原地**移除所有数值等于 *val* 的元素，返回移除后数组的新长度。

不要使用额外的数组空间，你必须在**原地修改输入数组**并在使用 O(1) 额外空间的条件下完成。

元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

**示例 1:**

```
给定 nums = [3,2,2,3], val = 3,

函数应该返回新的长度 2, 并且 nums 中的前两个元素均为 2。

你不需要考虑数组中超出新长度后面的元素。
```

**示例 2:**

```
给定 nums = [0,1,2,2,3,0,4,2], val = 2,

函数应该返回新的长度 5, 并且 nums 中的前五个元素为 0, 1, 3, 0, 4。

注意这五个元素可为任意顺序。

你不需要考虑数组中超出新长度后面的元素。
```

``` java
class Solution {
    public int removeElement(int[] nums, int val) {
        int i = 0;
        for (int j = 0; j < nums.length; j++) {
            if (nums[j] != val) {
                nums[i] = nums[j];
                i++;
            }
        }
        return i;
    }
}
```



## 最大连续1的个数

给定一个二进制数组， 计算其中最大连续1的个数。

**示例 1:**

```
输入: [1,1,0,1,1,1]
输出: 3
解释: 开头的两位和最后的三位都是连续1，所以最大连续1的个数是 3.
```

``` java
class Solution {
    public int findMaxConsecutiveOnes(int[] nums) {
        int temp = 0;
        int result = 0;
        for (int i = 0; i < nums.length; i++) {
            if(nums[i]==1){
                temp++;
            }
            if(nums[i]==0&&temp!=0){
                result=Math.max(temp,result);
                temp=0;
            }
        }
        result=Math.max(temp,result);
        return result;
    }
}
```



## 长度最小的子数组

给定一个含有 **n** 个正整数的数组和一个正整数 **s ，**找出该数组中满足其和 **≥ s** 的长度最小的连续子数组**。**如果不存在符合条件的连续子数组，返回 0。

**示例:** 

```
输入: s = 7, nums = [2,3,1,2,4,3]
输出: 2
解释: 子数组 [4,3] 是该条件下的长度最小的连续子数组。
```

**进阶:**

如果你已经完成了*O*(*n*) 时间复杂度的解法, 请尝试 *O*(*n* log *n*) 时间复杂度的解法。

``` java
class Solution {
    public int minSubArrayLen(int s, int[] nums) {
        int ans = Integer.MAX_VALUE;
        int left = 0;
        int sum = 0;
        for (int i = 0; i < nums.length; i++) {
            sum+=nums[i];
            while(sum>=s){
                ans = Math.min(ans,i+1-left);
                sum-=nums[left++];
            }
        }
        return ans!=Integer.MAX_VALUE?ans:0;
    }
}
```

## 旋转数组

给定一个数组，将数组中的元素向右移动 *k* 个位置，其中 *k* 是非负数。

**示例 1:**

```
输入: [1,2,3,4,5,6,7] 和 k = 3
输出: [5,6,7,1,2,3,4]
解释:
向右旋转 1 步: [7,1,2,3,4,5,6]
向右旋转 2 步: [6,7,1,2,3,4,5]
向右旋转 3 步: [5,6,7,1,2,3,4]
```

**示例 2:**

```
输入: [-1,-100,3,99] 和 k = 2
输出: [3,99,-1,-100]
解释: 
向右旋转 1 步: [99,-1,-100,3]
向右旋转 2 步: [3,99,-1,-100]
```

1. 旋转K次

``` java
class Solution {
    public void rotate(int[] nums, int k) {
        if(k==0||k%nums.length==0){
            return;
        }
        int x = k%nums.length;
        for (int i = 0; i < x; i++) {
            rotateNums(nums);
        }
        
    }

    private void rotateNums(int[] nums) {
        int i = nums[nums.length-1];
        for (int j = nums.length-1; j >0; j--) {
            nums[j]=nums[j-1];
        }
        nums[0]=i;
    }
}
```

2. 使用环状替代

``` java
public class Solution {
    public void rotate(int[] nums, int k) {
        k = k % nums.length;
        int count = 0;
        for (int start = 0; count < nums.length; start++) {
            int current = start;
            int prev = nums[start];
            do {
                int next = (current + k) % nums.length;
                int temp = nums[next];
                nums[next] = prev;
                prev = temp;
                current = next;
                count++;
            } while (start != current);
        }
    }
}
```

## 翻转字符串里的单词

给定一个字符串，逐个翻转字符串中的每个单词。

**示例 1：**

```
输入: "the sky is blue"
输出: "blue is sky the"
```

**示例 2：**

```
输入: "  hello world!  "
输出: "world! hello"
解释: 输入字符串可以在前面或者后面包含多余的空格，但是反转后的字符不能包括。
```

**示例 3：**

```
输入: "a good   example"
输出: "example good a"
解释: 如果两个单词间有多余的空格，将反转后单词间的空格减少到只含一个。
```

``` java
class Solution {
    public String reverseWords(String s) {
        if(s.trim().equals("")){
            return s.trim();
        }
        String str[] = s.trim().split(" ");
        StringBuilder result = new StringBuilder();
        for (int i = str.length-1; i >=0; i--) {
            if(!str[i].trim().equals("")){
                result.append(str[i]);
                result.append(" ");
            }
        }
        return result.toString().trim();
    }
}
```



## 反转字符串中的单词 III

给定一个字符串，你需要反转字符串中每个单词的字符顺序，同时仍保留空格和单词的初始顺序。

**示例 1:**

```
输入: "Let's take LeetCode contest"
输出: "s'teL ekat edoCteeL tsetnoc" 
```

``` java
class Solution {
    public String reverseWords(String s) {
        if(s.trim().equals("")){
            return s.trim();
        }
        String str[] = s.trim().split(" ");
        StringBuilder result = new StringBuilder();
        for (int i = 0; i <=str.length-1; i++) {
            if(!str[i].trim().equals("")){
                StringBuilder temp = new StringBuilder(str[i]);
                result.append(temp.reverse());
                result.append(" ");
            }
        }
        return result.toString().trim();
    }
}
```

## 删除排序数组中的重复项

给定一个排序数组，你需要在**原地**删除重复出现的元素，使得每个元素只出现一次，返回移除后数组的新长度。

不要使用额外的数组空间，你必须在**原地修改输入数组**并在使用 O(1) 额外空间的条件下完成。

**示例 1:**

```
给定数组 nums = [1,1,2], 

函数应该返回新的长度 2, 并且原数组 nums 的前两个元素被修改为 1, 2。 

你不需要考虑数组中超出新长度后面的元素。
```

**示例 2:**

```
给定 nums = [0,0,1,1,1,2,2,3,3,4],

函数应该返回新的长度 5, 并且原数组 nums 的前五个元素被修改为 0, 1, 2, 3, 4。

你不需要考虑数组中超出新长度后面的元素。
```

``` java
class Solution {
    public int removeDuplicates(int[] nums) {
        if(nums==null||nums.length==0){
            return 0;
        }
        int x = nums[0];
        int index = 1;
        for (int i = 1; i < nums.length; i++) {
            if(nums[i]!=x){
                nums[index]=nums[i];
                index++;
                x=nums[i];
            }
        }
        return index;
    }
}
```

## 移动零

给定一个数组 `nums`，编写一个函数将所有 `0` 移动到数组的末尾，同时保持非零元素的相对顺序。

**示例:**

```
输入: [0,1,0,3,12]
输出: [1,3,12,0,0]
```

``` java
class Solution {
    public void moveZeroes(int[] nums) {
        int num=0;
        for (int i = 0; i < nums.length; i++) {
            if(nums[i]!=0){
                nums[i-num]=nums[i];
            }else{
                num++;
            }
        }
        for (int i = nums.length-1; i > nums.length-1-num; i--) {
            nums[i]=0;
        }
    }
}
```



## 环形链表

给定一个链表，判断链表中是否有环。

为了表示给定链表中的环，我们使用整数 `pos` 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 `pos` 是 `-1`，则在该链表中没有环。

 

**示例 1：**

```
输入：head = [3,2,0,-4], pos = 1
输出：true
解释：链表中有一个环，其尾部连接到第二个节点。
```

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist.png)

**示例 2：**

```
输入：head = [1,2], pos = 0
输出：true
解释：链表中有一个环，其尾部连接到第一个节点。
```

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist_test2.png)

**示例 3：**

```
输入：head = [1], pos = -1
输出：false
解释：链表中没有环。
```

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist_test3.png)

 

**快慢指针**

``` java
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
        if(head==null||head.next==null){
            return false;
        }
        ListNode l1 = head.next;
        ListNode l2 = head.next.next;
        while (l1!=null&&l2!=null&&l2.next!=null){
            if(l1==l2){
                return true;
            }
            l1=l1.next;
            l2=l2.next.next;
        }
        return false;

    }
}
```

## 环形链表 II

给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 `null`。

为了表示给定链表中的环，我们使用整数 `pos` 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 `pos` 是 `-1`，则在该链表中没有环。

**说明：**不允许修改给定的链表。

 

**示例 1：**

```
输入：head = [3,2,0,-4], pos = 1
输出：tail connects to node index 1
解释：链表中有一个环，其尾部连接到第二个节点。
```

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist.png)

**示例 2：**

```
输入：head = [1,2], pos = 0
输出：tail connects to node index 0
解释：链表中有一个环，其尾部连接到第一个节点。
```

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist_test2.png)

**示例 3：**

```
输入：head = [1], pos = -1
输出：no cycle
解释：链表中没有环。
```

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist_test3.png)

``` java
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
    public ListNode detectCycle(ListNode head) {
        ListNode result = null;
        if(head==null||head.next==null){
            return result;
        }
        ListNode l1 = head.next;
        ListNode l2 = head.next.next;
        while (l1!=null&&l2!=null&&l2.next!=null){
           if(l1==l2){
                result = head;
                while (l1!=result){
                    result=result.next;
                    l1 = l1.next;

                }
                return result;
            }
            l1=l1.next;
            l2=l2.next.next;
        }
        return result;


    }
}
```



## 相交链表

编写一个程序，找到两个单链表相交的起始节点。

如下面的两个链表**：**

[![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_statement.png)](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_statement.png)

在节点 c1 开始相交。

 

**示例 1：**

[![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_example_1.png)](https://assets.leetcode.com/uploads/2018/12/13/160_example_1.png)

```
输入：intersectVal = 8, listA = [4,1,8,4,5], listB = [5,0,1,8,4,5], skipA = 2, skipB = 3
输出：Reference of the node with value = 8
输入解释：相交节点的值为 8 （注意，如果两个列表相交则不能为 0）。从各自的表头开始算起，链表 A 为 [4,1,8,4,5]，链表 B 为 [5,0,1,8,4,5]。在 A 中，相交节点前有 2 个节点；在 B 中，相交节点前有 3 个节点。
```

 

**示例 2：**

[![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_example_2.png)](https://assets.leetcode.com/uploads/2018/12/13/160_example_2.png)

```
输入：intersectVal = 2, listA = [0,9,1,2,4], listB = [3,2,4], skipA = 3, skipB = 1
输出：Reference of the node with value = 2
输入解释：相交节点的值为 2 （注意，如果两个列表相交则不能为 0）。从各自的表头开始算起，链表 A 为 [0,9,1,2,4]，链表 B 为 [3,2,4]。在 A 中，相交节点前有 3 个节点；在 B 中，相交节点前有 1 个节点。
```

 

**示例 3：**

[![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_example_3.png)](https://assets.leetcode.com/uploads/2018/12/13/160_example_3.png)

```
输入：intersectVal = 0, listA = [2,6,4], listB = [1,5], skipA = 3, skipB = 2
输出：null
输入解释：从各自的表头开始算起，链表 A 为 [2,6,4]，链表 B 为 [1,5]。由于这两个链表不相交，所以 intersectVal 必须为 0，而 skipA 和 skipB 可以是任意值。
解释：这两个链表不相交，因此返回 null。
```

哈希表法

``` java
public ListNode getIntersectionNode(ListNode headA, ListNode headB) {

        ListNode result = null;

        if(headA==null||headB==null){
            return result;
        }
        Set<ListNode> A = new HashSet<>();
        while(headA!=null){
            A.add(headA);
            headA=headA.next;
        }
        while(headB!=null){
            if(A.contains(headB)){
                result=headB;
                return result;
            }
            headB = headB.next;
        }
        return result;

    }
```

双指针法

``` java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        ListNode ha = headA, hb = headB;
        while (ha != hb) {
            ha = ha != null ? ha.next : headB;
            hb = hb != null ? hb.next : headA;
        }
        return ha;
    }
}
```



## 删除链表的倒数第N个节点

给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。

**示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**暴力法**

``` java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        int length = 0;
        ListNode l1 = head;
        ListNode l2 = new ListNode(0);
        ListNode result = l2;
        l2.next = head;
        while(l1!=null){
            length++;
            l1 = l1.next;
        }
        for (int i = 0; i <= length-n; i++) {
            if(i==length-n){
                ListNode temp = l2.next.next;
                l2.next = temp;
            }else {
                l2=l2.next;
            }
        }
        return result.next;
    }
}
```

**延迟指针**

``` java
public ListNode removeNthFromEnd(ListNode head, int n) {
    ListNode dummy = new ListNode(0);
    dummy.next = head;
    ListNode first = dummy;
    ListNode second = dummy;
    // Advances first pointer so that the gap between first and second is n nodes apart
    for (int i = 1; i <= n + 1; i++) {
        first = first.next;
    }
    // Move first to the end, maintaining the gap
    while (first != null) {
        first = first.next;
        second = second.next;
    }
    second.next = second.next.next;
    return dummy.next;
}
```



## 移除链表元素

删除链表中等于给定值 **val** 的所有节点。

**示例:**

```
输入: 1->2->6->3->4->5->6, val = 6
输出: 1->2->3->4->5
```

``` java
class Solution {
    public ListNode removeElements(ListNode head, int val) {
        ListNode l = new ListNode(0);
        ListNode result = l;
        l.next=head;
        while(l!=null&&l.next!=null){
            if(l.next.val==val){
                l.next  = l.next.next;
            }else {
                l=l.next;
            }
        }
        return result.next;
    }
}
```



## 奇偶链表

给定一个单链表，把所有的奇数节点和偶数节点分别排在一起。请注意，这里的奇数节点和偶数节点指的是节点编号的奇偶性，而不是节点的值的奇偶性。

请尝试使用原地算法完成。你的算法的空间复杂度应为 O(1)，时间复杂度应为 O(nodes)，nodes 为节点总数。

**示例 1:**

```
输入: 1->2->3->4->5->NULL
输出: 1->3->5->2->4->NULL
```

**示例 2:**

```
输入: 2->1->3->5->6->4->7->NULL 
输出: 2->3->6->7->1->5->4->NULL
```

``` java
public class Solution {
    public ListNode oddEvenList(ListNode head) {
        if (head == null) return null;
        ListNode odd = head, even = head.next, evenHead = even;
        while (even != null && even.next != null) {
            odd.next = even.next;
            odd = odd.next;
            even.next = odd.next;
            even = even.next;
        }
        odd.next = evenHead;
        return head;
    }
}
```



## 回文链表

请判断一个链表是否为回文链表。

**示例 1:**

```
输入: 1->2
输出: false
```

**示例 2:**

```
输入: 1->2->2->1
输出: true
```

**快慢指针加反转链表**

``` java
class Solution {
    public boolean isPalindrome(ListNode head) {
        ListNode fast = new ListNode(0);
        ListNode slow = fast;
        fast.next=head;
        while (fast!=null&&fast.next!=null){
            fast=fast.next.next;
            slow=slow.next;
        }
        if(fast==null){
            ListNode x = slow.next;
            slow.next=null;
            reverseList(head);
            slow=slow.next;
            while (x!=null&&slow!=null){
                if(x.val!=slow.val){
                    return false;
                }
                x=x.next;
                slow=slow.next;
            }
            return true;
        }else {
            ListNode x = slow.next;
            slow.next=null;
            reverseList(head);
            while (x!=null&&slow!=null){
                if(x.val!=slow.val){
                    return false;
                }
                x=x.next;
                slow=slow.next;
            }
            return true;
        }

    }

    private ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode p = reverseList(head.next);
        head.next.next = head;
        head.next = null;
        return p;
    }
}
```



##  扁平化多级双向链表

您将获得一个双向链表，除了下一个和前一个指针之外，它还有一个子指针，可能指向单独的双向链表。这些子列表可能有一个或多个自己的子项，依此类推，生成多级数据结构，如下面的示例所示。

扁平化列表，使所有结点出现在单级双链表中。您将获得列表第一级的头部。

**示例:**

```
输入:
 1---2---3---4---5---6--NULL
         |
         7---8---9---10--NULL
             |
             11--12--NULL

输出:
1-2-3-7-8-11-12-9-10-4-5-6-NULL
```

``` java
class Solution {
    public Node flatten(Node head) {
            if (head == null) {
                return null;
            }
            dfs(head);
            head.prev = null;
            return head;
        }

        /**
         * 展开链表，展开后链表的头结点的 prev 指向尾结点
         *
         * @param head 头结点
         * @return 展开后链表的头结点
         */
        private Node dfs(Node head) {
            Node cur = head;
            while (cur != null) {
                head.prev = cur;    // 更改头结点的 prev 指向尾结点
                Node next = cur.next;
                if (cur.child != null) {
                    Node h = dfs(cur.child);
                    cur.child = null;
                    Node t = h.prev;
                    // 链接 cur、h、t、next
                    cur.next = h;
                    h.prev = cur;
                    t.next = next;
                    if (next != null) {
                        next.prev = t;
                    }
                    head.prev = t;  // 更改头结点的 prev 指向尾结点
                }
                cur = next;
            }
            return head;
        }
}
```



## 复制带随机指针的链表

给定一个链表，每个节点包含一个额外增加的随机指针，该指针可以指向链表中的任何节点或空节点。

要求返回这个链表的**深拷贝**。 

 

**示例：**



**![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2019/02/23/1470150906153-2yxeznm.png)**

**思路：先存next的复制，依次放在Map中，后取random的复制时只需一一对应即可。**

``` java
class Solution {
    public Node copyRandomList(Node head) {
            if(head == null) return null;
        HashMap<Node, Node> map = new HashMap<>();
        
        Node node = head;
        Node newHead = new Node();
        Node pre = newHead;
        while(node != null) {
            Node n = new Node();
            n.val = node.val;
            map.put(node, n);
            pre.next = n;
            pre = n;
            node = node.next;
        }
        node = head;
        pre = newHead.next;
        while(node != null) {
            if(node.random != null) {
               pre.random = map.get(node.random);
           } 
            node = node.next;
            pre = pre.next;
        }
        return newHead.next;
    }
}
```

**思路2：新旧节点形成新的链表，然后进行random的连接。**

``` java
class Solution {
    public Node copyRandomList(Node pHead) {
         if(pHead==null)
            return null;
        Node pCur = pHead;
        //复制next 如原来是A->B->C 变成A->A'->B->B'->C->C'
        while(pCur!=null){
            Node node = new Node(pCur.val);
            node.next = pCur.next;
            pCur.next = node;
            pCur = node.next;
        }
        pCur = pHead;
        //复制random pCur是原来链表的结点 pCur.next是复制pCur的结点
        while(pCur!=null){
            if(pCur.random!=null)
                pCur.next.random = pCur.random.next;
            pCur = pCur.next.next;
        }
        Node head = pHead.next;
        Node cur = head;
        pCur = pHead;
        //拆分链表
        while(pCur!=null){
            pCur.next = pCur.next.next;
            if(cur.next!=null)
                cur.next = cur.next.next;
            cur = cur.next;
            pCur = pCur.next;
        }
        return head;    
    }
}
```



## 旋转链表

给定一个链表，旋转链表，将链表每个节点向右移动 *k* 个位置，其中 *k* 是非负数。

**示例 1:**

```
输入: 1->2->3->4->5->NULL, k = 2
输出: 4->5->1->2->3->NULL
解释:
向右旋转 1 步: 5->1->2->3->4->NULL
向右旋转 2 步: 4->5->1->2->3->NULL
```

**示例 2:**

```
输入: 0->1->2->NULL, k = 4
输出: 2->0->1->NULL
解释:
向右旋转 1 步: 2->0->1->NULL
向右旋转 2 步: 1->2->0->NULL
向右旋转 3 步: 0->1->2->NULL
向右旋转 4 步: 2->0->1->NULL
```

**确定位置后使用延迟指针**

``` java
class Solution {
    public ListNode rotateRight(ListNode head, int k) {
        if(head==null){
            return head;
        }
        ListNode temp = head;
        int length = 0;
        while (temp!=null){
            length++;
            temp=temp.next;
        }
        int x = k%length;
        ListNode fast = head;
        ListNode slow = head;
        ListNode pre=fast;
        ListNode next=slow;
        if(x>0){
            while(fast!=null&&slow!=null){
            pre=fast;
            fast=fast.next;
            x--;
            if(x<0){
                next=slow;
                slow=slow.next;
            }
            }
        pre.next=head;
        next.next=null;
        }
        return slow;

    }
}
```

