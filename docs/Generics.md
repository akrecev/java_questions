## Дженерики

### 1. Что такое дженерики?
Дженерики (Generics) — это механизм обобщённого программирования в Java, который позволяет параметризовать типы данных.

**Основная идея**: код становится более универсальным и безопасным, исключая ошибки приведения типов.

Пример:
```java
class Box<T> { // T - параметр типа
    private T value;
    public void set(T value) { this.value = value; }
    public T get() { return value; }
}

Box<String> stringBox = new Box<>();
stringBox.set("Hello");
String str = stringBox.get(); // Без явного приведения типа
```

---

### 2. Для чего нужны дженерики?
Дженерики используются для:
- Обеспечения **type-safety** (безопасности типов) во время компиляции.
- Исключения **ошибок приведения типов**.
- Повторного использования кода без потери безопасности типов.
- Улучшения читаемости и поддержки кода.

Пример без дженериков:
```java
List list = new ArrayList();
list.add("Java");
String str = (String) list.get(0); // Нужно явное приведение типа
```
Пример с дженериками:
```java
List<String> list = new ArrayList<>();
list.add("Java");
String str = list.get(0); // Без приведения типа
```

---

### 3. Параметр vs Аргумент (в дженериках)?
**Параметр типа** — это переменная, используемая в объявлении обобщённого класса или метода (например, `T` в `List<T>`).

**Аргумент типа** — это конкретное значение параметра типа при использовании обобщённого класса или метода (например, `String` в `List<String>`).

Пример:
```java
class Box<T> { // T — параметр типа
    private T value;
}
Box<String> box = new Box<>(); // String — аргумент типа
```

---

### 4. Типизированные методы (Generic Methods)
Типизированные методы объявляют собственный параметр типа и могут использовать его независимо от параметра типа класса.

Пример:
```java
class Util {
    public static <T> void print(T value) {
        System.out.println(value);
    }
}
Util.print("Hello"); // T заменяется на String
Util.print(100); // T заменяется на Integer
```

---

### 5. Типизированные классы (Generic Types)
Обобщённые (generic) классы позволяют параметризовать тип на уровне класса.

Пример:
```java
class Pair<K, V> {
    private K key;
    private V value;
    public Pair(K key, V value) {
        this.key = key;
        this.value = value;
    }
    public K getKey() { return key; }
    public V getValue() { return value; }
}
Pair<Integer, String> pair = new Pair<>(1, "One");
```

---

### 6. Что такое сырые типы (raw type)?
Сырые типы (raw types) — это использование обобщённых классов **без указания параметра типа**.

Пример:
```java
List list = new ArrayList(); // Raw type (небезопасно)
list.add("Hello");
list.add(10); // Ошибка обнаружится только во время выполнения
```

**Проблема:** теряется безопасность типов, можно добавить объекты разного типа.

**Правильное использование:**
```java
List<String> list = new ArrayList<>();
```

---

### 7. Что такое вайлдкарды?
Wildcard (`?`) — это специальный символ, позволяющий параметризовать тип без указания конкретного аргумента.

Пример использования:
```java
void printList(List<?> list) { // ? означает любой тип
    for (Object obj : list) {
        System.out.println(obj);
    }
}
```

Применение:
- Используется для **повышения гибкости** при работе с дженериками.
- Позволяет передавать списки с разными типами (`List<Integer>`, `List<String>` и т. д.).

---

### 8. Расскажите про принцип PECS
PECS (Producer Extends, Consumer Super) — принцип работы с **ограниченными вайлдкартами**:
- `? extends T` — для **чтения** (Producer, "производитель").
- `? super T` — для **записи** (Consumer, "потребитель").

Пример:
```java
List<? extends Number> producerList = new ArrayList<Integer>();
Number num = producerList.get(0); // Можно читать
producerList.add(10); // Ошибка: нельзя добавлять
```
```java
List<? super Integer> consumerList = new ArrayList<Number>();
consumerList.add(10); // Можно добавлять
Number num = consumerList.get(0); // Ошибка: нельзя безопасно читать
```

**Принцип:**
- Если нужно **только читать**, используем `? extends T`.
- Если нужно **только записывать**, используем `? super T`.
