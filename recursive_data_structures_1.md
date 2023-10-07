---
layout: page
title: Recursive data structures
within: programming
---

<details class="prereq" markdown="1"><summary>Assumed Knowledge</summary>

  * <a href="./recursion">Recursion</a>
  * <a href="./lists">Lists</a>
</details>

<details class="outcomes" markdown="1"><summary>Learning Outcomes</summary>

  * Be able to create and operate on a class holding one or more references of the same type

</details>

## Author: Gaurav Gupta

# The Node class

<!--<iframe width="560" height="315" src="https://www.youtube.com/embed/fDU7AtmQBjA" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

<p>&nbsp;</p>
-->

Consider the following class:

```java
public class Node {
	public int data;
	public Node next;

	public Node(int d, Node n) {
		data = d;
		next = n;
	}
}
```

Every Node object holds a reference to another Node object.

> ```java
> Node a = new Node(10, null);
> ```
>
> ![](./fig/06-lists/node/node-figure0.png)


> ```java
> Node b = new Node(20, a);
> ```
>
> ![](./fig/06-lists/node/node-figure1.png)

> ```java
> Node head = new Node(20, new Node(10, null));
> ```
>
> ![](./fig/06-lists/node/node-figure2.png)

Here, we created an *anonymous* `Node` object - `new Node(10, null)` - and passed it as a parameter to the constructor of `head`.


> ```java
> head.next.next = new Node(-50, null);
> ```
>
> ![](./fig/06-lists/node/node-figure3.png)


## Linking nodes

<iframe width="560" height="315" src="https://www.youtube.com/embed/Jqu9IpulHvU" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

<p>&nbsp;</p>

We can link any number of nodes as we want.

```java
Node n5 = new Node(20, null);
Node n4 = new Node(70, n5);
Node n3 = new Node(10, n4);
Node n2 = new Node(90, n3);
Node n1 = new Node(30, n2);
```

![](./fig/06-lists/linkedlists/linkedlists-figure4.png)

Simplified representaton:

![](./fig/06-lists/linkedlists/linkedlists-figure5.png)

We can get all the values just using `n1` :)

```java
System.out.println(n1.data); //30
System.out.println(n1.next.data); //90
System.out.println(n1.next.next.data); //10
System.out.println(n1.next.next.next.data); //70
System.out.println(n1.next.next.next.next.data); //20
```

If we create a `Node` reference `temp` initialized to `n1`, we can re-reference it to the `Node` after it using `temp = temp.next`. Thereby, repeating the operation over and over.

```java
Node temp = n1; //temp refers to same instance as n1
temp = temp.next; //temp refers to node after temp or n1 which is n2
temp = temp.next; //temp refers to node after temp or n2 which is n3
temp = temp.next; //temp refers to n4
temp = temp.next; //temp refers to n5
temp = temp.next; //temp is now null - STOP
```

Abstracting into a loop to add all the values:

```java
Node temp = n1;
int total = 0;
while(temp != null) {
	total = total + temp.data;
	temp = temp.next;
}
```

![](./fig/06-lists/linkedlists/linkedlists-figure12.png)

## Careful to not lose the reference to the starting node

What would happen if we write the following code?

```java
int total = 0;
while(n1 != null) {
	total = total + n1.data;
	n1 = n1.next;
}
```

`total` will hold the correct value but `n1` now becomes `null`.

Once, instances don't have any incoming references, they are deleted by Java.

So, if you try to do anything using `n1` again, well ... good luck!

Hence, we should always make a reference copy of the starting node before operating on it.

## Passing a node to a function

Thankfully, that is exactly how objects are passed to functions - as reference copies of the actual parameter.

So, if I had a function:

```
public static int sum(Node start) {
	int total = 0;
	while(start != null) {
		total = total + start.data;
		start = start.next;
	}
	return total;
}
```

I can call it as `sum(n1)`, thereby `start ` being a reference copy of `n1` and updated. But not `n1`. Life is good again :)

## Recursion is a beautiful thing

To calculate the sum of all nodes starting at a node `start`, 

- if `start` is `null`, you can return 0,
- otherwise, you can add `start.data` to the sum of all nodes starting at `start.next`.

```
public static int sum(Node start) {
	if(start == null) {
		return 0;
	}
	else {
		return start.data + sum(start.next);
	}
}
```

YESSS!!!

## Keep moving

What is the bug in the following code?

```
public static int sumPositives(Node start) {
	int total = 0;
	while(start != null) {
		if(start.data > 0) {
			total = total + start.data;
			start = start.next;
		}
	}
	return total;
}
```

We only move to the next node if the value in the current one is greater than 0.
The movement forward (`start = start.next`), just like the classic `i++`, is almost always unconditional.

Correct code:

```
public static int sumPositives(Node start) {
	int total = 0;
	while(start != null) {
		if(start.data > 0) {
			total = total + start.data;
		}
		start = start.next;
	}
	return total;
}
```

Some people prefer a `for-loop` for this very reason:

```
public static int sumPositives(Node start) {
	int total = 0;
	for(; start != null; start = start.next) {
		if(start.data > 0) {
			total = total + start.data;
		}
	}
	return total;
}
```

If, for any reason, you need to hold on to the original reference of `start`, you can always copy into another variable as,

