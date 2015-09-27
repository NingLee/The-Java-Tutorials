##线程对象

每个线程都和一个Thread类的实例关联。使用Thread对象来创建一个并发应用程序有两种基本方式。

 - 直接控制线程的创建和管理，当程序需要一个异步Task的时候，直接创建一个Thread对象。
 - 从应用程序的其他部分抽象线程管理，把程序的任务传递给一个执行器。
这部分展示的是Thread对象的使用。执行器的用法在高级并发对象部分讨论。

### 定义并启动一个线程
应用程序创建了一个Therad的实例，必须提供线程中要执行的代码。有两种方法：

 - 实现Runnable接口。Runnable接口定义了一个run方法，包含了线程要执行的代码。Runnable对象可以被传给Thread的构造函数，像下面的HelloRunnable的例子:

```java
public class HelloRunnable implements Runnable {

    public void run() {
        System.out.println("Hello from a thread!");
    }

    public static void main(String args[]) {
        (new Thread(new HelloRunnable())).start();
    }

}
```

 - 继承Thread类。Thread类自己实现了Runnable方法，尽管他的run方法啥也没做。应用程序可以继承Thread类，提供自己的run方法，参考下面的HelloThread例子：
```java
public class HelloThread extends Thread {

    public void run() {
        System.out.println("Hello from a thread!");
    }

    public static void main(String args[]) {
        (new HelloThread()).start();
    }

}
```
两个例子都调用了Thread.start来启动一个新的线程。

你更喜欢哪种方式？第一种方式，实现了Runnable对象，更通用，因为Runnable对象可以继承Thread以外的类。第二种方式在简单的程序中更容易使用，但是有个限制就是必须继承Thread类。这个课程聚焦于第一种方法，将Runnable任务和执行Task的Thread对象分开。这种方法不仅更灵活，而且更适用于后面要讲的高级线程管理API。

Thread类定义了用于线程管理的有用的方法。其中包括静态方法，提供调用该方法的线程状态的信息；另外一些方法是从其他线程调用的，包括管理线程和Thread对象。下面一段我们会介绍这些部分方法。

###使用Sleep方法暂停执行线程
Thread的sleep方法会导致当前线程暂停执行一段时间。这使得处理器时间对该程序的其他线程或者运行在一个系统中的其他程序可用，这个是很有意义的。sleep方法也用来控制节奏，就像在下面的例子里，以及在SimpleThread例子中等待另一个有时间需求的线程。

sleep方法有2个重载的版本：一个指定睡眠时间为毫秒，一个指定为微秒。然而这些睡眠时间是不保证精确的，因为这受限于底层操作系统的机制。当然，sleep也可以被中断而结束，我们在后面的章节会演示。任何时候，都不能假定调用sleep就会将线程挂起指定的时间。

SleepMessage例子使用sleep来实现每4秒打印消息。
```java
public class SleepMessages {
    public static void main(String args[])
        throws InterruptedException {
        String importantInfo[] = {
            "Mares eat oats",
            "Does eat oats",
            "Little lambs eat ivy",
            "A kid will eat ivy too"
        };

        for (int i = 0;
             i < importantInfo.length;
             i++) {
            //Pause for 4 seconds
            Thread.sleep(4000);
            //Print a message
            System.out.println(importantInfo[i]);
        }
    }
}
```
注意main函数声明了他会抛出InterruptedException。当一个线程sleep过程中被其他线程打断了，当前线程会抛出这个异常。因为这个程序没有定义另一个线程引起这个中断，所以就不用处理InterruptedException了(直接抛出就可以了，不必catch它)。


