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
if (phonebook.containsKey("Kevin")){
  phonebook.remove("Kevin")
}
System.out.println(phonebook);
```
In the below LinkedHashMap the first entry specifies the capacity of the map, the second entry specifies the percentage at while the map is full before augmenting in size. In the LinkedHashMap there is an optional third argument. We have the following code :

```
public static void main(String[] args) {
    LinkedHashMap<String, Integer> phonebook = new LinkedHashMap(4, 0.75f, false);
    phonebook.put("Joe", 12345);
    System.out.println("Joe's number : " + phonebook.get("Joe"));
    
    for(Map.Entry<String, Integer> entry : phonebook.entrySet()) {
      System.out.println(entry.getKey() + ": " + entry.getValue());
    }
}
```
A functional interface is an interface which can have any number of default, static methods but can contain only one abstract method.

```
@FunctionalInterface
public interface GreetingMessage {
  public abstract void greet(String name);
}
```
When you instantiate you have to implement the greet metho which is abstract and was therefore not implemented earlier in the GreetingMessage interface.

```
public static void main(String[] args) {

  GreetingMessage gm = new GreetingMessage() {
    @Override
    public void greet(String name) {
      System.out.println("Hello" + name)
    };
    
    // call the method as follows
    
    gm.greet("Bethan");
  }
}
```

Another way to define a function at the point where you want to use it is as such :

```
GreetingMessage gm2 = (String name) -> {
  System.out.println("Hello " + name);
};
```

Lambdas are used to implement functional interfaces when you are implementing the abstract method. Below we override the get area method using the below lambda.

```
public static void main(String[] args) {

  Square s = new Square(4);
  
  Shapes shapes = (Square square) -> {
    return square.calculateArea();
  };
  
  System.out.println("Area: " + shapes.getArea(s));
}
```
This can also be written as :

```
public static void main(String[] args) {

  Square s = new Square(4);
  
  Shapes shapes = Square::calculateArea;  // meaning here we take the square class and the corresponding metho
  
  System.out.println("Area: " + shapes.getArea(s));
}
```

We can write filters as follows. Here you call the println method on the  System.out class : 

```
books.stream().filter(book -> { return book.getAuthor().startWith("J");}).forEach(System.out::println);
```

Modularity makes it easier to write well encapsulated code. Modules contain code, some resources. It also contains some information describing itself. We can write this as :

```
module HelloWorld {
  requires java.desktop;
  exports helloworld;
}
```

The structure of a simple modular application :

```
package pack

public class Class {
}
```
