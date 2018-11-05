---
title: Designs and Declarations, Notes(4), Effective C++
categories:
  - Doing
  - CPP
  - 
tags:
  - 
  - 
date: 2017-09-04 13:19:00
toc: true

---

## Designs and Declarations
(I read a Chinese version of the book, any translation problem plz point out. 

<!--- more --->

### Make interfaces easy to use correctly and hard to use incorrectly

* introduce a new type.
  `Data d(Month(9), Day(4), Year(2017));`
* restrict some invalid operations on types.
  add `const` is a useful way. 

```cpp
if(a * b = c) // ...

// It will avoid the problem above.
const Type operator*(const Type& a, const Type& b);

```
* predefine all the valid values, if we want type safety.
```cpp
class Month {
public:
    static Month Jan() { return Month(1); }
    static Month Feb() { return Month(2); }
    // ...
    static Month Dec() { return Month(12); }
    // ...
private:
    explicit Month(int m);
    // ...
};
```
* make interfaces be compatible with built-in types, aka. providing the interfaces of consistent behaviors.
* force users to use smart pointers. e.g., factory function returns a smart pointer.
  `std::shared_ptr<Investment> createInvestment();`

### Treat class design as type design
Questions to think:

* create and destroy
* initialize and assign
* pass by value -> copy constructor
* valid values -> invariants(约束) 
* inheritance
* conversion
* functions and operators
* access specifiers
* undeclared interface ???
* generalization -> templates
* really need a new type? maybe some non-member functions or templates achieve the goal.

### Prefer pass-by-reference-to-const to pass-by-value

* avoid any construction and destructions, more efficient.
* avoid the slicing problem brought by derived class upcast to base class.
* pass-by-value is proper for built-in types, e.g., STL iterators and functors.

### Don't try to return a reference when you must return an object
try the one that behave correctly, and throw the responsibility to the compilers.

### Prefer non-member, non-friend functions to member functions  
a natural way, let tool function be a non-member function and put inside the same namespace where the classes it operates is.
then, for future expansion of more tool functions, sperate them in new headers but in the same namespaces.
**increase encapsulation, packaging flexibility, and function expansibility.**

```cpp
// webbrowser.h
// Main functionalities of webbrowser.
namespace WebBrowserStuff {
    class WebBrowser {
    public:
        // ...
        void clearCache();
        void clearHistory();
        void removeCookies();
        // ...
    };
    
    void clearBrowser(WebBrowser& wb) {
        wb.clearCache();
        wb.clearHistory();
        wb.removeCookies();
    }
}

// webbrowser_bookmarks.h
namespace WebBrowserStuff {
    // Some tool functions related to bookmarks. 
    // ...
}

// webbrowser_cookies.h
namespace WebBrowserStuff {
    // Some tool functions related to cookies.
    // ...
}
```

### Declare non-member functions when type conversions should apply to all parameters
* The parameter can be a participant of implicit type conversion, only when the parameter is in parameter list.
* friend should be avoided when it can be.
  **Observation**: non-member functions is the opposite of member ones, not the friend functions.

### Consider support for a non-throwing swap
for pointer to implementation (pimpl), more efficient way is to do:

* provide a public member `swap` function, this function should never throw, because it is the insurance for exception safety.
  moreover, the default swap is used for built-in types and the built-in types never throws, we should keep consistent.
* provide a non-member `swap` which calls the member `swap` in the namespace where your class or template is. 
* provide a `std::swap` total template specilization, if you're trying to design a class not a class template.
```cpp
class Widget {
public:
    void swap(Widget& other) {
        using std::swap;
        swap(pImpl, other.pImpl);
    }
};
namespace std {
    template<>                                // Total template specilization
    void swap<Widget>(Widget& a, Widget& b) { 
        a.swap(b); 
    }
}
```
* we are not allowed to change anything in namespace `std`, but we can create specialization.
  it is a UB, if you insist on doing so.
* CPP points out, we can only paritially specialized class templates, function templates is not allowed. so provide non-member `swap` for function templates.
```cpp
namespace WidgetStuff {
    template<typename T>
    class Widget { ... };
    
    template<typename T>                    // Non-member function
    void swap(Widget<T>& a, Widget<T>& b) {
        a.swap(b); 
    }
}
```
* when calling `swap`, please ensure a `using std::swap`, then call `swap` directly without any namespace specifier.
  let the compiler to find a proper one.
  **CPP name lookup rules (argument-dependent lookup or Koenig lookup rule):**
  try to find a specific `swap` of `T` in global scope or the namespace where `T` is, then the generic one (`std::swap`).
```cpp
template<typename T>
void doSomething(T& obj1, T& obj2) {
    using std::swap;
    // ...
    swap(obj1, obj2);
    // ...
}
```
