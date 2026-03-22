

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

### what is the need of private method inside functional interfaces which was introduced in Java 9?

- It allow for code reuse among non-abstract methods (default and static methods) within the same interface, without exposing the shared logic to implemeting classes.
- This improves encapsulation and code organization.
- Without private methods, this led to duplicate code across the default methods.
- Java 9 also allows private static methods, which can be used to shre common utility code among different public static methods within the same interface
- In short, In short, private interface methods solve the problem of code redundancy in default and static methods while maintaining a clean, public API for implementing classes.


### @Qualifier and @Primary usage and differences

- @Primary (Default Choice): Applied to the bean definition (e.g., @Component or @Bean) to set it as the preferred choice when multiple options exist.
- @Qualifier (Specific Choice): Applied at the injection point (e.g., @Autowired) to specify exactly which named bean to inject.
- **Priority**: If a bean is marked @Primary but a consumer uses @Qualifier, the @Qualifier choice wins.
- **Usage**: Use @Primary for a general default implementation, and @Qualifier for specific, granular control.


### How to debug OutOfMemoryError?

- A `java.lang.OutOfMemoryError` is a critical runtime error that occurs when the JVM cannot allocate an object due to insufficient available memory, and the garbage collector is unable to free up the required space.
- This error indicates that the application has exhausted the physical or configured memory limits of a specific JVM memory region.

Common Types and Causes:

- `java.lang.OutOfMemoryError: Java heap space`: The application is creating too many objects or holding references to objects longer than necessary (a memory leak), filling the main memory region where objects are stored (the heap).

    Solution: Increase the maximum heap size using the -Xmx JVM argument or, more importantly, use memory profiling tools to identify and fix memory leaks or inefficient code.

- `java.lang.OutOfMemoryError: GC overhead limit exceeded`: The garbage collector (GC) is running almost continuously (spending more than 98% of the total time on GC) but is recovering very little memory (less than 2% of the heap), meaning the application is making little to no progress.

    Solution: This often points to an underlying memory leak or a heap that is nearly full. Increasing the heap size might offer a temporary fix, but fixing the code or tuning the GC is the better long-term solution.

- `java.lang.OutOfMemoryError: Metaspace`: The Metaspace (in Java 8+; previously PermGen) which stores class metadata, has run out of space. 
    Soultion: Increase the MaxMetaspaceSize configuration if needed, though often it points to an application dynamically loading too many classes.

- `java.lang.OutOfMemoryError: unable to create new native thread`: The OS is unable to allocate a new native thread, often because the JVM's heap is too large, consuming all available RAM and leaving no space for thread stacks.
    Soultion: Reduce the JVM heap size (-Xmx) to leave more memory for the operating system and native processes

**Troubleshooting steps**  

1. Analyze the error message to determine the specific type and memory region affected.
2. Generate a heap dump automatically on error by adding the -XX:+HeapDumpOnOutOfMemoryError flag to the JVM options. This creates a snapshot of the memory at the time of the crash.
3. Analyze the heap dump using profiling tools like VisualVM or Eclipse Memory Analyzer Tool (MAT) to identify large objects or memory leaks.
4. Monitor GC logs using the -verbosegc flag to see how often GC runs and how much memory it reclaims.


### Scenario: DataSource Connection method close() should not be used but connection should be closed. 

- The recommended way to ensure connections are closed (returned to the pool [HikariCP/Tomcat JDBC]) properly, even if exceptions occur, is to use a try-with-resources statement.
- The key is that the close() method on a pooled connection does not physically terminate the database session; instead, it logically closes the connection and returns it to the connection pool for reuse.
- Not calling close() will cause connection leaks, as the connection remains checked out from the pool and is unavailable for other parts of your application, potentially leading to connection pool exhaustion.
- **Standard Practice**: The JDBC specification explicitly states that applications should call Connection.close() regardless of whether pooling is used or not; the pooling implementation handles the specifics transparently.


### How many ways we can create beans?

- **Annotation-Based Configuration (Component Scanning)** : Spring automatically detects classes annotated with specific stereotype annotations within the application's base package and its sub-packages. \
Ex: @Component, @Service, @Repository, @RestController / @Controller. 

- **Java-Based Configuration (@Configuration and @Bean)** : This approach provides explicit, manual control over bean creation, which is useful when you need to customize the instantiation logic or configure third-party classes. Ex: @Configuration: This annotation marks a class as a source of bean definitions.\
@Bean: This annotation is placed on a method within a @Configuration class. The object returned by that method will be registered as a bean in the Spring application context.

