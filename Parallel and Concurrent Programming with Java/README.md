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
A common design pattern in concurrent programming is the producer-consumer architecture. In this pattern, some threads act as producers, they add elements to shared data structures, while other threads act as consumers and remove elements from shared data structures. This pattern operates on the principle of first in first out. First off, the queue is a shared resource so we need to enforce the mutual exclusion of producers and consumers and make sure that only one thread can use it at a time to add or remove items. We also need to make sure that producers do not add data when the queue is full. Consumers won't try to remove data from an empty buffer. It is also possible to have unbounded queues, which is a queue with an unlimited capacity.

A pipeline is a chain of processing elements arranged so that the output of each element is the input to the next one. Java's `ArrayBlockingQueue` is a concrete class that implements the blocking queue interface. This mechanism is thread safe. We have :

```
import java.util.concurrrent.*;

class SoupProducer extends Thread {
  
  private BlockinQueue servingLine;
  
  public SoupProducer(BlockingQueue servingLine) {this.servingLine = servingLine; }
  
  public void run() {
    for (int i=0; i<20; i++) {
      try {
        servingLine.add("Bowl #" + i);
        System.out.format("Served bowl #%d - remanining capacity: %d\n", i, servingLine.remainingCapacity());
        Thread.sleep(200);
      } catch (Exception e) { e.printStackTrace(); }
    }
  }
}

class SoupConsumer extends Thread {
  
  private BlockingQueue servingLine;
  
  public SoupConsumer(BlockingQueue servingLine) {this.servingLine = servingLine; }
  
  public void run() {
    while (true) {
      try {
        String bowl = (String)servingLine.take();
        if (bowl == "no soup for you!") break;
        System.out.format("Ate %s\n", bowl);
        Thread.sleep(300);
      } catch(Exception e) { e.printStackTrace(); }
    }
  }
}

public class ProducerConsumerDemo {
  public static void main(String args[]) {
    BlockingQueue servingLine = new ArrayBlockingQueue<String>(5);
    new SoupConsumer(servingLine).start();
    new SoupProducer(servingLine).start();
  }
} 
```
Semaphore is a synchronization mechanism that can be used to control the access to shared resources. The semaphore can be used by multiple threads at the same time. The semaphore has a counter, as long as it is positive, we decrement the counter. If the counter is zero, we wait until the semaphore becomes available. When a thread is done using the semaphore it releases the semaphore, and increments the counter value. A binary sempahore can have one of two values, zero or one, where zero represents locked and one represents unlocked. It can be used in a similar way to the mutex by acquiring and releasing. However there are differences between the mutex and the semaphore. The mutex can only be acquired or released by the same thread, that originally locked it. A semaphore can be acquired and released by different threads. This whole procedure is called the signalling mechanism. A semaphore can be used in the same way to condition variables, to synchronize producer and consumer threads, adding and removing elements from a shared finite queueor buffer. An example of the semaphore is :

```
import java.util.concurrent.*;

class CellPhone extends Thread {
  
  private static Semaphore charger = new Semaphore(4);
  
  public CellPhone(String name) {
    this.setName(name);
  }
  
  public void run() {
    try {
      charger.acquire();
      System.out.println(this.getName() + " is charging ...");
      Thread.sleep(ThreadLocalRandom.current().nextInt(1000, 2000));
    } catch (InterruptedException e) {
      e.printStackTrace();
    } finally {
      System.out.println(this.getName() + "is done charging");
      charger.release();
    }
  }
}
```
Data races and race conditions are different potential probelsm in concurrent programs. Data races can occur when two or more threads concurrently access the same memory location. For this we need to ensure mutual exclusion for shared resources. You can use sleep statements to modify timing and execution order. We have the following example where we also implented a barrier. A barrier prevets a group of threads from proceeding until enough threads have reached the barrier.

```
import java.util.concurrent.locks.*;
import java.util.concurrent.*;

class Shopper extends Thread {

  public static int bagsOfChips = 1;
  private static Lock pencil = new ReentrantLock();
  private static CyclicBarrier fistBump = new CyclicBarrier(10);
  
  public Shopper(String name) {
    this.setName(name);
  }
  
  public void run() {
    if (this.getName().contains("Olivia")) {
      pencil.lock();
      try {
        bagsOfChips += 3;
        System.out.println(this.getName() + "Added three bags of chips");
      } finally {
        pencil-unlock();
      }
      try {
        fistbump.await();
      } catch (InterruptedException | BrokenBarrierException e) {
        e.printStackTrace();
      }
    } else {
      try {
        fistbump.await();
      } catch (InterruptedException | BrokenBarrierException e) {
        e.printStackTrace();
      }
      pencil.lock();
      try {
        bagOfChips *= 2;
        System.out.println(this.getName() + "doubled the bags of chips");
      } finally {
        pencil.unlock();
      }
    }
  }
}

public class RaceConditionDemo {
  public static void main(String args[]) throws InterruptedException {
  
    Shopper[] shoppers = new Shopper[10];
    for (int i=0; i<shoppers.length/2; i++) {
      shoppers[2*i] = new Shopper("Barron="+i);
      shopper[2*i+1] = new Shopper("Olivia="+i);
    }
    for (Shopper s : shoppers) 
      s.start();
    for (Shopper s : shoppers) 
      s.join();
    System.out.println("We need to buy " + Shopper.bagsOfChips + " bags of chips.");
  }
}
```
There are some CyclicBarrier methods such as : `int getParties()` which gives the total number of threads needed to trip the barrier, `int getNumberWaiting()` which gives the current number of threads waiting on the barrier, `void reset()` which resets the barrier to the initial state, `boolean isBroken()` has a thread broken out since the last reset. There is another synchronization mechanism called a CountDownLatch, that allows one or more threads to wait until a set of operations being performed in other threads completes. You initialize this with `CountDownLatch(value)` where we have the initial value `value`, and we can use the keyword `await()` in order to wait for the count value to reach zero, we also have `countDown()` which decrements the count value. The difference between the two is that the cyclic barrier releases when a certain number of threads are waiting, and the count down latch releases when the count value reaches zero.
