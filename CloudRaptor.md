### Where is service discovey placed in microservices architecture?

Service discovery is placed between service consumers and providers in microservices, acting as a central registry that maps service names to dynamic IP addresses. It is typically implemented either as a centralized database (e.g., Consul, Eureka) for client-side lookup or integrated directly into infrastructure (e.g., Kubernetes DNS) for server-side routing, enabling dynamic scaling. 

**Key Aspects of Service Discovery Placement:**

- **The Service Registry:** This is the central database or cluster that stores the locations of all active service instances, often implemented as standalone tools like Netflix Eureka, HashiCorp Consul, or distributed systems like ZooKeeper.

- **Client-Side Discovery Pattern:** The client (service consumer) calls the service registry directly to find available instances and performs its own load balancing.

- **Server-Side Discovery Pattern:** The client sends requests through a router or load balancer (e.g., Nginx, Kubernetes ingress). This router queries the service registry and forwards the request to an available instance.

- **Deployment Location:** It is often integrated directly into orchestration platforms such as Kubernetes, AWS, or Azure, which provide built-in service registration and discovery mechanisms, 

### Which service dicovery you have used and it's advantages?

Netflix Eureka integrated with netfkix zuul[API gateway].

Netflix Eureka is a REST-based Service Discovery Server and Client used in microservices architectures for service registration and discovery, primarily enabling instances to find and communicate with each other dynamically. It supports load balancing and failover, with services sending heartbeats to confirm availability. 

**Key Aspects of Netflix Eureka:**

**Service Registry:** A centralized registry where microservices register their network locations (IP, port) upon startup.

**Client-Side Discovery:** Clients query the Eureka server to find available service instances, enabling dynamic scaling without hard-coded addresses.

**Heartbeats & Health Checks:** Registered services send periodic heartbeats; if heartbeats stop, Eureka removes the instance from the registry, improving system robustness.


### What is Netflix zuul?

Netflix Zuul is an open-source, Java-based edge service and API gateway that enables dynamic routing, monitoring, resiliency, and security for microservices. It acts as the front door for all requests, handling cross-cutting concerns like authentication, load balancing, and traffic routing to downstream services.

**Key Features and Functions:**

- **Routing:** Directs incoming requests to appropriate backend services (e.g., AWS Auto Scaling Groups).

- **Filters:** Uses a "Request Context" to execute custom filters (PRE, ROUTE, POST, ERROR) for modifying requests/responses, logging, or security.

- **Resiliency:** Features adaptive retries and origin concurrency protection to prevent service overloads.

- **Zuul 2:** The latest version focuses on non-blocking, asynchronous networking (using Netty) to handle high traffic volumes efficiently, reducing connection churn.

- **Integration:** Commonly used with Netflix Eureka for service discovery and Spring Cloud for integration into Java ecosystems. 

**Common Use Cases:**

- **Authentication:** Securing endpoints before passing requests to backend services.
- **Dynamic Routing:** Routing traffic to different clusters or regions for testing or failover.
- **Streaming/Load Balancing:** Managing API traffic with non-blocking connections for high performance.



### Ehcache and its use?

Ehcache is a widely used, open-source Java-based caching library developed by Terracotta, Inc. that improves application performance by reducing database load. It supports in-process caching (on-heap/off-heap/disk) and distributed caching, acting as a flexible, fast, and scalable solution for Java EE applications and popular frameworks like Spring Boot.

**Key Features of Ehcache 3.x**

- **Storage Tiers:** Offers tiered storage, including on-heap (Java memory), off-heap (RAM), disk storage, and clustered storage.

- **Performance:** Designed to be fast and lightweight, enhancing application speed.

- **Standards-Based:** Fully compliant with the javax.cache API (JSR-107).

- **Cache Eviction:** Supports common eviction policies such as LRU (Least Recently Used), LFU (Least Frequently Used), and FIFO (First-In-First-Out).

- **Integration:** Integrates easily with Spring Boot and Hibernate.

**Using Ehcache in Spring Boot:**

- **Dependency:** Add the org.ehcache:ehcache dependency (often with the jakarta classifier for newer Spring versions).

