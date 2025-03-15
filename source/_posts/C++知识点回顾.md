---
title: C++知识点回顾
date: 2024-2-1 8:25:00
update: 2023-8-25 8:25:00
tags: C++
cover: https://images5.alphacoders.com/124/thumbbig-1240551.webp
---

## C++中的sizeof关键字
是c语言中的运算符，计算一个类型/对象所占用的内存的大小。
- 指针的大小是固定的。32位处理器就是4字节，64位处理器就是8字节
- 数组当函数参数时会退化为指针，大小按指针算
- 计算struct大小时要考虑内存对齐
- 字符串数组大小要包含最后的'\0'

## C++中的const关键字
用于表示常量。
- 修饰变量时代表该变量只读
- 修饰指针时用于声明本身为只读变量或者指向只读变量的指针

``` c++
const int *p;//指向只读变量的指针，可以改变指针的指向，不能通过指针修改所指向只读变量的值
int a = 10;
const int b = 20;
p = &a;//合法，可以指向普通变量
p = &b;//合法，可以指向只读变量
*p = 30;//非法，不能通过指针修改只读变量的值
```

```c++
int a = 10;
int b = 20;
int* const p = &a;//只读指针,指向a
*p = 30;//合法，可以通过指针修改指向变量的值
p = &b;//非法，不能修改指针（即修改指针的指向）
```

## C++字节对齐
C++字节对齐是对内存分配的一种策略。编译器会自动调整数据结构在内存中的分布。如果不对内存中的数据进行适当的对齐而只是顺序排列，会降低存取效率。

优点：提高了内存访问的效率l

缺点：浪费内存空间

## typedef和define的区别
define用于定义预处理宏，在预处理阶段进行处理，在编译之前有预处理器进行文本替换。无类型检查。全局有效。

typedef用于为现有的数据类型创建别名，在编译时进行处理。提供类型检查，保证类型安全。有作用域，仅定义它的作用域内有效。

## C++中class和struct的区别
C++兼容了C中的struct，并扩展其含义。在C中，struct只能包含成员变量，不能包含成员函数，而C++中既可包含成员变量，又可定义成员函数。类似class。

不同点：
- class中成员默认是private，struct中默认是public
- class继承默认是private继承，struct继承默认是public继承
- class可以用来定义模板参数，struct则不行

## C++内联函数（inline）
函数声明前加上inline，编译器会尝试用函数体替换内联函数调用处，从而避免函数调用的开销。

但是编译器不一定会将所有声明为内联函数的函数都进行内联，是否执行取决于编译器的优化和是实现策略。优点是类型安全，可调试，可优化。同时注意由于函数体会被复制多次，会占用代码段空间，有些情况下会导致代码膨胀。

## C++类型转换
- static_cast
``` c++
//基本类型之间的转换
int a = 42;
double b = static_cast<double>(a);//将整数a转换为双精度浮点数b

//指针之间的转换
class Base{}
class Derived: public Base{}

Base* base_ptr = new Derived();
//将基类的base_ptr转换为派生类的derived_ptr
Derived* derived_ptr = static_cast<Derived*>(base_ptr);

Derived derived_obj;
Base& base_ref = derived_obj;
//将基类的引用转换为派生类的引用
Derived& derived_ref = static_cast<Derived&>(base_ref);
```
static_cast在编译器进行类型转换，在进行指针或引用转换时，保证转换合法。

- dynamic_cast
``` c++
class Base { virtual void dummy() {} };
class Derived : public Base { int a; };

Base* base_ptr = new Derived();
// 将基类指针base_ptr转换为派生类指针derived_ptr，如果类型兼容，则成功
Derived* derived_ptr = dynamic_cast<Derived*>(base_ptr); 

//用于多态类型检查
class Animal { public: virtual ~Animal() {} };
class Dog : public Animal { public: void bark() { /* ... */ } };
class Cat : public Animal { public: void meow() { /* ... */ } };

Animal* animal_ptr = /* ... */;

// 尝试将Animal指针转换为Dog指针
Dog* dog_ptr = dynamic_cast<Dog*>(animal_ptr);
if (dog_ptr) {
    dog_ptr->bark();
}

// 尝试将Animal指针转换为Cat指针
Cat* cat_ptr = dynamic_cast<Cat*>(animal_ptr);
if (cat_ptr) {
    cat_ptr->meow();
}
```
dynamic_cast主要应用于父类与子类中的安全类型转换，在运行时执行类型检查，所以相比static_cast其更安全。

dynamic_cast要想有效，基类必须有虚函数（虚函数表），这样才有可能将基类指针转换为子类。

- const_cast
``` C++
//可以修改const属性，使得可以修改
const int a = 5;
int* mutable_ptr = const_cast<int*>(&a);
*mutable_ptr = 4;
```
const_cast<new type> (expression)，new type必须是一个指针、引用或者指向对象类型成员的指针。

- reinterpret_cast
``` C++
int a = 42;
int *int_ptr = &a;
char *char_ptr = reinterpret_cast<int*>(int_ptr);
```
字面意义上看是重新翻译转型，就是重新解释底层bit，而不做任何类型检查。

