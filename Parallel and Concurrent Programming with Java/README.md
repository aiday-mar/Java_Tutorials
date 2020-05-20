# Parallel and Concurrent Programming with Java

These are notes taken from the course on parallel programming in Java.

Busy waiting or spinning is the act of repeatedly acquiring and releasing the lock to check for a certain condition to continue. A condition variable serves as a queue or a container for threads, that are waiting for a certain condition to occur. A condition variable is associated to a mutex, and toegther they implement a higher level construct called a monitor. Monitors protect sections of code with mutual exclusion, and they provide the ability for threads to wait until a condition occurs or block until a certain condition has become true. A monitor contains the procedures and the shared data that you want to protect. Only one thread works in the monitor and the mutex is a sort of lock.

The condition variable has three main operations : wait, signal and broadcast. Before using a condition variable, you need to acquire the mutex associated with it. The wait operation automatically relases the lock on the mutex. The signal thread is used to wake up one thread from the condition variable queue, so it can acquire the lock. The broadcast operation wakes up all the threads from the condition variable queue. We have the following code. We use a condition variable to allow the threads to communicate : 

```
import java.util.concurrent.locks.*;

class HungryPerson extends Thread {
  
  private int personID;
  private static Lock slowCookerLid = new ReentrantLock();
  private static int servings = 11;
  private static Condition soupTaken = slowCookerLid.newCondition();  // new condition created on the lock
  
  public HungryPerson(int personID) {
    this.personID = personID;
  }
  
  public void run() {
    while (servings > 0) {
      slowCookerLid.lock();           // you lock the lock
      try {                           // check if it's their turn
        while ((personID != servings % 2) && servings > 0) {
          soupTaken.await();
        }
        if (servings > 0) {
          servings--;
          soupTaken.signalAll();
        }
      } catch (Exception e) {
        e.printStackTrace();
      } finally {
        slowCookerLid.unlock();
      }
    }
  }
}

public class ConditionVariableDemo {
  public static void main(String args[]) {
    for (int i=0; i<2; i++) {
      new HungryPerson(i).start();
    }
  }
}
```
A common design pattern in concurrent programming is the producer-consumer architecture. In this pattern, some threads act as producers, they add elements to shared data structures, while other threads act as consumers and remove elements from shared data structures. This pattern operates on the principle of first in first out. First off, the queue is a shared resource so we need to enforce the mutual exclusion of producers and consumers and make sure that only one thread can use it at a time to add or remove items. We also need to make sure that producers do not add data 