- **XML-Based Configuration** : This is a traditional method from the core Spring framework. While less common in modern Spring Boot applications, it is still a valid way to define beans using XML configuration files.\
Beans are defined using the `<bean>` element in an XML file, specifying the class and a unique ID or name. 


### Major migration from spring boot 2 to spring boot 3 with respect to Security?

- Prerequesites :  Spring Boot 3 requires a minimum of Java 17. 
- removal of the deprecated `WebSecurityConfigurerAdapter` and a shift to a more functional, component-based approach using SecurityFilterChain beans.
- authorizeRequests() is replaced by authorizeHttpRequests(). antMatchers(), mvcMatchers(), and regexMatchers() are consolidated and replaced by requestMatchers().


### What are the ways to handle fault tolerance?

**Core Fault Tolerance Strategies**

- **Redundancy & Replication**: 
    - Data: Duplicate data across nodes to ensure durability.
    - Components: Use redundant servers, network paths (e.g., NIC teaming), or hardware (e.g., RAID) to eliminate single points of failure.

- **Failover Mechanisms** : 
    - Active-Passive: A standby component takes over only when the active component fails.
    - Active-Active: Multiple components share the load simultaneously, allowing others to absorb traffic if one fails.


- **Error Detection & Recovery**:
    - Heartbeats: Periodic signals between components to detect outages.
    - Checkpointing/Rollback: Periodically saving system state to resume from a known good point after a failure.
    - Retries & Timeouts: Automatically re-executing transiently failed operations.

- **Isolation & Design Patterns**:
    - Bulkheads: Partitioning resources to prevent a failure in one area from cascading to others.
    - Circuit Breakers: Stopping requests to a failing service to allow it time to recover.
    - Asynchronous Processing: Using message queues (e.g., Kafka, RabbitMQ) to decouple services and handle load spikes without blocking.

- **Monitoring & Observability**:
    - Continuous tracking of system health metrics (CPU, latency, error rates) via tools like Prometheus and Grafana ensures issues are detected before they cause downtime.


### What is change detection in angular?

- Change detection in Angular is the mechanism that synchronizes the application's data model with the user interface (UI). Its primary purpose is to detect when the underlying data (model) changes and automatically update the relevant parts of the Document Object Model (DOM) to reflect those changes.

- Change Detection Strategies
    - Default ( ChangeDetectionStrategy.Default ): The default behavior, where the entire component tree is checked for changes after every single asynchronous event. This is convenient for development but can lead to performance issues in large, complex applications.

    - OnPush ( ChangeDetectionStrategy.OnPush ): An optimization strategy that tells Angular to only run change detection for a component and its subtree in specific situations:
    
    - An @Input property reference changes (not just a mutation of an existing object).
    
    - A DOM event handler is triggered within the component or its children.

    - An observable consumed by the async pipe emits a new value.
    Change detection is explicitly triggered using methods like markForCheck() or detectChanges() from ChangeDetectorRef.

> With the introduction of Angular Signals (v16+) and the zoneless change detection mode (v17.1+), Angular is moving towards a more fine-grained, local change detection approach. Signals notify Angular precisely which components need updating when their values change, potentially allowing applications to run without Zone.js and providing substantial performance gains by checking only the affected components.


### HttpClient vs HttpInterceptor?

The HttpClient is a built-in service class in Angular, part of the @angular/common/http package, used to facilitate communication between the front-end application and back-end services over the HTTP protocol. It simplifies the process of fetching or sending data (like JSON) to a server using standard HTTP methods.

Key Features:

- **RxJS and Observables:** All HttpClient methods (e.g., get, post, put, delete) return RxJS Observables, which makes it easier to work with asynchronous data and handle single-value responses.
- **Typed Requests and Responses:** It supports strong typing for request and response objects, providing better type safety and making the code cleaner and less error-prone.

- **Interceptors:** Interceptors act as middleware, allowing developers to intercept and modify HTTP requests and responses globally. This is ideal for cross-cutting concerns like adding authentication tokens or handling errors uniformly.

- **Streamlined Error Handling:** It provides a consistent and efficient mechanism for handling errors across the application.

- **Modern API:** It was introduced in Angular 4.3 as a replacement for the older Http module, offering better performance and features.

