# Polymorphism

`virtual` used to enable late binding when derived class inherits from parent

In this example, the `display()` method for both the base and derived class have the same signature. The compiler binds the implementation of `display()` based on the **static type** of the object, rather than the **dynamic type**. The static type is `Person` and so the compiler uses the `Person.display()` implementation even though the dynamic type of `arr[0]` is `Student`. This is called **early binding** using the keyword `virtual`, we get dynamic binding where the implementation of the display method is based on the **dynamic type** of the object.

e.g.

```C++
class Person
{
public:
    void display() { cout << "I am a person\n"; };
    virtual void display() { cout << "I am a person\n"; }; // LATE BINDING
};

class Student : public Person
{
public:
    void display() { cout << "I am a student\n"; };
};

int main()
{
    Person *arr[2];
    Student s = Student();
    arr[0] = &s;
    arr[0]->display();
    cout << "Expected: 'I am a student'\n";
}
```

- The `virtual` keyword enables polymorphism which allows the implementation of methods to be bound to objects at run time, much like `@overloading` in Java.
- It is a good idea to make the `dtor` virtual ⇒ why?
    
    - when we call `delete p` we call `A`'s dtor which removes `s1` from memory, `s2` is not removed from memory and we get a memory leak
    - this is due to early-binding b/c the static type of p is `A` but the dynamic type is `B`
    
    ```C++
    class A{
    	char* s1;
    	public:
    		A(int n){s1 = new char[n];}
    		~A(){delete[] s1;}
    };
    
    class B: public A{
    	char* s2;
    	public:
    		B(int n, int m): A(n){s2 = new char[m];}
    		~B(){delete[] s2;}
    }
    
    int main(){
    	A* p = new B(5, 6); // static type is A
    	delete p; // calls A's dtor => does not remove s2 from mem => mem leak
    }
    ```
    

# Abstract Classes

- keyword: `pure virtual`
- if a class has a `pure virtual` method, the class will be an `abstract class`
- `pure virtual` methods in the Base class do not need an implementation but they do in Derived classes
    
    syntax:
    
    ```C++
    virtal method_name() = 0
    ```
    

# Interfaces

- interfaces are classes that **only** have pure virtual methods
- e.g.
    
    ```C++
    class Resizeable{
    	virtual void enlarge() = 0;
    	virtual void shrink() = 0;
    }
    ```