- **Configuration:** Create an ehcache.xml file to define cache properties, often stored in src/main/resources.

- **Enable Caching:** Use the @EnableCaching annotation in the Spring Boot application class.

- **Usage:** Apply the @Cacheable annotation to methods to cache results. 

### What is BFF? What are its advantages and uses?

A Backend for Frontend (BFF) is a design pattern that creates a dedicated backend service for each specific frontend application—such as desktop, mobile, or smart TV. It acts as an intermediary, optimizing API calls, aggregating data, and handling client-specific logic to improve performance and user experience. 

**Key Aspects of the BFF Pattern:**

**Tailored APIs:** Each client gets an optimized API tailored to its specific needs, preventing over-fetching or multiple calls for data.

**Decoupling:** Decouples frontend teams from backend services, enabling independent development and faster deployments.

**Optimized Performance:** Reduces bandwidth, particularly useful for mobile apps, by simplifying data payloads.

**Enhanced Security:** Allows customized access control and token management tailored to specific user experiences.

**Better Error Handling:** Maps server errors into user-friendly messages rather than exposing low-level system errors.

**Common Use Cases:**

- **Mobile vs. Web:** A mobile app might require only a small subset of user data, while a desktop web application requires a detailed profile.
- **Legacy System Wrapper:** Wrapping old, complex systems to present a clean, modern API for new frontend applications.

**Benefits and Trade-offs:**

**Pros:** Improved developer productivity, faster UI response times, and better security.

**Cons:** Risk of code duplication between different BFFs if not structured properly. 

> BFF is a cornerstone of MACH (Microservices, API-first, Cloud-native, Headless) architecture for modern web applications. 


### What is DB per service and its advantages and disadvantages?

Database-per-service is a design pattern in microservices where each service owns its dedicated database, ensuring data encapsulation and service autonomy. It improves scalability and fault isolation, preventing direct data access by other services. Key drawbacks include complex distributed transactions, challenging cross-service queries, and increased operational overhead. 

**Key Advantages:**

- **Loose Coupling:** Services are independent; changes in one service's database schema do not affect others.

- **Independent Scalability:** Each service can scale its database based on its own traffic and performance needs.

- **Best-Fit Technology:** Services can use different database technologies (SQL vs. NoSQL) suited to their specific data needs.

- **Improved Resilience:** A database failure in one service does not take down the entire system (reduced "blast radius").

**Key Disadvantages:**

- **Distributed Transactions:** Implementing consistency across multiple services requires complex techniques like the Saga pattern.

- **Complex Reporting/Queries:** Joining data spread across multiple databases requires APIs or data aggregation tools, which is complex.

- **Operational Complexity:** Managing numerous databases increases operational effort for backups, monitoring, and maintenance. 

**Implementation Approaches:**

- **Database per service:** Each service has its own dedicated database instance.
- **Schema per service:** Each service has a private schema within a shared database server.
- **Private-table-per-service:** Each service has specific tables within a shared database. 


### How to handle micro/multiple transactions?

Handling microtransactions (high-frequency, low-value) and multiple transactions (distributed across services) requires balancing data consistency with system performance, typically moving away from traditional immediate consistency (ACID) towards eventual consistency and asynchronous processing. 

**Key strategies for handling multiple transactions (distributed) include:**

1. **Saga Pattern (Distributed Transactions)**
The Saga pattern is the preferred method for managing transactions that span multiple microservices, replacing traditional Two-Phase Commit (2PC) which does not scale.
    - **Choreography-based Saga:** Each service produces and listens to events, acting independently. For example, the Order Service creates an order and emits an OrderCreated event, which the Payment Service consumes.
    - **Orchestration-based Saga:** A central orchestrator (e.g., using AWS Step Functions or a custom service) manages the transaction flow and decides which services to call.
    - **Compensating Transactions:** Since sagas do not lock resources, if a step fails, the saga must execute compensating actions to undo previous successful steps (e.g., if payment fails, cancel the reservation). 

2. **Idempotency and Retries**
To handle network failures, retries, and duplicate requests, services must be designed to be idempotent—running the same transaction multiple times results in the same state.

    - **Unique Transaction IDs:** Use a unique ID for every request to identify duplicates.
    - **Retry Mechanisms:** Implement retries with exponential backoff for transient failures. 

