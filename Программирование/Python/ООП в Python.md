Класс - шаблон для создания объектов определенного типа, описывающий их структуру (набор полей и их начальное состояние) и алгоритмы (функции и методы) для работы с этими объектами.

Объект - конкретный экземпляр класса, представляющий собою сущность с уникальным состоянием и поведением.

В Python все классы являются наследниками базового класса `object`.

## Принципы ООП

### Наследование
Механизм, позволяющий одному классу перенять свойства и методы другого класса.

Пример:
```python
class Employee:
    def __init__(self, name: str, base_salary: float):
        self.name = name
        self.base_salary = base_salary
    
    def calculate_salary(self) -> float:
        return self.base_salary
    
    def name(self):
        return self._name
    
    @name.setter
    def name(self, value):
        self._name = value
    
    @property
    def base_salary(self):
        return self._base_salary
    
    @base_salary.setter
    def base_salary(self, value):
        self._base_salary = value


class Manager(Employee):
    def __init__(self, name: str, base_salary: float, bonus: float):
        super().__init__(name, base_salary)
        self.bonus = bonus
    
    def calculate_salary(self) -> float:
        return self.base_salary + self.bonus
    
    @property
    def bonus(self):
        return self._bonus
    
    @bonus.setter
    def bonus(self, value):
        self._bonus = value
```


### Полиморфизм
Означает способность объектов представлять себя как другой тип в зависимости от контекста.

 В Python существует только динамический полиморфизм.
#### Динамический полиморфизм
Решение о том, какой метод вызвать, принимается на этапе исполнения.

Пример:
```java
class Mammal:  
    def speak(self):  
        print("Mammal speaks something")  
  
class Human(Mammal):  
    def speak(self):  
        print(f"Human speaks \"Babuleh\"")  
  
mammal = Mammal()  
mammal.speak()  
  
human = Human()  
human.speak()
```

==На этапе компиляции вызываемый метод рассматривается как относящийся к типу ссылки. Но во время выполнения будет вызываться метод объекта, на который указывает ссылка.==

### Абстракция
Выделение общих свойств и методов объектов, и опускание деталей реализации.

Пример:
```java
from abc import ABC, abstractmethod  
  
class Shape(ABC):  
  
    @abstractmethod  
    def get_area(self) -> float:  
        pass  
  
    @abstractmethod  
    def get_perimeter(self) -> float:  
        pass  
  
    def print_info(self) -> None:  
        print(f"Area: {self.get_area()}. Perimeter: {self.get_perimeter()}")  
  
  
class Rectangle(Shape):  
    def __init__(self, width: float, height: float):  
        self.width = width  
        self.height = height  
  
    @property  
    def width(self) -> float:  
        return self._width  
  
    @width.setter  
    def width(self, value: float) -> None:  
        self._width = value  
  
    @property  
    def height(self) -> float:  
        return self._height  
  
    @height.setter  
    def height(self, value: float) -> None:  
        self._height = value  
  
    def get_area(self) -> float:  
        return self.width * self.height  
  
    def get_perimeter(self) -> float:  
        return self.width * 2 + self.height * 2  
  
  
if __name__ == "__main__":  
    rect = Rectangle(5, 3)  
    rect.print_info()  
  
    rect.width, rect.height = 10, 4
    rect.print_info()
```

### Инкапсуляция 
Объединение свойств и методов внутри единой оболочки (класса, пакета и т.д.), а также ограничение прямого доступа к ним извне. Защищает данные от некорректного использования и позволяет скрывать детали реализации, предоставляя лишь необходимый публичный интерфейс.

Пример:
```java
class BankAccount:  
    def __init__(self, account_number: str, owner_name: str, initial_balance: float):  
        if initial_balance < 0:  
            raise ValueError("Начальный баланс не может быть отрицательным")  
  
        self.__account_number = account_number  
        self.__balance = initial_balance  
        self.__owner_name = owner_name  
  
    @property  
    def account_number(self) -> str:  
        return self.__account_number  
  
    @property  
    def balance(self) -> float:  
        return self.__balance  
  
    @property  
    def owner_name(self) -> str:  
        return self.__owner_name  
  
    @owner_name.setter  
    def owner_name(self, value: str):  
        self.__owner_name = value  
  
    def deposit(self, amount: float):  
        if amount <= 0:  
            raise ValueError("Сумма пополнения должна быть положительной")  
        self.__balance += amount  
  
    def withdraw(self, amount: float):  
        if amount <= 0:  
            raise ValueError("Сумма снятия должна быть положительной")  
        if amount > self.__balance:  
            raise ValueError("Недостаточно средств")  
        self.__balance -= amount
``` 


## Интерфейсы


## Модификаторы доступа
В Python модификаторы доступа 

В Python используются следующие модификаторы доступа:
1. **public**. Обоначение: `field`. Поля и методы видны в любом месте программы.
   В исходном файле может быть только один класс с модификатором **public**, но количество классов с другими модификаторами может быть любым. Если в файле есть класс с данным модификатором, то имя файла должно совпадать с именем этого класса.
2. **private**. Обоначение: `__field`. Доступен только из кода в том же классе.
3. **protected**. Обоначение: `_field`. Класс или компонент класса доступен из любого места в текущем классе, пакете или в производных классах, даже если они находятся в разных пакетах.