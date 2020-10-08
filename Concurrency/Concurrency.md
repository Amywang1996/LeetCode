   * [1114. 按序打印](#1114-按序打印)

# 1114. 按序打印

<B>题目</B>   

我们提供了一个类：

public class Foo {
  public void first() { print("first"); }
  public void second() { print("second"); }
  public void third() { print("third"); }
}

三个不同的线程将会共用一个 Foo 实例。

线程 A 将会调用 first() 方法
线程 B 将会调用 second() 方法
线程 C 将会调用 third() 方法

请设计修改程序，以确保 `second()` 方法在 `first()` 方法之后被执行，`third()` 方法在 `second()` 方法之后被执行。

**示例 1:**

输入: [1,2,3]
输出: "firstsecondthird"
解释: 
有三个线程会被异步启动。
输入 [1,2,3] 表示线程 A 将会调用 first() 方法，线程 B 将会调用 second() 方法，线程 C 将会调用 third() 方法。
正确的输出是 "firstsecondthird"。

**示例 2:**

输入: [1,3,2]
输出: "firstsecondthird"
解释: 
输入 [1,3,2] 表示线程 A 将会调用 first() 方法，线程 B 将会调用 third() 方法，线程 C 将会调用 second() 方法。
正确的输出是 "firstsecondthird"。

<B>分析：</B> 

方法一：这道题目的目的是:first()、second()、third()这三个方法按序执行，即second()方法调用前必须掉了first()方法，third()方法执行前必须调了second()方法。这里想到了ReentrantLock的Condition的使用，使用condition的signal()方法达到精确唤醒的目的。first()方法执行完后唤醒调用second()方法的线程，执行second方法时若发现first()没有执行则调用await()方法进行阻塞；second()方法执行完后唤醒执行Third()方法的线程，执行third()方法时若发现second()没有执行则调用await()方法进行阻塞。

方法二：定义两个int型原子类firstTaskComplete、secondTaskComplete,firstTaskComplete在first()方法执行后加一，secondTaskComplete在second()方法执行后加一，执行second()方法时判断firstTaskComplete是否等于1，如果等于1说明first()方法已经执行了否则自旋直到first(方法)执行完后再执行，执行third()方法时判断secondTaskComplete是否等于1，如果等于1说明second()方法已经执行了否则自旋直到second()方法执行完后再执行。

**Java代码(方法一)：**

```java
class Foo {
    private int num =1;
    private Lock lock=new ReentrantLock();
    private Condition c1 = lock.newCondition();
    private Condition c2 = lock.newCondition();
    private Condition c3 = lock.newCondition();
    public Foo() {

    }
    public void first(Runnable printFirst) throws InterruptedException {
        lock.lock();
        while (num!=1){
            c1.await();
        }
        // printFirst.run() outputs "first". Do not change or remove this line.
        printFirst.run();
        num=2;
        c2.signal();
        lock.unlock();
    }
    public void second(Runnable printSecond) throws InterruptedException {
        lock.lock();
       while (num!=2){
           c2.await();
       }
        // printSecond.run() outputs "second". Do not change or remove this line.
        printSecond.run();
       num=3;
       c3.signal();
        lock.unlock();
    }
    public void third(Runnable printThird) throws InterruptedException {
        lock.lock();
        while (num!=3){
            c3.await();
        }
        // printThird.run() outputs "third". Do not change or remove this line.
        printThird.run();
        lock.unlock();
    }
}
```

**Java代码(方法二)：**

```java
class Foo {
    private AtomicInteger firstTaskComplete = new AtomicInteger(0); 
    private AtomicInteger secondTaskComplete = new AtomicInteger(0); 
    public Foo() {
    }
    public void first(Runnable printFirst) throws InterruptedException {
        // printFirst.run() outputs "first". Do not change or remove this line.
        printFirst.run();
        firstTaskComplete.incrementAndGet();
    }

    public void second(Runnable printSecond) throws InterruptedException {
       while (firstTaskComplete.get()!=1){
       }
        // printSecond.run() outputs "second". Do not change or remove this line.
      printSecond.run();
      secondTaskComplete.incrementAndGet();
    }

    public void third(Runnable printThird) throws InterruptedException {
       while (secondTaskComplete.get()!=1){
       }
        // printThird.run() outputs "third". Do not change or remove this line.
        printThird.run();
    }
}
```

