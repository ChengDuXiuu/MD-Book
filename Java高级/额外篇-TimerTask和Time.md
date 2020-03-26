### Timer和TimerTask

　　Timer是jdk中提供的一个定时器工具，<code><font size=4 color=ff00ff>使用的时候会在主线程之外起一个单独的线程执行指定的计划任务，可以指定执行一次或者反复执行多次。</font></code>

　　TimerTask是一个实现了Runnable接口的抽象类，代表一个可以被Timer执行的任务。

```java
import java.util.Timer;
import java.util.TimerTask;

public class TimeClass {
    private Log log= LogFactory.get();
    public class ScheduleClass{
        Timer time;
        public ScheduleClass(int second){
            time=new Timer();
            time.schedule(new TimerTask() {
                @Override
                public void run() {
                    log.debug("执行定时任务方法...");
                    time.cancel();
                }
            }, second*1000);
        }
    }

    public static void main(String[] args) {
        System.out.println("About to schedule task.");
        TimeClass timeClass=new TimeClass();
        TimeClass.ScheduleClass scheduleClass=timeClass.new ScheduleClass(3);
        System.out.println("Task scheduled.");
    }
}
```
>About to schedule task.
DEBUG [main] - Use [Slf4j] Logger As Default.
Task scheduled.
DEBUG [Timer-0] - 执行定时任务方法...

### 如何终止Timer线程

<font color=ff0f00>默认情况下，创建的timer线程会一直执行</font>，主要有下面四种方式来终止timer线程：

1. 调用timer的cancle方法

2. 把timer线程设置成daemon线程，（new Timer(true)创建daemon线程），在jvm里，如果所有用户线程结束，那么守护线程也会被终止，不过这种方法一般不用。

3. 当所有任务执行结束后，删除对应timer对象的引用，线程也会被终止。

4. 调用System.exit方法终止程序


### Timer常用方法

第一种方法：指定任务task在指定时间time执行
```java
schedule(TimerTask task, Date time)
```
```java
public static void timer1() {
Calendar calendar = Calendar.getInstance();
calendar.set(Calendar.HOUR_OF_DAY, 00); // 控制时
calendar.set(Calendar.MINUTE, 00); // 控制分
calendar.set(Calendar.SECOND, 0); // 控制秒
Date time = calendar.getTime(); // 得出执行任务的时间,此处为今天的00：00：00

    Timer timer = new Timer();
    timer.schedule(new TimerTask() {
        public void run() {
            System.out.println(new Date().getTime()+"-------定时任务1--------");
        }
    }, time);
}
```

第二种方法：指定任务task在指定延迟delay后执行
```java
schedule(TimerTask task, long delay)
```
```java
public static void timer2() {
    Timer timer = new Timer();
    timer.schedule(new TimerTask() {
        public void run() {
            System.out.println(new Date().getTime()+"-------定时任务2--------");
        }
    }, 2000);// 指定延迟2000毫秒后执行
}
```

第三种方法：指定任务task在指定时间firstTime执行后，进行重复固定延迟频率peroid的执行

```java
schedule(TimerTask task, Date firstTime,long period)
```
```java
public static void timer3() {
Calendar calendar = Calendar.getInstance();
calendar.set(Calendar.HOUR_OF_DAY, 00); // 控制时
calendar.set(Calendar.MINUTE, 00); // 控制分
calendar.set(Calendar.SECOND, 0); // 控制秒
Date time = calendar.getTime(); // 得出执行任务的时间,此处为今天的00：00：00

    Timer timer = new Timer();
    timer.schedule(new TimerTask() {
        public void run() {
            System.out.println(new Date().getTime()+"-------定时任务3--------");
        }
    }, time, 1000 * 60 * 60 * 24);
}

```

第四种方法：指定任务task 在指定延迟delay 后，进行重复固定延迟频率peroid的执行

```java
schedule(TimerTask task, long delay, long period)
```
```java
public static void timer4() {
    Timer timer = new Timer();
        timer.schedule(new TimerTask() {
        public void run() {
            System.out.println(new Date().getTime()+"-------定时任务4--------");
        }
    }, 1000, 5000);
}

```
第五种方法：指定任务task在指定时间firstTime执行后，进行重复固定延迟频率peroid的执行


```java
scheduleAtFixedRate(TimerTask task,Date firstTime,long period)
```
```java
public static void timer5() {
Calendar calendar = Calendar.getInstance();
calendar.set(Calendar.HOUR_OF_DAY, 00); // 控制时
calendar.set(Calendar.MINUTE, 00); // 控制分
calendar.set(Calendar.SECOND, 0); // 控制秒
Date time = calendar.getTime(); // 得出执行任务的时间,此处为今天的00：00：00

    Timer timer = new Timer();
    timer.scheduleAtFixedRate(new TimerTask() {
        public void run() {
            System.out.println(new Date().getTime()+"-------定时任务5--------");
        }
    }, time, 1000 * 60 * 60 * 24);
}

```
第六种方法：指定任务task 在指定延迟delay 后，进行重复固定延迟频率peroid的执行

```java
scheduleAtFixedRate(TimerTask task, long delay, long period)
```
```java
public static void timer6() {
    Timer timer = new Timer();
        timer.scheduleAtFixedRate(new TimerTask() {
            public void run() {
                System.out.println(new Date().getTime()+"-------定时任务6--------");
            }
        }, 1000, 2000);
    }
}
```

### 注意点

    每一个Timer仅对应唯一一个线程。

    Timer不保证任务执行的十分精确。

    Timer类的线程安全的。

    schedule方法：“fixed-delay”；如果第一次执行时间被delay了，随后的执行时间按 照 上一次 实际执行完成的时间点 进行计算

    scheduleAtFixedRate方法：“fixed-rate”；如果第一次执行时间被delay了，随后的执行时间按照 上一次开始的 时间点 进行计算，并且为了”catch up”会多次执行任务,TimerTask中的执行体需要考虑同步
