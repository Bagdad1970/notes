Операция `==` не рекомендуется для сравнения объектов т.к. только сравнивает ссылаются ли ссылки на один и тот же объект.
# Hashcode
Метод `hashcode()` возвращает хэш-код объекта. По умолчанию хэш-код генерируется путем преобразования адреса объекта в памяти в число.

Контракт hashcode:
- Детерминированность
- $\forall obj1, obj2: obj1.equals(obj2) = true \implies obj1.hashcode() = obj2.hashcode()$
- $\forall obj1, obj2: obj1.equals(obj2) = false \implies obj1.hashcode() \neq obj2.hashcode()$

**Замечание**: При переопределении equals() всегда нужно переопределять hashcode(), иначе класс будет неверно работать с коллекциями на основе хэша (HashMap, HashSet).

Пример переорпделения hashcode:
```java
class MyClass {
	private String field1;
	private short field2;
	
	@Override
	public int hashcode() {
		int result = 1;
		final int prime = 31;
		result = prime * result + ((field1 == null ? 0 : field1.hashcode()));
		result = prime * result + Short.hashcode(field2);
		return result;
	}
}
```

**Замечание**: Хэш объекта вычисляется заново при изменении полей объекта.

# Equals
Метод `equals(Object obj)` показывает, что один объект равен другому.
Свойства:
- Рефлексивность: `x.equals(x) -> true`
- Симметричность: `x.equals(y) -> true <-> y.equals(x) -> true`
- Транзитивность: `x.equals(y) -> true и y.equals(z) -> true => x.equals(z) -> true`
- Стабильность
- Работа с null: `x.equals(null) -> false`

Пример переопределения equals:
```java
class MyClass {
	private String field1;
	private short field2;
	
	@Override
	public boolean equals(final Object o) {
		if (this == o) return true;
		if (o == null || getClass() != o.getClass()) return false;
		MyClass obj = (MyClass) o;
		return field1.equals(obj.field1) && field2 == obj.field2;
	}
}
```