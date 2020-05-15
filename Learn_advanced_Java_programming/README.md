# Learn advanced Java programming

Generics tell what type of objects a collection can contain. Generic methods are methods that allows you to create a new type parameter just for that method. In the below for example I use a generic type which we will label as T.

```
public class GenericMethods {
  
  static Boolean[] boolArray = {true, false, true};
  
  public static<T> List<T> arrayToList(T[] array, List<T> list) {
    for (T object : array ) {
      list.add(object)
    }
    
    return list;
  }
  
  public static void main(String[] args) {
  
    // below we have T = Boolean
    List<Boolean> boolList = arrayToList(boolArray, new ArrayList<>());
  }
}
```

When you can either have an array of arguments or just one argument you can use varargs as follows :

```
private static void printShoppingList(String... items) {
  System.out.println("Shopping lis");
  for (int i=0; i < items.length; i++) {
    System.out.println(i + 1 + ":" + items[i]);
  }
  System.out.println();
}
```

The substitution principle allows you to write maintainable and reusable code. If you have a variable of a given type you can assign it a subtype of that type. Suppose b is a subtype of a then you can call the function as both : `function(a)` and `function(b)`.

To use a wildcard I put a question mark and then write extends. In the below you can also pass as an argument a list of any type that extends the building class. Wildcards can also be used to specify that super types can be used when a subtype is specified. You can also write `? super` in front of the type in the example below:

````
static void printBuildings(List <? extends Building> buildings)
{ ... }

static void addHouseToList(List<? super House> buildings) 
{ ... }
````

Collections allow us to group objects together in a container. There are important key factors : if the order is important, are duplicates allowed, how fast are operations performed, memory use is another factor. There is a set of interfaces that define different types of collections. At the top of the hierarchy is iterable. This iterable declares a forEach method. The collection interface extends the iterable interface. Examples of collections are sets, lists or queues.

Sets are a type of collection that allow no duplicates, they are unordered. Lists allow duplicate entries and the order is significant. Queues are a type of collection that lets you add elements. These use first-in, first-out operations. Maps do not extend the collection interface, they contain key-value pairs.

A linked list is a doubly linked collection of elements. Each entry in the list also holds a reference to the address of the next and the previous items in the list. The main advantage of using a linked list is that they are very quick for inserting and removing elements in the middle of a list.

```
LinkedList<String> myList = new LinkedList();
myList.add(0, "A");
System.out.println(myList);
```

Below we will then implement a queue with a linked list.

```
static void serveCustomer(LinkedList<Customer> queue) {

  Customer c = queue.poll();
  c.serve();
}
```

In the above, serve is a method called on the customer. HashMaps are common data structures, they allow us to store pairs of values together. Hashmaps do not allow duplicate keys. Hashmaps allow to have null as the value for a key.

```
HashMap<String, Integer> phonebook = new HashMap<>();
phonebook.put("Kevin", 12345);
phonebook.put("Jill", 98765);
System.out.println(phonebook);
```