3. **Transactional Outbox Pattern**
To prevent inconsistencies between updating a database and publishing an event (e.g., to Kafka/RabbitMQ), use the Outbox pattern. 
    - Steps: 1. Update the local database. 
    - 2. Write the event to an "outbox" table in the same local transaction. 
    - 3. A separate message relay process reads the outbox and publishes the event. 

4. **Handling High-Volume Microtransactions**
For high-traffic, small-value transactions, focus on throughput and reducing locking.
    - **Separate Order and Payment:** Do not process payments within the same transaction as order creation. Keep order creation synchronous for user feedback, but process payments asynchronously.
    - **Async Processing:** Utilize message queues (e.g., Kafka, RabbitMQ) to decouple services and handle high load without blocking.
    - **Eventual Consistency:** Accept that data might not be consistent across all services instantly, allowing for higher availability. 

5. **Other Key Strategies**

**Locking:** Use optimistic or pessimistic locking for limited-stock items (e.g., flash sales) to ensure data integrity.

**TCC Pattern (Try-Confirm-Cancel):** A three-step process to reserve resources (Try), commit (Confirm), or release (Cancel), useful when stronger consistency is required than a SAGA provides. 

### How to manage high transactions per minute in microservices?

Managing high transactions per minute (TPS) in a microservices architecture requires a combination of architectural patterns, infrastructure best practices, and performance optimization. Key strategies include asynchronous processing, aggressive caching, horizontal scaling, and careful transaction management. 

**Architectural Patterns**

- **Event-Driven Architecture (EDA):** Use asynchronous communication with message queues (like RabbitMQ or Kafka) to decouple services. This allows non-critical tasks to be offloaded to background workers, freeing up primary request threads and improving API response times.

- **Saga Pattern:** For complex distributed transactions that span multiple services, use the saga pattern to maintain data consistency. This involves a sequence of local transactions coordinated by events or a central orchestrator, with compensating actions to roll back changes if a step fails.

- **CQRS (Command Query Responsibility Segregation):** Separate read and write operations to optimize them independently. This can improve performance and scalability by allowing the use of different data stores or optimization strategies for queries versus commands.

- **Idempotency and Retry Mechanisms:** Design operations to be idempotent, meaning they can be retried without causing unintended side effects (e.g., duplicate payments). Implement retry logic with exponential backoff for transient network or service failures. 

**Infrastructure and Scaling**

- **Horizontal Scaling:** Deploy multiple instances (pods/VMs) of your microservices across different servers or availability zones to distribute the load. Orchestration tools like Kubernetes can automate the deployment, scaling, and management of these containers based on demand.

- **Load Balancing:** Use load balancers (like NGINX or cloud-specific options like AWS ALB) at every layer to distribute incoming traffic evenly across service instances and prevent bottlenecks.

- **Connection Pooling:** Implement database and HTTP connection pooling to significantly reduce the overhead of repeatedly establishing and closing connections, which is critical under high load.

- **Rate Limiting:** Implement rate limiters to control the number of requests a client can make within a specific timeframe, preventing individual users or systems from overwhelming the service. 

**Performance Optimization**

- **Caching:** Aggressively cache frequently accessed data using in-memory stores like Redis or Memcached. This minimizes direct database calls and drastically reduces latency.
- **Optimize Data Access:** Tune database queries, use appropriate indexing, and consider sharding (horizontal partitioning) for high-write workloads to distribute the database load.
- **Efficient Communication:**
    - Minimize "chatty" communication patterns that involve many small inter-service calls.
    - Use efficient data serialization formats like Protocol Buffers (protobuf) or Avro instead of JSON for faster, more compact data exchange.
    - Consider protocols like gRPC for high-performance inter-service communication.

- **JVM Tuning:** For services running on the Java Virtual Machine, optimize settings like heap size and garbage collection strategy (e.g., using G1GC or ZGC for lower pause times) based on load testing results.

**Monitoring and Resilience**

