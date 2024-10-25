# Abstraction

- hiding complexity from users
- e.g. user can use a coffee machine without understand how it works ⇒ the user simply needs to know how to interact with the coffee machine (interface of coffee machine)

# Inheritance

- mechanism by which a class inherits the methods and attributes of its super class
- specifiers limit what methods and attributes can be inherited ⇒ the table below shows this concept in Java

|   |   |   |   |   |
|---|---|---|---|---|
|Modifier|Class|Package|Subclass|Other classes|
|Private|==Yes==|==No==|==No==|==No==|
|No Specifier|==Yes==|==Yes==|==No==|==No==|
|Protected|==Yes==|==Yes==|==Yes==|==No==|
|Public|==Yes==|==Yes==|==Yes==|==Yes==|

# Encapsulation

Reference:

> [!info] What is Encapsulation exactly?  
> Asked This question already has answers here: Closed 7 years ago.  
> [https://stackoverflow.com/questions/28612420/what-is-encapsulation-exactly](https://stackoverflow.com/questions/28612420/what-is-encapsulation-exactly)  

- bundling data together with methods that are related to it
- allows us to quickly see what data members are available and what we can do with them
- prevents developers from re-writing and duplicating methods
- Makes information hiding possible
    - information hiding is when we restrict access to data members within a particular scope (private, protected, public)
    - if we make data members private, but don’t package the methods together with the data, we would not be able to access it anywhere

## Example

WITHOUT ENCAPSULATION Represent a complex number like:

```Java
class Complex{
	double real;
	double imaginary;

}
```

Say we wanted the absolute value of Complex, we can define a function

```Java
double absolute(doubl real, double imaginary)
```

To calculate the complex number:

```Java
Complex A;
A.real = 1;
A.imaginary = -2;

// Call the method
absolute(A.real, A.imaginary)
```

WITH ENCAPSULATION ⇒ the absolute method is encapsulated within the complex class along with the real and imaginary data

```Java
class Complex{
	double real;
	double imaginary;
	double absolute();
}
```

To calculate the absolute value

```Java
A.absolute();
```

# Polymorphism

- **Dynamic Binding (aka late binding, runtime binding)** - method call is bound to the correct implementation at run time (by the JVM in java)
- **Static Binding** - Java compiler resolves association between a method call and the methods implementation at compile time
- In Java, all methods use dynamic binding except for `final` and `private` methods
- **static type** - objects type at declaration
- **dynamic type** - objects type when method calls are made (different than static type if object is reassigned)

Three kinds of Polymorphism in Java

1) overriding inherited methods

- methods that are over ridden have different behaviour based on the dynamic type of the objects they are called on
    
    In the example below, obj.toString() has different outputs based on the dynamic type of the object at the time. The JVM looks for an implementation of in the dynamic type class at run time
    
    ```Java
    class Base {
    	public String toString(){
    		return "Base Class";
    	}
    }
    
    class Derived extends Base{
    	@Override
    	public String toString(){
    		return "Derived Class";
    	}
    }
    
    public static void main(String[] args){
    	// every class in java is extended from Object
    	Object obj;
    	obj = new Base();
    	obj.toString() // "Base Class"
    	obj = new Derived();
    	obj.toString() // "Derived CLass"
    }
    ```
    

2) implementing abstract methods

- methods inherited from an abstract class are implemented in the subclass. The inherited method implementations are called at runtime
- in the example below, `Cat` and `Cow` are subclasses of the `Abstract Animal class`, they must implement the `speak()` method. At run time, the implementations of `speak()` are dynamically bound to `animal` object based on the dynamic type
- **Advantage of polymorphism:** provides extensibility of Base classes without having to redefine and recompile them ⇒ subclasses can simply be created and implemented

```Java
public abstract class Animal(){
	// accesible to all subclasses
	protected String kind;

	public Animal(){}
	
	public String toString(){
		return "I am a " + kind + " and I go " + speak();
	}
	
	// to be implemented by subclasses
	public abstract String speak(){}
}

public class Cow extends Animal{
	public Cow(){
		kind = "Cow";
	}
	
	// must implement this method
	public String speak(){
		return "Moo";
	}
}

public class Cat extends Animal{
	public Cat(){
		kind = "Cat";
	}

	public String speak(){
		return "Meow";
	}
}

public class Test{
	public static void main(String[] args){
		Animal animal = new Cow();
		System.out.println(animal); // output: I am a Cow I go Moo
		animal = new Cat();
		System.out.println(animal); // output: I am a Cat I got Meow
	}
}
```

3) implementing Java interfaces

_**What is an interface?**_

- interfaces provide a way (in the form of functionality) for objects to interact with other objects that require specific functionality ⇒ Interfaces give an object that is not a _Wizzler_ the ability to _Wizzle_ for objects that require a _Wizzler_

Classes to implement an interface get that Interface as one of their types, for example, the Cat is both an `Animal` AND a `Speakable`

```Java
public class Cat extends Animal implements Speakable{
	...
}
```

- Implementations of methods from classes that implement interfaces are bound at runtime
    
    - note that `Animal` does not have a `speak()` method which is why the cast is required in the `toString()` method
    
      
    
    ```Java
    public interface Speakable{
    	public String speak();
    }
    
    public class Animal{
    	protected String kind;
    	
    public String toString(){
    	
    	return "I am a " + kind + " and i go " ((Speakable)this).speak();
    }
    
    }
    
    public class Cat extends Animal implements Speakable { 
    	public Cat() { kind = ”cat”; }
    	public String speak() { return ”meow”; }
    }
    
    public class Cow extends Animal implements Speakable {
    	public Cow() { kind = ”cow”; }
    	public String speak() { return ”moo”; }
    }
    
    Animal animal = new Cow(); 
    System.out.println(animal.toString()); // A cow goes moo 
    animal = new Cat();
    System.out.println(animal.toString()); // A cat goes meow
    ```
    
    - `((Speakable)this).speak()` casts the calling object to a speakable so that it can use the `speak()` method
    
      
    

[[Testing-Library]]
