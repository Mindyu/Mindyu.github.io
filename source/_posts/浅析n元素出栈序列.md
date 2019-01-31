---
title: 浅析n元素出栈序列
date: 2018-02-24 14:34:21
tags: [栈,数据结构,卡特兰数,Catalen]
categories: [算法分析]
---

## 栈的简介
​	栈（stack）又名堆栈，它是一种运算受限的线性表。其限制是仅允许在表的一端进行插入和删除运算。这一端被称为栈顶，相对地，把另一端称为栈底。向一个栈插入新元素又称作进栈、入栈或压栈，它是把新元素放到栈顶元素的上面，使之成为新的栈顶元素；从一个栈删除元素又称作出栈或退栈，它是把栈顶元素删除掉，使其相邻的元素成为新的栈顶元素。遵循后进先出的原则。<!--more-->

## n个元素出栈顺序种数

 1. **问题描述**
 有n个元素依次进栈，请问总共有多少种出栈序列？

 2. **算法分析**
首先列举出简单的情况：
当1个元素进栈，有1种出栈顺序；
当2个元素进栈，有2种出栈顺序；
当3个元素进栈，有5种出栈顺序 ；
我们把n个元素的出栈个数的记为f(n), 则对于1，2，3有
```
f(1) = 1     //即 1
f(2) = 2     //即 12、21
f(3) = 5     //即 123、132、213、321、231
```
​	对于f(4)，我们假定是a,b,c,d四个元素。任意一个元素在其出栈序列中只有4个位置。取元素a分别讨论其在出栈序列中的位置：

1. 当a元素在位置1时，只可能是a先入栈，然后出栈。接下来就是其它三个元素b,c,d的出栈序列，也就是子问题f(3)。

2. 元素a在位置2处，有一个元素比a先出栈可能的序列即f(1)，另外两个元素在位置3，4即f(2)。

3. 元素a在位置3处，有两个元素比a先出栈可能的序列即f(2)，另外两个元素在位置4即f(1)。

4. 元素a在位置4处，有三个元素比a先出栈可能的序列即f(3).

   那么f(4) =  f(3) + f(2) * f(1) + f(1) * f(2) + f(3); 然后推广到n，按同理我们可以很容易的得到： 
   f(n) = f(0)*f(n-1) + f(1)*f(n-2) + … + f(n-1)*f(0) 

上式也就是**卡特兰数（Catalen）**:
卡特兰数的通项公式为：![enter description here][1]
变换形式：![enter description here][2]
另类递归式：  C(n)=((4*n-2)/(n+1))*C(n-1);



卡特兰数的应用：

1. 括号化问题。

​	矩阵链乘： P=a1×a2×a3×……×an，依据乘法结合律，不改变其顺序，只用括号表示成对的乘积，试问有几种括号化的方案？(C(n-1)种)

2. 出栈次序问题。

​	n个元素依次进栈，请问总共有多少种出栈序列？
	案例：2012腾讯实习招聘笔试题
在图书馆一共6个人在排队，3个还《面试宝典》一书，3个在借《面试宝典》一书，图书馆此时没有了面试宝典了，求他们排队的总数？
	解析：还书相当于入栈，借书相当于出栈。只有当栈内元素不为空时才可以借书。C(3) = 5。借书3个人，还书3个人求全排列。
	总数为5*3!*3! = 180。

3. 将多边行划分为三角形问题。

将一个凸N+2多边形区域分成三角形区域的方法数?
类似：一位大城市的律师在她住所以北n个街区和以东n个街区处工作。每天她走2n个街区去上班。如果她
从不穿越（但可以碰到）从家到办公室的对角线，那么有多少条可能的道路？

类似：在圆上选择2n个点,将这些点成对连接起来使得所得到的n条线段不相交的方法数?

4. 给顶节点组成二叉树的问题。

给定N个节点，能构成多少种不同的二叉树？
（能构成Cn个）
Catalan数的解法:
Catalan数的组合公式为 Cn=C(2n,n) / (n+1);
此数的递归公式为 C(n ) = C(n-1)*(4*n-2) / (n+1)

3. **代码实现**

``` java
import java.util.Scanner;

public class Main {
	
	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		int n = sc.nextInt();
		int[] arr = new int[n+1];
		arr[0] = 1;arr[1] = 1;
		
		Catalen_1(arr, n);
//		Catalen_2(arr, n);
		
	    for (int i = 1;i<=n;i++){
	    	System.out.println(arr[i]);
	    }
	    sc.close();
	}

	public static void Catalen_1(int[] arr, int n) {
		// 递推关系式   f(n) = f(0)*f(n-1) + f(1)*f(n-2) + … + f(n-1)*f(0) 
	    for (int i=2; i<=n; ++i)
	    {
	        for (int j=0; j<i; ++j)
	        {
	            arr[i] += arr[j] * arr[i-1-j];
	        }
	    }
	}
	
	public static void Catalen_2(int[] arr, int n) {
		// 递推关系式  C(n)=((4*n-2)/(n+1))*C(n-1)
	    for (int i=2; i<=n; ++i)
	    {
	    	arr[i] = arr[i-1] * (4*i-2) / (i+1);
	    }
	}
	
}

```
4. **运行结果**