- **Testing Utilities:** The module includes robust utilities that make it easier to test HTTP interactions within the application. 



An HTTP interceptor in Angular is a feature that acts as a middleware layer to inspect and transform HTTP requests and responses globally, as they flow between your application and the server. This allows you to centralize common tasks, such as authentication, logging, and error handling, in a single place, rather than implementing them in every HttpClient method call.

**How They Work**

Interceptors are services that you create and provide to Angular's dependency injection system. 

- Implementation: You create a class that implements the HttpInterceptor interface (or a functional interceptor using HttpInterceptorFn in modern Angular) and its intercept() method.
- The Chain: When an HTTP request is made, it passes through a chain of configured interceptors. Each interceptor can handle the request or pass it along to the next handler in the chain by calling next.handle(modifiedReq).
- Immutability: HTTP requests and responses are immutable in Angular. To modify a request, you must first clone() it and then modify the clone before passing it on.
- Responses: Interceptors can also transform responses on their way back to the application using RxJS operators like pipe(), tap(), and catchError().

**Common Use Cases**

Interceptors are powerful tools for cross-cutting concerns:

- **Authentication/Authorization:** Automatically add an authentication token (e.g., a JWT bearer token) to the headers of every outgoing request.
- **Error Handling:** Centralize global error handling logic (e.g., redirecting to a login page on a 401 Unauthorized status or displaying user-friendly notifications).
- **Logging/Profiling:** Log the details and timing of HTTP requests and responses for debugging and monitoring application performance.
- **Caching:** Implement custom caching strategies to store responses and serve them for subsequent requests, improving performance.
- **UI Indicators:** Manage a global loading spinner or progress bar for all active API calls.
- **URL Transformation:** Modify request URLs on the fly (e.g., prepending a base API URL).


### Difference between Observables and Promises?

**Observables**

An Observable is a data structure from the RxJS library that represents a stream of data or events over time. They are a core part of Angular's reactive programming model. 

- **Multiple Values:** Observables can emit zero, one, or multiple values sequentially over a period, until they are completed or encounter an error.
- **Lazy Execution:** They do not start executing their logic or making an API call until something (an observer) explicitly subscribes to them.
- **Cancellable:** You can stop an ongoing Observable execution by calling its unsubscribe() method, which is crucial for managing resources and preventing memory leaks in components.
- **Powerful Operators:** They come with a rich set of RxJS operators like map, filter, switchMap, and catchError for transforming, filtering, and composing asynchronous data streams.
- **Use Cases:** Ideal for real-time data, user input events (like keystrokes for type-ahead search), WebSocket connections, and most Angular HttpClient operations which return Observables by default.


**Promises**

A Promise is a built-in JavaScript object (part of the ECMAScript standard) that represents the eventual completion or failure of a single asynchronous operation and its resulting value. 
- **Single Value:** A Promise only emits a single value (either a resolution value or an error) once, after which it is considered settled and its value is immutable.
- **Eager Execution:** A Promise starts executing immediately when it is created, regardless of whether a .then() or .catch() callback is attached.
- **Not Cancellable:** Once a Promise operation starts, it cannot be canceled; it will eventually resolve or reject.
- **Basic Chaining:** They use .then() for handling the resolved value and .catch() for error handling, with the modern async/await syntax as a clean alternative.
- **Use Cases:** Best for simple, one-time asynchronous tasks like a single file read operation or a simple HTTP GET request where only one result is needed. 


### What should be the best approach to load 10 million records to browser in angular?

The best approach to handle 10 million records is a combination of server-side data management (pagination and filtering) and client-side virtual scrolling. It is not feasible to load all 10 million records into the browser's memory and DOM simultaneously without causing significant performance issues and potential application crashes.

**Recommended Approach**

1. **Server-Side Management (Mandatory)**
The critical first step is to never fetch the entire dataset at once. The server should manage the large dataset and only send small, manageable chunks to the client. 
    - **Pagination:** Implement server-side pagination to fetch data in small batches (e.g., 50-100 records at a time).
    - **Filtering, Sorting, and Searching:** All data operations like searching and filtering must be handled on the server side. The client sends the filter/sort criteria to the server, and the server returns only the matching paginated results. This significantly reduces the data transferred over the network and processed by the browser. 

