[design pattern is awesome]


#设计模式
[TOC]

- 创建型
- 结构型
- 行为型

##Creational Patterns ==创建型==

###builder**生成器**
The Builder Creational Pattern is used to separate the construction of a complex object from its representation so that the same construction process can create different objects representations.
####problem
we want to construct a complex object,however wo do not want have a complex constructor member or one that would need more arguments.
####solution
Define an intermediate object whose member functions define the desired object part by part before the object is available to the client.Builder Pattern lets ue defer the construction of the object until all the options for creation have been specified.
#####Example 1
```cpp
#include <iostream>
#include <string>
#include <memory>
using namespace std;
//"Product"
class Pizza
{
public:
	void setDough(const string& dough)
    {
    	m_dough = dough;
    }
    void setSauce(const string& sauce)
    {
    	m_sauce = sauce;
    }
    void setTopping(const string& topping)
    {
    	m_topping = topping;
    }
    void open() const
    {
    	cout << "Pizza with"<< m_dough <<"dough"<< m_sauce
        	<<"sauce and"<< m_topping<<"topping. Mmm" <<endl;
    }
private:
	string m_dough;
    string m_sauce;
    string m_topping;
};

//"Abstract Builder"
class PizzaBuilder
{
public:
	virtual ~PizzaBuilder() {} ;
    Pizza *getPizza*();
    {
    	return m_pizza.get();
    }
    void createNewPizzaProduct()
    {
    	m_pizza = make_unique<Pizza>();
    }
    virtual void buildDough() = 0;
    virtual void buildSauce() = 0;
    virtual void buildTopping() = 0;
protected:
	unique_ptr<Pizza> m_pizza;
};
//----------------------------------------------------------------

class HawaiianPizzaBuilder : public PizzaBuilder
{
public:
	virtual ~HawaiianPizzaBuilder() {};

	virtual void buildDough()
	{
		m_pizza->setDough("cross");
	}
	virtual void buildSauce()
	{
		m_pizza->setSauce("mild");
	}
	virtual void buildTopping()
	{
		m_pizza->setTopping("ham+pineapple");
	}
};

class SpicyPizzaBuilder : public PizzaBuilder
{
public:
	virtual ~SpicyPizzaBuilder() {};

	virtual void buildDough()
	{
		m_pizza->setDough("pan baked");
	}
	virtual void buildSauce()
	{
		m_pizza->setSauce("hot");
	}
	virtual void buildTopping()
	{
		m_pizza->setTopping("pepperoni+salami");
	}
};

//----------------------------------------------------------------
//Director
class Cook
{
public:
	void openPizza()
	{
		m_pizzaBuilder->getPizza()->open();
	}
	void makePizza(PizzaBuilder* pb)
	{
		m_pizzaBuilder = pb;
		m_pizzaBuilder->createNewPizzaProduct();
		m_pizzaBuilder->buildDough();
		m_pizzaBuilder->buildSauce();
		m_pizzaBuilder->buildTopping();
	}
private:
	PizzaBuilder* m_pizzaBuilder;
};

int main()
{
	Cook cook;
	HawaiianPizzaBuilder hawaiianPizzaBuilder;
	SpicyPizzaBuilder    spicyPizzaBuilder;

	cook.makePizza(&hawaiianPizzaBuilder);
	cook.openPizza();

	cook.makePizza(&spicyPizzaBuilder);
	cook.openPizza();
}

```
#####Example 2

