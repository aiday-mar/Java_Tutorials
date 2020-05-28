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

You divide the task into smaller task and run each independently. The thread pool creates and maintains a collection of worker threads. It reuses existing threads to execute tasks. We will use the executor service interface in the `java.util.concurrent` package.  It is a higher level interface for running tasks. In the executor service there is a task queue which connects to a thread pool. We have an executors class which has different methods as follows : a `newSingleThreadExecutor()` method which creates an executor that uses a single thread to execute tasks, and a `newFixedThreadPool(int nThreads)` which creates a thread pool that reuses a fixed number of threads to execute tasks. An example :

```
import java.util.concurrent.*;

class VegetableChopper extends Thread {
  public void run() {
    System.out.println(Thread.currentThread().getName() + " chopped a vegetable !");
  }
}

public class ThreadPoolDemo {
  public static void main(String args[]) {
    int numProcs = Runtime.getRuntime().availableProcessors();
    ExecutorService pool = Executors.newFixedThreadPool(numProcs);
    for (int i=0; i<100; i++)
      pool.submit(new VegetableChopper());
    pool.shutdown();
  }
}
```
You can return a tak of result of type V, and may throw an exception with `Callable<V> Interface`.

```
import java.util.concurrent.*;

class HowManyVegetables implements Callable {
  public Integer call() throws Exception {
    System.out.println("Olivia is counting vegetables..");
    Thread.sleep(3000);
    return 42;
  }
}

public class FutureDemo {
  public static void main(String args[]) throws ExecutionException, InterruptedException {
    System.out.println("Barron asks Olivia how many vegetables are in the pantry.");
    ExecutorService executor = Executors.newSingleThreadExecutor();
    Future result = executor.submit(new HowManyVegetables());
    System.our.println("Barron can do other things while he waits for the result...");
    System.out.println("Olivia respondeed with " + result.get());
    executor.shutdown();
  }
}
```
A Fork/Join framework is a framework for executing recursive divide-and-conquer work with multiple processors. The ForkJoinPool distributes tasks to its worker threads. The ExecutorService that executes FrokJoinTasks. The `fork()` method asynchronously executes a task in the ForkJoinPool. The `join()` method results in a computation when it is done. The subclasses are `RecursiveTask<V>` returns a result and the `RecursiveAction` does not return a result.

```
import java.util.concurrent.*;

class RecursiveSum extends RecursiveTask<Long> {
  
  private long lo, hi;
  
  public RecursiveSum(long lo, long hi) {
    this.lo = lo;
    this.hi = hi;
  }
  
  protected Long compute() {
    if (hi - lo <= 100_000) {
      long total = 0;
      for (long i = lo; i <= hi; i++) total += i;
      return total;
    } else {
      long mid = (hi+lo)/2;
      RecursiveSum left = new RecursiveSum(lo, mid);
      Recursiveum right = new RecursiveSum(mid+1, hi);
      left.fork();
      return right.compute() + left.join();
    }
  }
}

public class DivideAndConquerDemo {
  public static void main(String args[]) {
    ForkJoinPool pool = ForkJoinPool.commonPool();
    Long total = pool.invoke(new RecursiveSum(0, 1_000_000_000));
    pool.shutdown();
    System.out.println("Total sum is " + total);
  }
}
```
Strong scaling requires a variable number of processors with fixed total problem size. The throughput is the number of tasks divided by the time. The latency is the time needed to finish a task. The speed up is equal to the sequential execution time divided by the parallel execution time with N workers. In Amdahl's law, the P represents the portion of a program that is parallelizable. S is the speedup of the parallelizable portion. The overall speedup is :

```
overall speedup = 1/((1-p) + p/S)
```

The efficiency is a measure of how well additional resources are utilized. The effiency is equal to the speedup divided by the number of processors. In synchronous blocking communication, tasks wait until the entire communication is complete, you can not do other work while this is in progress. In asynchrnous nonblocking communication, the tasks do not wait for the communication to be complete. The overhead is the compute time/resources spent on comunication. Using fine-grained parallelism, the problem is broken down into a large number of small tasks. In the fine-grained parallelism there is a good distribution of the workload, and there is a low computation-to-communication ratio. Coarse-grained parallelism splits the program into a small number of large tasks. This coarse-grained parallelism has a high computation-to-communication ratio, and it has inefficient load balancing.