2. **Client-Side Rendering (Optimization)**
Once you're only receiving smaller data chunks, you can use client-side techniques to optimize how they are displayed. 

    - **Virtual Scrolling:** Use the Angular Component Development Kit (CDK) ScrollingModule for virtual scrolling. This technique only renders the items currently visible in the user's viewport, dynamically adding or removing DOM elements as the user scrolls, keeping the DOM size small and performance high.
    - **Infinite Scrolling (Optional):** Combine virtual scrolling with infinite scrolling, which loads the next "page" of data from the server automatically as the user approaches the end of the current list.
    - Use **trackBy** in *ngFor: When rendering lists, always use the trackBy function with *ngFor to help Angular efficiently update only the items that have changed, preventing unnecessary re-rendering of the entire list.
    - Use **OnPush Change Detection:** Set the ChangeDetectionStrategy to OnPush for your components to reduce the number of times Angular checks for changes, boosting runtime performance.


### What are DDL, DML and DCL?

DDL, DML, and DCL are categories of SQL (Structured Query Language) commands used to manage and interact with relational databases.

**DDL (Data Definition Language):** Manages the structure or schema of the database. DDL commands create, modify, or delete database objects like tables and indexes, but do not affect the data stored within those structures. Changes are generally permanent 

- **Commands**
    - **CREATE:** To create a database or its objects, such as tables, views, or indexes.
    - **ALTER:** To modify the structure of an existing database object, for example, adding or deleting columns in a table 
    - **DROP:** To permanently delete an entire object (e.g., a table) from the database, including all its data and structure
    - **TRUNCATE:** To quickly delete all records from a table while keeping the table structure intact.


**DML (Data Manipulation Language):** Manages the data stored within the database tables. These commands are used by most database users for daily operations like adding, editing, or deleting records.
- **Commands:**
    - **INSERT:** To add new rows or records of data into a table (Programiz).
    - **UPDATE:** To modify existing data within a table (dbt Labs).
    - **DELETE:** To remove specific records from a table (supports the WHERE clause to filter which rows to delete) (Great Learning).
    - **SELECT:** While sometimes classified separately as DQL (Data Query Language), SELECT is often grouped with DML as it interacts with the data. It retrieves data from one or more tables.


**DCL (Data Control Language):** Controls access and permissions to the data in the database. These commands manage the rights and privileges of database users and are typically used by database administrators.
Commands:
**GRANT:** To provide specific users with access privileges or permissions on database objects.
**REVOKE:** To remove or withdraw previously granted access privileges from a user.


### What are StoredProcedures, Triggers and Cursors?
Stored Procedures, Triggers, and Cursors are database objects used to manage data and procedural logic. Stored Procedures are reusable, precompiled SQL code blocks called manually. Triggers are special procedures that run automatically upon database events (INSERT, UPDATE, DELETE). Cursors enable row-by-row processing of query results.

**Stored Procedures**
- Stored procedures are prepared SQL code that you can save and reuse, acting like functions in programming.
- Purpose: Encapsulate complex logic, reduce network traffic (run multiple statements at once), and improve performance.
- Execution: Explicitly called using EXECUTE or CALL.
- Use Cases: Automating database tasks, validating data, and security (limiting direct table access).

```sql
-- Creating a stored procedure
CREATE PROCEDURE GetEmployeeById (@EmpID INT)
AS
BEGIN
    SELECT * FROM Employees WHERE EmployeeID = @EmpID;
END;

-- Executing the stored procedure
EXEC GetEmployeeById @EmpID = 101;
```

**Triggers**
- Triggers are specialized stored procedures that execute automatically (or "fire") in response to specific events on a table or view. 
- Purpose: Maintain database integrity, audit changes, and enforce complex business rules.
- Execution: Automatically runs BEFORE or AFTER an INSERT, UPDATE, or DELETE event.
- Key Constraint: Cannot be called directly and are hard to debug. 

**Cursors**
A cursor is a database object used to navigate through a query result set row-by-row, rather than the standard set-based approach of SQL. 
- Purpose: Necessary when complex, procedural processing is needed for each individual record.
- Lifecycle: Must be declared, opened, fetched (to retrieve data), and closed/deallocated.
- Disadvantage: Resource-intensive compared to set-based operations. 


### What is IAM in AWS?

AWS Identity and Access Management (IAM) is a free, centralized web service that securely manages access to AWS resources. It provides fine-grained control over authentication (who is signed in) and authorization (what permissions they have), ensuring only authorized users or services can act within an AWS environment. 

