---
title: Accustoming to CPP, Notes(1), Effective C++
categories:
  - Doing
  - CPP
  - 
tags:
  - 
  - 
date: 2017-06-11 11:20:10
toc: true

---
(I read a Chinese version of the book, any translation problem plz point out. 

<!--- more --->

### View CPP as a federation of languages

* a multiparadigm programming language
  * procedural
  * object-oriented programming (OOP)
  * functional programming (FP)
  * generic
  * template metaprogramming (TMP)
  
* tips
  * pass-by-value is more efficient for built-in types (C-like types)
  * pass-by-reference-to-const is better for user-defined types
  * pass-by-value is better for STL iterators and functors (both implemented based on C-pointers)
  

### Prefer compliers to preprocessors

#### Use consts for constants

* consts can be in the symbol table and will be seen by compliers
```cpp
const char* const authorName = "Scott Meyers";
//std::string is better than char*-based string
const std::string authorName("Scott Meyers");
```

* consts in class for scope
```cpp
// Well, it is a declaration not a definition. Normally CPP requires a definition for anything you use.
// But static consts in class with integral types(ints, chars, bools) need to be treated specially.
class GamePlayer {
private:
    static const int NumTurns = 5; //declaration
    int scores[NumTurns];
    // ...
};
```
**No need to provide definitions when their addresses are never taken.**
**Some compliers may wrongly require a definition, then it will look like:**
```cpp
// definiton, no more initialization due to done in declaration
const int GamePlayer::NumTurns; 
```

* \#defines have no scope
once a macro is defined, it is taken into effect during all the later process of compiling(unless #undef is done).
**no encapsulation**

* enum hack
```cpp
// some like #defines
// consts can be taken the addresses while enums cannot
class GamePlayer {
private:
    enum { NumTurns = 5 };
    int scores[NumTurns];
    // ...
};
```
**For acquaintance, enum hack is basic technique in TMP**

#### Use template inlines for mini functions
```cpp
// disgusting
#define CALL_WITH_MAX(a, b) f((a) > (b) ? (a) : (b))

template<typename T>
inline void callWithMAX(const T& a, const T& b) {
    f(a > b ? a : b);
}
```

### Use consts whenever possible
```cpp
char greeting[] = "Hello";
char* p = greeting;              // non-const pointer, non-const data
const char* p = greeting;        // non-const pointer, const data
char* const p = greeting;        // const pointer, non-const data
const char* const p = greeting;  // const pointer, const data
```

* STL iterator
iterator acts like a T* pointer.
```cpp
std::vector<int> vec;
// iter acts like T* const
const std::vector<int>::iterator iter = vec.begin(); 
*iter = 10;  // ok
++iter;      // CE

// cIter acts like const T*
std::vector<int>::const_iterator cIter = vec.begin();
++iter;      // ok
*iter = 10;  // CE
```

* return const value
```cpp
class Rational { // ... };
const Rational operator* (const Rational& lhs, const Rational& rhs);

Rational a, b, c;
if(a * b = c) {} // CE, without const it will be ok
```
**CPP returns object by value, so the assignment is never legal, for only changing a copy.**

* const member function
**two member functions can be overloaded even if they are only different in constness.**
**in real, const member function is overloaded when const objects are used in passed by pointer-to-const or passed by reference-to-const.**
```cpp
class TextBlock {
public: 
    const char& operator[](std::size_t position) const {
        // ...
        return text[position];
    }
    char& operator[](std::size_t position) {
        return const_cast<char&>(
            static_cast<const TextBlock&>(*this)[position]
        );
    }
};

void print(const TextBlock& ctb) {
    std::cout << ctb[0];
}
```
**non-const overloaded function can be implemented by calling the const version is safe, otherwise it is not.**

* bitwise constness or logical constness
**bitwist constness is the definition of CPP's constness, so the const member funcion cannot change any non-static member variable.**
use keyword `mutable` to release the constraint of CPP's bitwise constness.
**write codes by using logical constness.**
```cpp
class CTextBlock {
    public: 
        std::size_t length() const;
    private:
        char* pText;
        mutable std::size_t textLength;
        mutable bool lengthIsValid;
};
std::size_t CTextBlock::length() const {
    if(!lengthIsValid) {
        textLength = std::strlen(pText); // can be changed
        lengthIsValid = true;            // same as above
    }
    return textLength;
}
```

### Make sure that objects are initialized before used
* objects will be initialized when declared in heap while they won't when declared in stack.
* it is not easy to memorize it, whatever, keep initialization always.
* do not mix assignment and initialization.
* **for user-defined types, always use the member initialization list and always list all the member variables in the member initialization list.**
* sometimes, a class may have many constructors, and to avoid duplication, we can move the invitializatin of the variables that have same efficiency between assignment and initialization to some private function. (well, this 'pseudo-intialization' is not advocated, try member initialization list as much as possible)
* The order of initialization:
    * base classes is initialized earlier than derived classes.
    * member variables is initialized as the order of that they are declared even if the order of the appearance in member initialization list is different.
* non-local static objects
   * static objects: global objects, objects defined in namespace, static objects in classes, functions, file scopes.
   * local static objects: static objects in functions.
   * no specific initialization order for non-local static objects in different translation unit.
   * Singleton:
     **use local static objects to replace the non-local ones.**
```cpp
class FileSystem { // ... };
FileSystem& tfs() {
    static FileSystem fs;
    return fs;
}
class Directory { // ... };
Directory::Directory( params ) {
    // ...
    std::size_t disks = tfs().numDisks();
}
Directory& tempDir() {
    static Directory td;
    return td;
}
```