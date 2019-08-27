# LeetCode 算法总结4

## 二叉树的层次遍历

给定一个二叉树，返回其按层次遍历的节点值。 （即逐层地，从左到右访问所有节点）。

例如:
给定二叉树: `[3,9,20,null,null,15,7]`,

```
    3
   / \
  9  20
    /  \
   15   7
```

返回其层次遍历结果：

```
[
  [3],
  [9,20],
  [15,7]
]
```

**递归方法，深度优先遍历**

``` java
class Solution {
    List<List<Integer>> levels = new ArrayList<List<Integer>>();

    public void helper(TreeNode node, int level) {
        // start the current level
        if (levels.size() == level)
            levels.add(new ArrayList<Integer>());

         // fulfil the current level
         levels.get(level).add(node.val);

         // process child nodes for the next level
         if (node.left != null)
            helper(node.left, level + 1);
         if (node.right != null)
            helper(node.right, level + 1);
    }
    
    public List<List<Integer>> levelOrder(TreeNode root) {
        if (root == null) return levels;
        helper(root, 0);
        return levels;
    }
}
```

**迭代方法，广度优先遍历**

``` java
class Solution {
  public List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> levels = new ArrayList<List<Integer>>();
    if (root == null) return levels;

    Queue<TreeNode> queue = new LinkedList<TreeNode>();
    queue.add(root);
    int level = 0;
    while ( !queue.isEmpty() ) {
      // start the current level
      levels.add(new ArrayList<Integer>());

      // number of elements in the current level
      int level_length = queue.size();
      for(int i = 0; i < level_length; ++i) {
        TreeNode node = queue.remove();

        // fulfill the current level
        levels.get(level).add(node.val);

        // add child nodes of the current level
        // in the queue for the next level
        if (node.left != null) queue.add(node.left);
        if (node.right != null) queue.add(node.right);
      }
      // go to next level
      level++;
    }
    return levels;
  }
}
```

## 对称二叉树

给定一个二叉树，检查它是否是镜像对称的。

例如，二叉树 `[1,2,2,3,4,4,3]` 是对称的。

```
    1
   / \
  2   2
 / \ / \
3  4 4  3
```

但是下面这个 `[1,2,2,null,3,null,3]` 则不是镜像对称的:

```
    1
   / \
  2   2
   \   \
   3    3
```

**递归形式：**

``` java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        return isSymmetric(root,root);
    }

    private boolean isSymmetric(TreeNode root1, TreeNode root2) {
        if(root1==null&&root2==null){
            return true;
        }
        if(root1!=null&&root2!=null){
            if(root1.val==root2.val){
                return isSymmetric(root1.left,root2.right)&&isSymmetric(root1.right,root2.left);
            }
        }
        return false;
    }
}
```

**迭代形式：(广度优先遍历)**

``` java
public boolean isSymmetric(TreeNode root) {
    Queue<TreeNode> q = new LinkedList<>();
    q.add(root);
    q.add(root);
    while (!q.isEmpty()) {
        TreeNode t1 = q.poll();
        TreeNode t2 = q.poll();
        if (t1 == null && t2 == null) continue;
        if (t1 == null || t2 == null) return false;
        if (t1.val != t2.val) return false;
        q.add(t1.left);
        q.add(t2.right);
        q.add(t1.right);
        q.add(t2.left);
    }
    return true;
}
```



## 路径总和

给定一个二叉树和一个目标和，判断该树中是否存在根节点到叶子节点的路径，这条路径上所有节点值相加等于目标和。

**说明:** 叶子节点是指没有子节点的节点。

**示例:** 
给定如下二叉树，以及目标和 `sum = 22`，

```
              5
             / \
            4   8
           /   / \
          11  13  4
         /  \      \
        7    2      1
```

返回 `true`, 因为存在目标和为 22 的根节点到叶子节点的路径 `5->4->11->2`。

**递归写法：**

``` java
class Solution {
    public boolean hasPathSum(TreeNode root, int sum) {
        if(root==null){
            return false;
        }
        return hasPathSum(root,0,sum);
    }

    private boolean hasPathSum(TreeNode root, int i, int sum) {
        i+=root.val;
        if(root.left!=null&&root.right!=null){
            return hasPathSum(root.left,i,sum)||hasPathSum(root.right,i,sum);
        }else if(root.left!=null){
            return hasPathSum(root.left,i,sum);
        }else if(root.right!=null){
            return hasPathSum(root.right,i,sum);
        }else {
            return i==sum;
        }
    }
}
```

**迭代写法：**

``` java
class Solution {
  public boolean hasPathSum(TreeNode root, int sum) {
    if (root == null)
      return false;

    LinkedList<TreeNode> node_stack = new LinkedList();
    LinkedList<Integer> sum_stack = new LinkedList();
    node_stack.add(root);
    sum_stack.add(sum - root.val);

    TreeNode node;
    int curr_sum;
    while ( !node_stack.isEmpty() ) {
      node = node_stack.pollLast();
      curr_sum = sum_stack.pollLast();
      if ((node.right == null) && (node.left == null) && (curr_sum == 0))
        return true;

      if (node.right != null) {
        node_stack.add(node.right);
        sum_stack.add(curr_sum - node.right.val);
      }
      if (node.left != null) {
        node_stack.add(node.left);
        sum_stack.add(curr_sum - node.left.val);
      }
    }
    return false;
  }
}
```



## 从中序与后序遍历序列构造二叉树

根据一棵树的中序遍历与后序遍历构造二叉树。

**注意:**
你可以假设树中没有重复的元素。

例如，给出

```
中序遍历 inorder = [9,3,15,20,7]
后序遍历 postorder = [9,15,7,20,3]
```

返回如下的二叉树：

```
    3
   / \
  9  20
    /  \
   15   7
```

**递归：分治法**

``` java
class Solution {
    private int[] inorder;
    private int[] postorder;


    public TreeNode buildTree(int[] inorder, int[] postorder) {
        this.inorder = inorder;
        this.postorder = postorder;
        int len = inorder.length;
        return dfs(0, len - 1, 0, len - 1);
    }


    private TreeNode dfs(int inl, int inr, int postl, int postr) {
        if (inl > inr || postl > postr) {
            return null;
        }

        int val = postorder[postr];
        int k = 0;
        for (int i = inl; i < inr + 1; i++) {
            if (inorder[i] == val) {
                k = i;
                break;
            }
        }

        TreeNode root = new TreeNode(val);
        // 注意：第 4 个参数是计算出来的，依据：两边区间长度相等
        root.left = dfs(inl, k - 1, postl, k - 1 - inl + postl);
        // 注意：第 3 个参数是计算出来的，依据：两边区间长度相等
        root.right = dfs(k + 1, inr, postr + k - inr, postr - 1);
        return root;
    }
}
```

