`Stream<T>` - последовательность элементов, поддерживающих последовательные и параллельные агрегатные операции.
#### Свойства потоков:
1. **Декларативность**: Потоки в Java позволяют описывать что нужно сделать с данными, а не как это должно быть реализовано. Вместо явного использования циклов и условий, разработчик может задать набор операций, которые Java выполнит под капотом. Такой подход повышает читаемость и простоту кода, поскольку скрывает сложные детали реализации.
2. **Ленивость**: Операции в потоке не выполняются сразу — они “откладываются” до тех пор, пока не будет вызвана терминальная операция. Это позволяет Java оптимизировать выполнение операций, обрабатывая данные по мере необходимости и избегая лишней работы.
3. **Одноразовость**: Потоки можно использовать только один раз. Как только была вызвана терминальная операция, поток считается исчерпанным и больше не может быть использован. Если необходимо выполнить другую операцию над теми же данными, нужно создать новый поток.
4. **Параллельность**: Потоки по умолчанию выполняются последовательно, но их можно легко распараллелить, используя метод `parallelStream()` или `parallel()`. Это позволяет значительно ускорить обработку больших наборов данных на многоядерных системах. Параллельные потоки автоматически разделяют данные на части и распределяют задачи между несколькими потоками, обеспечивая более эффективное использование ресурсов процессора.

#### Компоненты Stream API:
1. Источник - место, откуда поступают данные для обработки. Источником может быть массив, строка, коллекция, файл, генератор или другой источник данных
2. Операция - преобразование и/или манипуляция над данными в потоке. Описываются декларативно.
3. Поток - последовательность элементов, которая подлежит обработке. Может обрабатываться как в однопоточном, так и в многопоточном режиме
4. Пайплайн - цепочка промежуточных операций (преобразований), которая применяется к данным
5. Терминал - завершающая операция, которая возвращает результат или выполняет побочный эффект. После терминала поток закрывается.

**Замечание**: Промежуточные операции не должны изменять сам источник данных и не должны зависеть от состояния, которое может измениться во время выполнения потока.

Данные потока обрабатываются с помощью цепочек промежуточных и терминальных операций, но важная особенность заключается в том, что ==каждый элемент потока проходит через весь пайплайн поэтапно==. Операции не применяются к коллекции целиком, а работают над каждым элементом поочередно.

Операции различают состоянием:
- Без состояния. Не требуют информации о предыдущих или последующих элементах.
  Пример: map(), filter()
- С состоянием. Требуют информацию о других элементах потока. Такие операции должны обработать весь поток или часть для возврата результата.
  Пример: sort(), limit()


==Методы==

### Collector
Collector - объект, реализующий методы, которые работают вместе для накопления записей в изменяемом результирующем контейнере и, при необходимости, выполняют окончательное преобразование результата.

Интерфейс **Collector<T,A,R>**
![[Pasted image 20260216183954.png]]

**Методы интерфейса:**
- Supplier (supplier) - создает и возвращает итоговый mutable контейнер
- BiConsumer (accumulator) - включает новый элемент в контейнер
- BinaryOperator (combiner) - описывает порядок объединения данных между двумя потоками
- Function (finisher) - описывает порядок преобразования контейнера в возвращаемый тип
- Set<Collector.Characteristics> (characteristics) - предоставляет метаданные для оптимизации работы с потоками.

Последовательная реализация Collector создает единственный результирующий контейнер с использованием supplier() и вызывает accumulator() для каждого входного элемента.
Параллельная реализация разделяет входные данные, тем самым создавая результирующий контейнер для каждой секции. В каждой секции после обработки будет свой промежуточный контейнер, затем эти контейнеры объединяются в результирующий контейнер посредством combiner().

### Реализация Collector:
```java
@Data
@AllArgsConstructor
@NoArgsConstructor
class Order {  
    private Long id;
    private String customerEmail;
    private double amount;
}

@Data
@AllArgsConstructor
@NoArgsConstructor
class CustomerInfo {  
    private int orderCount;  
    private double totalSpent;  
    private Order mostExpensiveOrder;  
}

class CustomerInfoCollector implements Collector<Order, CustomerInfo, CustomerInfo> {  
  
    @Override  
    public Supplier<CustomerInfo> supplier() {  
        return CustomerInfo::new;  
    }  
  
    @Override  
    public BiConsumer<CustomerInfo, Order> accumulator() {  
        return (customerInfo, order) -> {  
            customerInfo.setOrderCount(customerInfo.getOrderCount() + 1);  
            customerInfo.setTotalSpent(customerInfo.getTotalSpent() + order.getAmount());  
  
            Order currentMaxOrder = customerInfo.getMostExpensiveOrder();  
            if (currentMaxOrder == null || order.getAmount() > currentMaxOrder.getAmount()) {  
                customerInfo.setMostExpensiveOrder(order);  
            }  
        };  
    }  
  
    @Override  
    public BinaryOperator<CustomerInfo> combiner() {  
        return (c1, c2) -> {  
            CustomerInfo combined = new CustomerInfo();  
            combined.setOrderCount(c1.getOrderCount() + c2.getOrderCount());  
            combined.setTotalSpent(c1.getTotalSpent() + c2.getTotalSpent());  
  
            Order c1MostExpensive = c1.getMostExpensiveOrder();  
            Order c2MostExpensive = c2.getMostExpensiveOrder();  
            Order mostExpensive;  
            if (c1MostExpensive == null) {  
                mostExpensive = c2MostExpensive;  
            }  
            else if (c2MostExpensive == null) {  
                mostExpensive = c1MostExpensive;  
            }  
            else {  
                mostExpensive = c1MostExpensive.getAmount() > c2MostExpensive.getAmount()  
                        ? c1MostExpensive : c2MostExpensive;  
            }  
            combined.setMostExpensiveOrder(mostExpensive);  
  
            return combined;  
        };  
    }  
  
    @Override  
    public Function<CustomerInfo, CustomerInfo> finisher() {  
        return Function.identity();  
    }  
  
    @Override  
    public Set<Characteristics> characteristics() {  
        return Set.of(Characteristics.IDENTITY_FINISH);  
    }  
}

public static CustomerInfo buildLoyaltyReport(List<Order> orders) {  
    CustomerInfoCollector customerInfoCollector = new CustomerInfoCollector();  
  
    return orders.stream()  
            .collect(customerInfoCollector);  // используем кастомный collector
}
```


## Литература:
1. https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html
2. https://struchkov.dev/blog/ru/java-stream-api/
3. https://blog.frankel.ch/custom-collectors-java-8/