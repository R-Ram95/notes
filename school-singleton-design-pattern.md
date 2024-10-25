Intent

Defintion

Singleton Pattern Interactions

Example

Step 1: Create Singleton class

Benefits and Drawbacks

# Intent

- Singleton Pattern is a design pattern that we can use to control access to shared resources
    - e.g. login process, debugging a shared resource

# Defintion

- Ctor is `private` → user should not be able to create instances of singleton pattern
- Self reference → Singleton class has private class data member of `Singleton` type
- Static method access to private member field

Java version


C++ version

NOTE that the assignment op is required here for when we set the static instance of singleton

# Singleton Pattern Interactions

- client 1 and 2 have access to the **SAME** Singleton object

# Example

## Step 1: Create Singleton class

```Java
public class Singleton {
	
	// class data member
	private static Singleton onlyInstance;
	// resources
	private ArrayList<String> usernameList;
	private ArrayList<String> passwordList;
	private ArrayList<String> nameList;
	
	// User has no access 
	private Singleton(){
		usernameList = new ArrayList<String>();
		passwordList = new ArrayList<String>();
		nameList = new ArrayList<String>();
	}

	public static Singleton getOnlyInstance() {
		// there can only be one instance
		if(onlyInstance == null)
			onlyInstance = new Singleton(); // we would need to define assignment op in C++ for this
		return onlyInstance;
	}

	// 
	public static void setOnlyInstance(Singleton onlyInstance) {
		SingletonLogin.onlyInstance = onlyInstance;
	}

	public void addUsername(String username) {
		usernameList.add(username);
	}

	public void setUsename(int index, String newUsername){
		usernameList.set(index, newUsername);
	}

	public void removeUsename(int index, String newUsername){
	}

}
```

```Java
public class DemoSingletoPattern {
	public static void main(String[] args) {
		// client 1 and 2 have access to the same Singleton object
		
		Singleton c1 = Singleton.getOnlyInstance(); // singleton object created here
		c1.addName(“Jack Lemon");
		c1.addUsername(“jlemon");
		c1.addPassword(”jl1234");

		Singleton c2 = Singleton.getOnlyInstance(); // singleton object accessed by c2 here
		c2.addName(”Merry Leu");
		c2.addUsername(mleu);
		c2.addPassword(“orange1234);
	}
}
```

# Benefits and Drawbacks

- useful for using a single copy of a shared resources

  

Drawbacks:

- Singleton cannot be subclasses
- Reduces testing flexibilities
    - advice: minimize dependencies between classes
