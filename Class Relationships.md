---
tags:
  - C/C++
  - Fundamentals
  - Java
---
**Object Composition** - complex objects are built from simpler ones. There are two types: 1) Composition 2) Aggregation

**Composition** - whole-part relationship, complex class manages the existence of its member classes

- part(member) is part of object (class)
- part(member) can only belong to one object (class) at a time
- part(member) has its existence managed by the object(class)
- part(member) does not know about the existence of the object(class)

**Aggregation** - has-a relationship between class and member. Class does not manage the existence of the members.

- part(member) is part of the object
- part can belong to more than one object at a time
- part does not have it existence managed by the object
- part does not know that the object exists

**Association** - uses relationship between classes

- associated object is unrelated to the object
- associated object can belong to more than one object at a time
- associated object does not have its existence managed by the object
- associated object may or may not know about the existence of the object

**Dependency** - one class uses another to perform a task. The dependent class is not a member of the class that uses. It is temporarily created, used, then destroyed or passed into a member fcn from external source.

**Association vs. Dependency**

- association is linked at the class-level ⇒ one class holds a reference to another in its data members
- dependency is one the object being depended on is not linked as a data member. Dependended object is created as needed

  

**Composition vs. Aggregation in C++**

`class Engine`

```C++
class Engine{
	private:
		int size;
	public:
		Engine (int size){this->size = size;}
}
```

`composition`

```C++
class Car{
	private:
		Engine engine;
	public:
		Car(int size): engine(size)

}
```

- car creates engine

```C++
class Car{
	private:
		Engine* engine;
	public:
		Car(int size){
			engine = new Engine(size);
		}

}
```

- Car dynamically allocates mem for Engine

`aggregation`

```C++
class Car{
	private:
		Engine* enginge;
	public:
		Car(Engine* e){
			this->engine = e;
		}
}
```

- Engine is created elsewhere are an assigned in car

**Association Example**

doctor has reference too all patients, patient has reference to all doctors

```C++
class Patient; // we make call to Patient in Doctor

class Doctor{
	private:
		string m_name;
		vector<reference_wrapper<const Patient>> m_patients; // reference to patients
	public:
		Doctor(const string& name):m_name(name);
		void addPatient(Patient& patient); // need def of patient to implement
		const string& get_name() const{return m_name;}
}

class Patient{
	private:
		string m_name;
		vector<reference_wrapper<const Doctor>> m_doctors;
		void addDoctor(const Doctor& doctor){
			m_doctors.push_back(doctor);
		}
	public:
		Patient(const string& name): m_name(name);
		const string& get_name() const{return m_name;}
}

void Doctor::addPatient(Patient& patient){
	this->addPatient(patient);
	patient.addDoctor(*this);
}
```