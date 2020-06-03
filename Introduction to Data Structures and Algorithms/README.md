# Introduction to Data Structures and Algorithms

Bubble sort algorith compares two numbers with each other and moves them around so that the highest number is on the lastposition. And then we repeat this procedure, the number of times there are numbers in the sequence. Data structures are just ways in which we store data. In the calculation of the time complexity, each operation takes a unit time. Here is the pseudo-code for the bubble sort algorithm.

```
for i = 0 to A.length - 2
  for j = 0 to A.legnth - 2 -i
    if A[j] > A[j+1]
      tmp = A[j + 1]
      A[j+1] = A[j]
      A[j] = tmp
```

Where here we are using the zero indexing.

For the Big O notation let's remember that : logn <= sqrt(n) <= nlogn <= n^2 <= n^3 <= 2^n <= n! .

The selection sort and the insertion sort  both take n^2 time. In the selection sort, we find at each time the smallest number out of the remaining numbers and place it towards the beginning of the sequence. We have the following pseudo code for the selection sort :

```
for i = 0 to A.length - 2
  minIndex = i
  for j = i + 1 to A.length - 1
    if (data[j] < data[minIndex])
      minIndex = j
  tmp = data[minIndex]
  data[minIndex] = data[i]
```

The insertion sort takes numbers consecutively and at each time places it in the right position in the order. Here is the pseduo code for the insertion sort:

```
for i = 0 to A.length - 1
  current = A[i]
  j = i - 1
  while j >= 0 && A[j] > current
    A[j+1] = A[j]
    j = j - 1
  A[j+1] = current
```

Now we study different types of data structures. The first one we will study is the linked list. In a linked list, each element contains a link to the next element in the order. The last element contains a link to a null element. Let us implement the linked list using the following model :

```
public class LinkedList {

  private Node head;
  public void insertAtHead(int data) {
    Node newNode = new Node(data);
    newNode.setNextNode(this.head);
    this.head = newNode;
  }
  
  @Override
  public string toString() {
    String result = "{";
    Node current = this.head;
    while (current != null) {
      result += current.toString() + ",";
      current = current.getNextNode();
    }
    result += "}";
    return result;
  } 
  
  public int length() {
    int length = 0;
    Node current = this.head;
    while (current != null) {
      length++;
      current=current.getNextNode();
    }
    return length;
  }
  
  public void deleteFromHead() {
    this.head = this.head.getNextNode();
  }
  
  public Node find(int data) {
    Node current = this.head;
    while (current != null)  {
      if (current.getData() == data) {
        return current;
      }
      current = current.getNextNode();
    }
    return null;
  }
}
```
We also study doubly ended lists that have a pointer to the head and the tail of the list. 

```
public class DoublyEndedList {

  private Node head;
  private Node tail;
  
  public void insertAtTail(int data) {
    Node newNode = new Node(data);
    if (this.head == null) {
      this.head = newNode;
    }
    if (this.tail != null) {
      this.tail.setNextNode(newNode);
      this.tail = newNode;
    }
  }
  
  @Override
  public string toString() {
    String result = "{";
    Node current = this.head;
    while (current != null) {
      result += current.toString() + ",";
      current = current.getNextNode();
    }
    result += "}";
    return result;
  }
}
```

In a doubly-linked list every node points to the next and the previous node.

```
public class DoublyLinkedList {
  
  private DoublyLinkedNode head;
  
  public void insertAtHead (int data) {
    DoublyLinkedNode newNode = new DoublyLinkedNode(data);
    newNode.setNextNode(this.head);
    if (this.head != null) {
      this.head.setPreviousNode(newNode);
    }
    this.head = newNode;
  }
  
  public int length() {
    if (head == null) {
      return 0;
    }
    int length = 0;
    DoublyLinkedNode current = this.head;
    
    while(current != null) {
      length += 1;
      current = current.getNextNode();
    }
    
    return length;
  }
  
  @Override 
  public String toString() {
    StringBuilder sb = new StringBuilder();
    DoublyLinkedNode n = this.head;
    
    while (n != null) {
      sb.append("Node data");
      sb.append(n);
      sb.append("\n");
      
      n = n.getNextNode();
    }
    
    return sb.toString();
  }
}
```
In stacks all the data elements are inserted from the top, and are also removed from the top. These two operations are called push and pop. Now we will implement stacks and queues using arrays. The enqueue operation inserts an element in the queue from the tail towards the head. The dequeue operation removes an element in the queue from the head of the queue. The peek operation returns the first element of the queue without removing it. 

Let's study some recursions. We have euclid's algorithm :

```
public int gcd (int a, int b) {

  if (b==0) return a;
  return gcd(b, a%b);
}
```

An example of a tail recursion to calculate the factorial :