![enter description here][3]


## n个元素出栈序列输出
1. **设计思路**

   所需数据结构，1栈2队列，输入队列从头部取出数据压入栈中，数据出栈进入输出队列。最终输出队列即为出栈序列的情况。
   采用递归的方式，将大问题分解为小问题。栈存在两种状态：1.输入队列入栈，2.输出队列出栈。

2. **代码实现**

``` java
import java.util.LinkedList;
import java.util.Queue;
import java.util.Scanner;
import java.util.Stack;

public class Main {
	
	static int n;
	
	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		n = sc.nextInt();		// 元素个数
		Stack<Integer> s = new Stack<Integer>(); 
		Queue<Integer> in = new LinkedList<Integer>();
		for (int i = 1; i <= n; i++) {
			in.offer(i);
		}
		Queue<Integer> out = new LinkedList<Integer>();
	
		printAllOutStackSeq(in, s, out);
		
	    sc.close();
	}

	@SuppressWarnings("unchecked")
	public static void printAllOutStackSeq(Queue<Integer> in, Stack<Integer> s, Queue<Integer> out) {
		if (out.size()==n) {      // 所有元素都出栈了
			 while( !out.isEmpty() ){  
		          System.out.print(out.poll()+"");; 
		     }  
			 System.out.println();
			 return;  
		}

		Queue<Integer> inCopy = new LinkedList<Integer>(in);
		Stack<Integer> sCopy = (Stack<Integer>) s.clone(); 
		Queue<Integer> outCopy = new LinkedList<Integer>(out);
		
		if( !s.empty() ) {		// 出栈，将元素出栈，push到结果队列中  
	        out.offer( s.pop() ); 
	        printAllOutStackSeq( inCopy, s, out );   
	    }  
		
		if ( !in.isEmpty() ) { 	// 入栈，将输入队列出队，进行入栈  
			sCopy.push(in.poll());
			printAllOutStackSeq( in, sCopy, outCopy );   
		}
		return;  
	}
}

```

3. **运行结果**

![输出所有出栈序列][4]


## 判断一个序列是否是可能的出栈序列
1. **设计思路**

   输入一个序列，判断该序列是否可能是正确的出栈序列（反向推理）。
   代码中A代表着有序的输入队列（1，2，3....，n）
   代码中B代表着所输入的目标序列的下标，从下标为1的位置开始匹配。
   代码中S代表栈，进行入栈、出栈操作。
   `A == target[B]` 当前输入队列头元素与目标序列B位置元素相同，也就是输入队列元素取头元素进行入栈，然后立即出栈。
   `!s.isEmpty() && s.peek() == target[B]` 当前栈顶元素与目标序列B位置元素相同，也就是栈内元素出栈。
   `A <= n` 上述两种情况都不满足时，也就是输入队列的头元素一直进行入栈操作。
   最后也不入栈也不出栈，也不就意味着目标序列不是出栈序列。

2. **代码实现**

``` java
import java.util.Scanner;
import java.util.Stack;

public class Main {
	
	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		while(sc.hasNext()){
			int n = sc.nextInt();		// 元素个数
			if (n==0) break;
			int[] target = new int[n+1];
			Stack<Integer> s = new Stack<Integer>(); 
			
			for (int i = 1; i <= n; i++) {
				target[i] = sc.nextInt();
			}
			
			int A = 1, B = 1, flag = 1;
			while(B <= n){
				if (A == target[B]) {
					A++;B++;
				}else if (!s.isEmpty() && s.peek() == target[B]) {
					s.pop(); B++;
				}else if (A <= n ) {
					s.push(A++);
				}else {
					flag = 0; break;
				}
			}
			
			System.out.println(flag==1?"Yes":"No");
		}
		
	    sc.close();
	}
}

```
3. **运行结果**

![判断一个序列是否是可能的出栈序列][5]




[1]: http://hexoblog-1253306922.cosgz.myqcloud.com/photo2018/catalen1.png
[2]: http://hexoblog-1253306922.cosgz.myqcloud.com/photo2018/catalen.png
[3]: http://hexoblog-1253306922.cosgz.myqcloud.com/photo2018/CatalenCode.png
[4]: http://hexoblog-1253306922.cosgz.myqcloud.com/photo2018/outStackSeq.png

[5]: http://hexoblog-1253306922.cosgz.myqcloud.com/photo2018/isOutStackSeq.png