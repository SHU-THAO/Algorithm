# LeetCode 算法总结3

## 设计哈希集合

不使用任何内建的哈希表库设计一个哈希集合

具体地说，你的设计应该包含以下的功能

- `add(value)`：向哈希集合中插入一个值。
- `contains(value)` ：返回哈希集合中是否存在这个值。
- `remove(value)`：将给定值从哈希集合中删除。如果哈希集合中没有这个值，什么也不做。


**示例:**

```
MyHashSet hashSet = new MyHashSet();
hashSet.add(1);         
hashSet.add(2);         
hashSet.contains(1);    // 返回 true
hashSet.contains(3);    // 返回 false (未找到)
hashSet.add(2);          
hashSet.contains(2);    // 返回 true
hashSet.remove(2);          
hashSet.contains(2);    // 返回  false (已经被删除)
```

``` java
class MyHashSet {
    List<LinkedList<Integer>> list;
    int bucketNum = 1000;

    /**
     * Initialize your data structure here.
     */
    public MyHashSet() {
        list = new ArrayList<>();
        for (int i = 0; i < bucketNum; i++) {
            list.add(new LinkedList<>());
        }
    }

    public void add(int key) {
        if (!contains(key)) {
            list.get(key % bucketNum).add(key);
        }
    }

    public void remove(int key) {
        if(contains(key)){
            list.get(key % bucketNum).removeFirstOccurrence(key);
        }
    }

    /**
     * Returns true if this set contains the specified element
     */
    public boolean contains(int key) {
        LinkedList<Integer> bucket = list.get(key % bucketNum);
        for (int num : bucket) {
            if (num == key)
                return true;
        }
        return false;
    }
}
```



## 设计哈希映射

不使用任何内建的哈希表库设计一个哈希映射

具体地说，你的设计应该包含以下的功能

- `put(key, value)`：向哈希映射中插入(键,值)的数值对。如果键对应的值已经存在，更新这个值。
- `get(key)`：返回给定的键所对应的值，如果映射中不包含这个键，返回-1。
- `remove(key)`：如果映射中存在这个键，删除这个数值对。


**示例：**

```
MyHashMap hashMap = new MyHashMap();
hashMap.put(1, 1);          
hashMap.put(2, 2);         
hashMap.get(1);            // 返回 1
hashMap.get(3);            // 返回 -1 (未找到)
hashMap.put(2, 1);         // 更新已有的值
hashMap.get(2);            // 返回 1 
hashMap.remove(2);         // 删除键为2的数据
hashMap.get(2);            // 返回 -1 (未找到) 
```

``` java
class MyHashMap {
    int k=10;
    Node[] hashArray;
    /** Initialize your data structure here. */
    public MyHashMap() {
        hashArray=new Node[k];
    }
    
    /** value will always be non-negative. */
    public void put(int key, int value) {
        Node node=hashArray[key%k];
        Node pre=node;
        if(node==null)
        { hashArray[key%k]=new Node(key,value);
         return;
        }
         while(node!=null){
            if(node.x==key){
                node.y=value;
                return;
            }
             pre=node;
            node=node.next;
        }
        pre.next=new Node(key,value);
        
    }
    
    /** Returns the value to which the specified key is mapped, or -1 if this map contains no mapping for the key */
    public int get(int key) {
        Node node=hashArray[key%k];
        while(node!=null){
            if(node.x==key){
                return node.y;
            }
            node=node.next;
        }
        return -1;
    }
    
    /** Removes the mapping of the specified value key if this map contains a mapping for the key */
    public void remove(int key) {
        Node node=hashArray[key%k];
        Node pre=node;
        while(node!=null){
            if(node.x==key){
                if(pre.x==node.x){
                    hashArray[key%k]=node.next;
                }else{
                    pre.next=node.next;
                }
            }
            pre=node;
            node=node.next;
        }
    }
}
```



## 存在重复元素

给定一个整数数组，判断是否存在重复元素。

如果任何值在数组中出现至少两次，函数返回 true。如果数组中每个元素都不相同，则返回 false。

**示例 1:**

```
输入: [1,2,3,1]
输出: true
```

**示例 2:**

```
输入: [1,2,3,4]
输出: false
```

**示例 3:**

```
输入: [1,1,1,3,3,4,3,2,4,2]
输出: true
```

``` java
class Solution {
    public boolean containsDuplicate(int[] nums) {
        Set<Integer> sets = new HashSet<>();
        for (int i = 0; i < nums.length; i++) {
            if(sets.contains(nums[i])){
                return true;
            }else {
                sets.add(nums[i]);
            }
        }
        return false;
    }
}
```



## 只出现一次的数字

给定一个**非空**整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

**说明：**

你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？

**示例 1:**

```
输入: [2,2,1]
输出: 1
```

**示例 2:**

```
输入: [4,1,2,1,2]
输出: 4
```

``` java
class Solution {
    public int singleNumber(int[] nums) {
        Map<Integer,Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            if(map.containsKey(nums[i])){
                map.put(nums[i],map.get(nums[i])+1);
            }else {
                map.put(nums[i],1);
            }
        }

        for (Integer key : map.keySet()) {
            if(map.get(key)==1){
                return key;
            }
        }
        return 0;
    }
}
```



##  两个数组的交集

给定两个数组，编写一个函数来计算它们的交集。

**示例 1:**

```
输入: nums1 = [1,2,2,1], nums2 = [2,2]
输出: [2]
```

**示例 2:**

```
输入: nums1 = [4,9,5], nums2 = [9,4,9,8,4]
输出: [9,4]
```

``` java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
            HashSet<Integer> set1 = new HashSet<Integer>();
            for (Integer n : nums1) set1.add(n);
            HashSet<Integer> set2 = new HashSet<Integer>();
            for (Integer n : nums2) set2.add(n);

            if (set1.size() < set2.size()) return set_intersection(set1, set2);
            else return set_intersection(set2, set1);
        }
    public int[] set_intersection(HashSet<Integer> set1, HashSet<Integer> set2) {
            int [] output = new int[set1.size()];
            int idx = 0;
            for (Integer s : set1)
                if (set2.contains(s)) output[idx++] = s;

            return Arrays.copyOf(output, idx);
    }
}
```



