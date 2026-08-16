Модуль `dataclasses` предоставляет декоратор `@dataclass`, который позволяет создавать data-классы. Датаклассы позволяют значительно сократить шаблонный код классов. Как правило, такие классы предназначены для хранения некоторого состояния, некоторых данных и когда не требуется какое-то поведение в виде функций.

Пример с классом:
```python
class InventoryItem:
    def __init__(self, name: str, unit_price: float, quantity_on_hand: int = 0):
        self.name = name
        self.unit_price = unit_price
        self.quantity_on_hand = quantity_on_hand
    
    def total_cost(self) -> float:
        return self.unit_price * self.quantity_on_hand
    
    def __repr__(self):
        return f"InventoryItem(name={self.name!r}, unit_price={self.unit_price!r}, quantity_on_hand={self.quantity_on_hand!r})"
    
    def __eq__(self, other):
        if not isinstance(other, InventoryItem):
            return NotImplemented
        return (self.name, self.unit_price, self.quantity_on_hand) == (other.name, other.unit_price, other.quantity_on_hand)
```

Пример с датаклассом:
```python
from dataclasses import dataclass

@dataclass
class InventoryItem:
    name: str
    unit_price: float
    quantity_on_hand: int = 0  # значение по умолчанию

    def total_cost(self) -> float:  # дополнительная функциональность
        return self.unit_price * self.quantity_on_hand
```


В примере кроме конструктора, также переопределяются методы `__repr__()` и `__eq__()`.

Полная сигнатура `@dataclass`:

```python
@dataclass(*, init=True, repr=True, eq=True, order=False, unsafe_hash=False, frozen=False, match_args=True, kw_only=False, slots=False, weakref_slot=False)
```

Параметры:
- `init` - создание конструктора
- `repr` - переопределение строкового представления объета
- `eq` - переопределение равенства
- `order` - переопределяет методов сравнения (`__lt__()`, `__le__()`, `__gt__()` и `__ge__()`). Они сравнивают набор полей объекта по порядку. Оба экземпляра в сравнении должны быть одинакового типа. Если `order=True and eq=False`, возникает ошибка ValueError
- `unsafe_hash` - переопределение `__hash__()`
- `frozen` - делает объект неизменяемым - изменение полей выбросит исключение
- `match_args`
- `kw_only`
- `slots`
- `weakref_slot`