```cpp
#include <iostream>
#include <string>

//Car parts
class Wheel{
public:
	int size;
};
class Engine{
public:
	int horsepower;
};
class Body{
public:
	std::string shape;
};

//Final product --- a car
class Car{
public:
	Wheel* wheels[4];
    Engine* engine;
    BOdy* body;
    
    void specification()
    {
    	std::cout << "Body:"<<body->shape << std::endl;
        std::cout << "engine horsepower:"<<engine->horsepower<<std::endl;
        std::cout << "tire size:"<<wheel[0]->size<<"'"<<std::endl;
    }
};

//创建一个车对象的各个部件指定抽象接口
class Builder{
public:
	virtual Wheel* getWheel() = 0;
    virtual Engine* getEngine() = 0;
    virtual Body* getBody() = 0;
};
//构造一个使用Builder接口的对象,控制整个过程
class Director{
	Builder* builder;
    
public:
    void setBuilder(Builder* newBuilder)
    {
    	builder = newBuilder;
    }
    Car* getCar()
    {
    	Car* car = new Car();
        car->body = builder->getBody();
        car->engine = builder->getEngine();
        
        car->wheels[0] = builder->getWheel();
        car->wheels[1] = builder->getWheel();
        car->wheels[2] = builder->getWheel();
        car->wheels[3] = builder->getWheel();
        
        return car;
    }
};

//Concrete Builder for Jeep SUV cars
class JeepBuilder : public Builder
{
public:
	Wheel* getWheel
    {
    	Wheel* wheel = new Wheel();
        wheel->size = 22;
        return wheel;
    }
    Engine* getEngine()
    {
    	Engine* engine = new Engine();
        engine->horsepower = 400;
        return engine;
    }
    Body* getBody()
    {
    	Body* body = new Body();
        body->shape = "SUV";
        return bodyl
    }
};

//Concrete builder for Nissan family cars
class NissanBuilder : public Builder
{
    public:
        Wheel* getWheel()
        {
            Wheel* wheel = new Wheel();
            wheel->size = 16;
            return wheel;
        }

        Engine* getEngine()
        {
            Engine* engine = new Engine();
            engine->horsepower = 85;
            return engine;
        }

        Body* getBody()
        {
            Body* body = new Body();
            body->shape = "hatchback";
        }
};

int main()
{
	Car* car;
    
    Director director;
    
    JeepBuilder jeepBuilder;
    NissanBuilder nissanBuilder;
    
    std::cout << "Jeep" << std::endl;
    director.setBuilder(&jeepBuilder);
    car = director.getCar();
    car->specifications();
    
    std::cout << "Nissan" << std::endl;
    director.setBuilder(&nissanBuilder);
    car = director.getCar();
    car->specifications();
    
    return 0;
    
}

```
###Factory**工厂模式**
A utility class that creates an instance of several families of classes.It can also return a factory for a certain group.The Factory Design Pattern is useful in a situation that requires that creation of many different types of objects,all derived from a common base type.The Factory Method defines a method for creating the objects,which subclassed can then override to specify the derived type that will be created.Thus,at run time,the Factory Method can be passed a description of a desired object(e.g.,string read from user input)and return a base class pointer to a new instance of that object.The pattern works best when a well-designed interface is used for the base class,so there is no need to cast the returned object.
####problem
We want to decide at run time what object is to be created based on some configuration or application parameter.When we write the code,we do not know what class should be instantiated.
####solution
Define an interface for creating an object,but let subclasses decide which class to instantiate.Factory Method lets a class defer instantiation to subclass.In the followinh example,a factory method is used to create laptop or desktop computer objects at run time.
Let's start by defining `Computer`,which is an abstract base class(interface) and its derived classes:`Laptop`and`Desktop`.
#####Example 1
```cpp
class Computer{
public:
	virtual void Run() = 0;
    virtual void Stop() = 0;
    
    virtual ~Computer(){};
};
class Laptop:public Computer
{
public:
	void Run() override {nHibernating = false;}
    void Stop() voerride {nHibernating = true;}
    virtual ~Laptop(){};
private:
	bool mHibernating;
};
class Desktop : public Computer
{
public:
	void Run() override {m0n = true;}
    void Stop() override {m0n = false;}
    virtual ~Desktop(){};
private:
	bool m0n;
};

```
The actual `ComputerFactory` class returns a `Computer`,given a real world description of the object.
```cpp
class ComputerFactory
{
public:
	static Computer *NewComputer(const std::string &description)
    {
    	if(description == "laptop")
        	return new Laptop;
        if(description == "desktop")
        	return new Desktop;
       	return NULL;
    }
};
```
`分析工厂模式设计的优点`
首先，编译上的优势。使用工厂模式，只需要关心接口，而接口在整个应用生命周期里应该保持不变。其次，当需要新建一个类时，使用工厂模式只需要简单的修改传入到工厂的变量，让其支持新建的类型，就可以完成新建类型的实例化。对象的创建和使用分离，从而使得我们只需要修改一点点代码就可以完成新类型的添加。
#####Example 2
```cpp
#include <iostream>
#include <stdexcept>
#include <memory>
using namespace std;

class Pizza{
public:
	virtual int getPrize() const = 0;
    virtual ~Pizza() {};
};

class HamAndMushroonPizza : public Pizza{
public:
	virtual int getPrice() const {return 850;}
    virtual ~HamAndMushroomPizza() {};
}

class DeluxePizza : public Pizza{
public:
	virutal int getPrice() const {return 1050;}
    virtual ~DeluxePizza() {};
};

class HawaiianPizza : public Pizza{
public:
	virutal int getPrice() const {return 1150;}
    virtual ~HawaiianPizza() {};
};

class PizzaFactory{
public:
	enum PizzaType{
    HamMushroom,Deluxe,Hawaiian
    };
    
    static unique_ptr<Pizza> createPizza(PizzaType)
    {
    	switch(pizzaType){
        case HamMushroom: return make_unique<HamMushroom>();//c++ 14
        case Deluxe:      return make_unique<DeluxePizza>();
		case Hawaiian:    return make_unique<HawaiianPizza>();
        }
        throw "invalid pizza type.";
    }
};

void print_information(PizzaFactory::PizzaType pizzatype)
{
	unique_ptr<Pizza> pizza = PizzaFactory::createPizza(pizzatype);
    cout << "Price of "<<pizzatype <<"is"<<pizza->getPrice() <<std::endl;
    
}
int main()
{
	pizza_information(PizzaFactory::HamMushroom);
	pizza_information(PizzaFactory::Deluxe);
	pizza_information(PizzaFactory::Hawaiian);
}
```
###Prototype==原型==
A prototype pattern is used in software development when the type of objects to create is determined by a prototype instance,which is cloned to produce new objects.This pattern is used,for example, when the inherent cost of creating a new object in the standard way(e.g.,using the `new` keyword) is prohibitively expensive for a given application.
####Implementation:
Declare an abstract base class that specifies a pure virtual `clone()` method.Any class that needs a "polymorphic constructor"capability derives itself from the abstract base class,and implements the operation.
#####Example 1
```cpp
#include <ostream>
#include <unordered_map>
#include <string>
#include <memory>
using namespace std;

/*Record is the base Prototype.*/
class Record
{
public:
	virtual ~Record() {};
    virtual void print() = 0;
    virtual unique_ptr<Record> clone() = 0;
}
/** CarRecord is a Concrete Prototype */
class CarRecord : public Record
{
private:
	string m_carName;
	int m_ID;

public:
	CarRecord(string carName, int ID) : m_carName(carName), m_ID(ID)
	{
	}

	void print() override
	{
		cout << "Car Record" << endl
		     << "Name  : "   << m_carName << endl
		     << "Number: "   << m_ID << endl << endl;
	}

	unique_ptr<Record> clone() override
	{
		return make_unique<CarRecord>(*this);
	}
};

/** BikeRecord is the Concrete Prototype */
class BikeRecord : public Record
{
private:
	string m_bikeName;
	int m_ID;

public:
	BikeRecord(string bikeName, int ID) : m_bikeName(bikeName), m_ID(ID)
	{
	}

	void print() override
	{
		cout << "Bike Record" << endl
		     << "Name  : " << m_bikeName << endl
		     << "Number: " << m_ID << endl << endl;
	}

	unique_ptr<Record> clone() override
	{
		return make_unique<BikeRecord>(*this);
	}
};


/** PersonRecord is the Concrete Prototype */
class PersonRecord : public Record
{
private:
	string m_personName;
	int m_age;

public:
	PersonRecord(string personName, int age) : m_personName(personName), m_age(age)
	{
	}

	void print() override
	{
		cout << "Person Record" << endl
			<< "Name : " << m_personName << endl
			<< "Age  : " << m_age << endl << endl;
	}

	unique_ptr<Record> clone() override
	{
		return make_unique<PersonRecord>(*this);
	}
};


/** Opaque record type, avoids exposing concrete implementations */
enum RecordType
{
	CAR,
	BIKE,
	PERSON
};

/** RecordFactory is the client */
class RecordFactory
{
private:
	unordered_map<RecordType, unique_ptr<Record>, hash<int> > m_records;

public:
	RecordFactory()
	{
		m_records[CAR]    = make_unique<CarRecord>("Ferrari", 5050);
		m_records[BIKE]   = make_unique<BikeRecord>("Yamaha", 2525);
		m_records[PERSON] = make_unique<PersonRecord>("Tom", 25);
	}

	unique_ptr<Record> createRecord(RecordType recordType)
	{
		return m_records[recordType]->clone();
	}
};

int main()
{
	RecordFactory recordFactory;

	auto record = recordFactory.createRecord(CAR);
	record->print();

	record = recordFactory.createRecord(BIKE);
	record->print();

	record = recordFactory.createRecord(PERSON);
	record->print();
}
```
#####Example 2
```cpp

```
###Singleton==单例==
The singleton pattern ensures that a class has only one instance and provides a global point of acess to that instance.It is named after the singleton set,which is defined to be a set containing one element.This is useful when exactly one object is needed to coordinate actions across the system.
####Check list
- Define a private static attribute in the "single instance" class.
- Define a public static accessor function in the class.
- Do "lazy initialization"(creation on first use)in the accessor function.//延迟初始化,对象的创建将会延迟到第一次使用该对象。
- Define all constructors to be protected or private.
- Clients may only use the accessor function to manipulate the Singleton.
++Tradional implementation used a static member function of the singleton class,which create a single instance of the Singleton class on the first call,and forever return that instance.The following code exampel illustrate the elements of a C++ singleton class,that simply stores a single string.++