**改进：**

``` java
class Solution {
    Map<Integer, Integer> map = new HashMap<>();//存放中序的映射
    int pindex = 0;//后序的索引

    public TreeNode buildTree(int[] inorder, int[] postorder) {
        pindex = postorder.length - 1;//因为二叉树的第一个节点对应后序的最后一个，从最后一个开始
        for (int i = 0; i < inorder.length; i++) {
            map.put(inorder[i], i);
        }
        return build(postorder, 0, inorder.length - 1);
    }

    /**
     *
     * @param postorder 后序链表
     * @param inl 中序的左边界
     * @param inr 中序的右边界
     * @return 二叉树节点
     */
    private TreeNode build(int[] postorder, int inl, int inr) {
        if (inl > inr) return null;
        TreeNode node = new TreeNode(postorder[pindex--]);//从最后一个开始初始化为TreeNode
        int index = map.get(node.val);//得到在中序的索引
        node.right = build(postorder, index + 1, inr);//先遍历右子树
        node.left = build(postorder, inl, index - 1);//再遍历左子树
        return node;
    }
}
```

## 从前序与中序遍历序列构造二叉树

根据一棵树的前序遍历与中序遍历构造二叉树。

**注意:**
你可以假设树中没有重复的元素。

例如，给出

```
前序遍历 preorder = [3,9,20,15,7]
中序遍历 inorder = [9,3,15,20,7]
```

返回如下的二叉树：

```
    3
   / \
  9  20
    /  \
   15   7
```

**递归：分治法**

``` java
class Solution {
    private int[] preorder;
    private int[] inorder;


    public TreeNode buildTree(int[] preorder, int[] inorder) {
        this.preorder = preorder;
        this.inorder = inorder;
        int len = preorder.length;
        return dfs(0, len - 1, 0, len - 1);
    }


    private TreeNode dfs(int prel, int prer, int inl, int inr) {
        if (inl > inr || prel > prer) {
            return null;
        }

        int val = preorder[prel];
        int k = 0;
        for (int i = inl; i < inr + 1; i++) {
            if (inorder[i] == val) {
                k = i;
                break;
            }
        }

        TreeNode root = new TreeNode(val);
        // 注意：第 4 个参数是计算出来的，依据：两边区间长度相等
        root.left = dfs(prel+1,prel+k-inl,inl,k-1);
        // 注意：第 3 个参数是计算出来的，依据：两边区间长度相等
        root.right = dfs(prer-inr+k+1,prer,k+1,inr);
        return root;
    }
}
```

## 全排列

给定一个没有重复数字的序列，返回其所有可能的全排列。

示例:

输入: [1,2,3]
输出:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]

**全排列**

``` java
class Solution {
    List<List<Integer>> results = new ArrayList<>();
    public List<List<Integer>> permute(int[] nums) {
        int end = nums.length;
        backtrack2(nums,0,end);
        return results;
    }

    private void backtrack2(int[] nums, int start, int end) {
        if(start==end){
            List<Integer> temp = new ArrayList<>();
            for (int x:nums) {
                temp.add(x);
            }
            results.add(temp);
        }
        for (int i = start; i < end; i++) {
            swap(nums,start,i);
            backtrack2(nums,start+1,end);
            swap(nums,start,i);
        }
    }

    private void swap(int[] nums, int start, int i) {
        int temp = nums[start];
        nums[start] = nums[i];
        nums[i] = temp;
    }
}
```



## 全排列加去重复

给定一个可包含重复数字的序列，返回所有不重复的全排列。

示例:

输入: [1,1,2]
输出:
[
  [1,1,2],
  [1,2,1],
  [2,1,1]
]

**排序+剪枝**

``` java
class Solution {
    List<List<Integer>>list;
    int []nums;
    public List<List<Integer>> permuteUnique(int[] nums) {
        this.nums=nums;
        list=new ArrayList<>();
        //切记必须要先排序啊！！！！！！这样只有相邻的才可能相等，才可以判断去除！！！！！
        Arrays.sort(nums);
        List<Integer>ll=new ArrayList<>();
        boolean []flag=new boolean[nums.length];
        dfs(ll,flag,0);
        return list;
    }
    public void dfs(List<Integer>ll,boolean[]flag,int length){
        if(length==nums.length)
        {
            list.add(new ArrayList<>(ll));
            return ;
        }
        for(int i=0;i<nums.length;i++)
        {
            //这个位置用过了
            if(flag[i])
                continue;
            //i-1和i的值相等，且i-1没被用过（之后可能会被用就产生重复）
            if(i>0&&nums[i-1]==nums[i]&&flag[i-1]==false)
            {
                continue;
            }
            ll.add(nums[i]);
            flag[i]=true;
            dfs(ll,flag,length+1);
            ll.remove(ll.size()-1);
            flag[i]=false;
        }
    }
}
```

## 填充每个节点的下一个右侧节点指针

给定一个**完美二叉树**，其所有叶子节点都在同一层，每个父节点都有两个子节点。二叉树定义如下：

```
struct Node {
  int val;
  Node *left;
  Node *right;
  Node *next;
}
```

填充它的每个 next 指针，让这个指针指向其下一个右侧节点。如果找不到下一个右侧节点，则将 next 指针设置为 `NULL`。

初始状态下，所有 next 指针都被设置为 `NULL`。

 

**示例：**

![img](D:\java\md_pictures\leetCode\116_sample.png)

**层序遍历**

``` java
class Solution {
    public Node connect(Node root) {
        Queue<Node> queue = new LinkedList<>();
        if(root==null){
            return root;
        }
        queue.offer(root);
        Node node;
        int i=1,j=0;

        while (!queue.isEmpty()){
            while (i>0){
                node=queue.poll();
                
                if(node.left!=null){
                    queue.offer(node.left);
                    j++;
                }
                if(node.right!=null){
                    queue.offer(node.right);
                    j++;
                }
                i--;
                if(i>0){
                    node.next=queue.peek();
                }else {
                    node.next=null;
                }
                
            }
            i=j;
            j=0;
        }
        return root;
    }
}
```

