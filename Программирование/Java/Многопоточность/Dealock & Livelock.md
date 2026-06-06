
## Deadlock
Взаимная блокировка возникает, когда два или более потоков бесконечно ожидают снятия блокировки или освобождения ресурса, удерживаемого другим потоком. В результате работа приложения может быть приостановлена или прервана, поскольку потоки, попавшие в тупик, не могут выполнять код дальше. 

```java
public class DeadlockExample {

    private Lock lock1 = new ReentrantLock(true);
    private Lock lock2 = new ReentrantLock(true);

    public static void main(String[] args) {
        DeadlockExample deadlock = new DeadlockExample();
        new Thread(deadlock::operation1, "T1").start();
        new Thread(deadlock::operation2, "T2").start();
    }

    public void operation1() {
        lock1.lock();
        print("lock1 acquired, waiting to acquire lock2.");
        sleep(50);

        lock2.lock();
        print("lock2 acquired");

        print("executing first operation.");

        lock2.unlock();
        lock1.unlock();
    }

    public void operation2() {
        lock2.lock();
        print("lock2 acquired, waiting to acquire lock1.");
        sleep(50);

        lock1.lock();
        print("lock1 acquired");

        print("executing second operation.");

        lock1.unlock();
        lock2.unlock();
    }
}
```


Вывод:
```
Thread T1: lock1 acquired, waiting to acquire lock2.
Thread T2: lock2 acquired, waiting to acquire lock1.
```

Для избежания взаимоблокировок можно:
- Следует избегать необходимости получения нескольких блокировок для потока. Но если потоку действительно требуется несколько блокировок, каждый поток должен получать блокировки в одинаковом порядке, чтобы избежать какой-либо циклической зависимости при получении блокировок.
- Использовать попытки блокировки с таймаутом (`tryLock`), чтобы гарантировать, что поток не будет блокироваться бесконечно, если ему не удастся получить блокировку.

## Livelock

Живая блокировка - ситуация,когда два или более потока продолжают передавать данные друг другу, в отличие от deadlock. В результате потоки не могут выполнять свои конкретные задачи.

Отличный пример livelock — система обмена сообщениями, в которой при возникновении исключения потребитель сообщений откатывает транзакцию и помещает сообщение обратно в начало очереди. Затем это же сообщение снова и снова считывается из очереди, что приводит к очередному исключению и помещению сообщения обратно в очередь. Потребитель никогда не получит из очереди ни одного другого сообщения.