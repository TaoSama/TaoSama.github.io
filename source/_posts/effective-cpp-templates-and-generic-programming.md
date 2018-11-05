---
title: Templates and Generic Programming, Notes(7), Effective C++
categories:
  - Doing
  - CPP
  - 
tags:
  - 
  - 
date: 2017-10-09 15:10:00
toc: true

---

## Templates and Generic Programming
(I read a Chinese version of the book, any translation problem plz point out. 

<!--- more --->

### Understand implicit interfaces and compile-time polymorphism

* classes and templates both support interfaces and polymorphism.
* as for classes, interfaces is explicit, based on function signatures, 
  and their polymorphism happen in run-time via virtual functions.
* as for templates, interfaces is implicit, based on valid expressions,
  and their polymorphism happen in compile-time via template instantiation and function overloading resolution.

### Understand the two meanings of typename

dependent names: the names appeared in template and dependent on some template parameter.
nested dependent names: the dependent name nested in class.
`C::const_iterator` is a **nested dependent type name**.

>`C::const_iterator* x`, what if `const_iterator` is a static member variable and `x` is a global variable?

* keyword `class` and `typename` are the same when declare template parameters.
* use `typename` to indentify the **nested dependent name**, 
  but it mustn't modify the base class in base class lists and member initialization lists.

```cpp
template<typename T>
class Derived: public Base<T>::Nested {
public:
    explicit Derived(int x): Basee<T>::Nested(x) {
        typename Base<T>::Nested tmp; 
    }
};
```

### Know how to access names in templatized base classes

* use `this->` to refer to the member names of base class templates in derived class templates.
* use base class modifier, `Base::name`


### Factor parameter-independent code out of templates

* templates can generate a couple of classes and functions,
  so any template code should not be dependent on some template parameter which can cause code bloat.
* code bloat caused by non-type template parameters can be removed via replacing them with
  function parameter or using class member variable.
* code bload caused by type parameters can be reduced via sharing the implementation 
  when the instantiation types are with completely same binary representations,
  such as strongly typed pointers `(T*)` to untyped pointers `(void*)`.

```cpp
template<typename T, std::size_t>
class SquareMatrix {
public:
    void invert();
};

/***************************************************/

template<typename T>
class SquareMatrixBase {
protected:
    SquareMatrixBase(std::size_t n, T* pMem)
    : size(n), pData(pMem) {}
    void setDataPtr(T* ptr) { pData = ptr; }
    void invert() {} 

private:
    std::size_t size;
    T* pData;
};

template<typename T, std::size_t n>
class SquareMatrix: private SquareMatrixBase<T> {
public:
    SquareMatrix()
    : SquareMatrixBase<T>(n, 0), pData(new T[n * n]) {
        this->setDataPtr(pData.get());
    }

    void invert() { SquareMatrixBase<T>::invert(); }
private:
    std::unique_ptr<T[]> pData;
    // T data[n * n]; // maybe it is your choice
};

// in the first version, the matrix size is a compile-time constant, and
// it can be optimized as immediate operand in generated instructions.
// while the second version, resulting in smaller executable size, it can reduce 
// the size of working set of the program, and strengthen the locality of reference in cache.
// well, only profiling matters!!!
```

### Use member function templates to accept "all compatible types"

* use member function templates to generate the functions which accept "all compatible types".
* you need to declare normal copy constructor and copy assignment operator when using 
   member function templates. or the compiler will generate one which may be not what
   you want.

```cpp
template<typename T>
class SmartPtr {
public:
    SmartPtr(const SmartPtr& r);
    
    template<typename U>
    SmartPtr(const SmartPtr<U>& other) // Make it more like a real pointer
    : heldPtr(other.get()) {}
    T* get() const { return heldPtr; }
    
private:
    T* heldPtr;
};
```

### Define non-member functions inside templates when type conversions are desired


```cpp
template<typename T> class Rational;

template<typename T>
const Rational<T> doMutiply(const Rational<T>& lhs, const Rational<T>& rhs);

template<typename T>
class Rational {
public:
    // use a helper to avoid the affect of inlining 
    friend Rational<T> operator*(const Rational<T>& lhs, const Rational<T>& rhs) {
        return doMultiply(lhs, rhs); 
    }
};
```

### Use traits classes for information about types

* traits classes make "type-related information" be available in compile-time,
  which is implemented by templates and template specializations.
* traits classes can execute the `if...else` test in compile-time via overloading.

```cpp
struct input_iterator_tag {};
struct output_iterator_tag {};
struct forward_iterator_tag: public input_iterator_tag {};
struct bidirectional_iterator_tag: public forward_iterator_tag {};
struct random_access_iterator_tag: public bidirectional_iterator_tag {};

template<typename IterT, typename DistT>
void doAdvance(IterT& iter, DistT d, std::random_access_iterator_tag) { iter += d; }
template<typename IterT, typename DistT>
void doAdvance(IterT& iter, DistT d, std::bidirectional_iterator_tag) {
    if(d >= 0) { while(d--) ++iter; }
    else { while(d++) --iter; }
}

// it can also accept forward_iterator_tag
template<typename IterT, typename DistT>
void doAdvance(IterT& iter, DistT d, std::input_iterator_tag) {
    if(d < 0) {
        throw std::out_of_range("Negative distance"); 
    }
    while(d--) ++iter;
}

template<typename T>
void advance(IterT& iter, DistT d) {
    doAdvance(iter, d, typename std::iterator_traits<IterT>::iterator_category());
}
```

### Be aware of templatee metaprogramming

* TMP is Turing-complete, and TMP loops is recursive template instantiation.

```cpp
// It may be implemented by `enum hack` in lower version of cpp compiler.
template<std::size_t n>
struct factorial {
    static const std::size_t value = n * factorial<n - 1>::value;    
};
template<>
struct factorial<0> {
    static const std::size_t value = 1;
};
```

* TMP can be used:
  * validate type informations or some others.
  * optimize matrix operations, such as [expression templates](https://en.wikipedia.org/wiki/Expression_templates).
  * generate custom design patterns for users. **(TMP-based policy-based design) -> generative programming**