## 快乐数

编写一个算法来判断一个数是不是“快乐数”。

一个“快乐数”定义为：对于一个正整数，每一次将该数替换为它每个位置上的数字的平方和，然后重复这个过程直到这个数变为 1，也可能是无限循环但始终变不到 1。如果可以变为 1，那么这个数就是快乐数。

**示例:** 

```
输入: 19
输出: true
解释: 
1^2 + 9^2 = 82
8^2 + 2^2 = 68
6^2 + 8^2 = 100
1^2 + 0^2 + 0^2 = 1
```

``` java
class Solution {
    public boolean isHappy(int n) {
        Set<Integer> set = new HashSet<>();
        int num=n;
        while (true){
            if(num==1){
                return true;
            }
            if(set.contains(num)){
                return false;
            }
            set.add(num);
            num = count(num);
        }

    }

    private int count(int num) {
        if(num==0){
            return 0;
        }
        else {
            return (int)Math.pow(num%10,2)+count(num/10);
        }
    }
}
```



## 同构字符串

给定两个字符串 ***s*** 和 **t**，判断它们是否是同构的。

如果 ***s*** 中的字符可以被替换得到 **t** ，那么这两个字符串是同构的。

所有出现的字符都必须用另一个字符替换，同时保留字符的顺序。两个字符不能映射到同一个字符上，但字符可以映射自己本身。

**示例 1:**

```
输入: s = "egg", t = "add"
输出: true
```

**示例 2:**

```
输入: s = "foo", t = "bar"
输出: false
```

**示例 3:**

```
输入: s = "paper", t = "title"
输出: true
```

**思路一：位置映射**

``` java
class Solution {
    public boolean isIsomorphic(String s, String t) {
        if(s.equals(t)){
            return true;
        }
        Map<Character,Integer> map1 = new HashMap<>();
        Map<Character,Integer> map2 = new HashMap<>();
        for (int i = 0; i < s.length(); i++) {
            if(map1.containsKey(s.charAt(i))&&map2.containsKey(t.charAt(i))){
                if(!map1.get(s.charAt(i)).equals(map2.get(t.charAt(i)))){
                    return false;
                }
                map1.put(s.charAt(i),i);
                map2.put(t.charAt(i),i);
            }else if(!map1.containsKey(s.charAt(i))&&!map2.containsKey(t.charAt(i))){
                map1.put(s.charAt(i),i);
                map2.put(t.charAt(i),i);
            }else {
                return false;
            }
        }
        return true;
    }
}
```

**思路二：字符映射**

``` java
class Solution {
    public boolean isIsomorphic(String s, String t) {
        HashMap<Character,Character> check = new HashMap<>();
        HashSet<Character> sole = new HashSet<>();
        for (int i = 0; i < s.length(); i++) {
            char a = s.charAt(i);
            char b = t.charAt(i);
            if(!check.containsKey(a)){
                if(sole.contains(b)){
                    return false;
                }
                sole.add(b);
                check.put(a ,b);
            }else if(check.get(a) != b){
                return false;
            }
        }
        return true;
    }
}
```

## 两个列表的最小索引总和

假设Andy和Doris想在晚餐时选择一家餐厅，并且他们都有一个表示最喜爱餐厅的列表，每个餐厅的名字用字符串表示。

你需要帮助他们用**最少的索引和**找出他们**共同喜爱的餐厅**。 如果答案不止一个，则输出所有答案并且不考虑顺序。 你可以假设总是存在一个答案。

**示例 1:**

```
输入:
["Shogun", "Tapioca Express", "Burger King", "KFC"]
["Piatti", "The Grill at Torrey Pines", "Hungry Hunter Steakhouse", "Shogun"]
输出: ["Shogun"]
解释: 他们唯一共同喜爱的餐厅是“Shogun”。
```

**示例 2:**

```
输入:
["Shogun", "Tapioca Express", "Burger King", "KFC"]
["KFC", "Shogun", "Burger King"]
输出: ["Shogun"]
解释: 他们共同喜爱且具有最小索引和的餐厅是“Shogun”，它有最小的索引和1(0+1)。
```

``` java
class Solution {
    public String[] findRestaurant(String[] list1, String[] list2) {
        Map<String,Integer> map = new HashMap<>();
        for (int i = 0; i < list1.length; i++) {
            map.put(list1[i],map.containsKey(list1[i])?map.get(list1[i])-i-1:-i-1);
        }
        for (int i = 0; i < list2.length; i++) {
            if(map.containsKey(list2[i])){
                map.put(list2[i],-map.get(list2[i])+i+1);
            }
        }
        List<String> result = new ArrayList<>();
        int index=Integer.MAX_VALUE;
        for (String s:map.keySet()) {
            if(map.get(s)>0){
                index=Math.min(index,map.get(s));
            }
        }
        for (String s: map.keySet()) {
            if(map.get(s)==index){
                result.add(s);
            }
        }
        String[] strings = new String[result.size()];
        return result.toArray(strings);
    }
}
```



## 字符串中的第一个唯一字符

给定一个字符串，找到它的第一个不重复的字符，并返回它的索引。如果不存在，则返回 -1。

**案例:**

```
s = "leetcode"
返回 0.

s = "loveleetcode",
返回 2.
```

**使用哈希表**

``` java
class Solution {
    public int firstUniqChar(String s) {
        if(s==null||s.equals("")){
            return -1;
        }
        char[] str = s.toCharArray();
        Map<Character,Integer> map = new HashMap<>();
        for (int i = 0; i < str.length; i++) {
            map.put(str[i],map.containsKey(str[i])?map.get(str[i])+1:1);
        }
        for (int i = 0; i < str.length; i++) {
            if(map.get(str[i])==1){
                return i;
            }
        }
        return -1;
    }
}
```

**使用数组来模拟哈希表**

