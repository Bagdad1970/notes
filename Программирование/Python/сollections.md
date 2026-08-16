Модуль сollections предоставляет специализированные типы данных на основе dict, set, list, tuple.

### Counter
Counter - наследник dict для подсчета хеш-объектов. Представляет словарь, в котором ключами являются объекты, а в качестве значений - их количество.

Основные методы:
1. `elements()` - Возвращает итератор, где каждый элемент повторяется столько раз, сколько указано в значении. Элементы возвращаются в порядке их первого появления. Если значение `count < 1`, то элемент будет проигнорирован.
2. `most_common(n=None)` - возвращает список длины n от наиболее часто встречающихся элементов к наименее часто встречающимся. Список имеет вид `[(key_1, count_1), ..., (key_n, count_n)]`. Если `n == None`, то возвращаются все элементы счетчика. Элементы с одинаковым количеством повторяются в порядке их первого появления.
3. `total()` - возвращает сумму всех значений

Пример подсчета типов событий:
```python
from collections import Counter
from typing import Iterable

def count_event_types(events: Iterable[dict[str, object]]) -> dict[str, int]:
    counts: Counter[str] = Counter()

    for event in events:
        raw_type = event.get("type")
        if raw_type is None:
            continue

        event_type = str(raw_type).strip().lower()
        counts[event_type] += 1

    return dict(counts)  # на границе системы лучше вернуть dict

events = [
    {"type": "Login"},
    {"type": "login"},
    {"type": "Purchase"},
]
```

### defaultdict
`defaultdict` ни чем не отличается от обычного словаря за исключением того, что при добавлении новых значений вызывается функция, которая возвращает значение по умолчанию.

Конструктор имеет вид: `defaultdict[default_factory[, ...]]`.
`default_factory` - тип данных или функция, которая возвращает значение по умолчанию для новых значений.

`dict` при попытке получить несуществующий ключ выбросит `KeyError`. `defaultdict` вместо этого добавит значение функции `default_factory` в словарь и вернет значение.

Пример задачи группировки значений по ключу:
```python
from collections import defaultdict
from typing import Iterable


def group_users_by_region(rows: Iterable[dict]) -> dict[str, list[int]]:
    grouped: defaultdict[str, list[int]] = defaultdict(list)
    
    for row in rows:
        grouped[row["region"]].append(int(row["user_id"]))
        
    return dict(grouped) # на границе системы лучше вернуть dict

rows = [
    {"user_id": 1, "region": "eu"},
    {"user_id": 2, "region": "us"},
    {"user_id": 3, "region": "eu"},
]
```

### namedtuple
В namedtuple каждой позиции в кортеже присваивается имя элемента. Они могут использоваться в качестве обычных кортежей, но, помимо обращения по индексу, добавляют возможность доступа к полям по имени.

Конструктор имеет вид:
`namedtuple(typename, field_names, *, \, rename=False, defaults=None, module=None)`.

Параметры конструктора:
- `typename` - задает имя самого кортежа;
- `field_names` - задает имена элементов кортежа;
- `rename` - bool позволяет автоматически заменить  некорректные и повторяющиеся имена элементов позиционными именами;
- `defaults=None` - описывает значения по умолчанию имен кортежа;
- `module=None` - атрибут `__module__` именованного кортежа.

Результатом создания namedtuple будет новый подкласс кортежа с именем `typename`

`namedtuple` актуален при использовании небольшой, фиксированной, неизменяемой записи данных. По смыслу это промежуточный вариант между обычным `tuple` и полноценным классом.

Пример:
```python
from collections import namedtuple

TokenData = namedtuple(
    "TokenData",
    field_names=["user_id", "region", "expires_at"],
    module=__name__
)

def parse_token(payload: dict) -> TokenData:
    return TokenData(
        user_id=int(payload["sub"]),
        region=str(payload["region"]),
        expires_at=int(payload["exp"]),
    )

token = parse_token({"sub": "42", "region": "eu", "exp": 1710000000})
```
