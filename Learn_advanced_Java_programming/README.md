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

You can run modules also from the command line as follows :

```
java --module-path output --module helloworld/com.bethan.helloworld.HelloWorld
```

Threads allow multiple actions to be performed in a single process. When a method is running on a thred, the local variables in the method are only available within the thread. Java has ab API for handling threads. Threads should be used for blocking input and output, for GUI applications. As well as if they use toolkits such as AWT or Swing. An example is as written below :

```
public class ThreadExample extends Thread {
  
  @Override
  public void run() {
    int i = 1;
    while (i <= 100) {
      System.out.println(i + " " + this.getName());
      i++;
    }
  }
}
```

So that the main class should be :

```
public class Main {
  
  public static void main(String[] args) {
    
    ThreadExample thread1 = new ThreadExample();
    thread1.SetName("My first thread");
    thread1.start();
  }
}
```

An example of the use of the runnable interface :

```
public class RunnableExample implements Runnable {

  @Override
  public void run() {
    int i = 0;
    while(i <= 100) {
      System.out.println(i + " " + Thread.currentThread().getName());
      i++;
    }
  }
}
```

And it's implementation here :

```
public static void main(String[] args) {
  Thread thread1 = new Thread(new RunnableExample());
  thread1.start();
  
  Thread thread2 = new Thread(() -> {
    int i = 0;
    while (i <= 100) {
      System.out.println(i + " " + Thread.currentThread().getName());
      i++;
    }
  });
  
  thread2.start();
}
```

In order to synchronize threads you may write the keyword `synchronized` before the name of the class as follows :

```
public static ATM {
  static synchronized void withdraw(BankAccount account, int amount) {
  ...
  }
}
```

A synchronized section is a set of code that only one thread can enter at a time. The parameter in the brackets is the monitor object. We can have situations where there are two threads running in parallel and with different orders of monitor objects, in this manner we have the program never comes to an end.

There are various implementations of InputStreams and OutputStreams. Examples of InputStreams are : FileInputStream, ByteArrayInputStream, FilterInputStream. Examples of OutputeStreams are : FileOutputStreams, ByteArrayOutputStreams, FilterOutputStreams. We can read data from the console as follows using the following code :

```
public class PersonCrearor {
  public static void main(String[] args) {
    Scanner scanner = new Scanner(System.in);
    System.out-print("Enter the name : ");
    String name = scanner.next();
    System.out.print("Enter the age : ");
    int age = scanner.nextInt();
    System.out.print("Enter a phone number : ");
    Long phoneNumber = scanner.nextLong();
    Person person = new Person(name, age, phoneNumber)
  } 
}
```

We provide an example of the buffered reader :

```
public class BufferedReaderExample {
  public static void main(String[] args) {
  
    File myFile = new File("example.txt");
    try {
      BufferedReader reader = new BufferedReader(new FileReader(myFile));
      String line;
      while((line = reader.readLine()) != null) {
        System.out.println(line);
      } 
    } catch (IOException e) {
      
    }
  }
}
```

Here is an example of a try-catch block with resources :

```
public class TryWithResourcesExample {
  
  public static void main(String[] args) {
    
    try(BufferedReader reader = new BufferedReader(new StringReader("Hello World"));
        StringWriter writer = new StringWriter();) {
     
      writer.write(reader.readLine());
      System.out.println(writer.toString());
     } catch(IOException ioe) {  }
  }
}
```

To create a new file we can run the following commands :

```
public class FileCreationExample {
  
  public static void main(String[] args) {
    
    File myFile = new File("C:\\Users\\aiday\\Desktop\\myFile.txt");
    try {
      boolean fileCreated = myFile.createNewFile();
      System.out.println(fileCreated);
    } catch (IOException ioe) {}
  }
}
```

Now there is a code snippet of working with directories. Consider the code below :

```
public static void main(String[] args) {
  
  FilenameFilter filter = (file, fileName) -> {
    return fileName.contains(".");
  }
  
  String[] contents = new File(".").list(filter);
  for (String file : contents) { 
    System.out.println(file);
  }
  
  new File("myDirectory").mkdir();
}
```

We also study how to use the Path class :

```
public static void main(String[] args) {
  
  Path path = Paths.get("Hello World-txt");
  try {
    Files.deleteIfExists(path);
  } catch (IOException ex) {}
  
  Path path2 = Paths.get("C:\\Users\\aiday\\Desktop\\myFiles\\example.txt");
  System.out.println(path2.getParent());
  System.out.println(path2.getRoot());
  System.out.println(path2.getFileName());
}
```

Next we have the following code to copy files into other files.

```
public static void main(String[] args) {
  
  Path source = Paths.get("C:\\Users\\aiday\\Desktop\\example.txt");
  Path dest = Paths.get("C:\\Users\\aiday\\Desktop\\next.txt");
  try {
    Files.copy(source, dest, REPLACE_EXISTING);
  } catch (IOException ex) {
    ex.prinStackTrace();
  }
}
```