``` java
class Solution {
    public int firstUniqChar(String s) {
        if (s == null || s.length() == 0)
            return -1;
        
        int[] freq = new int[26];
        for (int i = 0; i < s.length(); i++) {
            freq[s.charAt(i) - 'a']++;
        }
        
        for (int i = 0; i < s.length(); i++) {
            if (freq[s.charAt(i) - 'a'] == 1)
                return i;
        }
        
        return -1;
    }
}
```

**使用内置函数**

``` java
class Solution {
    public int firstUniqChar(String s) {
        int res = -1;
		for(char i = 'a'; i <= 'z'; i++) {
			int start = s.indexOf(i);
			if(start != -1 && start == s.lastIndexOf(i)) {
				if(res == -1)
					res = start;
				else if(res > start)
					res = start;
			}
		}
		return res;
    }
}
```



## 两个数组的交集 II

给定两个数组，编写一个函数来计算它们的交集。

**示例 1:**

```
输入: nums1 = [1,2,2,1], nums2 = [2,2]
输出: [2,2]
```

**示例 2:**

```
输入: nums1 = [4,9,5], nums2 = [9,4,9,8,4]
输出: [4,9]
```

**通过map映射实现**

``` java
class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        if(nums1.length==0||nums2.length==0){
            return new int[0];
        }
        Map<Integer,Integer> map = new HashMap<>();
        List<Integer> list = new ArrayList<>();
        for (int i = 0; i < nums1.length; i++) {
            map.put(nums1[i],map.containsKey(nums1[i])?map.get(nums1[i])+1:1);
        }
        for (int i = 0; i < nums2.length; i++) {
            if(map.get(nums2[i])!=null&&map.get(nums2[i])>0){
                list.add(nums2[i]);
                map.put(nums2[i],map.get(nums2[i])-1);
            }
        }
        int[] result = new int[list.size()];
        int i=0;
        for (int x:list) {
            result[i]=x;
            i++;
        }
        return result;
    }
}
```



## 存在重复元素 II

给定一个整数数组和一个整数 *k*，判断数组中是否**存在**两个不同的索引 *i* 和 *j*，使得 **nums [i] = nums [j]**，并且 *i* 和 *j* 的差的绝对值**最大为 *k***。

**示例 1:**

```
输入: nums = [1,2,3,1], k = 3
输出: true
```

**示例 2:**

```
输入: nums = [1,0,1,1], k = 1
输出: true
```

**示例 3:**

```
输入: nums = [1,2,3,1,2,3], k = 2
输出: false
```

``` java
class Solution {
    public boolean containsNearbyDuplicate(int[] nums, int k) {

        Map<Integer,List<Integer>> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            if(!map.containsKey(nums[i])){
                List<Integer> list = new ArrayList<>();
                list.add(i);
                map.put(nums[i],list);
            }else {
                List<Integer> list = map.get(nums[i]);
                for (int x: list) {
                    if(i-x<=k){
                        return true;
                    }
                }
                list.add(i);
                map.put(nums[i],list);
            }
        }
        return false;

    }
}
```



## 字母异位词分组

给定一个字符串数组，将字母异位词组合在一起。字母异位词指字母相同，但排列不同的字符串。

**示例:**

```
输入: ["eat", "tea", "tan", "ate", "nat", "bat"],
输出:
[
  ["ate","eat","tea"],
  ["nat","tan"],
  ["bat"]
]
```

**将key重新排列后再映射**

``` java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        if (strs.length == 0) return new ArrayList();
        Map<String, List> ans = new HashMap<String, List>();
        for (String s : strs) {
            char[] ca = s.toCharArray();
            Arrays.sort(ca);
            String key = String.valueOf(ca);
            if (!ans.containsKey(key)) ans.put(key, new ArrayList());
            ans.get(key).add(s);
        }
        return new ArrayList(ans.values());
    }
}
```



## 有效的数独

判断一个 9x9 的数独是否有效。只需要**根据以下规则**，验证已经填入的数字是否有效即可。

1. 数字 `1-9` 在每一行只能出现一次。
2. 数字 `1-9` 在每一列只能出现一次。
3. 数字 `1-9` 在每一个以粗实线分隔的 `3x3` 宫内只能出现一次。

