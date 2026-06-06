Класс - шаблон для создания объектов определенного типа, описывающий их структуру (набор полей и их начальное состояние) и алгоритмы (функции и методы) для работы с этими объектами.

Объект - конкретный экземпляр класса, представляющий собою сущность с уникальным состоянием и поведением.

## Принципы ООП

### Наследование
Механизм, позволяющий одному классу перенять свойства и методы другого класса.

Пример:
```java
@AllArgsConstructor
@Getter
@Setter
class Employee {  
    private String name;  
    private double baseSalary;  
  
    public double calculateSalary() {  
        return baseSalary;  
    }  
}  
  
@AllArgsConstructor
@Getter
@Setter
class Manager extends Employee {  
    private double bonus;  
  
    public Manager(String name, double baseSalary, double bonus) {  
        super(name, baseSalary);  
        this.bonus = bonus;  
    }
  
    @Override  
    public double calculateSalary() {  
        return getBaseSalary() + getBonus();  
    }   
}
```

### Полиморфизм
Означает способность объектов представлять себя как другой тип в зависимости от контекста.

Полиморфизм делится на статический и динамический.

#### Статический полиморфизм
Также известен как "Перегрузка методов" (overloading).
Означает представление методов в классе с одинаковым именем, но разными аргументами, в то время как возвращаемый тип, выбрасываемые исключения и модификаторы доступа могут отличаться, а может и не отличаться.
Решение о том, какой метод вызвать, принимается на этапе компиляции.

Пример:
```java
int add(int a, int b) {
	return a + b;
}

double add(double a, double b) {
	return a + b;
}
```

#### Динамический полиморфизм
Решение о том, какой метод вызвать, принимается на этапе исполнения.

При вызове переопределенного метода JVM динамически находит и вызывает ту версию метода, которая определена в подклассе. Это называется динамической диспетчеризацией методов (**dynamic method lookup**). Установка точной версии метода называется динамическим связыванием (**dynamic binding**)

Для упрощения поиска виртуальная машина заранее вычисляет таблицу методов для каждого класса (**method table**). В таблице методов перечисляются все сигнатуры методов и сами вызываемые методы. Виртуальная машина может построить таблицу методов после загрузки класса, объединив методы, найденные в файле класса, с таблицей методов базового класса. При фактическом вызове метода виртуальная машина выполняет поиск в таблице и для наследников находит перегруженную версию метода.

Пример:
```java
class Mammal {
    public void speak() {
        System.out.println("Mammal speaks something");
    }
}

class Human extends Mammal {
	@Override
	public void speak() {
		System.out.println("Hello");
	}
}

...

Mammal anyMammal = new Mammal();
anyMammal.speak();  // Method OverrideExample$Mammal."<init>":()V

Mammal humanMammal = new Human(); // (1)
humanMammal.speak(); // Method OverrideExample$Mammal.speak:()V

Human human = new Human();
human.speak();  // Method OverrideExample$Human.speak:()V
```

На этапе компиляции вызываемый метод рассматривается как относящийся к типу ссылки (1). Но во время выполнения будет вызываться метод объекта, на который указывает ссылка.

### Абстракция
Выделение общих свойств и методов объектов, и опускание деталей реализации.

Пример:
```java
abstract class Shape {  
	abstract double getArea();  
    abstract double getPerimeter();  
  
    void printInfo() {  
        System.out.println("Area: " + getArea() + ". Perimeter: " + getPerimeter());
    }  
}  
  
@AllArgsConstructor
@Getter
@Setter
class Rectangle extends Shape {  
    private double width;  
    private double height;  
  
    @Override  
    double getArea() {  
        return width * height;  
    }  
  
    @Override  
    double getPerimeter() {  
        return width * 2 + height * 2;  
    }  
}
```

