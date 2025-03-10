# Конспект по ООП и Java

## Сериализация и клонирование объектов в Java

### 1. Что такое сериализация и как она реализована в Java?
Сериализация: процесс преобразования объекта в поток байтов для сохранения или передачи.  
Реализация: класс должен реализовать Serializable. Используются ObjectOutputStream и ObjectInputStream.  
Пример:
```java
class Person implements Serializable {
    String name;
}
Person p = new Person();
ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream("file.ser"));
out.writeObject(p);
```

### 2. Для чего нужна сериализация?
Сохранение состояния объекта (в файл, БД).  
Передача объектов по сети (например, в RMI).  

### 3. Опиши процесс сериализации/десериализации с использованием Serializable
Сериализация: `writeObject()` записывает объект в поток.  
Десериализация: `readObject()` воссоздаёт объект из потока.  
Пример:
```java
ObjectInputStream in = new ObjectInputStream(new FileInputStream("file.ser"));
Person p = (Person) in.readObject();
```

### 4. Как изменить стандартное поведение сериализации/десериализации?
Переопредели методы `writeObject` и `readObject`.  
Пример:
```java
class Person implements Serializable {
    transient String secret;
    private void writeObject(ObjectOutputStream out) throws IOException {
        out.defaultWriteObject();
        out.writeObject(secret.hashCode());
    }
    private void readObject(ObjectInputStream in) throws IOException, ClassNotFoundException {
        in.defaultReadObject();
        secret = String.valueOf(in.readObject());
    }
}
```

### 5. Какие поля не будут сериализованы при сериализации? Будет ли сериализовано final поле?
Не сериализуются: `transient` и `static`.  
`final`: сериализуется, если не `transient`.  
Пример:
```java
class Test implements Serializable {
    transient int x; // не сериализуется
    final int y = 5; // сериализуется
}
```

### 6. Как создать собственный протокол сериализации?
Реализуй Externalizable с методами writeExternal и readExternal.  
Пример:
```java
class Custom implements Externalizable {
    String data;
    public void writeExternal(ObjectOutput out) { out.writeUTF(data); }
    public void readExternal(ObjectInput in) { data = in.readUTF(); }
}
```

### 7. Какая роль поля serialVersionUID в сериализации?
Это идентификатор версии класса. Если он не совпадает при десериализации, выбрасывается InvalidClassException.  
Пример:
```java
class Test implements Serializable {
    private static final long serialVersionUID = 1L;
}
```

### 8. Когда стоит изменять значение поля serialVersionUID?
При значительных изменениях структуры класса (добавление/удаление полей), если нужна несовместимость со старыми версиями.

### 9. В чём проблема сериализации Singleton?
Десериализация создаёт новый экземпляр, нарушая уникальность.  
Решение: переопределить readResolve():
```java
class Singleton implements Serializable {
    private static final Singleton INSTANCE = new Singleton();
    private Singleton() {}
    private Object readResolve() { return INSTANCE; }
}
```

### 10. Расскажи про клонирование объектов
Клонирование — создание копии объекта через clone().  
Класс должен реализовать Cloneable и переопределить clone().

### 11. В чём отличие между поверхностным и глубоким клонированием?
Поверхностное: копируются ссылки на вложенные объекты.  
Глубокое: копируются сами вложенные объекты.  
Пример:
```java
class A implements Cloneable {
    int x;
    B b = new B();
    public A clone() throws CloneNotSupportedException {
        return (A) super.clone(); // поверхностное
    }
}
class B { int y; }
```

### 12. Какой способ клонирования предпочтительней?
Глубокое, если нужна полная независимость копии.

### 13. Почему метод clone() объявлен в классе Object, а не в интерфейсе Cloneable?
`Object` предоставляет базовую реализацию, а `Cloneable` — маркер, сигнализирующий о поддержке клонирования.

### 14. Как создать глубокую копию объекта? (2 способа)
**Через `clone()`:**
```java
class A implements Cloneable {
    B b = new B();
    public A clone() throws CloneNotSupportedException {
        A copy = (A) super.clone();
        copy.b = b.clone();
        return copy;
    }
}
```
**Через сериализацию:** сериализовать и десериализовать объект.