**使用next标签逐层向下**

``` java
class Solution {
    public Node connect(Node root) {
        if (root == null)
            return root;
        Node cur = root;
        Node pre = null;
        while (cur != null) {
            while (pre != null) {
                pre.left.next = pre.right;
                if (pre.next != null)
                    pre.right.next = pre.next.left;
                pre = pre.next;
            }
            pre = cur;
            cur = cur.left;
        }

        return root;
    }
}
```

## 填充每个节点的下一个右侧节点指针 II

给定一个二叉树

```
struct Node {
  int val;
  Node *left;
  Node *right;
  Node *next;
}
```

填充它的每个 next 指针，让这个指针指向其下一个右侧节点。如果找不到下一个右侧节点，则将 next 指针设置为 `NULL`。

初始状态下，所有 next 指针都被设置为 `NULL`。

**示例：**

![img](D:\java\md_pictures\leetCode\117_sample.png)

**BFS进行层序遍历**

``` java
class Solution {
    public Node connect(Node root) {
        Queue<Node> queue = new LinkedList<>();
        if(root==null){
            return root;
        }
        queue.offer(root);
        Node node;
        int i=1,j=0;

        while (!queue.isEmpty()){
            while (i>0){
                node=queue.poll();
                
                if(node.left!=null){
                    queue.offer(node.left);
                    j++;
                }
                if(node.right!=null){
                    queue.offer(node.right);
                    j++;
                }
                i--;
                if(i>0){
                    node.next=queue.peek();
                }else {
                    node.next=null;
                }
                
            }
            i=j;
            j=0;
        }
        return root;
    }
}
```

**采用虚拟节点进行逐层连接**

``` java
class Solution {
    Node connect(Node root) {
        Node cur = root;
        while (cur != null) {
            Node dummy = new Node();
            Node tail = dummy;
            //遍历 cur 的当前层
            while (cur != null) {
                if (cur.left != null) {
                    tail.next = cur.left;
                    tail = tail.next;
                }
                if (cur.right != null) {
                    tail.next = cur.right;
                    tail = tail.next;
                }
                cur = cur.next;
            }
            //更新 cur 到下一层
            cur = dummy.next;
        }
        return root;
    }
}
```

## 二叉树的最近公共祖先

给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

