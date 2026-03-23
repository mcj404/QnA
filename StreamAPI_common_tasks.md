// Frequency Map of words

```java
List<String> fruits = Arrays.asList("apple", "orange", "banana", "apple", "apple", "banana");

// Create the frequency map using Stream API
Map<String, Long> frequencyMap = fruits.stream()
    .collect(Collectors.groupingBy(
        Function.identity(), // Classifier function: groups elements by the element itself
        Collectors.counting() // Downstream collector: counts occurrences of each group
    ));
```

// Frequency Map of string

```java
String str = "programming";

Map<Character, Long> counts = str.chars()
                                .mapToObj(c -> (char) c)
                                .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()))
```

// Sorting using comparator

```java
List<Employee> sortedBySalaryDesc = employees.stream()
    .sorted(Comparator.comparingInt(Employee::getSalary).reversed())
    .collect(Collectors.toList());

// Sort by name, then by salary if names are the same
List<Employee> sortedByNameThenSalary = employees.stream()
    .sorted(Comparator.comparing(Employee::getName)
                      .thenComparingInt(Employee::getSalary))
    .collect(Collectors.toList());

```