## C++面向对象三大特性
- 封装

封装就是将数据（属性）和操作这些数据的函数（方法）组合在一个类中的过程。隐藏了类的内部实现细节，仅暴露必要的接口给外部。

- 继承

继承是一个类从另一个类获取属性和方法的过程，允许我们创建类层次的代码结构，减少重复代码，提高代码的复用性和可维护性。

- 多态

多态是允许不同的类对象使用相同的接口名字，但具有不同的实现特性。
C++中多态主要通过虚函数和抽象基类来实现。

虚函数允许派生类实现基类的方法，而抽象基类包含至少一个纯虚函数，无法被实例化，只能作为其它派生类的基类。

通过多态，可以编写更加通用的代码，提高代码的灵活性。

## C++类初始化和析构顺序
初始化顺序：

1、首先是基类初始化顺序。如果当前的类继承自一或多个类，按照声明顺序进行初始化，但是有虚继承的话，优先虚继承。

2、成员变量初始化。按照在类中声明的顺序进行初始化。

3、执行该类构造函数。

析构顺序和初始化顺序相反。

## 深拷贝和浅拷贝
浅拷贝（shallow copy）是一种简单的拷贝方式，仅复制对象的基本类型成员和指针成员的值，而不复制指针所指向的内存。这样会导致两个类对象使用相同的内存，产生内存泄漏等问题。一般地，默认编译器实现的拷贝构造函数是浅拷贝。

深拷贝（deep copy）包含浅拷贝的情况下，还复制指针指向的内存，所以两个对象不用共享相同资源，避免潜在错误。深拷贝的实现通常需要自定义拷贝构造函数和重载=运算符。

``` c++
#include <iostream>
#include <cstring>

class MyClass {
public:
    MyClass(const char* str) {
        data = new char[strlen(str) + 1];
        strcpy(data, str);
    }

    // 深拷贝的拷贝构造函数
    MyClass(const MyClass& other) {
        data = new char[strlen(other.data) + 1];
        strcpy(data, other.data);
    }

    // 深拷贝的赋值运算符重载
    MyClass& operator=(const MyClass& other) {
        if (this == &other) {
            return *this;
        }
        
        delete[] data;
        data = new char[strlen(other.data) + 1];
        strcpy(data, other.data);
        
        return *this;
    }
    
   void SetString(const char* str) {
     if (data != NULL) {
       delete[] data;
     }
     data = new char[strlen(str) + 1];
     strcpy(data, str);
   }
   
    ~MyClass() {
        delete[] data;
    }

    void print() {
        std::cout << data << std::endl;
    }

private:
    char* data;
};

int main() {
    MyClass obj1("Hello, World!");
    MyClass obj2 = obj1; // 深拷贝

    obj1.print(); // 输出：Hello, World!
    obj2.print(); // 输出：Hello, World!

    // 修改obj2中的数据，不会影响obj1
    obj1.SetString("Test");
    obj2.print(); // 输出：Hello, World!
    return 0;
}
```

## C++多态实现的方式
C++中的多态指的是一个函数或者操作在不同的对象上有不同的表现方式，主要包括虚函数、纯虚函数和模板函数。虚函数和纯虚函数实现的多态称为动态多态，模板函数、重载实现的多态称为静态多态。

静态和动态的区别在于是在编译期还是运行时。

虚函数是基类声明的可以被派生类重写的函数。使用指向基类的指针或者引用可以调用被派生类重写的函数，实现多态。

- 通过基类指针或引用调用虚函数
- 被调用的函数是虚函数且必须被派生类重写

``` c++
class Shape {
   public:
      virtual int area() = 0;
};

class Rectangle: public Shape {
   public:
      int area () { 
         cout << "Rectangle class area :"; 
         return (width * height); 
      }
};

class Triangle: public Shape{
   public:
      int area () { 
         cout << "Triangle class area :"; 
         return (width * height / 2); 
      }
};

int main() {
   Shape *shape;
   Rectangle rec(10,7);
   Triangle  tri(10,5);

   shape = &rec;
   shape->area();

   shape = &tri;
   shape->area();

   return 0;
}
```

模板函数可以根据传递参数的不同类型，自动生成相应类型的函数代码，从而实现多态。

``` c++
template <class T>
T GetMax (T a, T b) {
   return (a>b?a:b);
}

int main () {
   int i=5, j=6, k;
   long l=10, m=5, n;
   k=GetMax<int>(i,j);
   n=GetMax<long>(l,m);
   cout << k << endl;
   cout << n << endl;
   return 0;
}
```
编译器会生成两个GetMax函数实例，参数类型分别为int和long。
 
## C++ RAII思想
RAII即Resource Acqusition is Initialization，资源获取即初始化。

RAII将资源的生命周期与对象的作用域关联起来，核心思想是将资源（如内存、文件句柄、网络连接等）的获取和释放与对象的生命周期绑定在一起，利用栈上局部变量的自动析构来保证资源一定会被释放。

一般设计一个RAII类的四个步骤：
- 设计一个类封装资源
- 在构造函数中执行资源的初始化，比如申请内存、打开文件、申请锁
- 在析构函数中执行销毁资源，比如释放内存、关闭文件、释放锁
- 使用时声明一个该类的对象