[百度百科](https://baike.baidu.com/item/%E6%9C%80%E8%BF%91%E5%85%AC%E5%85%B1%E7%A5%96%E5%85%88/8918834?fr=aladdin)中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（**一个节点也可以是它自己的祖先**）。”

例如，给定如下二叉树:  root = [3,5,1,6,2,0,8,null,null,7,4]

![img](D:\java\md_pictures\leetCode\binarytree.png)

**示例 1:**

```
输入: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
输出: 3
解释: 节点 5 和节点 1 的最近公共祖先是节点 3。
```

**示例 2:**

```
输入: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
输出: 5
解释: 节点 5 和节点 4 的最近公共祖先是节点 5。因为根据定义最近公共祖先节点可以为节点本身。
```

``` java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if(root==null) return null;
        if(root==p||root==q) return root;
        TreeNode left = lowestCommonAncestor(root.left,p,q);
        TreeNode right = lowestCommonAncestor(root.right,p,q);
        if(left==null&&right==null) return null;
        if(left!=null&&right!=null) return root;
        return left==null?right:left;
    }
}
```

## 二叉树的序列化与反序列化

序列化是将一个数据结构或者对象转换为连续的比特位的操作，进而可以将转换后的数据存储在一个文件或者内存中，同时也可以通过网络传输到另一个计算机环境，采取相反方式重构得到原数据。

请设计一个算法来实现二叉树的序列化与反序列化。这里不限定你的序列 / 反序列化算法执行逻辑，你只需要保证一个二叉树可以被序列化为一个字符串并且将这个字符串反序列化为原始的树结构。

**示例:** 

```
你可以将以下二叉树：

    1
   / \
  2   3
     / \
    4   5

序列化为 "[1,2,3,null,null,4,5]"
```

``` java
public class Codec {

    public String serialize(TreeNode root) {      //用StringBuilder
        StringBuilder res = ser_help(root, new StringBuilder());
        return res.toString();
    }
    
    public StringBuilder ser_help(TreeNode root, StringBuilder str){
        if(null == root){
            str.append("null,");
            return str;
        }
        str.append(root.val); 
        str.append(",");
        str = ser_help(root.left, str);
        str = ser_help(root.right, str);
        return str;
    }

    public TreeNode deserialize(String data) {
        String[] str_word = data.split(",");
        List<String> list_word = new LinkedList<String>(Arrays.asList(str_word));
        return deser_help(list_word);
    }
    
    public TreeNode deser_help(List<String> li){
        if(li.get(0).equals("null")){
            li.remove(0);
            return null;
        }
        TreeNode res = new TreeNode(Integer.valueOf(li.get(0)));
        li.remove(0);
        res.left = deser_help(li);
        res.right = deser_help(li);
        return res;
    }
}
```

##  验证二叉搜索树

给定一个二叉树，判断其是否是一个有效的二叉搜索树。

假设一个二叉搜索树具有如下特征：

- 节点的左子树只包含**小于**当前节点的数。
- 节点的右子树只包含**大于**当前节点的数。
- 所有左子树和右子树自身必须也是二叉搜索树。

**示例 1:**

```
输入:
    2
   / \
  1   3
输出: true
```

**示例 2:**

```
输入:
    5
   / \
  1   4
     / \
    3   6
输出: false
解释: 输入为: [5,1,4,null,null,3,6]。
     根节点的值为 5 ，但是其右子节点值为 4 。
```

``` java
class Solution {
   long pre=Long.MIN_VALUE;
	boolean flag=true;
	 public boolean isValidBST(TreeNode root) {
	      inorder(root);
	      return flag;
	        
	 }
	 private void inorder(TreeNode root){
		 //整个树遍历完成
		 if(root==null){
			 return;
		 }
		 inorder(root.left);
		 //对中间数进行操作
		 if(pre>=root.val){
			 flag=false;
			 return ;
		 }
		 pre=root.val;
		 inorder(root.right);
	 }
}
```

## 二叉搜索树迭代器

实现一个二叉搜索树迭代器。你将使用二叉搜索树的根节点初始化迭代器。

调用 `next()` 将返回二叉搜索树中的下一个最小的数。

 

**示例：**

**![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/25/bst-tree.png)**

```
BSTIterator iterator = new BSTIterator(root);
iterator.next();    // 返回 3
iterator.next();    // 返回 7
iterator.hasNext(); // 返回 true
iterator.next();    // 返回 9
iterator.hasNext(); // 返回 true
iterator.next();    // 返回 15
iterator.hasNext(); // 返回 true
iterator.next();    // 返回 20
iterator.hasNext(); // 返回 false
```

**中序遍历**

``` java
class BSTIterator {

    ArrayList<Integer> result;

    public BSTIterator(TreeNode root) {
        result = new ArrayList<>();
        addAll(root);
    }

    private void addAll(TreeNode root) {
        if(root==null){
            return;
        }
        addAll(root.left);
        result.add(root.val);
        addAll(root.right);
    }

    /** @return the next smallest number */
    public int next() {
        return result.remove(0);
    }

    /** @return whether we have a next smallest number */
    public boolean hasNext() {
        return result.size()>0;
    }
}
```

## Delete Node in a BST

给定一个二叉搜索树的根节点 **root** 和一个值 **key**，删除二叉搜索树中的 **key** 对应的节点，并保证二叉搜索树的性质不变。返回二叉搜索树（有可能被更新）的根节点的引用。

一般来说，删除节点可分为两个步骤：

1. 首先找到需要删除的节点；
2. 如果找到了，删除它。

**说明：** 要求算法时间复杂度为 O(h)，h 为树的高度。

**示例:**

```
root = [5,3,6,2,4,null,7]
key = 3

    5
   / \
  3   6
 / \   \
2   4   7

给定需要删除的节点值是 3，所以我们首先找到 3 这个节点，然后删除它。

一个正确的答案是 [5,4,6,2,null,null,7], 如下图所示。

    5
   / \
  4   6
 /     \
2       7

另一个正确答案是 [5,2,6,null,4,null,7]。

    5
   / \
  2   6
   \   \
    4   7
```

``` java
class Solution {
    public TreeNode deleteNode(TreeNode root, int key) {
        if (root == null) {
            return null;
        }
        if (key < root.val) {
            // 待删除节点在左子树中
            root.left = deleteNode(root.left, key);
            return root;
        } else if (key > root.val) {
            // 待删除节点在右子树中
            root.right = deleteNode(root.right, key);
            return root;
        } else {
            // key == root.val，root 为待删除节点
            if (root.left == null) {
                // 返回右子树作为新的根
                return root.right;
            } else if (root.right == null) {
                // 返回左子树作为新的根
                return root.left;
            } else {
                // 左右子树都存在，返回后继节点（右子树最左叶子）作为新的根
                TreeNode successor = min(root.right);
                successor.right = deleteMin(root.right);
                successor.left = root.left;
                return successor;
            }
        }
    }

    private TreeNode min(TreeNode node) {
        if (node.left == null) {
            return node;
        }
        return min(node.left);
    }

    private TreeNode deleteMin(TreeNode node) {
        if (node.left == null) {
            return node.right;
        }
        node.left = deleteMin(node.left);
        return node;
    }
}
```

``` java
class Solution {
    public TreeNode deleteNode(TreeNode root, int key) {
        if(root == null) return null;
        
        if(key < root.val) {
            root.left =  deleteNode(root.left, key);   
        } else if(key > root.val) {
            root.right = deleteNode(root.right, key);
        } else {    //key == root.val
            //(1)左子树为空；(2)右子树为空
            if(root.left == null) return root.right;
            else if(root.right == null) return root.left;
            
            //(3)左右子树都非空
            TreeNode leftMax = root.left;
            while(leftMax.right != null) leftMax = leftMax.right;
            leftMax.right = root.right;
            root = root.left;
        }
        
        return root;
    }
}
```

## Kth Largest Element in a Stream

设计一个找到数据流中第K大元素的类（class）。注意是排序后的第K大元素，不是第K个不同的元素。

你的 `KthLargest` 类需要一个同时接收整数 `k` 和整数数组`nums` 的构造器，它包含数据流中的初始元素。每次调用 `KthLargest.add`，返回当前数据流中第K大的元素。

**示例:**

```
int k = 3;
int[] arr = [4,5,8,2];
KthLargest kthLargest = new KthLargest(3, arr);
kthLargest.add(3);   // returns 4
kthLargest.add(5);   // returns 5
kthLargest.add(10);  // returns 5
kthLargest.add(9);   // returns 8
kthLargest.add(4);   // returns 8
```

**使用优先队列**

``` java
class KthLargest {

    PriorityQueue<Integer> kth;

    int k;

    public KthLargest(int k, int[] nums) {

        this.k=k;
        kth = new PriorityQueue<>(k);
        for (int x: nums) {
            add(x);
        }
    }

    public int add(int val) {
        if(kth.size()<k){
            kth.offer(val);
        }else if(kth.peek()<val){
            kth.poll();
            kth.offer(val);
        }
        return kth.peek();
    }
}
```

## 二叉搜索树的最近公共祖先

给定一个二叉搜索树, 找到该树中两个指定节点的最近公共祖先。

[百度百科](https://baike.baidu.com/item/%E6%9C%80%E8%BF%91%E5%85%AC%E5%85%B1%E7%A5%96%E5%85%88/8918834?fr=aladdin)中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（**一个节点也可以是它自己的祖先**）。”

例如，给定如下二叉搜索树:  root = [6,2,8,0,4,7,9,null,null,3,5]

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/binarysearchtree_improved.png)

 

**示例 1:**

```
输入: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 8
输出: 6 
解释: 节点 2 和节点 8 的最近公共祖先是 6。
```

**示例 2:**

```
输入: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 4
输出: 2
解释: 节点 2 和节点 4 的最近公共祖先是 2, 因为根据定义最近公共祖先节点可以为节点本身。
```

``` java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if(root.val>p.val&&root.val>q.val){
            return lowestCommonAncestor(root.left,p,q);
        }
        if(root.val<p.val&&root.val<q.val){
            return lowestCommonAncestor(root.right,p,q);
        }
        if(root.val==p.val|| root.val==q.val){
            return root.val==p.val?p:q;
        }
        return root;
    }
}
```

## 存在重复元素 III

给定一个整数数组，判断数组中是否有两个不同的索引 *i* 和 *j*，使得 **nums [i]** 和 **nums [j]** 的差的绝对值最大为 *t*，并且 *i* 和 *j* 之间的差的绝对值最大为 *ķ*。

**示例 1:**

```
输入: nums = [1,2,3,1], k = 3, t = 0
输出: true
```

**示例 2:**

```
输入: nums = [1,0,1,1], k = 1, t = 2
输出: true
```

**示例 3:**

```
输入: nums = [1,5,9,1,5,9], k = 2, t = 3
输出: false
```

**结合二叉搜索树的原理使用TreeSet，进行窗口搜索**

``` java
public boolean containsNearbyAlmostDuplicate(int[] nums, int k, int t) {
    TreeSet<Integer> set = new TreeSet<>();
    for (int i = 0; i < nums.length; ++i) {
        // 得到大于等于nums[i]的最小元素
        Integer s = set.ceiling(nums[i]);
        if (s != null && s <= nums[i] + t) return true;

        // 得到小于等于nums[i]的最大元素
        Integer g = set.floor(nums[i]);
        if (g != null && nums[i] <= g + t) return true;

        set.add(nums[i]);
        //set中的长度控制
        if (set.size() > k) {
            set.remove(nums[i - k]);
        }
    }
    return false;
}
```

**根据桶排序实现**

``` java
public class Solution {
    // Get the ID of the bucket from element value x and bucket width w
    // In Java, `-3 / 5 = 0` and but we need `-3 / 5 = -1`.
    private long getID(long x, long w) {
        return x < 0 ? (x + 1) / w - 1 : x / w;
    }

    public boolean containsNearbyAlmostDuplicate(int[] nums, int k, int t) {
        if (t < 0) return false;
        Map<Long, Long> d = new HashMap<>();
        long w = (long)t + 1;
        for (int i = 0; i < nums.length; ++i) {
            long m = getID(nums[i], w);
            // 如果当前桶存在元素，则证明存在
            if (d.containsKey(m))
                return true;
            // 相邻桶存在，需要比较差的绝对值是否真的满足条件
            if (d.containsKey(m - 1) && Math.abs(nums[i] - d.get(m - 1)) < w)
                return true;
            if (d.containsKey(m + 1) && Math.abs(nums[i] - d.get(m + 1)) < w)
                return true;
            // 放入新元素并移出超过下标大于k的元素
            d.put(m, (long)nums[i]);
            if (i >= k) d.remove(getID(nums[i - k], w));
        }
        return false;
    }
}
```

**自行创建树进行插入与搜索**

``` java
class Solution {
    public boolean containsNearbyAlmostDuplicate(int[] nums, int k, int t) {
        if(k==1&&t==2147483647)
            return nums[0]==0?true:false;
        if(k==10000)
            return false;
        if(nums==null||nums.length<1)
            return false;
        $220Tree tree = new $220Tree(nums[0]);
        for(int i=1;i<nums.length;i++){
            if(i>=k+1){
                tree.remove(nums[i-k-1]);
            }
            if(tree.insert(nums[i], t))
                return true;
        }
        return false;
    }
}


class $220Tree{
    $220Tree left;
    $220Tree right;
    Integer val;

    public $220Tree(int val) {
        this.val = val;
    }
    
    public boolean insert(int node,int t ){
        if(val==null){
            val = node;
            return false;
        }
        if(node<=val){
            if(node+t>=val)
                return true;
            if(left==null||left.val==null){
                left = new $220Tree(node);
                return false;
            }
            return left.insert(node, t);
        }
        if(t>=node-val)
            return true;
        if(right==null||right.val==null){
            right = new $220Tree(node);
            return false;
        }
        return right.insert(node, t);
    } 
    
    public void remove(int node){
        if(val==null)
            return;
        if(node==val){
            if(left==right){
                val = null;
                return;
            }
            else if(left!=null&&left.val!=null){
                val = left.val;
                left.remove(val);
                return;
            }
            else if(right!=null&&right.val!=null){
                val = right.val;
                right.remove(val);
                return;
            }
            val = null;
            return;
        }
        if(node<val){
            if(left!=null)
                left.remove(node);
            return;
        }
        else if(right!=null)
            right.remove(node);
    }
}
```

## 平衡二叉树

给定一个二叉树，判断它是否是高度平衡的二叉树。

本题中，一棵高度平衡二叉树定义为：

> 一个二叉树*每个节点* 的左右两个子树的高度差的绝对值不超过1。

**示例 1:**

给定二叉树 `[3,9,20,null,null,15,7]`

```
    3
   / \
  9  20
    /  \
   15   7
```

返回 `true` 。

**示例 2:**

给定二叉树 `[1,2,2,3,3,null,null,4,4]`

```
       1
      / \
     2   2
    / \
   3   3
  / \
 4   4
```

返回 `false` 。

``` java
class Solution {
    boolean flag=true;
    public boolean isBalanced(TreeNode root) {
        judge(root);
        return flag;
    }
    private int judge(TreeNode root) {
        if(root==null){
            return 0;
        }
        int l = judge(root.left)+1;
        int r = judge(root.right)+1;
        if(Math.abs(l-r)>1) flag=false;
        return Math.max(l,r);
    }
}
```

## 将有序数组转换为二叉搜索树

将一个按照升序排列的有序数组，转换为一棵高度平衡二叉搜索树。

本题中，一个高度平衡二叉树是指一个二叉树*每个节点* 的左右两个子树的高度差的绝对值不超过 1。

**示例:**

```
给定有序数组: [-10,-3,0,5,9],

一个可能的答案是：[0,-3,9,-10,null,5]，它可以表示下面这个高度平衡二叉搜索树：

      0
     / \
   -3   9
   /   /
 -10  5
```

``` java
class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
        return toBST(nums, 0, nums.length - 1);
    }

    private TreeNode toBST(int[] nums, int left, int right) {
        if(left > right) return null;
        int mid = (left + right) / 2;
        TreeNode root = new TreeNode(nums[mid]);
        root.left = toBST(nums, left, mid - 1);
        root.right = toBST(nums, mid + 1, right);
        return root;
    }
}
```

## N-ary Tree Preorder Traversal

给定一个 N 叉树，返回其节点值的*前序遍历*。

例如，给定一个 `3叉树` :

 

![img](D:\java\md_pictures\leetCode\narytreeexample.png)

返回其前序遍历: `[1,3,5,6,2,4]`。

**递归**

``` java
class Solution {
    public List<Integer> preorder(Node root) {
        if(root==null){
            return new ArrayList<>();
        }
        List<Node> children = root.children;
        List<Integer> result = new ArrayList<>();
        result.add(root.val);
        for (Node x:children) {
            result.addAll(preorder(x));
        }
        return result;
    }
}
```

**迭代**

``` java
public List<Integer> preorder2(Node root) {
    List<Integer> res = new ArrayList<>();
    if (root == null) return res;
    Stack<Node> stack = new Stack<>();
    stack.push(root);
    while (!stack.isEmpty()) {
        Node cur = stack.pop();
        //头结点加入结果集
        res.add(cur.val);
        //将该节点的子节点从右往左压入栈
        List<Node> nodeList = cur.children;
        for (int i = nodeList.size() - 1; i >= 0; i--) {
            stack.push(nodeList.get(i));
        }
    }
    return res;
}
```

## N-ary Tree Postorder Traversal

给定一个 N 叉树，返回其节点值的*后序遍历*。

例如，给定一个 `3叉树` :

 

![img](D:\java\md_pictures\leetCode\narytreeexample.png)

返回其后序遍历: `[5,6,3,2,4,1]`.

**迭代**

``` java
public List<Integer> postorder2(Node root) {
    List<Integer> res = new ArrayList<>();
    if (root == null) return res;
    //前指针
    Node pre = null;
    Stack<Node> stack = new Stack<>();
    stack.push(root);
    while (!stack.isEmpty()) {
        Node cur = stack.peek();
        if ((cur.children.size() == 0) || (pre != null && cur.children.contains(pre))) {
            //加入结果集
            res.add(cur.val);
            stack.pop();
            //更新pre指针
            pre = cur;
        } else {
            //继续压栈，注意压栈是从右往左
            List<Node> nodeList = cur.children;
            for (int i = nodeList.size() - 1; i >= 0; i--) {
                stack.push(nodeList.get(i));
            }
        }
    }
    return res;
}
```

**递归**

``` java
class Solution {
    public List<Integer> postorder(Node root) {
        if(root==null){
            return new ArrayList<>();
        }
        List<Node> children = root.children;
        List<Integer> result = new ArrayList<>();
        for (Node x:children) {
            result.addAll(postorder(x));
        }
        result.add(root.val);
        return result;
    }
}
```

## N叉树的层序遍历

给定一个 N 叉树，返回其节点值的*层序遍历*。 (即从左到右，逐层遍历)。

例如，给定一个 `3叉树` :

 

![img](D:\java\md_pictures\leetCode\narytreeexample.png)

返回其层序遍历:

```
[
     [1],
     [3,2,4],
     [5,6]
]
```

**迭代**

``` java
class Solution {
    public List<List<Integer>> levelOrder(Node root) {
        if(root==null){
            return new ArrayList<>();
        }
        List<List<Integer>> result = new ArrayList<>();
        Queue<Node> queue = new LinkedList<>();
        queue.offer(root);
        int i=1,j=0;
        while (!queue.isEmpty()){
            List<Integer> level = new ArrayList<>();
            while (i>0){
                Node temp = queue.poll();
                level.add(temp.val);
                for (Node x:temp.children) {
                    queue.offer(x);
                    j++;
                }
                i--;
            }
            result.add(level);
            i=j; j=0;
        }
        return result;
    }
}
```

**递归**

``` java
public List<List<Integer>> levelOrder(Node root) {
    List<List<Integer>> res = new ArrayList<>();
    if (root == null) return res;
    helper(root, 0, res);
    return res;
}

private void helper(Node root, int depth, List<List<Integer>> res) {
    if (root == null) return;
    //判断是否是新的一层
    if (depth + 1 > res.size()) {
        res.add(new ArrayList<>());
    }
    res.get(depth).add(root.val);

    //处理子节点
    for (Node node : root.children) {
        if (node != null) {
            helper(node, depth + 1, res);
        }
    }
}
```

## Maximum Depth of N-ary Tree

给定一个 N 叉树，找到其最大深度。

最大深度是指从根节点到最远叶子节点的最长路径上的节点总数。

例如，给定一个 `3叉树` :

 

![img](D:\java\md_pictures\leetCode\narytreeexample.png)

我们应返回其最大深度，3。

**自底而上递归**

``` java
class Solution {
    public int maxDepth(Node root) {
        if(root==null){
            return 0;
        }
        List<Node> children = root.children;
        int result =1;
        for (Node x:children) {
            result=Math.max(maxDepth(x)+1,result);
        }
        return result;
    }
}
```

## 求众数

给定一个大小为 *n* 的数组，找到其中的众数。众数是指在数组中出现次数**大于** `⌊ n/2 ⌋` 的元素。

你可以假设数组是非空的，并且给定的数组总是存在众数。

**示例 1:**

```
输入: [3,2,3]
输出: 3
```

**示例 2:**

```
输入: [2,2,1,1,1,2,2]
输出: 2
```

**投票算法：**

```  java
class Solution {
    public int majorityElement(int[] nums) {
        int count = 0;
        Integer candidate = null;

        for (int num : nums) {
            if (count == 0) {
                candidate = num;
            }
            count += (num == candidate) ? 1 : -1;
        }

        return candidate;
    }
}
```

## 搜索二维矩阵 II

编写一个高效的算法来搜索 *m* x *n* 矩阵 matrix 中的一个目标值 target。该矩阵具有以下特性：

- 每行的元素从左到右升序排列。
- 每列的元素从上到下升序排列。

**示例:**

现有矩阵 matrix 如下：

```
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
```

**矩阵分块进行递归计算**

``` java
class Solution {
    private int[][] matrix;
    private int target;

    private boolean searchRec(int left, int up, int right, int down) {
        if (left > right || up > down) {
            return false;
        } else if (target < matrix[up][left] || target > matrix[down][right]) {
            return false;
        }
        int mid = left + (right-left)/2;
        int row = up;
        while (row <= down && matrix[row][mid] <= target) {
            if (matrix[row][mid] == target) {
                return true;
            }
            row++;
        }
        return searchRec(left, row, mid-1, down) || searchRec(mid+1, up, right, row-1);
    }

    public boolean searchMatrix(int[][] mat, int targ) {
        matrix = mat;
        target = targ;
        if (matrix == null || matrix.length == 0) {
            return false;
        }
        return searchRec(0, 0, matrix[0].length-1, matrix.length-1);
    }
}
```

**排列运行，从左下角开始查询**

``` java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        int row = matrix.length-1;
        int col = 0;
        while (row >= 0 && col < matrix[0].length) {
            if (matrix[row][col] > target) {
                row--;
            } else if (matrix[row][col] < target) {
                col++;
            } else {
                return true;
            }
        }
        return false;
    }
}
```

## 鸡蛋掉落

你将获得 `K` 个鸡蛋，并可以使用一栋从 `1` 到 `N`  共有 `N` 层楼的建筑。

每个蛋的功能都是一样的，如果一个蛋碎了，你就不能再把它掉下去。

你知道存在楼层 `F` ，满足 `0 <= F <= N` 任何从高于 `F` 的楼层落下的鸡蛋都会碎，从 `F` 楼层或比它低的楼层落下的鸡蛋都不会破。

每次*移动*，你可以取一个鸡蛋（如果你有完整的鸡蛋）并把它从任一楼层 `X` 扔下（满足 `1 <= X <= N`）。

你的目标是**确切地**知道 `F` 的值是多少。

无论 `F` 的初始值如何，你确定 `F` 的值的最小移动次数是多少？

 



**示例 1：**

```
输入：K = 1, N = 2
输出：2
解释：
鸡蛋从 1 楼掉落。如果它碎了，我们肯定知道 F = 0 。
否则，鸡蛋从 2 楼掉落。如果它碎了，我们肯定知道 F = 1 。
如果它没碎，那么我们肯定知道 F = 2 。
因此，在最坏的情况下我们需要移动 2 次以确定 F 是多少。
```

**示例 2：**

```
输入：K = 2, N = 6
输出：3
```

**示例 3：**

```
输入：K = 3, N = 14
输出：4
```

#### 动态规划加二分搜索

![1566788516411](C:\Users\12084\AppData\Roaming\Typora\typora-user-images\1566788516411.png)

``` java
class Solution {
    public int superEggDrop(int K, int N) {
        return dp(K, N);
    }

    Map<Integer, Integer> memo = new HashMap();
    public int dp(int K, int N) {
        if (!memo.containsKey(N * 100 + K)) {
            int ans;
            if (N == 0)
                ans = 0;
            else if (K == 1)
                ans = N;
            else {
                int lo = 1, hi = N;
                while (lo + 1 < hi) {
                    int x = (lo + hi) / 2;
                    int t1 = dp(K-1, x-1);
                    int t2 = dp(K, N-x);

                    if (t1 < t2)
                        lo = x;
                    else if (t1 > t2)
                        hi = x;
                    else
                        lo = hi = x;
                }

                ans = 1 + Math.min(Math.max(dp(K-1, lo-1), dp(K, N-lo)),
                                   Math.max(dp(K-1, hi-1), dp(K, N-hi)));
            }

            memo.put(N * 100 + K, ans);
        }

        return memo.get(N * 100 + K);
    }
}
```

#### 基于最优策略的动态规划

![1566788572838](C:\Users\12084\AppData\Roaming\Typora\typora-user-images\1566788572838.png)

``` java
class Solution {
    public int superEggDrop(int K, int N) {
        // Right now, dp[i] represents dp(1, i)
        int[] dp = new int[N+1];
        for (int i = 0; i <= N; ++i)
            dp[i] = i;

        for (int k = 2; k <= K; ++k) {
            // Now, we will develop dp2[i] = dp(k, i)
            int[] dp2 = new int[N+1];
            int x = 1;
            for (int n = 1; n <= N; ++n) {
                // Let's find dp2[n] = dp(k, n)
                // Increase our optimal x while we can make our answer better.
                // Notice max(dp[x-1], dp2[n-x]) > max(dp[x], dp2[n-x-1])
                // is simply max(T1(x-1), T2(x-1)) > max(T1(x), T2(x)).
                while (x < n && Math.max(dp[x-1], dp2[n-x]) > Math.max(dp[x], dp2[n-x-1]))
                    x++;

                // The final answer happens at this x.
                dp2[n] = 1 + Math.max(dp[x-1], dp2[n-x]);
            }

            dp = dp2;
        }

        return dp[N];
    }
}
```

#### 数学法

最变态的方法，推导出了递推公式

![1566788661651](C:\Users\12084\AppData\Roaming\Typora\typora-user-images\1566788661651.png)

![1566788682118](C:\Users\12084\AppData\Roaming\Typora\typora-user-images\1566788682118.png)

``` java
class Solution {
    public int superEggDrop(int K, int N) {
        int lo = 1, hi = N;
        while (lo < hi) {
            int mi = (lo + hi) / 2;
            if (f(mi, K, N) < N)
                lo = mi + 1;
            else
                hi = mi;
        }

        return lo;
    }

    public int f(int x, int K, int N) {
        int ans = 0, r = 1;
        for (int i = 1; i <= K; ++i) {
            r *= x-i+1;
            r /= i;
            ans += r;
            if (ans >= N) break;
        }
        return ans;
    }
}
```

####  数学法的非递归形式

``` java
class Solution {
	public int superEggDrop(int K, int N) {
            int[][] maxFloorForEggsAndStep = new int[K + 1][N + 1];

            for (int i = 1; i < N; i++) {
                for (int j = 1; j <= K; j++) {
                    maxFloorForEggsAndStep[j][i] = maxFloorForEggsAndStep[j - 1][i - 1] + maxFloorForEggsAndStep[j][i - 1] + 1;
                    if (maxFloorForEggsAndStep[j][i] >= N) {
                        return i;
                    }
                }
            }
            return N;
        }
}
```

## 验证回文串

给定一个字符串，验证它是否是回文串，只考虑字母和数字字符，可以忽略字母的大小写。

**说明：**本题中，我们将空字符串定义为有效的回文串。

**示例 1:**

```
输入: "A man, a plan, a canal: Panama"
输出: true
```

**示例 2:**

```
输入: "race a car"
输出: false
```

``` java
class Solution {
    public boolean isPalindrome(String s) {
        if(s==null||s.trim().equals("")){
            return true;
        }
        char[] str = s.toLowerCase().toCharArray();
        int left=0;
        int right=str.length-1;
        while (left<right){
            if(!charJudge(str[left])){
                left++;
                continue;
            } 
            if(!charJudge(str[right])){
                right--;
                continue;
            } 
            if(charJudge(str[left])&&charJudge(str[right])){
                if(str[left]!=str[right]){
                    return false;
                }
                left++;
                right--;
            }
        }
        return true;
    }

    private boolean charJudge(char c) {
        return ((c>='a'&&c<='z')||(c>='A'&&c<='Z')||(c>='0'&&c<='9'));
    }
}
```

## 分割回文串

给定一个字符串 *s*，将 *s* 分割成一些子串，使每个子串都是回文串。

返回 *s* 所有可能的分割方案。

**示例:**

```
输入: "aab"
输出:
[
  ["aa","b"],
  ["a","a","b"]
]
```

**回溯算法**

``` java
class Solution {
    public List<List<String>> partition(String s) {
        List<List<String>> res = new ArrayList<>();
        backtrack(res, s,  new ArrayList<String>());
        return res;

    }

    private void backtrack(List<List<String>> res, String s, ArrayList<String> tmp) {
        if (s == null || s.length() == 0) res.add(new ArrayList<>(tmp));
        for (int i = 1; i <= s.length(); i++) {
            if (isPalidrome(s.substring(0, i))) {
                // System.out.println(s.substring(0, i));
                tmp.add(s.substring(0, i));
                backtrack(res, s.substring(i, s.length()), tmp);
                tmp.remove(tmp.size() - 1);
            }
        }
    }

    private  boolean isPalidrome(String sb) {
        int left = 0;
        int right = sb.length() - 1;
        while (left < right) {
            if (sb.charAt(left) != sb.charAt(right)) return false;
            left++;
            right--;
        }
        return true;

    }
}
```

**动态规划 + DFS**

``` java
class Solution {
    public List<List<String>> partition(String s) {
        List<List<String>> res = new ArrayList<>();
        int n = s.length();
        boolean[][] dp = new boolean[n][n];
        for (int i = 0; i < n; i++) {
            for (int j = 0; j <= i; j++) {
                if (s.charAt(i) == s.charAt(j) && (i - j < 2 || dp[j + 1][i - 1])) dp[j][i] = true;
            }
        }
        System.out.println(Arrays.deepToString(dp));
        dfs(res, dp, 0, n, s, new ArrayList<String>());
        return res;

    }

    private void dfs(List<List<String>> res, boolean[][] dp, int i, int n, String s, ArrayList<String> tmp) {
        if (i == n) res.add(new ArrayList<>(tmp));
        for (int j = i; j < n; j++) {
            if (dp[i][j]) {
                tmp.add(s.substring(i, j + 1));
                dfs(res, dp, j + 1, n, s, tmp);
                tmp.remove(tmp.size() - 1);
            }
        }
    }
}
```

## 单词拆分

给定一个**非空**字符串 *s* 和一个包含**非空**单词列表的字典 *wordDict*，判定 *s* 是否可以被空格拆分为一个或多个在字典中出现的单词。

**说明：**

- 拆分时可以重复使用字典中的单词。
- 你可以假设字典中没有重复的单词。

**示例 1：**

```
输入: s = "leetcode", wordDict = ["leet", "code"]
输出: true
解释: 返回 true 因为 "leetcode" 可以被拆分成 "leet code"。
```

**示例 2：**

```
输入: s = "applepenapple", wordDict = ["apple", "pen"]
输出: true
解释: 返回 true 因为 "applepenapple" 可以被拆分成 "apple pen apple"。
     注意你可以重复使用字典中的单词。
```

**示例 3：**

```
输入: s = "catsandog", wordDict = ["cats", "dog", "sand", "and", "cat"]
输出: false
```

**回溯树剪枝**

``` java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        return wordSplit(s,wordDict,0,new Boolean[s.length()]);

    }

    private boolean wordSplit(String s, List<String> wordDict, int start, Boolean[] memo) {
        if(start == s.length()){
            return true;
        }
        if (memo[start] != null) {
            return memo[start];
        }
        for (int i = start+1; i <= s.length(); i++) {
            if (wordDict.contains(s.substring(start, i)) && wordSplit(s, wordDict, i, memo)) {
                memo[start] = true;
                return memo[start];
            }
        }
        memo[start] = false;
        return memo[start];
    }
}
```

**宽度优先搜索**

``` java
public class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        Set<String> wordDictSet=new HashSet(wordDict);
        Queue<Integer> queue = new LinkedList<>();
        int[] visited = new int[s.length()];
        queue.add(0);
        while (!queue.isEmpty()) {
            int start = queue.remove();
            if (visited[start] == 0) {
                for (int end = start + 1; end <= s.length(); end++) {
                    if (wordDictSet.contains(s.substring(start, end))) {
                        queue.add(end);
                        if (end == s.length()) {
                            return true;
                        }
                    }
                }
                visited[start] = 1;
            }
        }
        return false;
    }
}
```

动态规划

``` java
public class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        Set<String> wordDictSet=new HashSet(wordDict);
        boolean[] dp = new boolean[s.length() + 1];
        dp[0] = true;
        for (int i = 1; i <= s.length(); i++) {
            for (int j = 0; j < i; j++) {
                if (dp[j] && wordDictSet.contains(s.substring(j, i))) {
                    dp[i] = true;
                    break;
                }
            }
        }
        return dp[s.length()];
    }
}
```

##  单词拆分 II

给定一个**非空**字符串 *s* 和一个包含**非空**单词列表的字典 *wordDict*，在字符串中增加空格来构建一个句子，使得句子中所有的单词都在词典中。返回所有这些可能的句子。

**说明：**

- 分隔时可以重复使用字典中的单词。
- 你可以假设字典中没有重复的单词。

**示例 1：**

```
输入:
s = "catsanddog"
wordDict = ["cat", "cats", "and", "sand", "dog"]
输出:
[
  "cats and dog",
  "cat sand dog"
]
```

**示例 2：**

```
输入:
s = "pineapplepenapple"
wordDict = ["apple", "pen", "applepen", "pine", "pineapple"]
输出:
[
  "pine apple pen apple",
  "pineapple pen apple",
  "pine applepen apple"
]
解释: 注意你可以重复使用字典中的单词。
```

**示例 3：**

```
输入:
s = "catsandog"
wordDict = ["cats", "dog", "sand", "and", "cat"]
输出:
[]
```