- **Monitoring and Tracing:** Use tools like Prometheus or OpenTelemetry for comprehensive monitoring, logging, and distributed tracing. This helps identify performance bottlenecks and track transactions across multiple services.
- **Circuit Breakers and Timeouts:** Implement circuit breaker patterns to prevent cascading failures. If a service is slow or failing, the circuit breaker stops further requests to that service and can provide a fallback response, protecting the overall system.
- **Design for Failure:** Assume failures will happen. Build fault tolerance into each step of a workflow and have clear error recovery strategies, such as retry mechanisms or compensating transactions.

### MongoDB basic queries?

These are the fundamental methods used in the MongoDB shell (mongosh) for data retrieval. 
- **db.collection.find()**: Retrieves all documents that match the specified filter and returns a cursor.
    - All documents: db.collection.find({}) or simply db.collection.find(). Adding .pretty() formats the output for better readability: db.collection.find().pretty().
    - Specific condition (Equality): db.collection.find({ field: "value" }). This finds documents where the field exactly matches the value.
- **db.collection.findOne()**: Retrieves the first document that matches the given criteria, or returns null if no document is found.
    - First document: db.collection.findOne({}) or db.collection.findOne().
    - Specific condition: db.collection.findOne({ author: "Alen" }).

**Common Query Operators**
MongoDB uses operators (prefixed with $) for advanced filtering, including comparison **($eq, $ne, $gt, $lt, $in)** and logical operators **($or, $and, $not)**. The $exists operator checks for field presence.

### What parameters will you look for before selecting a database?

1. **Data Structure and Type**
- **Structured Data (Relational):** If your data is highly structured, requires schema rigidity, and needs complex joins (e.g., financial systems), opt for RDBMS like PostgreSQL or MySQL.
- **Semi-structured/Unstructured Data (NoSQL):** If your data is hierarchical, evolving, or lacks a fixed schema (e.g., content management), consider MongoDB (document-based).
- **Interconnected Data:** If your application relies on deep relationships (e.g., social networks), a graph database like Neo4j is suitable.
- **Time-Series Data:** Use specialized databases like InfluxDB or Prometheus for data generated over time, such as IoT sensor data or logs.

2. **Workload and Performance Needs**
- **Read vs. Write Heavy:** Assess if your application is mostly reading data (e.g., content apps) or writing data (e.g., logging systems). Key-value stores like Redis are ideal for low-latency, fast reads/writes.
- **Query Complexity:** If complex analytics and reporting are required, RDBMS are generally better. For high-throughput simple lookups, NoSQL is superior.
- **Latency Requirements:** In-memory databases are necessary for sub-millisecond responses.

3. **Scalability and Growth** 
- **Vertical Scaling (Scale-up):** Adding more CPU/RAM to a single server. Common for RDBMS.
- **Horizontal Scaling (Scale-out):** Distributing data across multiple servers. NoSQL databases (e.g., Cassandra, DynamoDB) are designed for this.
- **Expected Growth:** Analyze the projected data volume and traffic load (QPS - Queries Per Second) over the next 1–5 years.

4. **Data Consistency and Transactions (ACID vs. BASE)**
- **Strong Consistency (ACID):** Required for financial or transactional systems (e.g., PostgreSQL). Transactions are ACID (Atomicity, Consistency, Isolation, Durability) compliant.
- **Eventual Consistency (BASE):** Suitable for scenarios where immediate data accuracy is less critical than availability, such as social media feeds (e.g., Cassandra).

5. **Deployment, Cost, and Maintenance**
- **Managed Services (PaaS):** Using cloud-managed databases (e.g., Amazon RDS, MongoDB Atlas) reduces operational overhead regarding backups and patching, though they may have higher long-term costs.
- **Self-Hosted:** Lower licensing fees but higher staffing and maintenance costs.
Total Cost of Ownership (TCO): Includes licensing, infrastructure, and training costs. 

6. **Security and Compliance**
- **Security Features:** Evaluate support for encryption at rest and in transit (SSL/TLS), and robust role-based access control (RBAC).
- **Compliance:** Ensure the database meets required regulations such as GDPR, HIPAA, or PCI DSS.

