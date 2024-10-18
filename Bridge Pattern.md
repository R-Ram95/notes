# Intent

The bridge pattern is used to decouple abstraction from implementation in order to change or extend each independently. An interface is separate from the functionality of concrete classes from interface implementers

- the structure of both can be altered without effecting the other

## Key Elements

**Abstraction -** the core element, it is an abstract class with a reference to the implementor

**Refined Abstraction** - derivation of Abstraction → provides finer details and hides them from implementor

**Implementor** - interface of implementation classes

**Concrete Implementation** - implements the implementor

## UML Class Diagram

![[/Untitled 78.png|Untitled 78.png]]

# Example

## Implementor

```Java
interface Fill{
	public void fill(String f);
}
```

## Concrete Implementors

```Java
class Color implements Fill{
	public void fill(String f){
		System.out.println("Fills with color" + f);
	}
}

class Pattern implements Fill{
	public void fill(String p){
		System.out.println("Flls with pattern" + p);
	}
}
```

## Abstraction

```Java
abstract class Widget{
	protected f: Fill; // reference to Implementor
	abstract public string draw();
}
```

## Refined Abstraction

- these classes inherit from the abstract class → have access to the Implementor

```Java
class TextBox extends Widget{
	public string draw(){
		return ("Fill textbox with" + f.fill());
	}
}
```

```Java
class DialogBox extends Widget{
	public string draw(){
		return ("Fill textbox with "+ f.fill());
	}
}
```

  

[[Bridge Pattern - Usage]]