```
//this is the classic handshake algorithm

public static boolean allUnique(Node start) {
	for(Node nodeA = start; nodeA != null; nodeA = nodeA.next) { //for all nodes
		//check against all other nodes AFTER it
		for(Node nodeB = nodeA.next; nodeB != null; nodeB = nodeB.next) { 
			if(nodeA.data == nodeB.data) {
				return false;
			}
	}
	return true;
}
```

## Nodes can hold other objects too

In the previous example, we saw a node holding integer data, but it can hold any kind of data. For starters, take a look at `RNode` holding `Rectangle` object.

For the classes defined in,

- [Rectangle.java](./Rectangle.java),
- [RNode.java](./RNode.java),

Consider the following code,

```java
Rectangle r1 = new Rectangle(2.5, 1.5);
Rectangle r2 = new Rectangle(4.2, 3.6);
RNode q = new RNode(r2, null);
RNode p = new RNode(r1, q);
```

![](./fig/06-lists/linkedlists/linkedlists-figure2.png)

We can create anonymous objects to reduce variable count.

```java
RNode q = new RNode(new Rectangle(4.2, 3.6), null);
RNode p = new RNode(new Rectangle(2.5, 1.5), q);
```

![](./fig/06-lists/linkedlists/linkedlists-figure3.png)

# Be careful while comparing objects!!!

Consider the following function that attempts to check if a specific rectangle exists in a list or not:

```
public static boolean contains(RNode start, Rectangle target) {
	for(Node current = start; current != null; current = current.next) { 
			if(current.data == target) {
				return true;
			}
	}
	return false;
}
```

Here, the `current.data == target` checks if the two are reference copies!

The right version is:


```
public static boolean contains(RNode start, Rectangle target) {
	for(Node current = start; current != null; current = current.next) { 
			if(current.data.equals(target)) {
				return true;
			}
	}
	return false;
}
```

Here, the `current.data.equals(target)` checks for equality based on the definition of what "equal" is (defined in class `Rectangle`).

# Homework - 2

### Task 1

For the class [Node](./Node.java), draw the memory diagram to illustrate objects after the last statement of the following code executes.

```java
Node a = new Node(20, null);
Node b = new Node(70, a);
Node c = new Node(10, a);
Node d = new Node(90, c);
```

### Task 2

For the class [Node](./Node.java), draw the memory diagram to illustrate objects after the last statement of the following code executes.

```java
Node a = new Node(20, null);
Node b = new Node(70, a);
Node c = new Node(10, b);
Node d = new Node(90, c);
a.next = d;
```

### Task 3

For the class [Node](./Node.java), draw the memory diagram to illustrate objects after the last statement of the following code executes.

```java
Node a = new Node(20, null);
Node b = new Node(70, a);
Node c = new Node(10, b);
Node d = new Node(90, c);
a.next = d.next;
```

### Task 4

For the class [Node](./Node.java), draw the memory diagram to illustrate objects after the last statement of the following code executes.

```java
Node a = new Node(20, null);
Node b = new Node(70, a);
Node c = new Node(10, b);
Node d = new Node(90, c);
a.next = d.next.next;
```

### Task 5

For the class [Node](./Node.java), the following code attempts to store the sum of all items in the chain of nodes into a varaible `total`. However, it has a a bug. Briefly explain what is the problem with the code, and correct it.

```java
Node a = new Node(20, null);
Node b = new Node(70, a);
Node c = new Node(10, b);
Node d = new Node(90, c);
int total = 0;
Node current = d;
while(current != null) {
	total = total + current;
	current = current.next;
}
```

### Task 6

For the class [Node](./Node.java), the following code attempts to store the number of nodes in the chain into a variable `size`. However, it has a a bug. Briefly explain what is the problem with the code, and correct it.

```java
Node a = new Node(20, null);
Node b = new Node(70, a);
Node c = new Node(10, b);
Node d = new Node(90, c);
int size = 0;
Node current = d;
while(current != null) {
	size = size + 1;
}
```

### Task 7

For the class [Node](./Node.java), what is the value of `result` after the following code is executed?

```java
Node a = new Node(20, null);
Node b = new Node(70, a);
Node c = new Node(10, b);
Node d = new Node(90, c);
int result = 0;
Node current = d;
while(current != null) {
	if(current.data >= 20) {
		result = result * 10 + 1;
	}
	else {
		result = result * 10;
	}
	current = current.next;
}
```

### Task 8

For the class [Node](./Node.java), what is the value of `result` after the following code executes?

```java
Node a = new Node(9, null);
Node b = new Node(2, a);
Node c = new Node(7, b);
Node d = new Node(1, c);
a.next = d;
a.data = 	1000*d.data +
			100*d.next.data +
			10*d.next.next.data +
			1*d.next.next.next.data;
```

### Task 9

Consider the following class definition for `TreeNode`:

```java
public class TreeNode {
	public int data;
	public TreeNode left, right;
	public TreeNode(int d, TreeNode l, TreeNode r) {
		data = d;
		left = l;
		right = r;
	}
}
```

Draw the memory diagram to illustrate objects after the last statement of the following code executes. Also, state the number of instances and references in the diagram.

```java
TreeNode t1 = new TreeNode(20, null, null);
TreeNode t2 = new TreeNode(-10, null, null);
TreeNode t3 = new TreeNode(70, t1, t2);
```
