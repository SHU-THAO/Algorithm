# 剑指OFFER题目

## 抽象建模

### 扑克牌顺子

使用扑克牌模拟抽取卡片排序的过程， 如果牌能组成顺子就输出true，否则就输出false。为了方便起见,你可以认为大小王是0，并能变为任意数字。

思路：

1. 对输入数组进行排序，并以0为界分割，统计数目。
2. 统计非零的相邻大于1的误差和（遇到相同数字则直接返回false）。
3. 比较误差和与0的数目，并返回结果。

``` java
import java.util.ArrayList;
import java.util.Collections;

public class Solution {
    public boolean isContinuous(int [] numbers) {
	   if(numbers==null||numbers.length!=5) {
	        	return false;
	        }
	   ArrayList<Integer> list = new ArrayList<>();
	        for(int i=0;i<5;i++) {
	        	list.add(numbers[i]);
	        }
	        Collections.sort(list);
	        int l=0;
	        for(int x=0;x<list.size();x++) {
	        	if(list.get(x)==0) 
	        		l++;
	        }
	        boolean result = countnumbers(list,l);
	        return result;
	}

	private boolean countnumbers(ArrayList<Integer> list, int l) {
		int sum=0;
		for(int i=l;i<list.size()-1;i++) {
			if(list.get(i)==list.get(i+1)) 
				return false;
			if((list.get(i+1)-list.get(i))>1) {
				sum=sum+list.get(i+1)-list.get(i)-1;
			}
		}
		if(sum<=l) {
			return true;
		}
		return false;
	}
}
```

### 圆圈中最后剩下的数（约瑟夫环）

0，1，...，n-1这n个数排成一个圆圈，从数字0开始。每次从这个圆圈中删除第m个数字，求出这个圆圈中剩下的最后一个数字。

思路：

​	第一种方法：使用环形链表，依次删除数字，直到链表中剩下最后一个数字。

​	第二种方法：通过数学方法找出规律，简化流程。

``` java
//第一种方法，用数组来模拟环
public class Solution {
    public static int findLastNumber(int n,int m){
        if(n<1||m<1) return -1;
        int[] array = new int[n];
        int i = -1,step = 0, count = n;
        while(count>0){   //跳出循环时将最后一个元素也设置为了-1
            i++;          //指向上一个被删除对象的下一个元素。
            if(i>=n) i=0;  //模拟环。
            if(array[i] == -1) continue; //跳过被删除的对象。
            step++;                     //记录已走过的。
            if(step==m) {               //找到待删除的对象。
                array[i]=-1;
                step = 0;
                count--;
            }        
        }
        return i;//返回跳出循环时的i,即最后一个被设置为-1的元素
    }
}
```

``` java
//第二种方法，循环实现 f(n,m)=[f(n-1,m)+m]%n  n>1
public class Solution {
    public int LastRemaining_Solution(int n, int m) {
			if(n<1||m<1){
				return -1;
			}
			int last=0;			//n=1的时候
	        for(int i=2;i<=n;i++) {
	        	last = (last+m)%i;
	        }
	        return last;
	}
}
```

## 发散思维

### 求1+2+3+...+n

求1+2+3+...+n，要求不能使用乘除法、for、while、if、else、switch、case等关键字及条件判断语句（A?B:C）

思路：

​	使用短路的思想实现递归，&&与||在前面的结果满足条件后，就不会执行后面的条件了。

​	&&前部分为真，仍会执行后面部分，但前面部分为假则会直接跳出。

``` java
public class Solution {
    public int Sum_Solution(int n) {
	        int sum=n;
	        boolean flag = (sum>0)&&((sum+=Sum_Solution(--n))>0);
	        return sum;
	}
}
```

### 不用加减乘除做加法

写一个函数，求两个整数之和，要求在函数体内不得使用+、-、*、/四则运算符号。

思路——利用异或运算和与运算来代替求和

1. 求两个数的异或运算。（这一步需要进位的部分为0）
2. 求两个数的与运算，这样需要进位的部分为1，将与运算的结果保存左移。
3. 重复上面的步骤直到两个数的与运算结果为0，没有需要进位的部分。

``` java
public class Solution {
    public int Add(int num1,int num2) {
	        int sum=0;
	        int move=1;
	        while(move!=0) {
	        	sum=num1^num2;
	        	move=(num1&num2)<<1;
	        	num1=sum;
	        	num2=move;
	        }
	        return sum;
    }
}
```

### 构建乘积数组