7. **Ecosystem and Community Support**
- **Driver Compatibility:** Ensure the database supports your programming language (e.g., Python, Node.js).
- **Community Support:** A large, active community provides better troubleshooting, documentation, and tools

### ACID vs CAP theorem?

ACID principles (Atomicity, Consistency, Isolation, Durability) are a set of database properties that guarantee reliable transaction processing, ensuring data integrity even during failures. These four properties ensure that all parts of a transaction succeed or fail together (Atomicity), the database remains valid (Consistency), concurrent transactions don't interfere (Isolation), and completed data is permanent (Durability).

**The Four ACID Principles Defined**

- **Atomicity:** Treats a transaction as a single "all or nothing" unit. If any part of the transaction fails, the entire transaction fails, and the database remains unchanged. For example, in a bank transfer, money is both debited from one account and credited to another, or neither happens.

- **Consistency:** Ensures that a transaction brings the database from one valid state to another, maintaining all predefined rules, constraints, cascades, and triggers. It guarantees that the database adheres to structural integrity.

- **Isolation:** Ensures that concurrent execution of transactions leaves the database in the same state as if they were executed sequentially. This prevents intermediate, uncommitted data from being visible to other transactions, avoiding issues like double-booking a seat.

- **Durability:** Guarantees that once a transaction has been committed, it will remain committed, even in the case of a system failure, power loss, or crash. Data is recorded in non-volatile memory. 

**Importance and Best Practices**

- ACID properties are essential for applications requiring high data accuracy, such as banking systems, inventory management, and online ordering. To maintain these principles effectively: 
- Use ACID for critical data: Ideal for systems where transaction errors cannot be tolerated.
- Break down long transactions: Shorter transactions consume fewer resources.
- Leverage DBMS tools: Most relational databases like PostgreSQL, MySQL, and Oracle provide ACID compliance out of the box.
- Configure error handling: Set up proper retry mechanisms for failed transactions.

ACID is frequently contrasted with the BASE model (Basically Available, Soft state, Eventual consistency), which prioritizes availability over immediate consistency. 

BASE (Basically Available, Soft state, Eventual consistency) is a database consistency model for distributed systems that prioritizes high availability and partition tolerance over immediate consistency. Unlike ACID, BASE accepts temporary data inconsistencies, ensuring the system remains functional during heavy load or network failures.

**Core Principles of BASE**

**Basically Available (BA):** The system guarantees that data is available and nodes continue to operate even during failures or partial network outages.
**Soft State (S):** The state of the system may change over time without input due to data replication and updates propagating throughout the network.
**Eventual Consistency (E):** The system will eventually become consistent across all nodes once data has stopped changing, allowing temporary inconsistencies for better performance. 

**BASE vs. ACID**

**ACID (Traditional/SQL):** Focuses on "pessimistic" strict consistency, ensuring immediate, perfect data accuracy.

**BASE (NoSQL/Distributed):** Focuses on "optimistic" availability, favoring speed, scalability, and resilience.

**Common Use Cases**

- Social Media Feeds: Facebook updates your feed continuously without requiring a hard refresh.
- E-commerce/Streaming: Netflix reduces video quality rather than stopping playback during high load.
- Distributed Storage: Cassandra, MongoDB, and DynamoDB utilize BASE for high-volume, global systems. 

### Caching mechanism types?

Caching mechanisms are categorized by where they reside in the system, how they interact with the data source, and how they decide which data to remove. 

**1. Types by System Layer:**

    Caching can occur at various points between the user and the final data source to reduce latency and server load.

- **Client-Side / Browser Cache:** Stores static assets (images, CSS, JS) locally on the user's device.
- **CDN (Content Delivery Network) Cache:** Distributed servers store content geographically closer to users to speed up delivery.
- **DNS Cache:** Stores IP addresses of previously visited domain names to avoid repeated lookups.
- **Application / In-Memory Cache:** Stores frequently accessed data (like user sessions or API responses) in the application's RAM using tools like Redis or Memcached.
- **Database Cache:** Built-in buffers within the database or external layers that store query results to avoid expensive disk reads.
- **CPU Cache:** Extremely high-speed hardware memory (L1, L2, L3) located directly on or near the processor chip to bridge the speed gap with RAM.

