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





## Интерфейсы