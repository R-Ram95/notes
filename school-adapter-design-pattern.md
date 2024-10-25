[[school]]
# Intent

Adapter pattern is used to allow incompatible classes to work together. An example is if we have legacy software that we want to use, but it's methods are un-useable by the client. Here is the general UML class diagram:


**Target** - defines an interface that is useable by the client

**Adaptee** - has methods that we want to use, but it's interface is not useable by the client

**Adapter** - wraps the adaptee and implements (Realization) the methods defined by the target, i.e. the methods (interface) that the client is able to use. Each method that we want to use from the adaptee will be used within the adapters methods.

## General Template

```Java
class Adaptee {
	// this is a method that we would like to use
	legacyMethod(){
	}

}

interface Target{
	clientMethod(); // this method is uesable by the client
}

class Adapter implements Target{
	private Adaptee adaptee

	public Adapter(Adaptee adaptee){
		this.adaptee = adaptee; // aggregation but could be association if we pass Adaptee
														// into the clientMethod() argument
	}
	// this class MUST implement clientMethod()
	clientMethod(){
		// in this method we can call the legacy methods
		adaptee.legacyMethod
	}

}

class Client{
	userAdapter(){
		Target x = new Adapter(); // Dependency => Target class is instantiated in method of Client
		x.clientMethod(); // we indirectly call the legacy methods from here
	}
}
```

# Example:

- legacy code for line and rectangle
- client needs to use the code, but it's interface does not match

## Step 1: Legacy Classes (Adaptees)

These are the legacy classes that the client needs to use

```Java
class Line {
	public void display(int x1, int y1, int x2, int y2){
		System.out.print("Coordinates of line are: (" + x1 + "," 
				+ y1 + "), and (" + x2 + "," + y2 + ")"));
	}
	}
```

```Java
class Rectangle {
	public void display(int x, int y, int width, int height) {
		System.out.print("Coordinates of the Left-corner are (" + x + "," + y +
			"), width: " + width + ", height: " + height);
	}
}
```

## Step 2: Creating Target Interface

This class defines the interface that the client is able to use

```Java
interface Target 
{
	void display(int x, int y, int z, int w, String color);
}
```

## Step 3: Creating an Adapter for class Line

This class connects the clients interface to the legacy classes methods through it's association/aggregation relationship with the Legacy class

```Java
class LineAdapter implements Target {
	private Line adaptee;

	public LineAdapter(Line line) 
	{
		this.adaptee = line; // aggregation
	}

	@Override
	public void display(int x1, int y1, int x2, int y2, String color) 
	{
		adaptee.display(x1, y1, x2, y2); // legacy class methods called here
		System.out.println(" and its Color is: " + color);
	}
}
```

- Note how the legacy classes methods are called within the implementation of the display() method which makes up the clients interface

## Step 4: Adapter for Rectangle Class

```Java
class RectangleAdapter implements Target {
	private Rectangle adaptee;

	public RectangleAdapter(Rectangle rectangle) {
		this.adaptee = rectangle;
	}

	@Override
	public void display(int x, int y, int z, int w, String color) {
		adaptee.display(x, y, z, w);
		System.out.println(" and its color is: " + color);
	}
}
```

## Step 5: Testing

- this is the client class
- NOTE how it does not explicitly use the Rectangle and Line classes

```Java
public class AdapterDemo {
	public static void main(String[] args) 
	{
		Target[] shapes = {new RectangleAdapter(new Rectangle()),
											new LineAdapter(new Line())};

		int x1 = 10, y1 = 20;
		int x2 = 30, y2 = 60;
		for (Target shape : shapes) {
			shape.display(x1, y1, x2, y2, "Red");
		}
	}
}
```