**2. Caching Strategies (Read/Write Patterns)**

    These define how an application manages data flow between the cache and the permanent data store.

- **Cache-Aside (Lazy Loading):** The application checks the cache first; if the data isn't there, it fetches it from the database and updates the cache for next time.
- **Read-Through:** The application only communicates with the cache. On a miss, the cache itself retrieves data from the database and returns it.
- **Write-Through:** Data is written to both the cache and the database simultaneously, ensuring consistency but adding write latency.
- **Write-Back (Write-Behind):** Data is written to the cache first and then persisted to the database asynchronously after a delay.
- **Write-Around:** Data is written directly to the database, bypassing the cache entirely; it is only cached when later requested and missed. 

**3. Eviction Policies**

    Because cache memory is limited, these algorithms decide which data to remove when the cache is full. 

- **LRU (Least Recently Used):** Removes the item that hasn't been accessed for the longest time.
- **LFU (Least Frequently Used):** Removes the item accessed the least number of times.
- **FIFO (First-In, First-Out):** Removes the oldest item based on when it was first added.
- **TTL (Time-to-Live):** Data is automatically expired after a predefined time period.

### MemCache and Redis advandages and uses?

Redis and Memcached are top in-memory NoSQL data stores providing sub-millisecond latency. Redis is best for complex data structures, persistence, and high availability (pub/sub, sorted sets). Memcached shines in simple, high-throughput caching (small strings/integers) using a multi-threaded architecture. Redis is generally preferred today for its versatility.

> Memcached

**Advantages:**
- **Simplicity:** Highly efficient and easy to deploy for simple key-value caching.
Performance: Excellent for read-heavy workloads due to multi-threaded architecture.
- **Memory Management:** Efficient memory usage for smaller, static data, say Medium.

**Best Uses:**

- Caching simple data like database queries, HTML fragments, or user sessions.
When high-speed performance is required without the need for persistence.
- Horizontal scaling for small object caching. 

> Redis

**Advantages:**
- **Rich Data Structures:** Supports strings, hashes, lists, sets, sorted sets, bitmaps, and spatial indexes, say GeeksforGeeks.
- **Persistence & Replication:** Offers data persistence (dumping to disk) and high-availability master-slave replication.
- **Flexibility:** Supports pub/sub messaging, Lua scripting, and transactions.

**Best Uses:**
- **Real-time Analytics:** Leverages sorted sets to manage leaderboards and real-time data, say LinkedIn.
- **Session Management:** Stores session data securely with persistence options.
- **Message Queues:** Utilizes lists for robust queueing systems.
- **Geospatial Data:** Useful for location-based apps.

### How to write a custom query in Spring Data JPA?

A. JPQL (Java Persistence Query Language)

Using Named Parameters (Recommended): Use the @Param annotation for clarity and to prevent SQL injection vulnerabilities.

```java

public interface UserRepository extends JpaRepository<User, Long> {
    @Query("SELECT u FROM User u WHERE u.firstName = :firstName AND u.email = :email")
    List<User> findByFirstNameAndEmail(@Param("firstName") String firstName, @Param("email") String email);
}
```

Using Positional Parameters: Parameters are referenced by their position in the method signature (e.g., ?1 for the first parameter).

```java
    @Query("SELECT u FROM User u WHERE u.firstName = ?1 AND u.email = ?2")
    List<User> findByFirstNameAndEmailPositional(String firstName, String email);
```

B. Native SQL Queries

You can execute native SQL queries by setting the nativeQuery attribute of the @Query annotation to true. Note that native queries are database-specific.

```java
public interface UserRepository extends JpaRepository<User, Long> {
    @Query(value = "SELECT * FROM users WHERE first_name = :firstName", nativeQuery = true)
    List<User> findUsersByFirstNameNative(@Param("firstName") String firstName);
}
```

**Modifying Queries (INSERT, UPDATE, DELETE)**

To perform write operations, you must add the @Modifying annotation in addition to @Query. These operations should be used within a transactional context.

