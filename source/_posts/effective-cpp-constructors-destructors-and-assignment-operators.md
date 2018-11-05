---
title: Constructors, Destrustors and Assignment Operators, Notes(2), Effective C++
categories:
  - Doing
  - CPP
  - 
tags:
  - 
  - 
date: 2017-06-15 21:20:10
toc: true

---
(I read a Chinese version of the book, any translation problem plz point out. 

<!--- more --->

## Constructors, Destructors and Assignment Operator

### Know what functions CPP silently writes and calls
For empty class, the complier will declare a copy constructor, a copy assignment operator, a destructor, **if no any constructor is declared, one more default constructor is declared by compiler**.
They are all public and inlined, and **when called, they will be created (during compiling period)**.

### Explicitly disallow the use of compiler-generated functions you do not want

* declare as private with no definition.
  when member function or friend function calls, **a linkage error** will be reported.
```cpp
class HomeForSale {
public:
    // ...
private:
    HomeForSale(const HomeForSale&);
    HomeForSale& operator=(const HomeForSale&);
};
```

* **earlier? -> compiling period**
```cpp
class Uncopyable {
protected:
    Uncopyable() {}
    ~Uncopyable() {}
private:
    Uncopyable(const Uncopyable&);
    Uncopyable& operator=(const Uncopyable&);
};

class HomeForSale: private Uncopyable {
    // ...
};
```
**why?**
anyone tries to copy `HomeForSale`, the compiler will try to generate a copy constructor and a copy assignment operator, then "compiler-generated version" tries to call the ones in base classes respectively, and it will be refused due to the privateness of copying functions in base classes.

* Boost have one class `noncopyable` similarly that mentioned above.
* CPP11 new feature, `=delete` 

### Declare vitual destructors in polymorphic base classes.

* CPP points out specificly, it is a **undefined behavior** when a object of derived class is deleted by a pointer of base class, which has a non-virtual destructor.
* Any class that has a virtual function should have a virtual destructor.
* A class with no virtual destructor mustn't be used as a base class, such as STL containers. In other words, **if some class is not designed for using as a base class, it shouldn't declare a virtual destructor**.
* Declare a pure virtual destrutor when creating an abstract class without any other pure virtual functions.
```cpp
class AWOV {
public:
    virtual ~AWOV() = 0;
};
AWOV::~AWOV() {}
```

### Prevent exceptions from leaving destructors
**When 2 exceptions exist at the same time, the program either aborts or results in a undefined behavior**.

**double insurance**
if customer need to response to the exceptions which was thrown by the run-time of some function, the class should provide a normal function (other than handled in the destructor).
```cpp
class DBConn {
public:
    void close() {
        db.close();
        closed = true;
    }
    ~DBConn() {
        if(!closed) {
            try {
                db.close();
            }
            catch (...) {
                // log, then abort or swallow the exception
            }
        }
    }
private:
    DBConnection db;
    bool closed;
};
```

### Never call virtual functions during construction or destruction
* virtual functions never downcast to derived classes, when base classed is constructing.
* once called, it is a **undefined behavior** because the members haven't been initialized yet.
* **if needed, declare as non-virtual**
 constructors of derived classes passes the parameters to the ones of base classes (static functions will also avoid the problem).
```cpp
class Transaction {
public:
    explicit Transaction(const std::string& logInfo);
    void logTransaction(const std::string& logInfo) const; // non-virtual
};
Transaction::Transaction(const std::string& logInfo) {
    logTransaction(logInfo);
}

class BuyTransaction: public Transaction {
public:
    BuyTransaction( ... )
        : Transaction(createLogString( ... ))
    {}
private:
    static std::string createLogString( ...);
);
```

### Assignment operator
* Have assignment operators return a reference to `*this`
* Handle assignment to self
  **copy and swap** technique
```cpp
Widget& Widget::operator=(const Widget& rhs) {
    Widget temp(rhs);
    swap(*this, temp);
    return *this;
}
```

### Copy all parts of an object
* compiler may generate no warnings or errors when you implement your own copy constructors or copy assignment operators
```cpp
class Customer {
public:
    Customer(const Customer& rhs);
    Customer& operator=(const Customer& rhs);
private:
    std::string name;
};

class PriorityCustomer: public Customer {
public:
    PriorityCustomer(const PriorityCustomer& rhs);
    PriorityCustomer& operator=(const PriorityCustomer& rhs);
private:
    int priority;
};

PriorityCustomer::PriorityCustomer(const PriorityCustomer& rhs)
    : Customer(rhs),
      priority(rhs.priority) {}

PriorityCustomer& PriorityCustomer::operator=(const PriorityCustomer& rhs) {
    Customer::operator=(rhs);
    priority = rhs.priority;
    return *this;
}
```

* **Do not try to use some copying function to implement another one**.
  If you wanna avoid code duplicate, try to introduce a new function, maybe called `init()` to be called by the two copying functions.