---
title: Inheritance and Object-Oriented Design, Notes(6), Effective C++
categories:
  - Doing
  - CPP
  - 
tags:
  - 
  - 
date: 2017-09-11 17:22:00
toc: true

---

## Inheritance and Object-Oriented Design
(I read a Chinese version of the book, any translation problem plz point out. 

<!--- more --->

### Make sure public inheritance models "is-a"

```cpp
class Bird {
    // ...
};

class FlyingBird: public Bird {
public:
    virtual void fly();
    // ...
};

class Penguin: public Bird {
    // ...
};
```

public inheritance means everything must be suitable for derived class if it is suitable for base class,
because every derived class object is a base class object.

**is a square a rectangle?**

### Avoid hiding inherited names

CPP name-hiding rules:
all the functions of base class with the same name as derived class will be hiden,
even if the parameter lists is different. it is both suitable for **virtual and non-virtual functions**.

CPP name-lookup rules:
local -> derived -> namespace of derived -> base -> namespace of base -> global

but if you use public inheritance without inheriting the overloaded functions, it violates the "is-a" relation between base and derived class.

* `using` declaration
  introduce all the functions of specific name of base class to derived class,
  it is ok for public inheritance.
```cpp
class Base {
private:
    int x;
public:
    virtual void mf1() = 0;
    virtual void mf1(int);
    virtual void mf2();
    void mf3();
    void mf3(double);
};

class Derived: public Base {
public:
    using Base::mf1;
    using Base::mf3;
    virtual void mf1();
    void mf3();
    void mf4();
};
```

what if for partially inheriting the functions?

* forwarding function
  use for private inheritance.
```cpp
class Base {
public:
    virtual void mf1() = 0;
    virtual void mf1(int);
};

class Derived: private Base {
public:
    virtual void mf1() { // Forwarding function
        Base::mf1();     // Implicitly inline
    }
};
```

### Differentiate between inheritance of interface and inhertiace of implementation

* pure virtual functions: only inherit interface.
* impure virtual functions: inherit interface and a default implementation.
* non-virtual functons: inherit interface and a forced implementation. invariant far more than specialization.

### Consider alternatives to virtual functions

* use **Non-Virtual Interface** to implement **Template Method** pattern.
**advantages**: do something before and after the operation, like mutex, log entry, validation of constraints.
```cpp
class GameCharacter {
public:
    // This non-virtual function is a wrapper for the virtual function.
    int healthValue() const { 
        // ...
        int retVal = doHealthValue();
        // ...
        return retVal;
    }
private:
    virtual int doHealthValue() const {
        // ... 
    }
};
```
* use **Function Pointers** to implement **Strategy** pattern.
```cpp
class GameCharacter;
int defaultHealthCalc(const GameCharacter& gc);
class GameCharacter {
public:
    typedef int (*HealthCalcFunc)(const GameCharacter&);
    explicit GameCharacter(HealthCalcFunc hcf = defaultHealthCalc)
                            : healthFunc(hcf) {}
    int healthValue() const { return healthFunc(*this); }
private:
    HealthCalcFunc healthFunc;
};
```

**advantages**:
different entities of same type can have different function pointers.
function pointers can be changed in run-time.
**disadvantages**:
once needing to access the non-public members, you have to weaken the encapsulation of class.
```cpp
class EvilBadGuy: public GameCharacter {
public:
    explicit EvilBadGuy(HealthCalcFunc hcf = defaultHealthCalc)
                            : healthFunc(hcf) {}
};

int loseHealthQuickly(const GameCharacter&);
int loseHealthSlowly(const GameCharacter&);

EvilBadGuy ebg1(loseHealthQuickly);
EvilBadGuy ebg2(loseHealthSlowly);
```

with `std::function`:
call accept all the callable entities **compatible** with target signature (**implicit conversion**).
with `std::bind`:
bind a member function with an object.

```cpp
class GameCharacter;
int defaultHealthCalc(const GameCharacter& gc);

class GameCharacter {
public:
    typedef std::function<int (const GameCharacter&)> HealthCalcFunc;  
    explicit GameCharacter(HealthCalcFunc hcf = defaultHealthCalc)
                            : healthFunc(hcf) {}
    int healthValue() const { return healthFunc(*this); }
private:
    HealthCalcFunc healthFunc;
};

class EvilBadGuy: public GameCharacter {
    // ...
};

short calcHealth(const GameCharacter&);
struct HealthCalculator {
    int operator()(const GameCharacter&）const {}
};
class GameLevel {
public:
    float health(const GameCharacter&) const;
};

EvilBadGuy ebg1(calcHealth); // Function pointer
EvilBadGuy ebg2(HealthCalculator()); // Functor

GameLevel currentLevel;
EvilBadGuy ebg3(std::bind(&GameLevel::health, currentLevel, std::placeholders::_1)); 
```

* classical **Strategy** pattern (with virtual functions)
make `HealthCalcFunc` be a seperate hierarchy of inheritance.
```cpp
class GameCharacter;
class HealthCalcFunc {
public:
    virtual int calc(const GameCharacter& gc) const { }
} defaultHealthCalc;

class GameCharacter {
public:
    explicit GameCharacter(HealthCalcFunc* phcf = &defaultHealthCalc)
                            : pHealthFunc(phcf) {}
    int healthValue() const { return pHealthFun->(*this); }
private:
    HealthCalcFunc* pHealthFunc;
};
```

### Never redefine an inherited non-virtual function

### Never redefine a function's inherited default parameter value
* virtual functions is dynamically bound, but default parameter values is statically bound.
* static type is the type declared, and dynamic type is the type pointed to currently.
* use **NVI (non-virtual interface)** to substitute it.

```cpp
class Shape {
public:
    enum ShapeColor { Red, Green, Blue };
    void draw(ShapeColor color = Red) const {
        doDraw(color); 
    }
private:
    virtual void doDraw(ShapeColor color) const = 0;
};

class Rectangle: public Shape {
public:
    // ...
private:
    virtual void doDraw(ShapeColor color) const;
};
```

### Model "has-a" or "is-implemented-in-terms-of" through composition

* composition has many **synonyms**: layering, containment, aggregation, embedding.
* in application domain, composition means "has-a", but in inplementation domain, it means "is-implemented-in-terms-of".

### Use private inheritance judiciously
* private inheritance is a technique of implementation.
* private inheritance means only implementation is inherited and interfaces should be omitted.
* private inheritance is ok, when you want to redefine the inherited virtual functions.  

```cpp
class Timer {
public:
    explicit Timer(int tickFrequency);
    virtual void onTick() const;
};

// Althrough Widget reuse Timer, it exposes `onTick` to the user
class Widget: private Timer {
private:
    virtual void onTick() const;
};

// This one is kind of complicated, but it can prevent from using WidgetTimer
// in the derived classes of Widget, (something like Java `final`, C# `sealed`)
// Once changed to WidgetTimer*, it can also lower the compilation dependency
class Widget {
public:
    class WidgetTimer: public Timer {
    public:
        virtual void onTick() const;
    };
    
    WidgetTimer timer;
};
```

* when facing space optimization, private inheritance may be the best choice.
  empty class: with no non-static variables, no virtual functions and no virtual base classes.
  **EBO (empty class optimization)** will let your base class take no space.

### Use multiple inheritance judiciously

```cpp
class File { // ... };
class InputFile: public File { // ... };
class OutputFile: public File { // ... };
class IOFile: public InputFile, public OutputFile { // ... };
```
* MI will copy the data through each inheritance path (such as `File::name`).
* once not, make the class with the data to be a virtual base class, and all the classes intermediately inherited **virtual inherit** it.

```cpp
class File { // ... };
class InputFile: virtual public File { // ... };
class OutputFile: virtual public File { // ... };
class IOFile: public InputFile, public OutputFile { // ... };
```

* **the initialization of virtual base class is granted to the most derived class**.
* virtual inheritance will increase the cost of size, speed, and initialization(assignment). 
  the virtual base classes with no data will be best-pratical situation.
* MI has some usages: one is the combination of "public inheritance inherits some interface class" and "private inheritance inherits some helper class for implementation".
  `class CPerson: public IPerson, private PersonInfo;`

