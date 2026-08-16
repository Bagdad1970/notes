Декоратор метода `@property` превращает метод в поле объекта.
Также `@property` позволяет добавлять метод setter, который будет отвечать за изменение значения переменной и, так как это тоже метод, позволяет включить логику с проверкой или динамическим вычислением значения.

Пример:
```python
class Person:
    def __init__(self, name, age):
        self._name = name
        self._age = age
    
    @property
    def age(self):  # getter
        print("Получение возраста...")
        return self._age
    
    @age.setter
    def age(self, value):  # setter
        if value < 0:
            raise ValueError("Возраст не может быть отрицательным!")
        if value > 150:
            raise ValueError("Слишком большой возраст!")
        self._age = value


person = Person("Алексей", 25)

print(person.age)  # вызов геттера
person.age = 30    # вызов сеттера
person.age = -5  # выбросит ошибку ValueError
```