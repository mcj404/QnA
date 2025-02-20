# OOPS

### Q. How do you know whether to make a class, a subclass, an abstract class, or an interface?

- Make a class that doesn't extend anything (other than Object) when your new class doesn't pass the IS-A test for any other type.
- Make a subclass (extend a class) only when you need to make a more specific version of a class and need to override or add new behaviours.
- Use an abstract class when you want to define a template for a group of subclasses, and you have at least some implementation code that all subclasses could use. Make the class abstract when you want to guarantee that nobody can make objects of that type.
- Use an interface when you want to define a role that other classes can play, regardless of where those classes are in the inheritance tree.



### Q. What is object caching?
A. It optimizes performance and memory by caching values of certain range.

Cache range Table

| Wrapper Class | Primitive Class | Cached Value Range |
|---------------|-----------------|--------------------|
| Byte          | byte            | -128 to 127        |
| Short         | byte            | -128 to 127        |
| Integer       | int             | -128 to 127        |
| Long          | long            | -128 to 127        |
| Character     | char            | \u0000 to \u007F   |
| Boolean       | boolean         | true or false       |


	Why only -128 to 127?
	-> Small integers are used frequently in programs
	-> Optimize performance & memory in large apps
	-> 0, 1 and -1
		Loops
		Array Indices
		Basic Arithmetic
		
	Advantages
		-Reduced memory usage
		-Re-using instances improves performance
		-New Objects are expensive
		-Time and resources are saved		
		
		"==" compares the reference of the variable.
		
		NOT APPLICABLE when objects are created using the 'new' keyword. It will create a new object.
		
		Auto-boxing internally calls .valueOf() method.
		valueof() method applies caching.

```java
Integer a = 1;
Integer b = 2;
SOP(a==b);       //true


Integer x = 1000;
Integer y = 1000;
SOP(x==y);        //false
```

### Q. What is Cohesion and coupling? What is the relation between them?

| Cohesion                                                                                                                                                                             | Coupling                                                                                                     |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| Cohesion is defined as the degree to which the elements of a particular module are functionally related. Basically, cohesion is used to measure the functional strength of a module. | Coupling is defined  as the degree to which the two modules are dependent on each other. It measures the strength of relationships between modules. |
| Types of Cohesion <br/> - Functional <br/> - Sequential<br/> - Logical <br/> - Structural <br/>                                                                                                                      | Types of Coupling <br/> - Data <br/> - Content <br/> - Control                                               |



### Q. What is the purpose of garbage collection?

Garbage collection is a process that automatically frees memory by identifying the objects which are no longer referenced or needed by a program so that their resources can be reclaimed and reused. These identified objects will be discarded.


### Q. What is the difference between a stack and a heap?

The Stack is a LIFO (Last In First Out) structure, while the Heap is a FILO (First In Last Out) structure.


### Q. What is dynamic class loading?

→ Dynamic loading is a technique for programmatically invoking the functions of a class loader at run time. <br/>
→ Let us look at how to load classes dynamically by using Class.forName (String className); method, it is a static method. The above static method returns the class object associated with the class name. The string className can be supplied dynamically at run time. <br/>
→ Once the class is dynamically loaded the class.newInstance () method returns an instance of the loaded class. It is just like creating a class object with no arguments. <br/>
→ A ClassNotFoundException is thrown when an application tries to load in a class through its class name, but no definition for the class with the specified name could be found on the classpath. <br/>


<style>
h1 {
  color: #3498db;
  text-align: center;
}

h3 {
  color: #072AD0FF;
}

code {
  background-color: #f4f4f4;
  border-radius: 4px;
  padding: 2px 4px;
}
</style>