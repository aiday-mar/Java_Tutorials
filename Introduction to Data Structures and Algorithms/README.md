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
We also study doubly linked lists that have a pointer to the head and the tail of the list. 

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
