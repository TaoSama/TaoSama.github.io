---
title: Customizing new and delete, Notes(8), Effective C++
categories:
  - Doing
  - CPP
  - 
tags:
  - 
  - 
date: 2017-10-12 20:47:00
toc: true

---

## Customizing new and delete 
(I read a Chinese version of the book, any translation problem plz point out. 

<!--- more --->

### Understand the behavior of the new-handler

well-designed `new-handler` should do:

* make more memory to be used, i.e., more likely to let the next `operator new` be successful.
* install another `new-handler`, i.e., let `operator new` call `set_new_handler`.
* uninstall `new-handler`, i.e., pass `nullptr` to `operator new`.
* throw `bad_alloc` (or derived from `bad_alloc`) error.
* no return (usually call `abort` or `exit`).

implement the `new_handler` of class:
```cpp
class Widget {
public:
    static std::new_handler set_new_handler(std::new_handler p) throw();
    static void* operator new(std::size_t size) throw(std::bad_alloc);
private:
    static std::new_handler currentHandler;
};
std::new_handler Widget::currentHandler = 0;

std::new_handler Widget::set_new_handler(std::new_handler p) throw() {
    std::new_hanlder old = currentHandler;
    currentHandler = p;
    return old;
}

class NewHandlerHolder {
public:
    explicit NewHandlerHolder(std::new_handler nh)
    : handler(nh) {}
    ~NewHandlerHolder() { std::set_new_handler(handler); }
private:
    std::new_handler handler;
    NewHandlerHolder(const NewHandlerHolder&);
    NewHandlerHolder& operator=(const NewHandlerHolder&);
};

void* Widget::operator new(std::size_t new) throw(std::bad_alloc) {
    newhandlerholder h(std::set_new_handler(currenthandler));
    return ::oeprator new(size);
}
```

reuse the code above due to the same implementation for different classes:
```cpp
template<typename T>
class NewHandlerSupport {
public:
    static std::new_handler set_new_handler(std::new_handler p) throw();
    static void* operator new(std::size_t size) throw(std::bad_alloc);
private:
    static std::new_handler currentHandler;
};
template<typename T>
std::new_handler NewHandlerSupport<T>::currentHandler = 0;

template<typename T>
std::new_handler NewHandlerSupport::set_new_handler(std::new_handler p) throw() {
    std::new_hanlder old = currentHandler;
    currentHandler = p;
    return old;
}

template<typename T>
void* NewHandlerSupport::operator new(std::size_t new) throw(std::bad_alloc) {
    newhandlerholder h(std::set_new_handler(currenthandler));
    return ::oeprator new(size);
}

// curiously recurring template pattern, "mixin" style
class Widget: public NewHandlerSupport<Widget> {
    // ...
}
```

`nothrow new`:
it is limited, only **adapted to memory allocated**, and intermediate constructor called may throw exception.

```cpp
Widget* pw1 = new Widget;  // if fails, throw bad_alloc
Widget* pw2 = new(std::nothrow) Widget; // if fails, return nullptr
```

### Understand when it makes sense to replace new and delete

* to check the wrong usage.
* to improve the efficiency of allocation and deallocation.
* to collect the log of usage.
* to decrease the additional space cost caused by default memory manager.
* to improve the suboptimal alignment.
* to make related objects clustered.
* to obtain non-traditional behaviors.

### Adhere to convention when writing new and delete

* `operator new` should have an infinite loop which tries to allocate memory.
 
```cpp
void* operator new(std::size_t size) throw(std::bad_alloc) {
    using namespace std;
    if(size == 0) {
        size = 1; 
    }
    while(true) {
        // Try to allocate "size" bytes 
        if(allocation is ok) return (a pointer to the memory allocated);
        
        new_handler globalHandler = set_new_handler(0);
        set_new_handler(globalHandler); 
        
        if(globalHandler) (*globalHandler)();  
        else throw bad_alloc();
    }
}
```

* `operator delete` should do nothing when passed by `nullptr`, "Class version" should 
   handle the "wrong apply that requires the bigger size than the true size".

```cpp
class Base {
public:
    static void* operator new(std::size_t size) throw(std::bad_alloc);
    static void operator delete(void* rawMemoty, std::size_t size) throw();
};

void* Base::operator new(std::size_t size) throw(std::bad_alloc) {
    if(size != sizeof(Base)) return ::operator new(size);
    // ...
}

void Base::operator delete(void* rawMemory, std::size_t size) throw() {
    if(rawMemory == 0) return;
    if(size != sizeof(Base)) {
        ::operator delete(rawMemory);
        return;
    }
    // ...
}
```

### Write placement delete if you write placement new
* when write `Widget* pw = new Widget;`, two functions are called, one is `operator new`, one is Widget's default construtor.
  if the constructor throws, the memory allocated by the first one should be recovered, or it will be memory leak.
  so the corresponding `operator delete` should be provided correctly.
* when write a `placement operator new`, plz be sure to write a `placement operator delete`.

```cpp
void* operator new(std::size_t) throw(std::bad_alloc); // normal new
void* operator new(std::size_t, void*) throw(); // placement new
void* operator new(std::size_t, const std::nothrow_t&) throw(); // nothrow new
```

* when declaring `placement new` and `placement delete`, plz **do not hide the normal version** intendedly or unconsciously.

```cpp
class StandardNewDeleteForms {
public:
    // normal new/delete
    static void* operator new(std::size_t size) throw std::bad_alloc) {
        return ::operator new(size);
    }
    static void operator delete(void* pMemory) throw() {
        ::operator delete(pMemory);
    }
    // placement new/delete
    static void* operator new(std::size_t size, void* ptr) throw() {
        ::operator new(size, ptr);
    }
    static void operator delete(void* pMemory, void* ptr) throw() {
        ::operator delete(pMemory, ptr);
    }
    // nothrow new/delete
    static void* operator new(std::size_t size, const std::nothrow_t& nt) throw() {
        return ::operator new(size, nt);
    }
    static void operator delete(void* pMemory, const std::nothrow_t&) throw() {
        ::operator delete(pMemory);
    }
};

class Widget: public StandardNewDeleteForms {
public:
    // inherit normal new/delete
    using StandardNewDeleteForms::operator new;
    using StandardNetDeleteForms::operator delete;
    
    // customized placement new/delete
    static void* operator new(std::size_t size, std::ostream& logStream) throw(std::bad_alloc);
    static void operator delete(void* memory, std::ostream& logStream) throw();
};
```