给定一个数组A[0,1,...,n-1],请构建一个数组B[0,1,...,n-1],其中B中的元素B[i]=A[0]*A[1]*...*A[i-1]*A[i+1]*...*A[n-1]。不能使用除法。

思路：(用空间换时间)

1. B[i]的值可以看作下图的矩阵中每行的乘积。
2. 下三角用连乘可以很容求得，上三角，从下向上也是连乘。
3. 因此，先算下三角中的连乘，即我们先算出B[i]中的一部分，然后倒过来按上三角中的分布规律，把另一部分也乘进去。

![img](https://uploadfiles.nowcoder.com/images/20160829/841505_1472459965615_8640A8F86FB2AB3117629E2456D8C652)

``` java
		public int[] multiply(int[] A) {
			if(A.length==0) {
				return A;
			}
			int B[] = new int[A.length];
			B[0]=1;
            //计算下三角连乘
			for(int i=1;i<A.length;i++) {
				B[i]=B[i-1]*A[i-1];
			}
			int temp=1;
            //计算上三角连乘
			for(int j=A.length-2;j>=0;j--) {
				temp=temp*A[j+1];
				B[j]=B[j]*temp;
			}
			return B;
	    }
```



## 综合

### 把字符串转换为整数

```
输入一个字符串,包括数字字母符号,可以为空
```

```
如果是合法的数值表达则返回该数字，否则返回0
```

思路：

1. 将字符串转换为字符类型数组，判断字符的每个位是否符合类型，完全符合题目需求则进行转化。
2. 本题的关键在于特殊情况的考虑，观察问题的细致性。
3. 边界条件：数据上下溢出；空字符串；只有正负号；有无正负号；错误标志输出。

``` java
		//int类型的边界为2^31-1，此时再加一会变成-2^31
		public int StrToInt(String str) {
	        //为空或只有空格则返回0
	        //trim删除任何前导和尾随空格
	        if(str==null||str.trim().equals("")) {
	        	return 0;
	        }
	        //symbol=0,说明该数为正数;symbol=1，该数为负数;start用来区分第一位是否为符号位
	        int symbol = 0;
	        int start = 0;
	        char arr[] = str.trim().toCharArray();
	        if(arr[0]=='+') {
	        	start=1;
	        }else if(arr[0]=='-') {
	        	symbol=1;
	        	start=1;
	        }
	        int result = 0;
	        for(int i=start;i<arr.length;i++) {
	        	if(arr[i]>'9'||arr[i]<'0') {
	        		return 0;
	        	}
	        	//sum用于判断累加之后是否超出界限，超出界限会出现由正转负，可由此判断
	        	int sum=result*10+(int)(arr[i]-'0');
	        	
	        	if((sum-(int)(arr[i]-'0'))/10!=result) {
	        		return 0;
	        	}
	        	//得到结果
	        	result = result*10+(int)(arr[i]-'0');
	        }
	        result=(int)Math.pow(-1, symbol) * result;
	        return result;
	    }
```

## 数组

### 数组中重复的数字

在一个长度为n的数组里的所有数字都在0到n-1的范围内。 数组中某些数字是重复的(<u>已知有一些是重复的</u>)，但不知道有几个数字是重复的。也不知道每个数字重复几次。请找出数组中任意一个重复的数字。 例如，如果输入长度为7的数组{2,3,1,0,2,5,3}，那么对应的输出是第一个重复的数字2。

思路（这里使用时间复杂度为O(n)，空间复杂度为O(1)的方法）：

1. 从头到尾扫描数组，当扫描到下标为i的数组时，比较数组与下标。
2. 如果相等扫描下一个；如果不等则将其与第m个数字交换，如果交换的数字与第m个数字相等则找到了重复的数字，如果不等则重复第一步直到发现重复的数字。

``` java
		public boolean duplicate(int array[],int length,int [] duplication) {
			    if ( array==null ) return false;

			    // 判断数组是否合法（每个数都在0~n-1之间）
			    for ( int i=0; i<length; i++ ) {
			        if ( array[i]<0 || array[i]>length-1 ) {
			            return false;
			        }
			    }

			    // key step
			    for( int i=0; i<length; i++ ){
			        while( i!=array[i] ){
			            if ( array[i] == array[array[i]] ) {
			                duplication[0] = array[i];
			                return true;
			            }

			            int temp = array[i];
			            array[i] = array[array[i]];
			            array[temp] = temp;
			        }
			    }

			    return false;
		}
```

附：利用附加数组判断，这里用boolean数组比较省空间，省去了中间的while循环

``` java
//boolean只占一位，所以还是比较省的
    public boolean duplicate(int numbers[], int length, int[] duplication) {
        boolean[] k = new boolean[length];
        for (int i = 0; i < k.length; i++) {
            if (k[numbers[i]] == true) {
                duplication[0] = numbers[i];
                return true;
            }
            k[numbers[i]] = true;
        }
        return false;
    }
```

## 代码的完整性

### 正则表达式匹配

实现一个函数用来匹配包括'.'和''的正则表达式。模式中的字符'.'表示任意一个字符，而''表示它前面的字符可以出现任意次（包含0次）。 在本题中，匹配是指字符串的所有字符匹配整个模式。例如，字符串"aaa"与模式"a.a"和"abaca"匹配，但是与"aa.a"和"ab*a"均不匹配。

思路：首先要考虑特殊情况1. 两个字符串都为空，返回true；2. 当第一个字符串不空，而第二个字符串空了，返回false（因为这样，就无法匹配成功了,而如果第一个字符串空了，第二个字符串非空，还是可能匹配成功的，比如第二个字符串是“a**",由于‘*’之前的元素可以出现0次，所以有可能匹配成功）

之后进行字符的匹配，由上可知主要可以分为两种情况：

1. 当模式中的第二个字符不是‘*’时，直接比较第一个字符，相同进行下一个，不同则直接返回false;
2. 当模式中的第二个字符是‘*’时，该字符可以表示0个或者多个，这个要分三种情况考虑。匹配0个字符，模式后移两位；刚好匹配一个字符；匹配多个，需要进行继续匹配。

``` java
public boolean match(char[] str, char[] pattern){
	        if(str==null||pattern==null) {
	        	return false;
	        }
	        int strIndex=0;
	        int patternIndex=0;
	        //注意的是，JAVA中要时刻检验数字越界
	        return matchCore(str,strIndex,pattern,patternIndex);
	    }

private boolean matchCore(char[] str, int strIndex, char[] pattern, int patternIndex) {
			//有效性检验：str到尾，pattern到尾，匹配成功
		    if (strIndex == str.length && patternIndex == pattern.length) {
		        return true;
		    }
		    //pattern先到尾，匹配失败
		    if (strIndex != str.length && patternIndex == pattern.length) {
		        return false;
		    }
		    //模式第2个是*，且字符串第1个跟模式第1个匹配,分3种匹配模式；如不匹配，模式后移2位
if (patternIndex + 1 < pattern.length && pattern[patternIndex + 1] == '*') {
	if ((strIndex != str.length && pattern[patternIndex] == str[strIndex]) || (pattern[patternIndex] == '.' && strIndex != str.length)) {
	return matchCore(str, strIndex, pattern, patternIndex + 2)//模式后移2，视为x*匹配0个字符
	|| matchCore(str, strIndex + 1, pattern, patternIndex + 2)//视为模式匹配1个字符
	|| matchCore(str, strIndex + 1, pattern, patternIndex);//*匹配1个，再匹配str中的下一个
		        } else {
		            return matchCore(str, strIndex, pattern, patternIndex + 2);
		        }
		    }
		    //模式第2个不是*，且字符串第1个跟模式第1个匹配，则都后移1位，否则直接返回false
if ((strIndex != str.length && pattern[patternIndex] == str[strIndex]) || (pattern[patternIndex] == '.' && strIndex != str.length)) {
		        return matchCore(str, strIndex + 1, pattern, patternIndex + 1);
		    }
		    return false;
		}
```

### 表示数值的字符串

实现一个函数用来判断字符串是否表示数值（包括整数和小数）

``` java
		//正则表达式解法
		/*
		以下对正则进行解释:
		[\\+\\-]?            -> 正或负符号出现与否
		\\d*                 -> 整数部分是否出现，如-.34 或 +3.34均符合
		(\\.\\d+)?           -> 如果出现小数点，那么小数点后面必须有数字；
		                        否则一起不出现
		([eE][\\+\\-]?\\d+)? -> 如果存在指数部分，那么e或E肯定出现，+或-可以不出现，
		                        紧接着必须跟着整数；或者整个部分都不出现
		*/
		public boolean isNumeric(char[] str) {
		        String string = String.valueOf(str);
		        return string.matches("[\\+\\-]?\\d*(\\.\\d+)?([eE][\\+\\-]?\\d+)?");
		}
```

```java
		//分界判断
		private int index=0;
		public boolean isNumeric(char[] str) {
	        if(str.length<1)
	        	return false;
	        //整数检验
	        boolean flag = scanInteger(str);
	        //以小数点为分界进行整数检验(不能有正负)
	        if(index<str.length&&str[index]=='.') {
	        	index++;
	        	//小数点前可以没有整数部分，后可以没有数字
	        	flag = scanUnsignedInteger(str)||flag;
	        }
	        //以E为分界进行判断
	        if(index<str.length&&(str[index]=='E'||str[index]=='e')) {
	        	index++;
	        	//e或E的前后不能没有数字
	        	flag = flag&&scanInteger(str);
	        }
	        return flag&&index==str.length;
	        
	    }
		
		//检验是否存在数字，因此需要先检验正负
		private boolean scanInteger(char[] str) {
			if (index < str.length && (str[index] == '+' || str[index] == '-') )
	            index++;
	        return scanUnsignedInteger(str);
		}
		//检验是否为纯数字
		private boolean scanUnsignedInteger(char[] str) {
			int start = index;
			while(index<str.length&&(str[index]>='0')&&str[index]<='9') {
				index++;
			}
			return start<index;
		}
```

### 删除链表中的重复节点

在一个排序的链表中，存在重复的结点，请删除该链表中重复的结点，重复的结点不保留，返回链表头指针。 例如，链表1->2->3->3->4->4->5 处理后为 1->2->5

思路：

1. 非递归：创建一个节点n，n指向头结点，然后进行删除比较，最后返回n.next；
2. 递归：通过递归的方法实现跳过值与当前结点相同的全部结点、保留当前结点，从下一个结点开始递归操作。

``` java
//非递归    
public ListNode deleteDuplication(ListNode pHead){
	    	if(pHead==null)
	    		return pHead;
	    	ListNode n = new ListNode(0);
	    	ListNode p = pHead;
	    	ListNode pre = n;
	    	n.next=pHead;
	    	while(p!=null) {
	    		ListNode q=p.next;
	    		if(q==null) break;
	    		if(q.val==p.val) {
	    			while(q!=null&&q.val==p.val) {
	    				q=q.next;
	    			}
	    			pre.next=q;
	    			p=q;
	    		}else {
	    			pre=p;
	    			p=q;
	    		}	
	    	}
	    	return n.next;
} 
```

``` 递归
   public ListNode deleteDuplication(ListNode pHead) {
        if (pHead == null || pHead.next == null) { // 只有0个或1个结点，则返回
            return pHead;
        }
        if (pHead.val == pHead.next.val) { // 当前结点是重复结点
            ListNode pNode = pHead.next;
            while (pNode != null && pNode.val == pHead.val) {
                // 跳过值与当前结点相同的全部结点,找到第一个与当前结点不同的结点
                pNode = pNode.next;
            }
            return deleteDuplication(pNode); // 从第一个与当前结点不同的结点开始递归
        } else { // 当前结点不是重复结点
            pHead.next = deleteDuplication(pHead.next); // 保留当前结点，从下一个结点开始递归
            return pHead;
        }
    }
```



## 代码的鲁棒性

### 链表中环的入口节点

给一个链表，若其中包含环，请找出该链表的环的入口结点，否则，输出null。

思路：

1. 首先判断是否存在环：放置两个节点，一快一慢，若两者相遇，则证明存在环；否则都扫描到尾停止。
2. 判断完是否有环后，根据放置节点的速度来计算节点的位置，此处省略数学公式——结论是，当速度分别为1和2的时候，相遇点到环入口的距离为——链表长-起点到入口距离-入口到相遇点的距离。因此，在起点和相遇点放两个指针，下一个相遇点就是入口。

```java
public class Solution {
	//注意空指针的判断
    public ListNode EntryNodeOfLoop(ListNode pHead){
	    	ListNode slow = pHead,fast=pHead;
	    	boolean result = false;
	    	while(slow.next!=null&&fast.next!=null&&fast.next.next!=null) {
	    		slow=slow.next;
	    		fast=fast.next.next;
	    		if(slow==fast) {
	    			result=true;
	    			break;
	    		}
	    	}
	    	if(result==false)
	    		return null;
	    	slow=pHead;
	    	while(slow!=fast) {
	    		slow=slow.next;
	    		fast=fast.next;
	    	}
	    	return slow;
	    }
}
```

## 时间效率与空间效率的平衡

### 字符流中第一个只出现一次的字符

实现一个函数用来找出字符流中第一个只出现一次的字符。例如，当从字符流中只读出前两个字符"go"时，第一个只出现一次的字符是"g"。当从该字符流中读出前六个字符“google"时，第一个只出现一次的字符是"l"。

思路：

一般思路：使用使用一个HashMap来统计字符出现的次数，同时用一个ArrayList来记录输入流，每次返回第一个出现一次的字符都是在这个ArrayList（输入流）中的字符作为key去map中查找。

简易思路：使用一个int数组实现简易的HashMap表。

``` java
//一般思路方法
import java.util.*;
public class Solution {
    HashMap<Character, Integer> map=new HashMap();
    ArrayList<Character> list=new ArrayList<Character>();
    //Insert one char from stringstream
    public void Insert(char ch)
    {
        if(map.containsKey(ch)){
            map.put(ch,map.get(ch)+1);
        }else{
            map.put(ch,1);
        }
         
        list.add(ch);
    }
     
    //return the first appearence once char in current stringstream
    public char FirstAppearingOnce()
    {   char c='#';
        for(char key : list){
            if(map.get(key)==1){
                c=key;
                break;
            }
        }
         
        return c;
    }
}
```

``` java
//简易进阶
import java.util.ArrayList;

public class Solution {
    	int count[] = new int[256];
		ArrayList<Character> list2=new ArrayList<Character>();
	    public void Insert(char ch)
	    {
	        count[ch]++;
	        list2.add(ch);
	    }
	    public char FirstAppearingOnce()
	    {
	        for(char x:list2) {
	        	if(count[x]==1)
	        		return x;
	        }
	        return '#';
	    }
}
```

``` java
//最简
public class Solution {
    int count[]=new int[256];
    int index=1;
    public void Insert(char ch)
    {
        if(count[ch]==0){
          count[ch]=index++; 
        }
        else{
            count[ch]=-1;
        }
    }
    public char FirstAppearingOnce()
    {
        int temp=Integer.MAX_VALUE;
        char ch='#';
        for(int i=0;i<256;i++){
            if(count[i]!=0&&count[i]!=-1&&count[i]<temp){
                temp=count[i];
                ch=(char)i;
            }
        }
        return ch;
    }
}
```

## 树

### 二叉树的下一个节点

给定一个二叉树和其中的一个结点，请找出中序遍历顺序的下一个结点并且返回。注意，树中的结点不仅包含左右子结点，同时包含指向父结点的指针。

思路：主要分为两种情况：1.存在右子树；2.不存在右子树

``` java
public TreeLinkNode GetNext(TreeLinkNode pNode){
	        if(pNode==null)
	        	return pNode;
	        if(pNode.right!=null) {//存在右子树
	        	pNode=pNode.right;
	        	while(pNode.left!=null) {
	        		pNode=pNode.left;
	        	}
	        	return pNode;
	        }else{//不存在右子树
	        	while(pNode.next!=null) {
	        		TreeLinkNode p=pNode.next;
	        		if(p.left==pNode) {//为左子树直接返回上节点
	        			return p;
	        		}else {//为右子树继续向上一级，不存在则证明为终点
	        			pNode=pNode.next;
	        		}
	        	}
	        }
	        return null;
	}
```

## 抽象的问题形象化

### 判断二叉树的对称

请实现一个函数，用来判断一颗二叉树是不是对称的。注意，如果一个二叉树同此二叉树的镜像是同样的，定义其为对称的。

思路：既然是求对称，可以通过比较这个二叉树的自身来实现，主要满足三点：1.该点满足对称条件；2.该点的左与对称点的右相等；3.该点的右与对称点的左相等。由此便可以确认一个递归条件，写出递归方法。

``` java
public class Solution {
    boolean isSymmetrical(TreeNode pRoot){
	        return isSymmetrical(pRoot,pRoot);
	    }

		private boolean isSymmetrical(TreeNode pRoot1, TreeNode pRoot2) {
			if(pRoot1==null&&pRoot2==null)//注意节点为空的判断
				return true;
			if(pRoot1==null||pRoot2==null)
				return false;
			if(pRoot1.val!=pRoot2.val)
				return false;
			return isSymmetrical(pRoot1.left, pRoot2.right)&&isSymmetrical(pRoot1.right, pRoot2.left);
		}
}
```

另外利用深度有限与广度优先的思想，可以利用队列或者栈来解决此题

``` java
 boolean isSymmetricalDFS(TreeNode pRoot)
    {
        if(pRoot == null) return true;
        Stack<TreeNode> s = new Stack<>();
        s.push(pRoot.left);
        s.push(pRoot.right);
        while(!s.empty()) {
            TreeNode right = s.pop();//成对取出
            TreeNode left = s.pop();
            if(left == null && right == null) continue;
            if(left == null || right == null) return false;
            if(left.val != right.val) return false;
            //成对插入
            s.push(left.left);
            s.push(right.right);
            s.push(left.right);
            s.push(right.left);
        }
        return true;
    }
```

### 按之字形顺序打印二叉树

请实现一个函数按照之字形打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右至左的顺序打印，第三行按照从左到右的顺序打印，其他行以此类推。

思路：首先很容易想到按层来对二叉树进行遍历，但在此基础上需要实现分层与顺序的变换，因此，可以在用一个栈按层遍历的基础上再加上一个栈，两个栈依次存储下一层的二叉树节点进行打印。

``` java]
public class Solution {
    public ArrayList<ArrayList<Integer>> Print(TreeNode pRoot) {
				ArrayList<ArrayList<Integer>> list = new ArrayList<>();
				if(pRoot==null)
					return list;
				Stack<TreeNode> s1 = new Stack<>();
				s1.push(pRoot);
				Stack<TreeNode> s2 = new Stack<>();
				while(!s1.isEmpty()||!s2.isEmpty()) {
					if(!s1.isEmpty()) {
						ArrayList<Integer> alist = new ArrayList<>();
						while(!s1.isEmpty()) {
							TreeNode p = s1.pop();
							alist.add(p.val);
							if(p.left!=null) {
								s2.push(p.left);
							}
							if(p.right!=null) {
								s2.push(p.right);
							}
						}
						list.add(alist);
					}
					if(!s2.isEmpty()) {
						ArrayList<Integer> alist2 = new ArrayList<>();
						while(!s2.isEmpty()) {
							TreeNode p2 = s2.pop();
							alist2.add(p2.val);
							if(p2.right!=null) {
								s1.push(p2.right);
							}
							if(p2.left!=null) {
								s1.push(p2.left);
							}
						}
						list.add(alist2);
					}
				}
				return list;
	    }

}
```

### 把二叉树打印成多行

从上到下按层打印二叉树，同一层结点从左至右输出。每一层输出一行。

思路：

1. 类似于上一题，只需要改变栈为队列即可(浪费内存)
2. 记录每一层的队列数目，依次加入

``` java
public class Solution {
    ArrayList<ArrayList<Integer> > Print(TreeNode pRoot) {
        ArrayList<ArrayList<Integer>> ret = new ArrayList<>();
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(pRoot);
        while(!queue.isEmpty()){
            ArrayList<Integer> arr = new ArrayList<>();
            int cnt = queue.size();
            while(cnt -- > 0){
                TreeNode node = queue.poll();
                if(node ==null)
                    continue;
                arr.add(node.val);
                queue.add(node.left);
                queue.add(node.right);
            }
            if(arr.size() != 0)
                ret.add(arr);
        }
        return ret;
    }
     
}
```

## 复杂的问题简单化

### 二叉树的序列化

对于二叉树的序列化可以理解为将二叉树中的值以字符串的形式进行永久的存储

思路：以前序遍历对二叉树中的内容进行储存

``` java
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
     
    String Serialize(TreeNode root) {
        if(root == null)
            return "";
        StringBuilder sb = new StringBuilder();
        Serialize2(root, sb);
        return sb.toString();
    }
     
    void Serialize2(TreeNode root, StringBuilder sb) {
        if(root == null) {
            sb.append("#,");
            return;
        }
        sb.append(root.val);
        sb.append(',');
        Serialize2(root.left, sb);
        Serialize2(root.right, sb);
    }
     
    int index = -1;
     
    TreeNode Deserialize(String str) {
        if(str.length() == 0)
            return null;
        String[] strs = str.split(",");
        return Deserialize2(strs);
    }  
     
    TreeNode Deserialize2(String[] strs) {
        index++;
        if(!strs[index].equals("#")) {
            TreeNode root = new TreeNode(0);     
            root.val = Integer.parseInt(strs[index]);
            root.left = Deserialize2(strs);
            root.right = Deserialize2(strs);
            return root;
        }
        return null;
    }
}
```

## 知识迁移能力

### 二叉搜索树的第K个节点

给定一棵二叉搜索树，请找出其中的第k小的结点。例如， （5，3，7，2，4，6，8）    中，按结点数值大小顺序第三小结点的值为4。

思路：对二叉搜索树进行中序遍历，其排序刚好是从小到大排序，从前到后即可。(注意应该返回null的特殊情况)

``` java
import java.util.ArrayList;

public class Solution {
        TreeNode KthNode(TreeNode pRoot, int k){
	    	if(k==0)
	            return null;
	    	ArrayList<TreeNode> list = new ArrayList<>();
	    	inKthNode(pRoot,list);
	    	if(k<=list.size()) {
	    		return list.get(k-1);
	    	}else {
	    		return null;
	    	}
	    }

		private void inKthNode(TreeNode pRoot, ArrayList<TreeNode> list) {
			if(pRoot==null) {
				return;
			}
			inKthNode(pRoot.left,list);
			list.add(pRoot);
			inKthNode(pRoot.right,list);
		}


}
```

## 时间效率

### 数据流中的中位数

如何得到一个数据流中的中位数？如果从数据流中读出奇数个数值，那么中位数就是所有数值排序之后位于中间的数值。如果从数据流中读出偶数个数值，那么中位数就是所有数值排序之后中间两个数的平均值。我们使用Insert()方法读取数据流，使用GetMedian()方法获取当前读取数据的中位数。

思路：为了保持最低的时间复杂度，应该考虑在插入的同时进行排序，这样的时间复杂度为O(n).

如果在插入完成后进行排序，时间复杂度最少为O(n*log(n))

``` java
	LinkedList<Integer> list = new LinkedList<Integer>();
 
    public void Insert(Integer num) {
        if (list.size()==0||num < list.getFirst()) {
            list.addFirst(num);
        } else {
            boolean insertFlag = false;
            for (Integer e : list) {
                if (num < e) {
                    int index = list.indexOf(e);
                    list.add(index, num);
                    insertFlag = true;
                    break;
                }
            }
            if (!insertFlag) {
                list.addLast(num);
            }
        }
 
    }
 
    public Double GetMedian() {
        if (list.size() == 0) {
            return null;
        }
 
        if (list.size() % 2 == 0) {
            int i = list.size() / 2;
            Double a = Double.valueOf(list.get(i - 1) + list.get(i));
            return a / 2;
        }
        list.get(0);
        Double b = Double.valueOf(list.get((list.size() + 1) / 2 - 1));
        return Double.valueOf(list.get((list.size() + 1) / 2 - 1));
     
    }
```

### 滑动窗口的最大值

给定一个数组和滑动窗口的大小，找出所有滑动窗口里数值的最大值。例如，如果输入数组{2,3,4,2,6,2,5,1}及滑动窗口的大小3，那么一共存在6个滑动窗口，他们的最大值分别为{4,4,6,6,6,5}； 针对数组{2,3,4,2,6,2,5,1}的滑动窗口有以下6个： {[2,3,4],2,6,2,5,1}， {2,[3,4,2],6,2,5,1}， {2,3,[4,2,6],2,5,1}， {2,3,4,[2,6,2],5,1}， {2,3,4,2,[6,2,5],1}， {2,3,4,2,6,[2,5,1]}。

思路：

需要一个list存储结果，而滑动窗口的实现则可以通过双向链表实现，通过移动窗口在链表中存储下标，根据对添加元素的大小进行比较，通过链表的removefirst与removeLast来对链表进行更新。

思路：每添加一个元素K都与链表的尾部X比较，若K>X，则去掉X继续比较，直到链表为空或者X>K;

在确定在链表添加最大值之前确认链表头部的最大值是否满足下标要求，不满足去掉头部。

``` java
import java.util.LinkedList;
import java.util.ArrayList;

public class Solution {
    public ArrayList<Integer> maxInWindows(int [] num, int size){
             ArrayList<Integer> list = new ArrayList<>();
	        if(num==null||num.length<size||size<1)
	        	return list;
	        LinkedList<Integer> deque = new LinkedList<>();
	        for(int i=0;i<size-1;i++) {
	        	while(!deque.isEmpty()&&num[i]>num[deque.getLast()])
	        		deque.removeLast();
	        	deque.addLast(i);
	        }
	        for(int i=size-1;i<num.length;i++) {
	        	while(!deque.isEmpty()&&num[i]>num[deque.getLast()])
	        		deque.removeLast();
	        	deque.addLast(i);
	        	if(i-deque.getFirst()+1>size) {
	        		deque.removeFirst();
	        	}
	        	list.add(num[deque.getFirst()]);
	        }
	        return list;
	    }
}
```

## 回溯法

### 矩阵中的路径

请设计一个函数，用来判断在一个矩阵中是否存在一条包含某字符串所有字符的路径。路径可以从矩阵中的任意一个格子开始，每一步可以在矩阵中向左，向右，向上，向下移动一个格子。如果一条路径经过了矩阵中的某一个格子，则之后不能再次进入这个格子。 例如 a b c e s f c s a d e e 这样的3 X 4 矩阵中包含一条字符串"bcced"的路径，但是矩阵中不包含"abcb"路径，因为字符串的第一个字符b占据了矩阵中的第一行第二个格子之后，路径不能再次进入该格子。

思路：利用回溯法即可，注意其中的判断条件与细节。

用一个状态数组保存之前访问过的字符，然后再分别按上，下，左，右递归。

``` java
	    public boolean hasPath(char[] matrix, int rows, int cols, char[] str){
	    	if(rows<1||cols<1||(rows*cols!=matrix.length)||str.length<1||matrix==null || matrix.length==0||str==null || str.length==0||rows*cols<str.length)
	    		return false;
	    	ArrayList<Integer> listPath = null;
	    	boolean flag = false;
	    	for(int i=0;i<matrix.length;i++) {
	    		if(matrix[i]==str[0])
	    			listPath = new ArrayList<Integer>();
	    			flag = hasPathdect(matrix,i,str,0,rows,cols,listPath);
	    		if(flag)
	    			break;
	    	}
	    	return flag;
	    }
	    
		private boolean hasPathdect(char[] matrix, int i, char[] str, int j,int rows, int cols,ArrayList<Integer> listPath) {
			if(j==str.length)
				return true;
			if(i>=matrix.length||i<0)
				return false;
			if(str[j]!=matrix[i])
				return false;
			if(listPath.contains(i)) {
				return false;
			}else {
				listPath.add(i);
			}
            //这里的判断冗余
			if(i%cols==0) {
				return hasPathdect(matrix, i+1, str, j+1, rows, cols,listPath)||
						hasPathdect(matrix, i+cols, str, j+1, rows, cols,listPath)||
						hasPathdect(matrix, i-cols, str, j+1, rows, cols,listPath);
			}
			if(i%cols==(cols-1)) {
				return hasPathdect(matrix, i-1, str, j+1, rows, cols,listPath)||
						hasPathdect(matrix, i+cols, str, j+1, rows, cols,listPath)||
						hasPathdect(matrix, i-cols, str, j+1, rows, cols,listPath);
			}
			return hasPathdect(matrix, i+1, str, j+1, rows, cols,listPath)||
					hasPathdect(matrix, i-1, str, j+1, rows, cols,listPath)||
					hasPathdect(matrix, i+cols, str, j+1, rows, cols,listPath)||
					hasPathdect(matrix, i-cols, str, j+1, rows, cols,listPath);
		}
```

``` java
/**
用一个状态数组保存之前访问过的字符，然后再分别按上，下，左，右递归
*/
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

### 机器人的运动范围

地上有一个m行和n列的方格。一个机器人从坐标0,0的格子开始移动，每一次只能向左，右，上，下四个方向移动一格，但是不能进入行坐标和列坐标的数位之和大于k的格子。 例如，当k为18时，机器人能够进入方格（35,37），因为3+5+3+7 = 18。但是，它不能进入方格（35,38），因为3+5+3+8 = 19。请问该机器人能够达到多少个格子？

思路：回溯法的应用，同时要考虑不能返回的问题，与其中的判断。

``` java
public class Solution {
  public int movingCount(int threshold, int rows, int cols)
    {
        boolean[] visited=new boolean[rows*cols];
        return movingCountCore(threshold, rows, cols, 0,0,visited);
    }
    private int movingCountCore(int threshold, int rows, int cols,
            int row,int col,boolean[] visited) {
        if(row<0||row>=rows||col<0||col>=cols) return 0;
        int i=row*cols+col;
        if(visited[i]||!checkSum(threshold,row,col)) return 0;
        visited[i]=true;
        return 1+movingCountCore(threshold, rows, cols,row,col+1,visited)
                +movingCountCore(threshold, rows, cols,row,col-1,visited)
                +movingCountCore(threshold, rows, cols,row+1,col,visited)
                +movingCountCore(threshold, rows, cols,row-1,col,visited);
    }
    private boolean checkSum(int threshold, int row, int col) {
        int sum=0;
        while(row!=0){
            sum+=row%10;
            row=row/10;
        }
        while(col!=0){
            sum+=col%10;
            col=col/10;
        }
        if(sum>threshold) return false;
        return true;
    }
}

```

