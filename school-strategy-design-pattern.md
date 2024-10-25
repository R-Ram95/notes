# Intent

The strategy pattern and bridge pattern are similar, but differ on their intent. The intent of the **Strategy pattern** is to **separate, or decouple, changeable behaviours** from the static portions of the program. The intent of the **Bridge pattern is to separate, or decouple, abstraction from implementation.**

- The Strategy pattern provides a method to define a family of algorithms, encapsulate each one as an object, and make them interchangeably used by a client.

## Approach

- Identify aspects/behaviors that are subject to change
- create an interface for each changeable behavior
- create a class for each interface that only implements that interface

## General UML Class Diagram

- Context uses the Strategy interface
- Each variation of a behaviour is implemented in Strategy


## General Template in Java

Strategy Interface

```Java
Interface Strategy{
	public void doSomething(); // definition of changable method
}
```

Realization: Implementing Strategy Interface:

```Java
class Strategy1 implements Strategy{
	public void doSomething(){
		// Implement the algorithm here
	}
}

class Strategy2 implements Strategy{
	public void doSomething(){
		// Implement the algorithm here
	}
}

class Strategy2 implements Strategy{
	public void 3 doSomething(){
		// Implement the algorithm here
	}
}
```

Context: Using the Strategy Classes

```Java
class Context {
	Strategy str;
	public Context() {
		// code to initialize data members
	}
	
	public void setStrategy(Strategy s) {
		// s can be Strategy1 or Strategy2
		str = s;
	}
	public void performStrategy() {
		str.doSomething();
		}
}
```

- The Context class can choose which strategy to implement and it implement multiple strategies
- Strategies can be added without effecting the code of the Context class

  

# Example

- Game for children with core classes: Animal, Cat, Dog, Fish
- functionality includes: walk, swim, etc.

## UML Class Diagram

- Animals movement is changeable so it is an interface
- Types of movements Realize the interface

## Core Package

Context

```Java
abstract class Animal {
	
	// these are all inherited by the children
	protected double weight;
	protected String name;
	protected MoveStrategy moveStyle;

	public Animal(double wi){
		weight = wi;
		name = "";
	}

	public double getWeight() {
		return weight;
	}

	public void setWeight(double weight) {
		this.weight = weight;
	}
	
	// method to set the strategy
	public void setMoveStrategy(MoveStrategy m) {
		moveStyle = m;
	}
	
	// method to perform the strategy
	public void performMove() {
		moveStyle.move(name);
	}
}
```

```Java
class Cat extends Animal {
	public Cat(double w) {
		super(w);
		name = "Cat";
		// attribute inherited from Animal class
		moveStyle = new Walker ();
	} 
}

class Dog extends Animal {
	public Dog(double w) {
		super(w);
		name = "Dog";
		moveStyle = new Walker ();
	}
}

class Bird extends Animal { 
	public Bird(double w) {
		super(w);
		name = "Bird";
		moveStyle = new Flyer ();
	} 
}

class Fish extends Animal {
	public Fish(double w) {
		super(w);
		name = "Fish";
		moveStyle = new Swimmer ();
	}
}
```

## Strategy Package

Interface:

```Java
interface MoveStrategy {
	// defines the movement
	abstract void move(String s);
}
```

  

Implementing the interface

Each of the following have different implementation of movement

```Java
class Walker implements MoveStrategy {
	public void move(String s) {
		System.out.println("Walking" + " " + s );
	}
}


class Flyer implements MoveStrategy {
	public void move(String s) {
		System.out.println("Flying" + " " + s);
	}
}

class Swimmer implements MoveStrategy {
	public void move(String s) {
		System.out.println("Swimming" + " " + s);
	}
}
```

## Using Strategy Pattern

```Java
public class Game {
	public static void main (String [] s) {
		Cat kitty = new Cat(100);
		//prints - Walking Cat 
		kitty.performMove();

		Bird birdy = new Bird(400);
		//prints Flying Bird
		birdy.performMove();

		Fish fishy = new Fish(20);
		// prints Swimming Fish
		fishy.performMove();
	}
}
```

  

## Adding a New Animal

```Java
// The new movement
class Jumper implements MoveStrategy{
	public void move(String s){
		System.out.println("Jumping" + "" + s);
	}
}

class Cricket extends Animal{
	public Cricket(double w){
		super(w);
		name = "Cricket";
		moveStyle = new Jumper();
	}
}
```

- did not have to modify the core program → simply extended it

  

Can select a movement at runtime:

```Java
public class Game 
{
	public static void main (String [] s) 
	{
		Cricket fast = new Cricket(5);
		// prints- Jumping Cricket
		fast.performMove();
		
		// change movement of Cricket
		fast.setMoveStrategy(new Walker());
		// prints - Walking Cricket:
		fast.performMove();
	}
}
```

# Pros and Cons

## Pros

- creates a family of algorithms that are reusable (the classes that implement the interface)
- changeable behaviour is not hardcoded into the context
- code is cleaner and can reduce complexity of selecting a behaviour at runtime
- can provide different implementation of the same behaviour

## Cons

- communication overhead → lots of classes talking to each other
- memory overhead → creates more objects in memory
