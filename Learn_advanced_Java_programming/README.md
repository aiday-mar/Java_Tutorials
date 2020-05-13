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
