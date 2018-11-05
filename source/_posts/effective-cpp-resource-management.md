---
title: Resource Management, Notes(3), Effective C++
categories:
  - Doing
  - CPP
  - 
tags:
  - 
  - 
date: 2017-08-31 11:26:00
toc: true

---

## Resource Management 
(I read a Chinese version of the book, any translation problem plz point out. 

<!--- more --->

### Use objects to manage resources

* Priciples
  * put into managing object when acquired resources
    (Resource Acquistion Is Initialization; RAII).
  * managing object uses destructor to ensure the resources is released.

* Methods
  * auto_ptr
    once be assigned, the right side one is null.
    so the feature of abnormal assignment operator makes that it can't be put into containers.
  * referencing-counting smart pointer (RCSP)
    it can't break cycles of references.
    it seems they're in the "used" status when 2 unused objects point to each other. 

### Think carefully about copying behavior in resource-managing classes

* create a class to do this.
* no copying. -> `=delete or inherit from uncopyable`
* reference-count in the low-level resources (shared_ptr).
```cpp
class Lock {
public:
    explicit Lock(Mutex* pm):
        mutexPtr(pm, unlock) {
            lock(mutexPtr.get()); 
        }
    }
private:
    std::shared_ptr<Mutex> mutexPtr;
};
```
* deep copying.
  copy wrapped resources when copying the resource-managing object.
* transfer the ownership of low-level resources (auto_pr).

### Provide access to raw resources in resource-managing classes
Sometimes we need to provide compatibility to C APIs.

* provide a `get()` to access the raw pointer, safer.
```cpp
// C APIs.
FontHandle getFont();
void releaseFont(FontHandle fh);

Class Font {
public:
    explicit Font(FontHandle fh): f(fh) {}
    ~Font() { releaseFont(f); }
    // ...
    FontHandle get() const { return f; } 
    // ...
private:
    FontHandle f;    // Raw font resources
};
```
* provide implicit conversion function may offer convenience to customers. but the opportunities of unexpected error is increased.
```cpp
class Font {
public:
    // ...
    operator FontHandle() const { return f; } 
    // ..
}

Font f1(getFont());
// It is intended to copy a Font object
// but f1 is copied after it is implicitly conversed to FontHandle
FontHandle f2 = f1; 
```
* no contradiction with encapsulation, just to ensure resource releasing.

### Use the same form in corresponding uses of new and delete
`new->delete`
`new[]->delete[]`

* Tips:
  use containers to reduce the risk misusing `delete` when releasing memory of typedefined array.
```cpp
typedef std::string stringArray[4];
std::string* pal = new stringArray;
delete pal;   // Undefined Behavior
delete pal[]; // Good
```

### Stored newed objects in smart pointers in standalone statements

* look at the code below:
```cpp
int priority();
void processWidget(std::shared_ptr<Widget> pw, int priority);

processWidget(std::shared_ptr<Widget>(new Widget), priority());
```

* it is free for the compiler to reorder the operations inside one statement, what if in this order:
`new Widget -> priority() -> shared_ptr`
* once `priority()` throws, the newed pointer will be lost and memory leak may happen.
* the compiler can't reorder the operations between statements, so
  the code below avoids the risk above.
```cpp
std::shared_ptr<Widget> pw(new Widget);
processWidget(pw, priority());
```