![img](https://upload.wikimedia.org/wikipedia/commons/thumb/f/ff/Sudoku-by-L2G-20050714.svg/250px-Sudoku-by-L2G-20050714.svg.png)

上图是一个部分填充的有效的数独。

数独部分空格内已填入了数字，空白格用 `'.'` 表示。

**多次迭代**

``` java
class Solution {
    public boolean isValidSudoku(char[][] board) {
        for (int i = 0; i < 9; i++) {
            if(!check(board[i])){
                return false;
            }
        }
        for (int i = 0; i < 9; i++) {
            char[] test = new char[9];
            for (int j = 0; j < 9; j++) {
                test[j] = board[j][i];
            }
            if(!check(test)){
                return false;
            }
        }
        for (int i = 0; i < 9; i++) {
            char[] test = new char[9];
            for (int j = 0; j < 9; j++) {
                if(j<=2){
                    test[j]=board[i/3*3][i%3*3+j];
                }else if(j<=5){
                    test[j]=board[i/3*3+1][i%3*3+j-3];
                }else {
                    test[j]=board[i/3*3+2][i%3*3+j-6];
                }

            }
            if(!check(test)){
                return false;
            }
        }

        return true;
    }
    private boolean check(char[] chars) {
        Map<Integer,Integer> map = new HashMap<>();
        for (char c:chars) {
            if(c!='.'){
                int x = c-'0';
                if(map.containsKey(x)){
                    return false;
                }else {
                    map.put(x,1);
                }
            }
        }
        return true;
    }
}
```

**一次迭代**

``` java
class Solution {
  public boolean isValidSudoku(char[][] board) {
    // init data
    HashMap<Integer, Integer> [] rows = new HashMap[9];
    HashMap<Integer, Integer> [] columns = new HashMap[9];
    HashMap<Integer, Integer> [] boxes = new HashMap[9];
    for (int i = 0; i < 9; i++) {
      rows[i] = new HashMap<Integer, Integer>();
      columns[i] = new HashMap<Integer, Integer>();
      boxes[i] = new HashMap<Integer, Integer>();
    }

    // validate a board
    for (int i = 0; i < 9; i++) {
      for (int j = 0; j < 9; j++) {
        char num = board[i][j];
        if (num != '.') {
          int n = (int)num;
          int box_index = (i / 3 ) * 3 + j / 3;

          // keep the current cell value
          rows[i].put(n, rows[i].getOrDefault(n, 0) + 1);
          columns[j].put(n, columns[j].getOrDefault(n, 0) + 1);
          boxes[box_index].put(n, boxes[box_index].getOrDefault(n, 0) + 1);

          // check if this value has been already seen before
          if (rows[i].get(n) > 1 || columns[j].get(n) > 1 || boxes[box_index].get(n) > 1)
            return false;
        }
      }
    }
    return true;
  }
}
```

## 寻找重复的子树

给定一棵二叉树，返回所有重复的子树。对于同一类的重复子树，你只需要返回其中任意**一棵**的根结点即可。

两棵树重复是指它们具有相同的结构以及相同的结点值。

**示例 1：**

```
        1
       / \
      2   3
     /   / \
    4   2   4
       /
      4
```

下面是两个重复的子树：

```
      2
     /
    4
```

和

```
    4
```

**将路径存储为一致性的String进而可以将其表示为键**

``` java
class Solution {
    public List<TreeNode> findDuplicateSubtrees(TreeNode root) {
        List<TreeNode> res = new ArrayList<>();
        HashMap<String, Integer> map = new HashMap<>();
        if (root == null) return res;
        saveRoute(root, res, map);
        return res;
    }

    //递归获取每个子树的路径，保存于Map中
    private String saveRoute(TreeNode node, List<TreeNode> res, HashMap<String, Integer> map) {
        if (node == null) return "";
        //自底向上获取每个节点的序列化值
        String route = node.val + "," + saveRoute(node.left, res, map) + "," + saveRoute(node.right, res, map);
        //将结果放入map，判断是否有相同子树
        //避免出现多次相同子树
        if (map.get(route) != null && map.get(route) == 1) {
            res.add(node);
        }
        map.put(route, map.getOrDefault(route, 0) + 1);
        return route;
    }
}
```

## 宝石与石头

 给定字符串`J` 代表石头中宝石的类型，和字符串 `S`代表你拥有的石头。 `S` 中每个字符代表了一种你拥有的石头的类型，你想知道你拥有的石头中有多少是宝石。

`J` 中的字母不重复，`J` 和 `S`中的所有字符都是字母。字母区分大小写，因此`"a"`和`"A"`是不同类型的石头。

**示例 1:**

```
输入: J = "aA", S = "aAAbbbb"
输出: 3
```

**示例 2:**

```
输入: J = "z", S = "ZZ"
输出: 0
```



``` java
class Solution {
    public int numJewelsInStones(String J, String S) {
        char[] gems = J.toCharArray();
        char[] stones = S.toCharArray();
        Map<Character,Integer> map = new HashMap<>();
        for (int i = 0; i < gems.length; i++) {
            map.put(gems[i],0);
        }
        for (int i = 0; i < stones.length; i++) {
            if(map.containsKey(stones[i])){
                map.put(stones[i],map.get(stones[i])+1);
            }
        }
        int result = 0;
        for (Character c: map.keySet()) {
            result+=map.get(c);
        }
        return result;
    }
}
```



## 四数相加 II

给定四个包含整数的数组列表 A , B , C , D ,计算有多少个元组 `(i, j, k, l)` ，使得 `A[i] + B[j] + C[k] + D[l] = 0`。

为了使问题简单化，所有的 A, B, C, D 具有相同的长度 N，且 0 ≤ N ≤ 500 。所有整数的范围在 -228 到 228 - 1 之间，最终结果不会超过 231 - 1 。

**例如:**

```
输入:
A = [ 1, 2]
B = [-2,-1]
C = [-1, 2]
D = [ 0, 2]

输出:
2

解释:
两个元组如下:
1. (0, 0, 0, 1) -> A[0] + B[0] + C[0] + D[1] = 1 + (-2) + (-1) + 2 = 0
2. (1, 1, 0, 0) -> A[1] + B[1] + C[0] + D[0] = 2 + (-1) + (-1) + 0 = 0
```

**复杂度需要控制在n^2以内，因此选择两两相加，并使用hashMap来进行缓存**

``` java
class Solution {
    public  int fourSumCount(int[] A, int[] B, int[] C, int[] D) {
      int count = 0;
      Map<Integer,Integer> hashMap = new HashMap<>();
      for (int numA : A) {
          for (int numB : B) {
              int sumAB = numA + numB;
              hashMap.put(sumAB, hashMap.getOrDefault(sumAB, 0) + 1);
          }
      }
      for (int numC : C){
          for (int numD : D){
              int sumCD = numC + numD;
              count += hashMap.getOrDefault(-sumCD,0);
          }
      }
      return count;
  }

}
```

## 前 K 个高频元素

给定一个非空的整数数组，返回其中出现频率前 **k** 高的元素。

**示例 1:**

```
输入: nums = [1,1,1,2,2,3], k = 2
输出: [1,2]
```

**示例 2:**

```
输入: nums = [1], k = 1
输出: [1]
```

**hashMap+堆排序**

``` java
class Solution {
    public List<Integer> topKFrequent(int[] nums, int k) {
        if(nums==null||nums.length==0){
            return new ArrayList<>();
        }
        Map<Integer,Integer> map = new HashMap<>();
        for (int i : nums) {
            map.put(i,map.getOrDefault(i,0)+1);
        }
        int[][] num = new int[2][map.size()];
        int i=0;
        for (int x:map.keySet()) {
            num[0][i]=x;
            num[1][i]=map.get(x);
            i++;
        }
        num = heapSort(num);
        List<Integer> result = new ArrayList<>();
        for (int j = 0; j < k; j++) {
            result.add(num[0][num[0].length-j-1]);
        }
        return result;


    }

    public static int[][] heapSort(int[][] target) {
        if (target != null && target[1].length > 1) {

            // 调整为最大堆
            int pos = (target[1].length - 2) / 2;
            while (pos >= 0) {
                shiftDown(target, pos, target[1].length - 1);
                pos--;
            }

            // 堆排序
            for (int i = target[1].length-1; i > 0; i--) {
                int temp = target[1][i];
                int temp2 = target[0][i];
                target[1][i] = target[1][0];
                target[0][i] = target[0][0];
                target[1][0] = temp;
                target[0][0] = temp2;
                shiftDown(target, 0, i-1);
            }
            return target;
        }
        return target;
    }


    /**
     * @description 自上而下调整为最大堆,保证堆顶是最大值
     * @author rico
     * @created 2017年5月25日 上午9:45:40
     * @param target
     * @param start
     * @param end
     */
    private static void shiftDown(int[][] target, int start, int end) {
        int i = start;
        int j = 2 * start + 1;
        int temp = target[1][i];
        int temp2 = target[0][i];
        while (j <= end) {   // 迭代条件
            if (j < end && target[1][j + 1] > target[1][j]) {  //找出较大子女
                j = j + 1;
            }
            if (target[1][j] <= temp) {  // 父亲大于子女
                break;
            } else {
                target[1][i] = target[1][j];
                target[0][i] = target[0][j];
                i = j;
                j = 2 * j + 1;
            }
        }
        target[1][i] = temp;
        target[0][i] = temp2;
    }
}
```

**hashMap+优先队列**

``` java
class Solution {
  public List<Integer> topKFrequent(int[] nums, int k) {
    // build hash map : character and how often it appears
    HashMap<Integer, Integer> count = new HashMap();
    for (int n: nums) {
      count.put(n, count.getOrDefault(n, 0) + 1);
    }

    // init heap 'the less frequent element first'
    PriorityQueue<Integer> heap =
            new PriorityQueue<Integer>((n1, n2) -> count.get(n1) - count.get(n2));

    // keep k top frequent elements in the heap
    for (int n: count.keySet()) {
      heap.add(n);
      if (heap.size() > k)
        heap.poll();
    }

    // build output list
    List<Integer> top_k = new LinkedList();
    while (!heap.isEmpty())
      top_k.add(heap.poll());
    Collections.reverse(top_k);
    return top_k;
  }
}
```

``` java
class Solution {
    public List<Integer> topKFrequent(int[] nums, int k) {
        // 使用字典，统计每个元素出现的次数，元素为键，元素出现的次数为值
        HashMap<Integer,Integer> map = new HashMap();
        for(int num : nums){
            if (map.containsKey(num)) {
               map.put(num, map.get(num) + 1);
             } else {
                map.put(num, 1);
             }
        }
        // 遍历map，用最小堆保存频率最大的k个元素
        PriorityQueue<Integer> pq = new PriorityQueue<>(new Comparator<Integer>() {
            @Override
            public int compare(Integer a, Integer b) {
                return map.get(a) - map.get(b);
            }
        });
        for (Integer key : map.keySet()) {
            if (pq.size() < k) {
                pq.add(key);
            } else if (map.get(key) > map.get(pq.peek())) {
                pq.remove();
                pq.add(key);
            }
        }
        // 取出最小堆中的元素
        List<Integer> res = new ArrayList<>();
        while (!pq.isEmpty()) {
            res.add(pq.remove());
        }
        return res;
    }
}
```



## 常数时间插入、删除和获取随机元素

设计一个支持在*平均* 时间复杂度 **O(1)** 下，执行以下操作的数据结构。

1. `insert(val)`：当元素 val 不存在时，向集合中插入该项。
2. `remove(val)`：元素 val 存在时，从集合中移除该项。
3. `getRandom`：随机返回现有集合中的一项。每个元素应该有**相同的概率**被返回。

``` java
class RandomizedSet {

    private final HashMap<Integer, Integer> map;
    private final ArrayList<Integer> list;
    
    /** Initialize your data structure here. */
    public RandomizedSet() {
        map = new HashMap<>();
        list = new ArrayList<>();
    }
    
    /** Inserts a value to the set. Returns true if the set did not already contain the specified element. */
    public boolean insert(int val) {
        if (map.containsKey(val)) {
           return false;
        }
        map.put(val, map.size());
        list.add(val);
        return true;   
    }
    
    /** Removes a value from the set. Returns true if the set contained the specified element. */
    public boolean remove(int val) {
        int index = map.getOrDefault(val, -1);
        if (index == -1) {
            return false;
        }
        int lastIndex = list.size() - 1;
        if (index == lastIndex) {
            list.remove(index);
        } else {
            list.set(index, list.get(lastIndex));
            map.put(list.remove(lastIndex), index);
        }
        map.remove(val);
        return true;
    }
    
    /** Get a random element from the set. */
    public int getRandom() {
        int index = (int) (Math.random() * list.size());
        return list.get(index);
    }
}

/**
 * Your RandomizedSet object will be instantiated and called as such:
 * RandomizedSet obj = new RandomizedSet();
 * boolean param_1 = obj.insert(val);
 * boolean param_2 = obj.remove(val);
 * int param_3 = obj.getRandom();
 */
```

## 二分查找 

给定一个 `n` 个元素有序的（升序）整型数组 `nums` 和一个目标值 `target`  ，写一个函数搜索 `nums` 中的 `target`，如果目标值存在返回下标，否则返回 `-1`。


**示例 1:**

```
输入: nums = [-1,0,3,5,9,12], target = 9
输出: 4
解释: 9 出现在 nums 中并且下标为 4
```

**示例 2:**

```
输入: nums = [-1,0,3,5,9,12], target = 2
输出: -1
解释: 2 不存在 nums 中因此返回 -1
```

``` java
class Solution {
    public int search(int[] nums, int target) {
        if(nums==null||nums.length==0){
            return -1;
        }
        int i=0;
        int j=nums.length-1;
        while (i<=j){
            int x = (i+j)/2;
            if(nums[x]>target){
                j=x-1;
            }else if(nums[x]<target){
                i=x+1;
            }else {
                return x;
            }
        }
        return -1;
    }
}
```

## x 的平方根

实现 `int sqrt(int x)` 函数。

计算并返回 *x* 的平方根，其中 *x* 是非负整数。

由于返回类型是整数，结果只保留整数的部分，小数部分将被舍去。

**示例 1:**

```
输入: 4
输出: 2
```

**示例 2:**

```
输入: 8
输出: 2
说明: 8 的平方根是 2.82842..., 
     由于返回类型是整数，小数部分将被舍去。
```

**二分查找**

``` java
public class Solution {
    public int mySqrt(int x) {
        long i=0;
        long j=x/2+1;
        while (i<=j){
            long num = (i+j)/2;
            if(num*num>x){
                j=num-1;
            }else if(num*num<x){
                i=num+1;
            }else if(num*num==x){
                return (int)num;
            }
        }
        if(i*i>x){
            return (int)i-1;
        }else {
            return (int)i;
        }
    }
}
```

**牛顿法**

``` java
public class Solution {
    public int mySqrt(int a) {
        long x = a;
        while (x * x > a) {
            x = (x + a / x) / 2;
        }
        return (int) x;
    }
}
```

## 搜索旋转排序数组

假设按照升序排序的数组在预先未知的某个点上进行了旋转。

( 例如，数组 `[0,1,2,4,5,6,7]` 可能变为 `[4,5,6,7,0,1,2]` )。

搜索一个给定的目标值，如果数组中存在这个目标值，则返回它的索引，否则返回 `-1` 。

你可以假设数组中不存在重复的元素。

你的算法时间复杂度必须是 *O*(log *n*) 级别。

**示例 1:**

```
输入: nums = [4,5,6,7,0,1,2], target = 0
输出: 4
```

**示例 2:**

```
输入: nums = [4,5,6,7,0,1,2], target = 3
输出: -1
```

``` java
class Solution {
    public int search(int[] nums, int target) {
        if(nums==null||nums.length==0){
            return -1;
        }
        if(nums[0]>target){
            for (int i = nums.length-1; i >=0 ; i--) {
                if(nums[i]==target){
                    return i; 
                }else if(nums[i]<target){
                    return -1; 
                }else if(i+1<=nums.length-1&&nums[i]>nums[i+1]){
                    return -1;
                }
            }
        }else{
            for (int i = 0; i < nums.length;i++) {
                if(nums[i]==target){
                    return i;
                }else if(nums[i]>target){
                    return -1;
                }else if(i-1>=0&&nums[i]<nums[i-1]){
                    return -1;
                }
            }
        }
        return -1;

    }
}
```

**二分查找**

``` java
class Solution {
    public int search(int[] nums, int target) {
        return work(nums, 0, nums.length-1, target);
    }
    
    public int work(int [] nums, int start, int end, int target ){
        if(nums.length<=start||nums.length<=end) return -1;
        if(nums[start]==target) return start;
        if(nums[end]==target) return end;
        if(start>=end) return -1;
        
        int mid = (start+end)/2;
        int midNum = nums[mid];
        
        if(midNum==target) return mid;
        
        if(midNum>nums[start]){ //左边是排好的
            if(target<midNum&&target>nums[start]){
                return work(nums, start, mid, target);
            }else{
                return work(nums, mid+1,end, target);
            }
            
        }else{ //右边是排好的
            if(target<nums[end] && target>=nums[mid+1]){
                return work(nums, mid+1, end, target);
            }else{
                return work(nums, start, mid, target);
            }
        }
        
    }
}
```

## 第一个错误的版本

你是产品经理，目前正在带领一个团队开发新的产品。不幸的是，你的产品的最新版本没有通过质量检测。由于每个版本都是基于之前的版本开发的，所以错误的版本之后的所有版本都是错的。

假设你有 `n` 个版本 `[1, 2, ..., n]`，你想找出导致之后所有版本出错的第一个错误的版本。

你可以通过调用 `bool isBadVersion(version)` 接口来判断版本号 `version` 是否在单元测试中出错。实现一个函数来查找第一个错误的版本。你应该尽量减少对调用 API 的次数。

**示例:**

```
给定 n = 5，并且 version = 4 是第一个错误的版本。

调用 isBadVersion(3) -> false
调用 isBadVersion(5) -> true
调用 isBadVersion(4) -> true

所以，4 是第一个错误的版本。 
```

``` java
public class Solution extends VersionControl {
    public int firstBadVersion(int n) {
    	int left = 1;
    	int right = n;
    	while (left < right) {
        	int mid = left + (right - left) / 2;
        	if (isBadVersion(mid)) {
            	right = mid;
       		} else {
            	left = mid + 1;
        	}
    	}
    	return left;
	}
}
```



## 寻找峰值

峰值元素是指其值大于左右相邻值的元素。

给定一个输入数组 `nums`，其中 `nums[i] ≠ nums[i+1]`，找到峰值元素并返回其索引。

数组可能包含多个峰值，在这种情况下，返回任何一个峰值所在位置即可。

你可以假设 `nums[-1] = nums[n] = -∞`。

**示例 1:**

```
输入: nums = [1,2,3,1]
输出: 2
解释: 3 是峰值元素，你的函数应该返回其索引 2。
```

**示例 2:**

```
输入: nums = [1,2,1,3,5,6,4]
输出: 1 或 5 
解释: 你的函数可以返回索引 1，其峰值元素为 2；
     或者返回索引 5， 其峰值元素为 6。
```

``` java
class Solution {
    public int findPeakElement(int[] nums) {
        int l=0;
        int r=nums.length-1;
        while (l < r) {
            int mid = l + (r - l) / 2;
            if (nums[mid]>nums[mid+1]){
                r = mid;
            }else{
                l = mid + 1;
            }
        }
        return l;
    }
}
```

## 寻找旋转排序数组中的最小值

假设按照升序排序的数组在预先未知的某个点上进行了旋转。

( 例如，数组 `[0,1,2,4,5,6,7]` 可能变为 `[4,5,6,7,0,1,2]` )。

请找出其中最小的元素。

你可以假设数组中不存在重复元素。

**示例 1:**

```
输入: [3,4,5,1,2]
输出: 1
```

**示例 2:**

```
输入: [4,5,6,7,0,1,2]
输出: 0
```

``` java
class Solution {
    public int findMin(int[] nums) {
        int l=0;
        int r=nums.length-1;
        while(l<r){
            int mid = l + (r - l) / 2;
            if (nums[mid]<=nums[r]){
                r = mid;
            }else if(nums[mid]>=nums[l]){
                l = mid+1;
            }
        }
        return nums[l];
    }
}
```

## 在排序数组中查找元素的第一个和最后一个位置

给定一个按照升序排列的整数数组 `nums`，和一个目标值 `target`。找出给定目标值在数组中的开始位置和结束位置。

你的算法时间复杂度必须是 *O*(log *n*) 级别。

如果数组中不存在目标值，返回 `[-1, -1]`。

**示例 1:**

```
输入: nums = [5,7,7,8,8,10], target = 8
输出: [3,4]
```

**示例 2:**

```
输入: nums = [5,7,7,8,8,10], target = 6
输出: [-1,-1]
```

**根据二分查找先后搜索左右临界，注意特殊情况**

``` java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        if(nums==null||nums.length==0){
            return new int[]{-1,-1};
        }
        int l=0;
        int r=nums.length-1;
        while(l<r){
            int mid = l + (r - l) / 2;
            if (nums[mid]>=target){
                r = mid;
            }else if(nums[mid]<target){
                l = mid+1;
            }
        }
        if(nums[l]!=target){
            return new int[]{-1,-1};
        }
        int left=l;
        r=nums.length-1;
        while(l+1<r){
            int mid = l + (r - l) / 2;
            if (nums[mid]<=target){
                l = mid;
            }else if(nums[mid]>target){
                r = mid-1;
            }
        }
        if(nums[r]==target){
            return new int[]{left,r};
        }else{
            return new int[]{left,l};
        }
        
    }
}
```

## 找到 K 个最接近的元素

给定一个排序好的数组，两个整数 `k` 和 `x`，从数组中找到最靠近 `x`（两数之差最小）的 `k` 个数。返回的结果必须要是按升序排好的。如果有两个数与 `x` 的差值一样，优先选择数值较小的那个数。

**示例 1:**

```
输入: [1,2,3,4,5], k=4, x=3
输出: [1,2,3,4]
```

 

**示例 2:**

```
输入: [1,2,3,4,5], k=4, x=-1
输出: [1,2,3,4]
```

**双指针：**

``` java
class Solution {
    public List<Integer> findClosestElements(int[] arr, int k, int x) {
        int l=0;
        int r=arr.length-1;
        List<Integer> result = new ArrayList<>();
        while (r-l+1>k){
            if(Math.abs(arr[r]-x)<Math.abs(arr[l]-x)){
                l++;
            }else {
                r--;
            }
        }
        for (int i = l; i <= r; i++) {
            result.add(arr[i]);
        }
        return result;

    }
}
```

**二分查找，确定左边界即可，利用长度k来确定有边界**

``` java
import java.util.ArrayList;
import java.util.List;

public class Solution {

    public List<Integer> findClosestElements(int[] arr, int k, int x) {
        int size = arr.length;

        int left = 0;
        int right = size - k;

        while (left < right) {
            // int mid = left + (right - left) / 2;
            int mid = (left + right) >>> 1;
            // 尝试从长度为 k + 1 的连续子区间删除一个元素
            // 从而定位左区间端点的边界值
            if (x - arr[mid] > arr[mid + k] - x) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }

        List<Integer> res = new ArrayList<>();
        for (int i = left; i < left + k; i++) {
            res.add(arr[i]);
        }
        return res;
    }
}
```

## 寻找比目标字母大的最小字母

给定一个只包含小写字母的有序数组`letters` 和一个目标字母 `target`，寻找有序数组里面比目标字母大的最小字母。

数组里字母的顺序是循环的。举个例子，如果目标字母`target = 'z'` 并且有序数组为 `letters = ['a', 'b']`，则答案返回 `'a'`。

**示例:**

```
输入:
letters = ["c", "f", "j"]
target = "a"
输出: "c"

输入:
letters = ["c", "f", "j"]
target = "c"
输出: "f"

输入:
letters = ["c", "f", "j"]
target = "d"
输出: "f"

输入:
letters = ["c", "f", "j"]
target = "g"
输出: "j"

输入:
letters = ["c", "f", "j"]
target = "j"
输出: "c"

输入:
letters = ["c", "f", "j"]
target = "k"
输出: "c"
```

**二分查找**

``` java
class Solution {
    public char nextGreatestLetter(char[] letters, char target) {
        int l=0;
        int r=letters.length-1;
        if(letters[r]-target<=0||letters[l]-target>0){
            return letters[l];
        }
        while (l<r){
            int mid = l+(r-l)/2;
            if(letters[mid]>target){
                r=mid;
            }else if(letters[mid]<=target){
                l=mid+1;
            }
        }
        return letters[l];
    }
}
```

## 寻找旋转排序数组中的最小值 II

假设按照升序排序的数组在预先未知的某个点上进行了旋转。

( 例如，数组 `[0,1,2,4,5,6,7]` 可能变为 `[4,5,6,7,0,1,2]` )。

请找出其中最小的元素。

注意数组中可能存在重复的元素。

**示例 1：**

```
输入: [1,3,5]
输出: 1
```

**示例 2：**

```
输入: [2,2,2,0,1]
输出: 0
```

**只比较一边**

``` java
class Solution {
    public int findMin(int[] nums) {
        int left = 0, right = nums.length - 1;
        while (left < right) {
            int mid = (left + right) / 2;
            if (nums[mid] > nums[right]) left = mid + 1;
            else if (nums[mid] < nums[right]) right = mid;
            else right = right - 1;
        }
        return nums[left];
    }
}
```

**比较两边**

``` java
class Solution {
    public int findMin(int[] nums) {
        if(nums==null||nums.length==0){
            return -1;
        }
        int l=0;
        int r=nums.length-1;
        while (l<r){
            if(nums[l]<nums[r]){
                return nums[l];
            }
            int mid = l+(r-l)/2;
            if(nums[mid]==nums[l]){
                l++;
            }else if(nums[mid]==nums[r]){
                r--;
            }else if(nums[mid]>nums[l]){
                l=mid+1;
            }else if(nums[mid]<nums[r]){
                r=mid;
            }
            
        }
        return nums[l];
    }
}
```

## 寻找重复数

给定一个包含 *n* + 1 个整数的数组 *nums*，其数字都在 1 到 *n* 之间（包括 1 和 *n*），可知至少存在一个重复的整数。假设只有一个重复的整数，找出这个重复的数。

**示例 1:**

```
输入: [1,3,4,2,2]
输出: 2
```

**示例 2:**

```
输入: [3,1,3,4,2]
输出: 3
```

**弗洛伊德的乌龟和兔子（循环检测）**

如果我们对 nums 进行这样的解释，即对于每对索引 ii 和值 v_iv 
i
	
  而言，“下一个” v_jv 
j
	
  位于索引 v_iv 
i
	
  处，我们可以将此问题减少到循环检测。

算法：
首先，我们可以很容易地证明问题的约束意味着必须存在一个循环。因为 nums 中的每个数字都在 11 和 nn 之间，所以它必须指向存在的索引。此外，由于 00 不能作为 nums 中的值出现，nums[0] 不能作为循环的一部分。

``` java
class Solution {
    public int findDuplicate(int[] nums) {
        // Find the intersection point of the two runners.
        int tortoise = nums[0];
        int hare = nums[0];
        do {
            tortoise = nums[tortoise];
            hare = nums[nums[hare]];
        } while (tortoise != hare);

        // Find the "entrance" to the cycle.
        int ptr1 = nums[0];
        int ptr2 = tortoise;
        while (ptr1 != ptr2) {
            ptr1 = nums[ptr1];
            ptr2 = nums[ptr2];
        }

        return ptr1;
    }
}
```

## 找出第 k 小的距离对0

给定一个整数数组，返回所有数对之间的第 k 个最小**距离**。一对 (A, B) 的距离被定义为 A 和 B 之间的绝对差值。

**示例 1:**

```
输入：
nums = [1,3,1]
k = 1
输出：0 
解释：
所有数对如下：
(1,3) -> 2
(1,1) -> 0
(3,1) -> 2
因此第 1 个最小距离的数对是 (1,1)，它们之间的距离为 0。
```

**二分查找 + 双指针** 

``` java
class Solution {
    public int smallestDistancePair(int[] nums, int k) {
        Arrays.sort(nums);

        int lo = 0;
        int hi = nums[nums.length - 1] - nums[0];
        while (lo < hi) {
            int mi = (lo + hi) / 2;
            int count = 0, left = 0;
            for (int right = 0; right < nums.length; ++right) {
                while (nums[right] - nums[left] > mi) left++;
                count += right - left;
            }
            //count = number of pairs with distance <= mi
            if (count >= k) hi = mi;
            else lo = mi + 1;
        }
        return lo;
    }
}
```

## 分割数组的最大值

给定一个非负整数数组和一个整数 *m*，你需要将这个数组分成 *m* 个非空的连续子数组。设计一个算法使得这 *m* 个子数组各自和的最大值最小。

**注意:**
数组长度 *n* 满足以下条件:

- 1 ≤ *n* ≤ 1000
- 1 ≤ *m* ≤ min(50, *n*)

**示例:**

```
输入:
nums = [7,2,5,10,8]
m = 2

输出:
18

解释:
一共有四种方法将nums分割为2个子数组。
其中最好的方式是将其分为[7,2,5] 和 [10,8]，
因为此时这两个子数组各自的和的最大值为18，在所有情况中最小。
```

**动态规划**

思路

这个问题满足无后向性的特点。我们可以用动态规划来解决它。

无后向性的特点意味着，一旦当前状态确定了，它就不会被之后的状态影响。在这个问题里面，如果我们在将 nums[0..i] 分成 j 份时得到了当前最小的分割数组的最大值，不论后面的部分怎么分割这个值不会受到影响。

算法

首先我们把 f[i] [j] 定义为将 nums[0..i] 分成 j 份时能得到的最小的分割子数组最大值。

对于第 j 个子数组，它为数组中下标 k + 1 到 i 的这一段。因此，f[i] [j] 可以从 max(f[k] [j - 1], nums[k + 1] + ... + nums[i]) 这个公式中得到。遍历所有可能的 k，会得到 f[i] [j] 的最小值。

整个算法那的最终答案为 f[n] [m]，其中 n 为数组大小。

``` java
class Solution {
    public int splitArray(int[] nums, int m) {
        int n = nums.length;
        int[][] f = new int[n + 1][m + 1];
        int[] sub = new int[n + 1];
        for (int i = 0; i <= n; i++) {
            for (int j = 0; j <= m; j++) {
                f[i][j] = Integer.MAX_VALUE;
            }
        }
        for (int i = 0; i < n; i++) {
            sub[i + 1] = sub[i] + nums[i];
        }
        f[0][0] = 0;
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                for (int k = 0; k < i; k++) {
                    f[i][j] = Math.min(f[i][j], Math.max(f[k][j - 1], sub[i] - sub[k]));
                }
            }
        }
        return f[n][m];        
    }
}
```

**二分搜索 + 贪心**

``` java
class Solution {
    public int splitArray(int[] nums, int m) {
        long l = 0;
        long r = 0;        
        int n = nums.length;
        for (int i = 0; i < n; i++) {
            r += nums[i];
            if (l < nums[i]) {
                l = nums[i];
            }
        }
        long ans = r;
        while (l <= r) {
            long mid = (l + r) >> 1;
            long sum = 0;
            int cnt = 1;
            for (int i = 0; i < n; i++) {
                if (sum + nums[i] > mid) {
                    cnt ++;
                    sum = nums[i];
                } else {
                    sum += nums[i];
                }
            }
            if (cnt <= m) {
                ans = Math.min(ans, mid);
                r = mid - 1;
            } else {
                l = mid + 1;
            }
        }
        return (int)ans;      
    }
}
```

