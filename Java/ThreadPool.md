
## Executing multiple Threads with ExecutorService

### Code example

The following code shows the execution of two simultaneous tasks using the Java `ExecutorService`:

```java
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

public class ThreadPool {

    public static void main(String[] args) throws InterruptedException {
        CountDownLatch countDownLatch = new CountDownLatch(2);  // 2 = number of threads to execute

        Runnable task1 = () -> {
            System.out.println(
                "Task 1 will now wait for 3 seconds in thread " + Thread.currentThread().getName()
            );
            sleep(3);
            countDownLatch.countDown();  // Decrement countDownLatch
        };

        Runnable task2 = () -> {
            System.out.println(
                "Task 2 will now wait for 4 seconds in thread " + Thread.currentThread().getName()
            );
            sleep(4);
            countDownLatch.countDown();  // Decrement countDownLatch
        };

        // Creating a thread pool with a maximum of 5 threads
        ExecutorService executorService = Executors.newFixedThreadPool(5);

        // Executing task
        Future<?> task1Future = executorService.submit(task1);
        Future<?> task2Future = executorService.submit(task2);

        // Do not accept any more threads, and wait for the running ones to finish
        executorService.shutdown();

        // Read Future objects
        System.out.println("Task 1 Future done: " + task1Future.isDone());  // Task 1 Future done: false
        System.out.println("Task 2 Future done: " + task2Future.isDone());  // Task 2 Future done: false

        countDownLatch.await();  // Wait until threads are completed

        // Read Future objects again
        System.out.println("Task 1 Future done: " + task1Future.isDone());  // Task 1 Future done: true
        System.out.println("Task 2 Future done: " + task2Future.isDone());  // Task 2 Future done: true
    }

    private static void sleep(int seconds) {
        try {
            Thread.sleep(seconds * 1000);
        }
        catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

}
```

Output:

```
Task 1 will now wait for 3 seconds in thread pool-1-thread-1
Task 2 will now wait for 4 seconds in thread pool-1-thread-2
Task 1 Future done: false
Task 2 Future done: false
                             <<< waiting time
Task 1 Future done: true
Task 2 Future done: true
```

## Obtaining results from threads using Callable and Future

The following code obtains return values from the threads:

```java
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.*;

public class Callables {

    public static void main(String[] args) {

        Callable<String> callable = () -> {  // String is the return type
            Thread.sleep(2 * 1000);          // A 2-sec delay
            return "OK";                     // The return string
        };

        // A list to store the Future objects returned by executor service's submit() method
        List<Future<String>> futures = new ArrayList<>();

        // Thread pool
        ExecutorService executorService = Executors.newFixedThreadPool(5);

        // Execute tasks
        for (int i = 0; i < 5; i++)
            futures.add(executorService.submit(callable));

        // Do not accept any more threads, and wait for the running ones to finish
        executorService.shutdown();

        try {
            for (Future<String> future: futures)
                // Future.get(timeout) --> wait until the task returns a result
                System.out.println(future.get(20, TimeUnit.SECONDS));
        }
        catch (InterruptedException|TimeoutException e) {
            e.printStackTrace();
        }
        catch (ExecutionException e) {
            System.out.println("The execution has thrown an exception:");
            e.printStackTrace();
        }

    }  // end main()

}

```

Output:

```
     <<< ~2-sec delay
OK
OK
OK
OK
OK
```

## Scheduling tasks with a single-thread scheduled executor

```java
import org.joda.time.DateTime;  // Gradle dependency: compile group: 'joda-time', name: 'joda-time', version: '2.10.4'

import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.*;

public class Main {

    public static void main(String[] args) throws InterruptedException {
        ScheduledExecutorService executor  = Executors.newSingleThreadScheduledExecutor();
        List<ScheduledFuture<?>> schedules = new ArrayList<>();

        schedules.add(executor.schedule(new Task(), 5, TimeUnit.SECONDS));
        schedules.add(executor.schedule(new Task(), 5, TimeUnit.SECONDS));
        schedules.add(executor.schedule(new Task(), 5, TimeUnit.SECONDS));

        schedules.add(executor.schedule(new Task(), 10, TimeUnit.SECONDS));
        schedules.add(executor.schedule(new Task(), 10, TimeUnit.SECONDS));
        schedules.add(executor.schedule(new Task(), 10, TimeUnit.SECONDS));

        schedules.add(executor.schedule(new Task(), 15, TimeUnit.SECONDS));
        schedules.add(executor.schedule(new Task(), 15, TimeUnit.SECONDS));
        schedules.add(executor.schedule(new Task(), 15, TimeUnit.SECONDS));

        for (int i = 0; i < schedules.size(); i++)
            System.out.println("Schedule " + (i + 1) + " done: " + schedules.get(i).isDone());

        Thread.sleep(20 * 1000);

        for (int i = 0; i < schedules.size(); i++)
            System.out.println("Schedule " + (i + 1) + " done: " + schedules.get(i).isDone());

        executor.shutdown();
    }

    private static class Task implements Runnable {
        @Override public void run() {
            try {
                Thread.sleep(1000);
                System.out.println(Thread.currentThread().getName() + " " + new DateTime().toString());
            }
            catch (InterruptedException e) { e.printStackTrace(); }
        }
    }

}
```

Output:
```
Schedule 1 done: false
Schedule 2 done: false
Schedule 3 done: false
Schedule 4 done: false
Schedule 5 done: false
Schedule 6 done: false
Schedule 7 done: false
Schedule 8 done: false
Schedule 9 done: false
pool-1-thread-1 2019-10-08T10:44:41.243-03:00
pool-1-thread-1 2019-10-08T10:44:42.290-03:00
pool-1-thread-1 2019-10-08T10:44:43.290-03:00
pool-1-thread-1 2019-10-08T10:44:46.230-03:00
pool-1-thread-1 2019-10-08T10:44:47.230-03:00
pool-1-thread-1 2019-10-08T10:44:48.231-03:00
pool-1-thread-1 2019-10-08T10:44:51.230-03:00
pool-1-thread-1 2019-10-08T10:44:52.231-03:00
pool-1-thread-1 2019-10-08T10:44:53.231-03:00
Schedule 1 done: true
Schedule 2 done: true
Schedule 3 done: true
Schedule 4 done: true
Schedule 5 done: true
Schedule 6 done: true
Schedule 7 done: true
Schedule 8 done: true
Schedule 9 done: true
```

## References

 - [https://www.callicoder.com/java-executor-service-and-thread-pool-tutorial/](https://www.callicoder.com/java-executor-service-and-thread-pool-tutorial/)
 - [http://tutorials.jenkov.com/java-util-concurrent/executorservice.html](http://tutorials.jenkov.com/java-util-concurrent/executorservice.html)
- [https://www.baeldung.com/java-executor-service-tutorial](https://www.baeldung.com/java-executor-service-tutorial)
