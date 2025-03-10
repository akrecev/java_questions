# Конспект по ООП и Java

## Коллекции

### 1. Что такое «коллекция»?
Коллекция — это контейнер, предназначенный для хранения и управления группой объектов.

**Основные характеристики коллекций:**
- Динамическое управление размером.
- Встроенные механизмы работы с элементами (поиск, сортировка, удаление и т. д.).
- Разные структуры данных в зависимости от требований (списки, множества, очереди, ассоциативные массивы).

Пример:
```java
List<String> list = new ArrayList<>();
list.add("Hello");
list.add("World");
```

### 2. Расскажите про иерархию коллекций
Коллекции в Java представлены в пакете `java.util` и организованы в иерархию интерфейсов и классов:
- `Collection<E>` (базовый интерфейс)
    - `List<E>` (упорядоченные коллекции, допускающие дубли)
        - `ArrayList<E>`
        - `LinkedList<E>`
        - `Vector<E>`
    - `Set<E>` (не допускают дубли)
        - `HashSet<E>`
        - `TreeSet<E>`
        - `LinkedHashSet<E>`
    - `Queue<E>` (очереди)
        - `PriorityQueue<E>`
        - `ArrayDeque<E>`
- `Map<K, V>` (ассоциативные массивы, не наследуются от `Collection`)
    - `HashMap<K, V>`
    - `TreeMap<K, V>`
    - `LinkedHashMap<K, V>`

### 3. Почему Map — это не Collection, в то время как List и Set являются Collection?
Интерфейс `Map<K, V>` не реализует `Collection<E>`, так как `Map` работает с парами «ключ-значение», а не с простым набором элементов. В отличие от `List` и `Set`, `Map` имеет методы для работы с ключами и значениями:
```java
Map<Integer, String> map = new HashMap<>();
map.put(1, "One");
map.put(2, "Two");
```

### 4. В чем разница между классами java.util.Collection и java.util.Collections?
- `Collection` — интерфейс, определяющий основные операции для коллекций (`add()`, `remove()`, `contains()`, и т. д.).
- `Collections` — утилитарный класс, содержащий статические методы для работы с коллекциями (`sort()`, `shuffle()`, `synchronizedList()`, и т. д.).

Пример использования `Collections.sort()`:
```java
List<Integer> numbers = new ArrayList<>(List.of(3, 1, 4, 1, 5));
Collections.sort(numbers);
System.out.println(numbers); // [1, 1, 3, 4, 5]
```

### 5. Какая разница между итераторами с fail-fast и fail-safe поведением? (с примерами)
- **Fail-fast** — итератор выбрасывает `ConcurrentModificationException`, если коллекция изменяется во время обхода.
- **Fail-safe** — итератор создает копию данных, позволяя избежать исключения.

Пример **fail-fast**:
```java
List<String> list = new ArrayList<>(List.of("A", "B", "C"));
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
    list.add("D"); // ConcurrentModificationException
    System.out.println(iterator.next());
}
```

Пример **fail-safe**:
```java
CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>(List.of("A", "B", "C"));
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
    list.add("D"); // Не вызовет исключения
    System.out.println(iterator.next());
}
```

### 6. Чем различаются Enumeration и Iterator?
| Характеристика  | Enumeration | Iterator |
|----------------|-------------|----------|
| Методы        | `hasMoreElements()`, `nextElement()` | `hasNext()`, `next()`, `remove()` |
| Удаление элементов | Нет | Да (`remove()`) |
| Используется в | `Vector`, `Hashtable` | `Collection` |

Пример с `Enumeration`:
```java
Vector<String> vector = new Vector<>(List.of("A", "B", "C"));
Enumeration<String> enumeration = vector.elements();
while (enumeration.hasMoreElements()) {
    System.out.println(enumeration.nextElement());
}
```

### 7. Как избежать ConcurrentModificationException во время перебора коллекции?
1. Использовать `Iterator.remove()` вместо `list.remove()`.
2. Использовать `CopyOnWriteArrayList`.
3. Использовать `ConcurrentHashMap` для `Map`.
4. Перебирать через `Stream`.

Пример с `Iterator.remove()`:
```java
List<String> list = new ArrayList<>(List.of("A", "B", "C"));
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
    if (iterator.next().equals("B")) {
        iterator.remove();
    }
}
System.out.println(list); // [A, C]
```

### 8. Как между собой связаны Iterable, Iterator и «for-each»?
- `Iterable<E>` — интерфейс, содержащий метод `iterator()`.
- `Iterator<E>` — интерфейс, позволяющий перебирать элементы (`hasNext()`, `next()`).
- `for-each` использует `Iterator` в фоновом режиме.