```cpp
class StringSingleton
{
public:
	//some accessor functions for the class.
	std::string GetString() const {return mString;}
    void SetString(const std::string &newStr)
    {
    	mString = newStr;
    }
    //allow acess to the class from anywhere,to get the instance of the class,call StringSingleton::Instance().getString();
    static StringSingleton &Instance()
    {
    	//this line only run once,thus creating the only instace in existence.
    	static StringSingleton *instance = new StringSingleton;
        
    	return *instance;//returns the same instance.
    }
private:
	// We need to make some given functions private to finish the definition of the singleton.
	StringSingleton(){};
    //下面拷贝构造函数和拷贝赋值运算符定义为private，用户代码无法拷贝这个类型的对象，同时，没有函数体，表明只声明但未定义该成员函数，此时友元和成员函数都无法进行拷贝。如果访问该未定义的成员会导致一个链接时（编译阶段）错误。
    StringSingleton(const StringSingleton &old);
    const StringSingleton &operator=(const StringSingleton &old);
    //某些编译器可能未实现私有析构，但是这种析构可以阻止删除这个唯一实例（当我们规定类只能在堆上分配内存时）。
    /*
    补充：当析构函数定义为私有时，阻止了用户在类域外对析构函数的使用，主要表现为：
    禁止用户对此类型的变量进行定义，即禁止了在栈内存创建此类型的对象，要创建此类型的对象，必须使用new在堆上进行（不加new即在栈中分配）。
    禁止用户在程序中使用delete删除此类型的对象，对象的删除只能在类中实现。可以定义一个公有的destory()函数完成内存的释放。
    */
    ~StringSingleton(){};
private:
	std::string mString;
};

```
#####Example 2
```cpp
#include <iostream>
using namespace std;
//Mutex
class Mutex
{

};

class Lock
{
public:
	Lock(Mutex &m):mutex(m){}
    ~Lock(){};
private:
	Mutex & mutex;
};
class Singleton
{
public:
	static Singleton * GetInstance();
    int a;
    ~Singleton(){cout << "In Destructor" << endl;}
private:
	Singleton(int _a):a(_a){cout << "In Constructor" <<endl;}
    
    static Mutex mutex;
    
    Singleton(const Singleton& );
    Singleton& operator=(const Singleton& other);
    
};

Mutex Singleton::mutex;
Singleto* Singleton::GetInstance()
{
	Lock lock(mutex);
    cout << "Get Instance"<<endl;
    
    static Singleton inst(1);
    
    return &inst;
}

int main()
{
	Singleton* Singleton = Singleton::GetInstance();
    cout << "The value of the singleton:"<<singleton->a <<endl;
    return 0;
}
In the above example, the first call to Singleton::GetInstance will initialize the singleton instance. This example is for illustrative purposes only; for anything but a trivial example program, this code contains errors.
```