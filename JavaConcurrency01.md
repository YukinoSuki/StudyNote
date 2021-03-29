## 创建线程

最基本的两种创建线程方式是，**继承Thread类并重载run()方法** 和 **实现Runnable接口并作为参数构造一个Thread对象**。一般情况下，第二种方式是最值得我们推荐使用的，因为第一种方式不够灵活，且开销更大。


```Java 

public class WelcomeApp {

    public static void main(String[] args) {
        System.out.printf("I am the thread: %s.%n", Thread.currentThread().getName());

        WelcomeThread thread = new WelcomeThread();
        thread.start();
        // 这里是以匿名内部类及Lambda表达式的方式实现了Runnable接口
        Thread runnable = new Thread(() -> {
            System.out.printf("I am the thread: %s.%n", Thread.currentThread().getName());
        });
        runnable.start();
    }
    // 继承Thread并重载复写run方法
    static class WelcomeThread extends Thread{
        @Override
        public void run() {
            super.run();
            System.out.printf("I am the thread: %s.%n", Thread.currentThread().getName());
            while (true){
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("It is a Thread loop");
            }
        }
    }
}

```
