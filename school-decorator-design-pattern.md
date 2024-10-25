Intent

Usage

Decorator Definition

Benefits

Example

Step 1: Definition of Component Interface

Step 2: Defining Abstract Decorator (Could be Interface as well)

Step 3: Defining Concrete Decorator

Step 4: Another Decorator

Step 5: Concrete Components → Part of Core Program

Step 6: More Components

Step 7: Using Decorator

# Intent

Decorator pattern is used when we want to add properties to an existing object. One way to do this is through inheritance where we create a class with the implementation of extended features.

- Problem:
    - we must create a new class for every possible combination of features
    - what happens if a user wants a combination of features that is not implemented?

- Solution:
    - Decorator Pattern
    - ==Formal Def:== decorator pattern allows for **wrapping of objects** in order to modify their **existing responsibility and behaviours**
    - it allows us to add additional responsibilities to objects dynamically
    - creates classes, that implement additional features, and uses them as wrappers for the core program components thereby extending functionality dynamically

## Usage

- GUI components:
    - textView that we want to add: scroll bars (horizontal, vertical, etc), borders, etc.
        
        ![[/Untitled 76.png|Untitled 76.png]]
        

# Decorator Definition

![[/Untitled 1 53.png|Untitled 1 53.png]]

Main Elements:

- `**Component Interface**` - defines interface for objects that need their features to be added dynamically
- `**Concrete Component**` - implements the `Component` Interface
- `**The Decorator**`
    - implements `Component` interface
    - aggregates reference to `Component` → decorator essentially wraps the `Component`
    - one or more `Concrete Decorators` extend `Decorator`

NOTE: `Decorate`could be Abstract class

# Benefits

- flexible alternative to sub-classing to extend functionality
- allows behavior modification at **runtime**
- solves problem of many permutations of features → wrap core Component with any number of decorators
- Supports: Classes should be open of extension but closed for modification

# Example

![[/Untitled 2 43.png|Untitled 2 43.png]]

## Step 1: Definition of Component Interface

```Java
public interface Widget{
	void display();
}
```

## Step 2: Defining Abstract Decorator (Could be Interface as well)

```Java
abstract class Decorator implements Widget{  // Realizes Widget Interface
	Widget widget;
	public Decorator(Widget W){
		widget = w; // aggregation
	}
}
```

## Step 3: Defining Concrete Decorator

```Java
public class Border extends Decorator { // inherits from Decorator
	private int thickness;

	public Border(Widget w, int thick) {
		super(w); // call to Decorator ctor
		thickness = thick;
	}

	@Override
	public void display() {
		widget.display();
		System.out.print(". It's border thickness is: " + thickness);
	}
}
```

## Step 4: Another Decorator

```Java
public class ScrollBar extends Decorator { // inherits from Decorator
	private String type;

	public ScrollBar(Widget w, String st) {
		super(w); // call to Decorator ctor
		type = st;
	}

	@Override
	public void display() {
		widget.display();
		System.out.print(". It's scrollbar type is: " + type);
	}
}
```

## Step 5: Concrete Components → Part of Core Program

```Java
class Text implements Widget { // Realizes Widget
	protected int length;
	String text;

	public Text( String s) {
		text = s;
		length = text.length();
	}

	public void display() {
		System.out.print("This is a plain text: " + text + ", and its length is: " + length);
	}
}
```

## Step 6: More Components

```Java
public class WrappedText extends Text { // Inherits from Text
	private int width, lines;

	public WrappedText (String s, int w, int h) {
		super(s);
		width = w;
		lines = length / width;
	}

	public void display() {
		System.out.print("This is a wrapped Text: " + text + ", and its length is: " 
			+ length + " Its width is " + width + " and its height is: " + lines);
	}
}
```

```Java
public class ThreeDText extends Text {
	protected int depth;

	public ThreeDText(String s, int d){
		super(s); depth = d;
	}

	public void display() {
		System.out.print("3-D text, " + length + "character long " + depth + "pixel depth");
	}
}
```

## Step 7: Using Decorator

```Java
public class DemoDecorator {
	public static void main(String[] args) { 
		Widget t = new Text ("TXT");
		t.display();
		System.out.println();
		t = new Border(t, 2); // Polymorphism??
		t.display();
		System.out.println();
		t = new Scrollbar(t, "vertical"); // Polymorphism??
		t.display();
		// ASSUME MORE CODE TO ADD MORE DECORATORS 
	}
}
```

![[/Untitled 3 38.png|Untitled 3 38.png]]

- the functionality of `Border` and `ScrollBar` are added to `Text` at runtime