Пример:
```java
class MyCollection implements Iterable<String> {
    private List<String> data = List.of("A", "B", "C");
    @Override
    public Iterator<String> iterator() {
        return data.iterator();
    }
}

for (String item : new MyCollection()) {
    System.out.println(item);
}
```

### 9. Можно ли, обходя ArrayList, удалить элемент? Какое вылетит исключение?
Если использовать `list.remove(index)`, вылетит `ConcurrentModificationException`.

Пример:
```java
List<String> list = new ArrayList<>(List.of("A", "B", "C"));
for (String item : list) {
    list.remove(item); // ConcurrentModificationException
}
```

### 10. Как удалить элемент из ArrayList при итерации?
Использовать `Iterator.remove()`:
```java
List<String> list = new ArrayList<>(List.of("A", "B", "C"));
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
    if (iterator.next().equals("B")) {
        iterator.remove();
    }
}
System.out.println(list); // [A, C]
```

### 11. Как поведёт себя коллекция, если вызвать iterator.remove()?

Метод `iterator.remove()` используется для удаления текущего элемента коллекции, на который указывает итератор. Этот метод удаляет элемент безопасно, без выброса исключения `ConcurrentModificationException`, если коллекция не изменялась другим способом во время итерации.

**Особенности поведения:**
1. **Удаление текущего элемента:**
    - `iterator.remove()` удаляет элемент, который был возвращён последним вызовом `next()`.
    - Если `next()` не был вызван перед `remove()`, будет выброшено исключение `IllegalStateException`.

2. **Безопасное удаление:**
    - Этот метод является предпочтительным способом удаления элементов во время итерации, так как он не нарушает работу итератора.

3. **Изменение коллекции:**
    - После вызова `iterator.remove()` коллекция изменяется, и элемент удаляется из неё.

**Пример использования:**
```java
List<String> list = new ArrayList<>();
list.add("Apple");
list.add("Banana");
list.add("Cherry");

Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
String element = iterator.next();
    if (element.equals("Banana")) {
        iterator.remove(); // Удаляем "Banana"
    }
            }

            System.out.println(list); // [Apple, Cherry]
```
Важные моменты:

Если коллекция изменяется другим способом (например, через list.remove()), то при следующем вызове методов итератора (next() или remove()) будет выброшено исключение ConcurrentModificationException.  
Метод iterator.remove() поддерживается не всеми коллекциями. Например, в CopyOnWriteArrayList этот метод выбросит UnsupportedOperationException.  
**Пример с исключением:**
```java
List<String> list = new ArrayList<>();
list.add("Apple");
list.add("Banana");

Iterator<String> iterator = list.iterator();
list.remove("Apple"); // Изменение коллекции вне итератора
iterator.next(); // Выбросит ConcurrentModificationException
```
Итог:  
`iterator.remove()` — это безопасный способ удаления элементов во время итерации.  
Используйте его, если нужно удалить элементы из коллекции в процессе обхода.

### 12. Чем Set отличается от List?

**Основные различия между `Set` и `List`:**

1. **Уникальность элементов:**
    - `Set` не допускает дубликатов. Все элементы в `Set` уникальны.
    - `List` позволяет хранить дубликаты. Один и тот же элемент может встречаться несколько раз.

2. **Порядок элементов:**
    - `Set` не гарантирует порядка элементов (за исключением `LinkedHashSet` и `TreeSet`, которые поддерживают порядок вставки и сортировку соответственно).
    - `List` сохраняет порядок вставки элементов. Элементы можно получить по индексу.

3. **Методы доступа:**
    - `Set` не предоставляет методов для доступа к элементам по индексу (например, `get(index)`).
    - `List` предоставляет методы для доступа к элементам по индексу, такие как `get(index)`.

4. **Реализации:**
    - `Set` имеет реализации: `HashSet`, `LinkedHashSet`, `TreeSet`.
    - `List` имеет реализации: `ArrayList`, `LinkedList`, `Vector`.

**Пример использования `Set`:**
```java
Set<String> set = new HashSet<>();
set.add("Apple");
set.add("Banana");
set.add("Apple"); // Дубликат не будет добавлен
System.out.println(set); // [Apple, Banana]
```

**Пример использования `List`:**
```java
List<String> list = new ArrayList<>();
list.add("Apple");
list.add("Banana");
list.add("Apple"); // Дубликат будет добавлен
System.out.println(list); // [Apple, Banana, Apple]
```
Когда использовать:  
Используйте `Set`, если вам нужно хранить уникальные элементы.  
Используйте `List`, если вам важен порядок элементов и допустимы дубликаты.