### Инкапсуляция 
Объединение свойств и методов внутри единой оболочки (класса, пакета и т.д.), а также ограничение прямого доступа к ним извне. Защищает данные от некорректного использования и позволяет скрывать детали реализации, предоставляя лишь необходимый публичный интерфейс.
Пример:
```java
class BankAccount {
	@Getter
    private final String accountNumber;  // можно получить, но не изменить
    
    @Getter
    private double balance;  // можно получить, но изменить только через withdraw() и deposit()
    
    @Getter
    @Setter
    private String ownerName;
    
    public BankAccount(String accountNumber, String ownerName, double initialBalance) {
        if (initialBalance < 0) {
            throw new IllegalArgumentException("Начальный баланс не может быть отрицательным");
        }
        this.accountNumber = accountNumber;
        this.ownerName = ownerName;
        this.balance = initialBalance;
    }

    public void deposit(double amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException("Сумма пополнения должна быть положительной");
        }
        balance += amount;
    }
    
    public void withdraw(double amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException("Сумма снятия должна быть положительной");
        }
        if (amount > balance) {
            throw new IllegalArgumentException("Недостаточно средств");
        }
        balance -= amount;
    }
}
``` 


## Интерфейсы
Интерфейс в Java - это контракт, который описывает поведение (набор методов), но не состояние как абстрактные классы. Это обеспечивает слабую связанность и позволяет обойти ограничение на множественное наследование, так как класс может реализовать сколько угодно интерфейсов.

Методы могут быть:
- статическими;
- иметь реализацию по умолчанию;
- приватными.

Все переменные, описанные в интерфейсе, являются public static final (константами). Интерфейсы могут наследоваться от других интерфейсов через `extends`.

Пример использования интерфейсов:
```java
interface Vehicle {  
    int MAX_SPEED = 120;  
    String CATEGORY = "Transport";  
  
    void start();
  
    default void showInfo() {  
        System.out.println(getBasicInfo());  
        System.out.println("Max speed: " + MAX_SPEED);
    }  
  
    private String getBasicInfo() {  
        return "Vehicle category: " + CATEGORY;  
    }  
  
    static void printRules() {  
        System.out.println("Traffic rules...");  
    }  
}  
  
interface ElectricVehicle extends Vehicle {  
    int BATTERY_CAPACITY = 100;  
  
    void charge();  
  
    @Override  
    default void showInfo() {  
        Vehicle.super.showInfo();  
        System.out.println("Battery capacity: " + BATTERY_CAPACITY + " kWh");  
    }  
}  
  
class TeslaCar implements ElectricVehicle {  
    private String model;  
  
    public TeslaCar(String model) {  
        this.model = model;  
    }  
  
    @Override  
    public void start() {  
        System.out.println(model + " starts silently");  
    }  
  
    @Override  
    public void charge() {  
        System.out.println(model + " charging...");  
    }  
  
    @Override  
    public void showInfo() {  
        ElectricVehicle.super.showInfo();  
        System.out.println("Model: " + model);  
    }  
}  

...

Vehicle.printRules();  

TeslaCar tesla = new TeslaCar("Model 3");  
tesla.start();  
tesla.charge();  
tesla.showInfo();  

System.out.println("Max speed (interface): " + Vehicle.MAX_SPEED);  
System.out.println("Battery capacity (subinterface): " + ElectricVehicle.BATTERY_CAPACITY);  
```

## Модификаторы доступа
Модификаторы доступа управляют видимостью классов, методов и полей, тем самым определяя, как к компонентам класса можно получить доступ из других частей программы.

В Java используются следующие модификаторы доступа:
1. **public**. Поля и методы видны в любом месте программы.
   В исходном файле может быть только один класс с модификатором **public**, но количество классов с другими модификаторами может быть любым. Если в файле есть класс с данным модификатором, то имя файла должно совпадать с именем этого класса.
2. **private**. Доступен только из кода в том же классе.
3. **protected**. Класс или компонент класса доступен из любого места в текущем классе, пакете или в производных классах, даже если они находятся в разных пакетах.
4. **package-private** (отсутствие модификатора). Поля или методы видны всем классам в текущем пакете.


## Литература
1. https://metanit.com/java/tutorial/3.24.php