**Key Components and Usage Examples:**

- **Users & Groups:** Creating individual users (e.g., developers) or groups (e.g., admins) to manage security credentials and permissions.
- **Roles:** Assigning temporary permissions to trusted entities, such as allowing an EC2 instance to read from an S3 bucket without embedding credentials.
- **Policies:** Defining JSON documents that explicitly grant or deny access to AWS resources, enforcing the principle of least privilege.
- **Multi-Factor Authentication (MFA):** Adding an extra layer of security to user accounts. 


### What is EC2?

Amazon Elastic Compute Cloud (EC2) provides secure, resizable virtual servers (instances) in the AWS cloud, allowing users to *run applications, host websites, and process data without managing physical hardware*. It offers flexible computing capacity with various instance types optimized for CPU, memory, or storage, allowing scaling up or down in minutes. 

**Key Aspects of Amazon EC2**

- **Instances:** Virtual machines (e.g., Linux or Windows) that provide full root access, allowing you to install any software.

- **Instance Types:** Categories designed for different workloads, including General Purpose, Compute Optimized, Memory Optimized, Accelerated Computing, and Storage Optimized.

- **Pricing Models:**
    - **On-Demand:** Pay for capacity by the second, ideal for unpredictable workloads.
    - **Reserved Instances:** Commit to a 1–3 year term for up to 75% savings.
    - **Spot Instances:** Utilize unused AWS capacity for up to 90% savings, suitable for fault-tolerant tasks.
- **Security:** Uses Security Groups to act as virtual firewalls to control inbound and outbound traffic.
- **Scalability:** Elastic nature allows for automatic scaling to meet demand.

**Key Features and Tools**

- **Amazon Machine Images (AMIs):** Pre-configured templates for instances (OS, applications).
- **Amazon EBS:** Durable, block-level storage volumes for EC2 instances.
- **Auto Scaling:** Automatically adds or removes instances based on demand.
- **Elastic IPs:** Static IPv4 addresses designed for dynamic cloud computing. 

EC2 eliminates the need to invest in hardware upfront, allowing faster development and deployment of applications.


### What is ECS

Amazon Elastic Container Service (ECS) is a *highly scalable, fast container management service provided by AWS.* It allows users to easily *run, stop, and manage Docker containers on AWS*, supporting both serverless infrastructure with AWS Fargate and managed EC2 instances. It simplifies container orchestration, removing the need for complex cluster management.

**Key Aspects of Amazon ECS:**

**Cluster Management:** ECS clusters are logical groupings of tasks or services, capable of running on AWS Fargate (serverless) or Amazon EC2 instances.

**AWS Fargate:** Enables running containers without managing servers, handling infrastructure capacity planning and security isolation.

**Networking Options:** Supports多种 networking modes including VPC (default, high isolation), Bridge (internal docker network), Host (direct mapping), and None.

**Integration:** Integrates with AWS services like Elastic Load Balancing, Amazon RDS, and AWS IAM for security.

**Components:** Core components include Tasks (running containers), Services (defined task count), and Clusters.

**ECS Anywhere:** Allows running and managing containers on on-premises infrastructure. 


### What is CI CD?

CI/CD stands for Continuous Integration and Continuous Delivery (or Deployment), a DevOps practice that automates the steps required to build, test, and release software. It enables developers to frequently push code changes to a central repository, allowing for automatic testing and reliable, fast software delivery to users.

**Key Components**

- **Continuous Integration (CI):** Developers frequently merge code changes into a shared repository (like GitHub or GitLab). This triggers automated builds and tests, ensuring the new code integrates smoothly and bugs are found early.
- **Continuous Delivery (CD):** Automated testing continues, and approved code changes are automatically prepared for release to a staging or production environment. This requires manual authorization to push to production.
- **Continuous Deployment (CD):** An extension of delivery where every change that passes all automated tests is automatically deployed to production, requiring no human intervention to reach the final user.

**Key Benefits of CI/CD**

- **Faster Release Rates:** Automated testing and deployment allow teams to deliver updates, features, and fixes faster.
- **Higher Quality Code:** Automated tests detect errors, regressions, and bugs early in the cycle, leading to more stable software.
- **Reduced Risk:** Smaller, more frequent updates make it easier to isolate problems, reducing the danger of large, failed releases.
- **Improved Efficiency:** Automation eliminates manual tasks, allowing developers to focus on writing code rather than managing builds and deployments. 