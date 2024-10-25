
# Intent

Object called `Subject` maintains a list of dependents called `Observers` and automatically notifies them of any state changes such as changes to subjects data.

# Problems Solved

A one-to-many dependency between objects where the subject notifies dependent objects directly is not flexible because it couples the subject to particular dependent objects. Observer pattern fixed this:

- one-to-many dependency between objects is defined without making objects tightly coupled
- when one object changes state any number of dependent objects are updated automatically
- one object can notify any number of other objects

# Usage

- usage in GUI applications:
    
    ![[/Untitled 80.png|Untitled 80.png]]
    
    - any changes in `Subject` data members a, b, or c will be translated in the three observer views
- usage in notification system:
    
    ![[/Untitled 1 56.png|Untitled 1 56.png]]
    
    - `JobSeeker` observes the `HeadHunter` for available jobs
        - `JobSeeker` is notified when jobs are added
    - `JobSeeker` is responsible for registering and unregistering itself
    
    - Can a JobSeeker be subscribed to multiple Subjects?

# How it works

**Observer** - object that watches the state of another object

Responsible for:

- register for notifications from subject
- unregister for notifications from subject
- update their state when notified by subject

**Subject** - object that allows objects to observer it

Responsible for:

- maintaining a list of observers
- notify them of state changes by calling `observer.update()` method

## Step 1)

Create `Observer` interface with an `update()` method

## Step 2)

Create an interface or abstract class for `Subject` that containts methods to `add()` or `remove()` observer objects

## Step3)

Create a class that implements `Subject`

## Step4)

Create one or more class that implements `Observer`

  

# Example

![[/Untitled 2 44.png|Untitled 2 44.png]]

- why don't horizontal view and vertical view show aggregation with Subject?

## Step 1)

```Java
public interface Observer{
	public void update(double data);
}
```

## Step 2)

Create an interface or abstract class of Subject

```Java
interface Subject{
	public void register(Observer o);
	public void remove(Observer o);
	public void notifyObserver();
}
```

## Step 3)

Class to implement Subject

```Java
class Weather implements Subject {
	private double temp;
	private ArrayList <Observer> observers;

	public Weather(double t) {
		observers = new ArrayList<Observer>();
		temp = t;
	}

	public void register(Observer o) {
		observers.add(o); // association
		o.update(temp);
	}

	public void remove(Observer o) {
	}

	public void notifyObserver() {
		for(int i = 0; i < observers.size(); i++)
		{
			Observer o = observers.get(i);
			o.update(temp);
		}
	}

	public double getTemp(){
		return temp;
	}

	public void setTemp(double t){
		temp = t;
		notifyObserver();
	}
}
```

## Step 4)

Classes to implement observer

Horizontal Display

```Java
class HorizontalDisplay implements Observer {
	double temp;
	Subject weather;

	public HorizontalDisplay(Subject w) {
		weather = w;
		weather.register(this); // aggregation
	}

	@Override
	public void update(double temp) {
		this.temp = temp;
		display();
	}

	public void display(){
		// code to display horizontally
	}
}
```

```Java
class VerticalDisplay implements Observer, {
	double temp;
	Subject weather;

	public VerticalDisplay(Subject w) {
		weather = w; // aggregation
		weather.register(this);
	}

	@Override
	public void update(double temp) {
		this.temp = temp;
		display();
	}

	public void display(){
		// code to display vertically
	}
}
```

## Step 5)

Create client class to use observers

```Java
public class Cient {
public static void main(String []s) {
	Weather w = new Weather(34.5);
	HorizontalDisplay h = new HorizontalDisplay(w);
	VerticalDisplay v = new VerticalDisplay(w);
	w.setTemp(55);
	h.display(); // displays horizontally
	v.display(); // displays vertically
	}
}
```
