# Comparator
Comparator - отдельный класс-сравнитель, который позволяет выполнять различные сортировки, не изменяя саму логику внутри класса.

```java
interface Comparator<T> {
	int compare(T o1, T o2);
}
```

Метод compare(T o1, T o2) сравнивает 2 объекта ...


# Comparable
Comparable - вводит полный порядок на объекты класса, который реализует его. Такой порядок называется естественным порядком класса, и метод `compareTo` называется его естественным методом сравнения.

```java
interface Comparable<T> {
	int compareTo(T o);
}
```

int compareTo(T o) - сравнивает this объект с переданным.
Возвращает:
- отрицательное число, если левый < правый;
- 0, если левый = правый;
- положительное число, если левый > правый.

Требования к compareTo(...):
1. $\forall x, y: \operatorname{sgn}(x.\text{compareTo}(y)) = -\operatorname{sgn}(y.\text{compareTo}(x))$
2. $x.\text{compareTo}(y) > 0 \land y.\text{compareTo}(z) > 0 \implies x.\text{compareTo}(z) > 0$
3. $x.\text{compareTo}(y) = 0$ подразумевает, что $\forall z: \operatorname{sgn}(x.\text{compareTo}(z)) = \operatorname{sgn}(y.\text{compareTo}(z))$

Желательно также: `(x.compareTo(y) == 0) == x.equals(y)` 

Объекты классов, реализующих этот интерфейс, могут быть ключами в SortedMap (TreeMap) и элементами в SortedSet (TreeSet) без необходимости указывать Comparator.

Естественный порядок (natural ordering) согласуется с методом equals() тогда и только тогда, когда e1.compareTo(e2) возвращает то же булево значение, что и e1.equals(e2). 

**Замечание**: null не является объектом какого-либо класса, и e.compareTo(null) должно выбрасывать NullPointerException даже если e.equals(null) = false.

Настоятельно рекомендуется, чтобы естественный порядок и equals совпадали, поскольку SortedSets и SortedMaps без явных Comparators могут вести себя нестандартно, когда они работают с элементами (ключами), чей естественный порядок не совпадает с equals.

Пример: если добавить два ключа a и b такие, что !a.equals(b) && a.compareTo(b) == 0 в SortedSet, который не имеет явного Comparator, то повторное добавление вернет false (и размер не изменится) т.к. a и b равны с точки зрения SortedSet.

