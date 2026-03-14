- From a given string array, find the second largest word with unique characters 
- 
- How to debug OutOfMemoryError?
- DataSource Connection close() should not be used but should be closed. How to handle this scenario?
- @Qualifier and @Primary
- How many ways we can create beans? Setter / constructor injection?
- Major migration from spring boot 2 to spring boot 3 wrt Security?
- How to handle fault tolerance?
- What is change detection in angular?
- HttpClient vs HttpInterceptor?
- Difference between Observables and Promises?
- How to load 10 million records to browser? Best approach?
- DDL, DML and DCL?
- StoredProcedures, Triggers and Cursors? Their need and how to use them?
- What is IAM (AWS)?
- EC2 vs ECS?
- CI CD tools worked with?


### What is the difference between FI and Method References?

Functional Interface: An interface with exactly one abstract method.
- It can contain any number of default or static methods.
- Examples : Runnable (run), Callable (call), Predicate(test), Consumer(accept), Supplier(get), Function(apply)

Method Reference: A shorthand notation of lambda expression to call an existing method directly.
- Used to improve code readibility when a lambda expression only calls an existing method directly.
- Types: Static method (Class::staticMethod), Instance method (obj::instanceMethod), Constructor Reference (Class::new)
- Conciseness : Reduces boilerplate code further than lambdas
- Readability: System.out::print is cleaner than s -> System.out.print(s)
- Performance: Method references can slightly be more efficient as they avaoid generating unnecessary lambda class objects.

```java
        String data = "Hello World!";

        Consumer<String> consumer = new Consumer<String>() {
            @Override
            public void accept(String s) {
                System.out.println(s);
            }
        };

        Consumer<String> lambdaConsumer = (s) -> System.out.println(s);

        Consumer<String> methodReferenceConsumer = System.out::println;

        consumer.accept(data);
```

### 