```
public int factorial (int n, int result) {
  if (n==0) return result;
  return factorial(n-1, n*result);
}

factorial(int n) {
  return factorial(n,1);
}
```

In the tail recursion there is an accumulator, because it accumulates values of the previous calculations, giving us a definite value everytime. We have the following pseudo-code : 

```
MergeSort(A, start, end)
  if start < end 
    middle = Floor[(start + end)/2]
    MergeSort(A, start, middle)
    MergeSort(A, middle+1, end)
    Merge(A, start, middle, end)
```
Now we also provide the code for the Merge algorithm :

```
Merge(A, start, mid, end)
  n1 = mid - start + 1
  n2 = end - mid
  Let left[0,...,n1] and right[0,...,n2] be new temp arrays
  for i=0 to n1-1
    left[i] = A[start + i]
  for j=0 to n2-i
    right[j] = A[mid + 1 + j]
  i, j = 0
  for k = start to end
    if left[i] <= right[j]
      A[k] = left[i]
      i = i+1
    else A[k] = right[j]
      j = j + 1
```
We have sorted arrays and linked lists. We can implement also binary trees. For that, we need the node of the three.

```
package pkg7

public class TreeNode {

  private Integer data;
  private TreeNode leftChild;
  private TreeNode rightChild;
  
  public TreeNode(Integer data) {
    this.data = data;
  }
  
  public TreeNode find(Integer data) {
    if (this.data == data) {
      return this;
    }
    if (data < this.data && leftChild != null) {
      return leftChild.find(data);
    }
    if (rightChild != null) {
      return rightChild.find(data);
    }
    return null;
  }
  
  public Integer getData() {
    return data;
  }
  
  public setLeftChild(TreeNode leftChild) {
    this.leftChild = leftChild;
  }
  
  public void setRightChild(TreeNode rightChild) {
    this.rightChild = rightChild;
  }
  
  public TreeNode getLeftChild() {
    return leftChild;
  }
  
  public TreeNode getRightChild() {
    return rightChild;
  }
  
  public void delete() {
    this.isDeleted = true;
  }
  
  public boolean isDeleted() {
    return isDeleted; 
  }
  
  public Integer smallest() {
    if (this.leftChild == null) return this.data;
    return this.leftChild.smallest();
  }
  
  public Integer largest() {
    if (this.rightChild == null) return this.data;
    return this.rightChild.largest();
  }
}
```
The corresponding binary tree is :

```
package pkg7

public class BinaryTree {
  
  private TreeNode root;
  
  public void insert(Integer data) {
    if (data >= this.data) {
      if (this.rightChild == null) { this.rightChild = new TreeNode(data)};
      else { this.rightChild.insert(data)};
    } else {
      if (this.leftChild == null) { this.leftChild = new TreeNode(data) };
      else { this.lefttChild.insert(data) };
    }
  }
  
  public Integer smallest() {
    if (this.root != null) return root.smallest();
    return null;
  }
  
  public Integer largest() {
    if (this.root != null) return root.largest();
    return null;
  }
  
  public TreeNode find(Integer data) { 
    if(root != null) {
      return root.find(data);
    }
    return null;
  }
  
  public void delete(Integer data) {
  
    TreeNode current = this.root;
    TreeNode parent = this.root;
    boolean isLeftChild = false;
    
    if (current == null) return;
    
    while (current != null && current.getData() != data) {
    
      parent = current;
      if (data < current.getData()) {
        current = current.getLeftChild();
        isLeftChild = true;
      } else {
        current = current.getRightChild();
        isLeftChild = false;
      }
    }
    
    if (current == null) return;
    
    if (current.getLeftChild()== null && current.getRightChild == null) {
      if (current == root) { root = null; }
      else {
        if (isLeftChild) { parent.setLeftChild(null); }
        else { parent.setRightChild(null); }
      }
    }
    else if (current.getRightChild() == null) {
      if (current == root) { root = current.getLeftChild(); }
      else if (isLeftChild) { parent.setLeftChild(current.getLeftChild()); }
      else { parent.setRightChild(current.getLeftChild());}
    }
     else if (current.getLeftChild() == null) {
      if (current == root) { root = current.getRightChild(); }
      else if (isLeftChild) { parent.setLeftChild(current.getRightChild()); }
      else { parent.setRightChild(current.getRightChild());}
    }
  }
}
```

The in order tree traversal requires you to traverse the left subtree, the root and the right subtree recursively. The pre-order traversal traverses the root first, then the left subtree, then the right subtree. 

We study some more sortin techniques : quick, shekk, countin, radix, bucket. The pseudo code for the quick sort is as follows :

```
QuickSort(A, start, end)
  if start < end  
    return
  pivot = Partition(A, start, end)
  QuickSort(A, start, pivot-1)
  QuickSort(A, pivot+1, end)
```