The mapping process does not apply to single-core processors and atuomated task scheduling. The operating system handles scheduling threads to execute on specific processor cores. The goal of a mapping algorithm is to minimize the total execution time of the program. Here you need to place tasks that can execute concurrently on different processors. Or you can place tasks that communicate frquently on the same processor.

We include a couple of examples of parallel and concurrent programming in java.

```
import java.util.Arrays;
import java.util.Random;
import java.util.concurrent.*;

/* sequential implementation of matrix multiplication */
class SequentialMatrixMultiplier {

    private int[][] A, B;
    private int numRowsA, numColsA, numRowsB, numColsB;

    public SequentialMatrixMultiplier(int[][] A, int[][] B) {
        this.A = A;
        this.B = B;
        this.numRowsA = A.length;
        this.numColsA = A[0].length;
        this.numRowsB = B.length;
        this.numColsB = B[0].length;
        if (numColsA != numRowsB)
            throw new Error(String.format("Invalid dimensions; Cannot multiply %dx%d*%dx%d\n", numRowsA, numRowsB, numColsA, numColsB));
    }

    /* returns matrix product C = AB */
    public int[][] computeProduct() {
        int[][] C = new int[numRowsA][numColsB];
        for (int i=0; i<numRowsA; i++) {
            for (int k=0; k<numColsB; k++) {
                int sum = 0;
                for (int j=0; j<numColsA; j++) {
                    sum += A[i][j] * B[j][k];
                }
                C[i][k] = sum;
            }
        }
        return C;
    }
}

/* parallel implementation of matrix multiplication */
class ParallelMatrixMultiplier {

    private int[][] A, B;
    private int numRowsA, numColsA, numRowsB, numColsB;

    public ParallelMatrixMultiplier(int[][] A, int[][] B) {
        this.A = A;
        this.B = B;
        this.numRowsA = A.length;
        this.numColsA = A[0].length;
        this.numRowsB = B.length;
        this.numColsB = B[0].length;
        if (numColsA != numRowsB)
            throw new Error(String.format("Invalid dimensions; Cannot multiply %dx%d*%dx%d\n", numRowsA, numRowsB, numColsA, numColsB));
    }

    /* returns matrix product C = AB */
    public int[][] computeProduct() {
        // create thread pool
        int numWorkers = Runtime.getRuntime().availableProcessors();
        ExecutorService pool = Executors.newFixedThreadPool(numWorkers);

        // submit tasks to calculate partial results
        int chunkSize = (int) Math.ceil((double) numRowsA / numWorkers);
        Future<int[][]>[] futures = new Future[numWorkers];
        for (int w=0; w<numWorkers; w++) {
            int start = Math.min(w * chunkSize, numRowsA);
            int end = Math.min((w + 1) * chunkSize, numRowsA);
            futures[w] = pool.submit(new ParallelWorker(start, end));
        }

        // merge partial results
        int[][] C = new int[numRowsA][numColsB];
        try {
            for (int w=0; w<numWorkers; w++) {
                // retrieve value from future
                int[][] partialC = futures[w].get();
                for (int i=0; i<partialC.length; i++)
                    for (int j=0; j<numColsB; j++)
                        C[i + (w * chunkSize)][j] = partialC[i][j];
            }
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        }
        pool.shutdown();
        return C;
    }

    /* worker calculates result for subset of rows in C */
    private class ParallelWorker implements Callable {

        private int rowStartC, rowEndC;

        public ParallelWorker(int rowStartC, int rowEndC) {
            this.rowStartC = rowStartC;
            this.rowEndC = rowEndC;
        }

        public int[][] call() {
            int[][] partialC = new int[rowEndC-rowStartC][numColsB];
            for(int i=0; i<rowEndC-rowStartC; i++) {
                for(int k=0; k<numColsB; k++) {
                    int sum = 0;
                    for(int j=0; j<numColsA; j++) {
                        sum += A[i+rowStartC][j]*B[j][k];
                    }
                    partialC[i][k] = sum;
                }
            }
            return partialC;
        }
    }
}

public class MatrixMultiplySolution {

    /* helper function to generate MxN matrix of random integers */
    public static int[][] generateRandomMatrix(int M, int N) {
        System.out.format("Generating random %d x %d matrix...\n", M, N);
        Random rand = new Random();
        int[][] output = new int[M][N];
        for (int i=0; i<M; i++)
            for (int j=0; j<N; j++)
                output[i][j] = rand.nextInt(100);
        return output;
    }

    /* evaluate performance of sequential and parallel implementations */
    public static void main(String args[]) {
        final int NUM_EVAL_RUNS = 5;
        final int[][] A = generateRandomMatrix(50,50);
        final int[][] B = generateRandomMatrix(50,50);

        System.out.println("Evaluating Sequential Implementation...");
        SequentialMatrixMultiplier smm = new SequentialMatrixMultiplier(A,B);
        int[][] sequentialResult = smm.computeProduct();
        double sequentialTime = 0;
        for(int i=0; i<NUM_EVAL_RUNS; i++) {
            long start = System.currentTimeMillis();
            smm.computeProduct();
            sequentialTime += System.currentTimeMillis() - start;
        }
        sequentialTime /= NUM_EVAL_RUNS;

        System.out.println("Evaluating Parallel Implementation...");
        ParallelMatrixMultiplier pmm = new ParallelMatrixMultiplier(A,B);
        int[][] parallelResult = pmm.computeProduct();
        double parallelTime = 0;
        for(int i=0; i<NUM_EVAL_RUNS; i++) {
            long start = System.currentTimeMillis();
            pmm.computeProduct();
            parallelTime += System.currentTimeMillis() - start;
        }
        parallelTime /= NUM_EVAL_RUNS;

        // display sequential and parallel results for comparison
        if (!Arrays.deepEquals(sequentialResult, parallelResult))
            throw new Error("ERROR: sequentialResult and parallelResult do not match!");
        System.out.format("Average Sequential Time: %.1f ms\n", sequentialTime);
        System.out.format("Average Parallel Time: %.1f ms\n", parallelTime);
        System.out.format("Speedup: %.2f \n", sequentialTime/parallelTime);
        System.out.format("Efficiency: %.2f%%\n", 100*(sequentialTime/parallelTime)/Runtime.getRuntime().availableProcessors());
    }
}
```

