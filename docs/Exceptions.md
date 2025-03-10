# Конспект по ООП и Java

## Обработка исключений в Java

### 1. Что такое исключения?
Исключения — это события, нарушающие нормальное выполнение программы (ошибки ввода, деление на ноль и т.д.). В Java они представлены объектами классов, наследующих `Throwable`.

### 2. Опиши иерархию исключений
`Throwable`:
- `Error`: необрабатываемые ошибки (например, `OutOfMemoryError`).
- `Exception`: обрабатываемые исключения:
  - `RuntimeException`: ошибки времени выполнения (например, `NullPointerException`).
  - Другие (например, `IOException`).

### 3. Расскажи про обрабатываемые и необрабатываемые исключения
- Обрабатываемые: подклассы `Exception` (кроме `RuntimeException`), требуют явной обработки (`try-catch` или `throws`).
- Необрабатываемые: `Error` и `RuntimeException`, не требуют обязательной обработки.

### 4. Можно ли обработать необрабатываемые исключения?
Да, можно поймать `Error` или `RuntimeException` в `try-catch`, но это не рекомендуется, так как они сигнализируют о серьёзных проблемах.

### 5. Какой оператор позволяет принудительно выбросить исключение?
`throw`.  
Пример: `throw new IOException("Ошибка");`.

### 6. О чём говорит ключевое слово throws?
Указывает, какие исключения метод может выбросить, передавая ответственность вызывающему коду.  
Пример:
```java
void method() throws IOException {}
```

### 7. Как создать собственное («пользовательское») исключение?
Наследуй от `Exception` или `RuntimeException`.  
Пример:
```java
class MyException extends Exception {
    public MyException(String message) { super(message); }
}
throw new MyException("Custom error");
```

### 8. Расскажи про механизм обработки исключений в Java (try-catch-finally)
`try`: блок с потенциально опасным кодом.  
`catch`: обрабатывает конкретное исключение.  
`finally`: выполняется всегда (например, для освобождения ресурсов).  
Пример:
```java
try {
    int x = 1 / 0;
} catch (ArithmeticException e) {
    System.out.println("Ошибка: " + e);
} finally {
    System.out.println("Завершение");
}
```

### 9. Возможно ли использование блока try-finally (без catch)?
Да, для гарантированного выполнения кода (например, закрытие файла).  
Пример:
```java
try {
    System.out.println("Работа");
} finally {
    System.out.println("Очистка");
}
```

### 10. Может ли один блок catch отлавливать сразу несколько исключений?
Да, с Java 7 через `|`.  
Пример:
```java
try {
    // код
} catch (IOException | SQLException e) {
    e.printStackTrace();
}
```

### 11. Всегда ли выполняется блок finally? Существуют ли ситуации, когда блок finally не будет выполнен?
Выполняется всегда, кроме:
- `System.exit()`.
- Сбой JVM.
- Бесконечный цикл или зависание в `try`.

Пример исключения:
```java
try {
    System.exit(0);
} finally {
    System.out.println("Не выполнится");
}
```

### 12. Может ли метод main() выбросить исключение во вне и если да, то где будет происходить обработка данного исключения?
Да, может. Исключение обрабатывается JVM, которая выводит стек вызовов в консоль и завершает программу.  
Пример:
```java
public static void main(String[] args) throws Exception {
    throw new Exception("Ошибка");
}
```

### 13. В каком порядке следует обрабатывать исключения в catch-блоках?
От специфичных к общим (сначала подклассы, затем суперклассы).  
Пример:
```java
try {
    // код
} catch (FileNotFoundException e) {
    // сначала
} catch (IOException e) {
    // потом
}
```

### 14. Что такое механизм try-with-resources?
Введён в Java 7 для автоматического закрытия ресурсов, реализующих `AutoCloseable`.  
Пример:
```java
try (FileReader fr = new FileReader("file.txt")) {
    fr.read();
} // fr.close() вызывается автоматически
```

### 15. Что произойдёт, если исключение будет выброшено из блока catch, после чего другое исключение будет выброшено из блока finally?
Исключение из `finally` подавляет исключение из `catch`.  
Пример:
```java
try {
    throw new Exception("Try");
} catch (Exception e) {
    throw new Exception("Catch");
} finally {
    throw new Exception("Finally"); // подавляет "Catch"
}
```

### 16. Что произойдёт, если исключение будет выброшено из блока catch, после чего другое исключение будет выброшено из метода close() при использовании try-with-resources?
Исключение из close() добавляется как подавленное (suppressed) к основному из catch.  
Пример:
```java
try (Resource r = new Resource()) {
    throw new Exception("Try");
} catch (Exception e) {
    throw new Exception("Catch");
}
// close() выбросит исключение, оно будет подавленным
```