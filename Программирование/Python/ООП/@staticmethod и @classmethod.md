## @staticmethod
Статический метод - это метод, который не привязан к состоянию экземпляра или класса, но логически связан с работой класса. Для создания статического метода используется декоратор `@staticmethod`.

Статический метод не получает неявный аргумент экземпляра класса.

Статический метод может быть вызван либо на классе (`C.f()`), либо на экземпляре (`C().f()`).

```python
class C:
    @staticmethod
    def f(arg1, arg2, argN): ...
```

Пример:
```python
class MathUtils:
    @staticmethod
	def is_even(number):
        return number % 2 == 0

>>> MathUtils.is_even(4)
True

>>> obj = MathUtils().is_even(10)  #  можно вызвать через экземпляр
True
```


## @classmethod
Преобразует метод в метод класса. Метод класса получает class в качестве неявного первого аргумента (cls), что позволяет ему получать и изменять данные уровня класса. Чтобы объявить метод класса, используйте эту идиому:

```python
class C:
    @classmethod
    def f(cls, arg1, arg2): ...
```

Где classmethod может использоваться:
- **Альтернативные конструкторы:** Создание новых объектов класса из других типов данных (например, из строки или словаря).
- **Фабричные методы**
- **Изменение или чтение с состоянием класса**
- Полиморфные операции на уровне класса, где нужен доступ к `cls`, а не к конкретному экземпляру.

Пример использования:
```python
from dataclasses import dataclass

@dataclass
class User:
    id: int
    email: str

    @classmethod
    def from_dict(cls, d: dict) -> "User":
        if "id" not in d or d["id"] is None:
            raise ValueError("id is required")
        if "email" not in d or not isinstance(d["email"], str) or not d["email"]:
            raise ValueError("email is required")

        return cls(id=int(d["id"]), email=d["email"])
```