``` c++
#include <iostream>
#include <fstream>

int main() {
    std::ifstream myfile("example.txt"); // 换自己的文件路径
    if (myfile.is_open()) {
        std::cout << "File is opened." << std::endl;
        // do some work with the file
    }
    else {
        std::cout << "Failed to open the file." << std::endl;
    }
    myfile.close();
    return 0;
}
```
上述代码手动实现文件的打开与关闭，但是当程序异常退出时，可能导致打开的文件没有关闭，导致资源的泄露。这是可以利用RAII的思想进行改进，如下。

```c++
#include <iostream>
#include <fstream>

class File {
public:
    File(const char* filename) : m_handle(std::ifstream(filename)) {}
    ~File() {
        if (m_handle.is_open()) {
            std::cout << "File is closed." << std::endl;
            m_handle.close();
        }
    }

    std::ifstream& getHandle() {
        return m_handle;
    }

private:
    std::ifstream m_handle;
};

int main() {
    File myfile("example.txt");
    if (myfile.getHandle().is_open()) {
        std::cout << "File is opened." << std::endl;
        // do some work with the file
    }
    else {
        std::cout << "Failed to open the file." << std::endl;
    }
    return 0;
}
```
定义一个File类，构造函数中实现打开文件，析构函数中实现关闭文件，这样即使程序遇到异常退出，析构函数也能保证文件的关闭，从而不会发生内存泄漏等问题。

RAII思想也可以包装mutex：

```c++
#include <iostream>
#include <mutex>
#include <thread>

class LockGuard {
public:
    explicit LockGuard(std::mutex &mtx) : mutex_(mtx) {
        mutex_.lock();
    }

    ~LockGuard() {
        mutex_.unlock();
    }

    // 禁止复制
    LockGuard(const LockGuard &) = delete;
    LockGuard &operator=(const LockGuard &) = delete;

private:
    std::mutex &mutex_;
};

// 互斥量
std::mutex mtx;
// 多线程操作的变量
int shared_data = 0;

void increment() {
    for (int i = 0; i < 10000; ++i) {
        // 申请锁
        LockGuard lock(mtx);
        ++shared_data;
        // 作用域结束后会析构 然后释放锁
    }
}

int main() {
    std::thread t1(increment);
    std::thread t2(increment);

    t1.join();
    t2.join();

    std::cout << "Shared data: " << shared_data << std::endl;

    return 0;
}
```

## C++智能指针
- std::shared_ptr

表示一个共享所有权的智能指针，它允许多个shared_ptr指向同一个对象，当最后一个shared_ptr超出作用域时，其所指向的内存才会被自动释放。

std::shared_ptr实现的关键是引用计数。shared_ptr内部存储了一个指向堆内存的指针，将计数变量存储在堆上。

shared_ptr可能会导致double free问题，即同一块内存可能会被释放两次。
解决该问题的方法：
- 使用make_shared函数创建shared_ptr实例，而不直接使用原始指针来创建，这样就确保所有的shared_ptr共享一个引用计数
- 对于出现循环引用的情况，使用weak_ptr
```c++
#include <memory>
#include <iostream>

int main() {
    std::shared_ptr<int> ptr1(new int(10));
    std::shared_ptr<int> ptr2 = ptr1; // 通过拷贝构造函数创建一个新的shared_ptr，此时引用计数为2
    std::cout << *ptr1 << " " << *ptr2 << std::endl; // 输出10 10
    // ptr2超出作用域时，所指向的内存不会被释放，因为此时ptr1仍然持有对该内存的引用
    return 0;
}
```
实现一个简易版本的shared_ptr
```c++
//实现一个简易版本的shared_ptr
#include <iostream>

template <typename T>
class SimperSharedPtr {
public:
    explicit SimperSharedPtr(T *_ptr = nullptr): ptr(_ptr), count(ptr ? new size_t(1) : nullptr){}

    SimperSharedPtr(const SimperSharedPtr& other): ptr(other.ptr), count(other.count) {
        if (count) {
            ++ (*count);
        }
    }

   SimperSharedPtr& operator=(const SimperSharedPtr& other) {
        if (this != &other) {
            release();
            ptr = other.ptr;
            count = other.count;
            if (count) {
                ++ (*count);
            }
        }
        return *this;
   } 
   
    ~ SimperSharedPtr() {
        release();
    }

private:
    void release() {
        if (count && -- (*count) == 0) {
            delete count;
            delete ptr;
        }
    }
    size_t* count;
    T* ptr;
};
```
- std::unique_ptr

表示一个独占所有权的智能指针，保证指向的内存只能被unique_ptr拥有。当它超过作用域时其所指向的内存会自动释放。
```c++
#include <memory>
#include <iostream>

int main() {
    std::unique_ptr<int> ptr(new int(5));
    std::cout << *ptr << std::endl; // 输出5
    // unique_ptr在超出作用域时自动释放所拥有的内存
    return 0;
}
```
智能指针可以提高程序的安全性和可靠性，避免内存泄漏和野指针的问题。

未完待续
---