```java
public interface UserRepository extends JpaRepository<User, Long> {
    @Modifying
    @Transactional // Usually needed for modifying operations
    @Query("UPDATE User u SET u.email = :newEmail WHERE u.firstName = :firstName")
    int updateEmailByFirstName(@Param("newEmail") String newEmail, @Param("firstName") String firstName);
}
```

**Pagination and Sorting**

You can add Pageable or Sort parameters to your custom query methods to enable pagination and dynamic sorting.

```java
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;

public interface UserRepository extends JpaRepository<User, Long> {
    @Query("SELECT u FROM User u ORDER BY u.lastName")
    Page<User> findAllUsersOrderedByLastName(Pageable pageable);
}
```

### How to save the data in differenct DBs when multiple database connection are there?

To save data in different databases when multiple connections are present in Spring Boot, you must define separate configuration classes for each data source, entity manager, and transaction manager, and then use @Qualifier annotations to link the correct repository to its respective database configuration. 

**Step-by-Step Guide**

- **Configure Database Properties:** Define the connection properties for each database in your application.properties or application.yml file, using distinct prefixes for each data source (e.g., spring.datasource.customer and spring.datasource.product).
- **Create Entity and Repository Packages:** Organize your entities and repositories into separate, database-specific packages. This is a crucial step for Spring to distinguish which configurations to apply.
- **Create Configuration Classes:** For each database, create a dedicated @Configuration class to manually set up the necessary beans:
    - **DataSource:** Use @ConfigurationProperties with the relevant prefix to create the data source bean.
    - **EntityManagerFactory:** Configure the LocalContainerEntityManagerFactoryBean and specify the packagesToScan for its associated entities.
    - **PlatformTransactionManager:** Create the transaction manager bean linked to its entity manager factory.
    - Use **@EnableJpaRepositories** on each configuration class, pointing to the base package of the repositories for that specific database and referencing the **corresponding entityManagerFactoryRef and transactionManagerRef.**
    - Annotate one of the configurations with **@Primary** to make it the default, while the others will be secondary.
- **Use Repositories in Services:** In your service layer, inject the specific repository using the @Qualifier annotation with the exact name of the configuration bean (e.g., customerEntityManager or productEntityManager) to ensure the correct data source is used for persistence operations.

Can one FI extends/implement another FI? Yes

Why only one method in FI? 

Can we use functional interface as normal interface? Yes

### Diamond problem in java and how to overcome?

The diamond problem is an ambiguity that arises in programming languages that support multiple inheritance when a class inherits the same method from multiple parent classes, creating a conflict over which implementation to use. 

Java prevents the diamond problem for classes by disallowing multiple inheritance with the extends keyword. A Java class can only extend a single parent class, which ensures a clear, linear inheritance hierarchy and avoids ambiguity in method resolution. 

**The Problem in Detail (Hypothetical Scenario)**

- The problem is named after the diamond shape of the class hierarchy diagram it creates: 

- Class A (Grandparent) has a method show().

- Class B and Class C (Parents) both inherit from A and override the show() method differently.

- Class D (Child) attempts to inherit from both B and C.
When a Class D object calls show(), the compiler doesn't know whether to execute 

- B's version or C's version, leading to ambiguity and a compiler error. 
Java's Solution: Interfaces and Resolution Rules

> While Java classes can only have single inheritance, the language supports multiple inheritance of type through interfaces. The introduction of default methods in Java 8 allowed interfaces to contain method implementations, which reintroduced the potential for the diamond problem in a different form. 

Java handles this potential conflict with explicit resolution rules: 

- **Classes Always Win:** A method implementation in the implementing class or any of its superclasses takes priority over a default method in an interface.

- **Most Specific Interface Wins:** If the conflict is between two interfaces, and one extends the other (making it more specific), the method from the more specific interface is used.

- **Explicit Override Required:** If two or more unrelated interfaces provide conflicting default methods, the Java compiler throws an error, and the developer is forced to explicitly override the method in the implementing class to specify which version to use (or provide a new implementation). This can be done using the `InterfaceName.super.methodName()` syntax. 

By design, Java prioritizes safety and predictability, forcing the developer to resolve any potential ambiguity explicitly, rather than allowing the compiler or runtime to make an arbitrary choice.