Next we try to implement the merge sort algorithm as above using parallel and concurrent programming principles : 

```
import java.util.Arrays;
import java.util.Random;
import java.util.concurrent.*;

/* sequential implementation of merge sort */
class SequentialMergeSorter {

    private int[] array;

    public SequentialMergeSorter(int[] array) {
        this.array = array;
    }

    /* returns sorted array */
    public int[] sort() {
        sort(0, array.length-1);
        return array;
    }

    /* helper method that gets called recursively */
    private void sort(int left, int right) {
        if (left < right) {
            int mid = (left+right)/2; // find the middle point
            sort(left, mid); // sort the left half
            sort(mid+1, right); // sort the right half
            merge(left, mid, right); // merge the two sorted halves
        }
    }

    /* helper method to merge two sorted subarrays array[l..m] and array[m+1..r] into array */
    private void merge(int left, int mid, int right) {
        // copy data to temp subarrays to be merged
        int leftTempArray[] = Arrays.copyOfRange(array, left, mid+1);
        int rightTempArray[] = Arrays.copyOfRange(array, mid+1, right+1);

        // initial indexes for left, right, and merged subarrays
        int leftTempIndex=0, rightTempIndex=0, mergeIndex=left;

        // merge temp arrays into original
        while (leftTempIndex < mid - left + 1 || rightTempIndex < right - mid) {
            if (leftTempIndex < mid - left + 1 && rightTempIndex < right - mid) {
                if (leftTempArray[leftTempIndex] <= rightTempArray[rightTempIndex]) {
                    array[mergeIndex ] = leftTempArray[leftTempIndex];
                    leftTempIndex++;
                } else {
                    array[mergeIndex ] = rightTempArray[rightTempIndex];
                    rightTempIndex++;
                }
            } else if (leftTempIndex < mid - left + 1) { // copy any remaining on left side
                array[mergeIndex ] = leftTempArray[leftTempIndex];
                leftTempIndex++;
            } else if (rightTempIndex < right - mid) { // copy any remaining on right side
                array[mergeIndex ] = rightTempArray[rightTempIndex];
                rightTempIndex++;
            }
            mergeIndex++;
        }
    }
}

/* parallel implementation of merge sort */
class ParallelMergeSorter {

    private int[] array;

    public ParallelMergeSorter(int[] array) {
        this.array = array;
    }

    /* returns sorted array */
    public int[] sort() {
        int numWorkers = Runtime.getRuntime().availableProcessors();
        ForkJoinPool pool = new ForkJoinPool(numWorkers);
        pool.invoke(new ParallelWorker(0,array.length-1));
        return array;
    }

    /* worker that gets called recursively */
    private class ParallelWorker extends RecursiveAction {

        private int left, right;

        public ParallelWorker(int left, int right) {
            this.left = left;
            this.right = right;
        }

        protected void compute() {
            if (left < right) {
                int mid = (left + right) / 2; // find the middle point
                ParallelWorker leftWorker = new ParallelWorker(left, mid);
                ParallelWorker rightWorker = new ParallelWorker(mid+1, right);
                invokeAll(leftWorker, rightWorker);
                merge(left, mid, right); // merge the two sorted halves
            }
        }
    }

    /* helper method to merge two sorted subarrays array[l..m] and array[m+1..r] into array */
    private void merge(int left, int mid, int right) {
        // copy data to temp subarrays to be merged
        int leftTempArray[] = Arrays.copyOfRange(array, left, mid+1);
        int rightTempArray[] = Arrays.copyOfRange(array, mid+1, right+1);

        // initial indexes for left, right, and merged subarrays
        int leftTempIndex=0, rightTempIndex=0, mergeIndex=left;

        // merge temp arrays into original
        while (leftTempIndex < mid - left + 1 || rightTempIndex < right - mid) {
            if (leftTempIndex < mid - left + 1 && rightTempIndex < right - mid) {
                if (leftTempArray[leftTempIndex] <= rightTempArray[rightTempIndex]) {
                    array[mergeIndex ] = leftTempArray[leftTempIndex];
                    leftTempIndex++;
                } else {
                    array[mergeIndex ] = rightTempArray[rightTempIndex];
                    rightTempIndex++;
                }
            } else if (leftTempIndex < mid - left + 1) { // copy any remaining on left side
                array[mergeIndex ] = leftTempArray[leftTempIndex];
                leftTempIndex++;
            } else if (rightTempIndex < right - mid) { // copy any remaining on right side
                array[mergeIndex ] = rightTempArray[rightTempIndex];
                rightTempIndex++;
            }
            mergeIndex++;
        }
    }
}

public class MergeSortSolution {

    /* helper function to generate array of random integers */
    public static int[] generateRandomArray(int length) {
        System.out.format("Generating random array int[%d]...\n", length);
        Random rand = new Random();
        int[] output = new int[length];
        for (int i=0; i<length; i++)
            output[i] = rand.nextInt();
        return output;
    }

    /* evaluate performance of sequential and parallel implementations */
    public static void main(String[] args) {
        final int NUM_EVAL_RUNS = 5;
        final int[] input = generateRandomArray(100_000_000);

        System.out.println("Evaluating Sequential Implementation...");
        SequentialMergeSorter sms = new SequentialMergeSorter(Arrays.copyOf(input, input.length));
        int[] sequentialResult = sms.sort();
        double sequentialTime = 0;
        for(int i=0; i<NUM_EVAL_RUNS; i++) {
            sms = new SequentialMergeSorter(Arrays.copyOf(input, input.length));
            long start = System.currentTimeMillis();
            sms.sort();
            sequentialTime += System.currentTimeMillis() - start;
        }
        sequentialTime /= NUM_EVAL_RUNS;

        System.out.println("Evaluating Parallel Implementation...");
        ParallelMergeSorter pms = new ParallelMergeSorter(Arrays.copyOf(input, input.length));
        int[] parallelResult = pms.sort();
        double parallelTime = 0;
        for(int i=0; i<NUM_EVAL_RUNS; i++) {
            pms = new ParallelMergeSorter(Arrays.copyOf(input, input.length));
            long start = System.currentTimeMillis();
            pms.sort();
            parallelTime += System.currentTimeMillis() - start;
        }
        parallelTime /= NUM_EVAL_RUNS;

        // display sequential and parallel results for comparison
        if (!Arrays.equals(sequentialResult, parallelResult))
            throw new Error("ERROR: sequentialResult and parallelResult do not match!");
        System.out.format("Average Sequential Time: %.1f ms\n", sequentialTime);
        System.out.format("Average Parallel Time: %.1f ms\n", parallelTime);
        System.out.format("Speedup: %.2f \n", sequentialTime/parallelTime);
        System.out.format("Efficiency: %.2f%%\n", 100*(sequentialTime/parallelTime)/Runtime.getRuntime().availableProcessors());
    }
}
```
