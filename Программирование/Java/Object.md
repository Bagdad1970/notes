Класс Object является корнем в иерархии классов. Все классы являются потомками класса Object и все потомки реализуют его методы.

### Методы Object
- `equals(Object obj)` - [[Equals и hashcode]]
- `hashCode()` - [[Equals и hashcode]]
- protected Object clone() - [[Клонирование]]
- `void wait(), wait(long timeout), wait(long timeout, int nanos), notify(), notifyAll()` - [[Многопоточность. Практика (База)]]
- `String toString()` - возвращает строковое представление объекта. По умолчанию вернет строку вида `имя_класса@хэш_код_объекта`
- `Class<?> getclass()` - возвращает тип объекта
- `protected finalize() throws Throwable` - вызывается для удаления сборщиком мусора, если ссылок на объект больше нет. Переопределяется для удаления системных ресурсов или выполнения другой очистки.