# C++常见面试题

## 一、基础语义和编译模型

### 1. C++ 程序从源码到可执行文件经历哪些阶段？

C++ 通常经历 **预处理 → 编译 → 汇编 → 链接 → 加载运行** 几个阶段。

1. **预处理**：处理 `#include`、`#define`、条件编译等，把头文件内容展开，宏替换后生成预处理结果。
2. **编译**：编译器进行词法、语法、语义分析，做类型检查、模板实例化、优化，生成汇编代码或中间表示。
3. **汇编**：把汇编代码转换成目标文件，目标文件中包含机器码、符号表、重定位信息等。
4. **链接**：把多个目标文件和静态库、动态库组合起来，解析外部符号，完成地址重定位，生成可执行文件或动态库。
5. **加载运行**：操作系统加载可执行文件，动态链接器加载依赖库，完成重定位、符号绑定、全局对象初始化后进入 `main()`。

头文件和源文件的关系、符号链接、重复定义是 C++ 编译模型中的关键内容。头文件只应该放声明、模板定义、`inline` 函数、类定义和必要的常量；普通函数和全局变量定义应放在 `.cpp` 文件中，否则多个 `.cpp` 同时包含同一个头文件时，链接阶段容易出现 duplicate symbol。

C++ 还有一个重要规则叫 **ODR（One Definition Rule，单一定义规则）**：同一个实体在整个程序中只能有一个定义。类、模板和 `inline` 函数可以出现在多个翻译单元中，但每份定义必须等价；普通函数和全局变量则不能在多个翻译单元重复定义。

### 2. C++ 和 C 的核心区别是什么？

C++ 兼容大部分 C 语法，但不仅仅是“带类的 C”。核心差异包括：

| 维度 | C | C++ |
|------|---|-----|
| 编程范式 | 过程式为主 | 过程式、面向对象、泛型、函数式混合 |
| 类型系统 | 相对弱一些 | 更强的类型检查，支持引用、类、模板 |
| 资源管理 | 手动管理为主 | RAII、构造析构、智能指针 |
| 多态 | 函数指针、手动分发 | 虚函数、模板、重载 |
| 标准库 | libc | STL、容器、算法、线程、智能指针 |
| 错误处理 | 返回码、`errno` | 返回码、异常、`std::optional`、`std::expected` 等 |

C++ 在 C 的基础上引入了 **抽象能力** 和 **确定性资源管理能力**。类和模板提高抽象层次，构造函数和析构函数让资源生命周期绑定到对象生命周期，RAII 是 C++ 工程实践中最重要的思想之一。

### 3. C++ 中内存区域通常怎么划分？

常见划分如下：

| 区域 | 存放内容 | 生命周期 |
|------|----------|----------|
| 栈区 | 局部变量、函数参数、返回地址 | 随函数调用自动创建和销毁 |
| 堆区 | `new`、`malloc` 动态申请的内存 | 手动释放或由智能指针释放 |
| 全局/静态区 | 全局变量、静态变量 | 进程生命周期 |
| 常量区 | 字符串字面量、只读常量 | 进程生命周期 |
| 代码区 | 函数机器码 | 进程生命周期 |

例如：

```cpp
int g_count = 1;                 // 全局/静态区
static int s_count = 2;          // 全局/静态区
const char* text = "hello";      // text 指针在相应作用域，"hello" 在只读常量区

void foo() {
    int x = 10;                  // 栈区
    static int y = 20;           // 全局/静态区
    int* p = new int(30);        // p 在栈区，指向的 int 在堆区
    delete p;
}
```

这是一种便于理解的逻辑划分，真实进程内存布局会受操作系统、ABI、链接器和编译选项影响。理解内存区域时，核心是区分生命周期、所有权和释放时机。

### 4. 指针和引用有什么区别？

指针是一个变量，保存某个对象的地址；引用是某个对象的别名。

| 对比项 | 指针 | 引用 |
|--------|------|------|
| 是否可以为空 | 可以为 `nullptr` | 一般不能绑定空对象 |
| 是否可以重新指向 | 可以 | 初始化后不能改绑 |
| 使用语法 | 需要 `*`、`->` | 像普通对象一样使用 |
| 是否占用存储 | 通常占用指针大小 | 实现上常类似指针，但语义上是别名 |
| 典型用途 | 可选对象、数组遍历、动态资源 | 参数传递、返回别名、运算符重载 |

```cpp
void updateByPointer(int* p) {
    if (p != nullptr) {
        *p = 10;
    }
}

void updateByReference(int& value) {
    value = 10;
}
```

语言层面引用应该绑定到有效对象，但如果通过非法手段解引用空指针再绑定引用，例如 `int& r = *p;` 且 `p == nullptr`，这已经是未定义行为。因此工程上不能利用引用实现“可空语义”，可空对象应该用指针、`std::optional` 或其他明确表达方式。

### 5. `const`、`constexpr`、`static`、`inline`、`volatile` 分别怎么理解？

**`const`** 表示只读语义：

```cpp
const int a = 10;        // a 不可修改
const int* p1 = &a;      // 指向 const int，不能通过 p1 修改值
int* const p2 = nullptr; // const 指针，p2 自身不能改指向
```

成员函数后的 `const` 表示不修改对象的逻辑状态：

```cpp
class User {
public:
    std::string name() const {
        return name_;
    }

private:
    std::string name_;
};
```

**`constexpr`** 表示可以在编译期求值。它比 `const` 更强调“编译期常量表达式”：

```cpp
constexpr int square(int x) {
    return x * x;
}

int arr[square(3)] = {};
```

**`static`** 有多种含义：

1. 局部静态变量：生命周期延长到进程结束，只初始化一次。
2. 文件作用域静态变量/函数：限制链接可见性为当前翻译单元。
3. 类静态成员：属于类本身，不属于某个对象。

**`inline`** 原始语义是建议编译器内联，但现代 C++ 中更重要的是允许函数或变量在多个翻译单元中有等价定义，避免 ODR 违反。真正是否内联由编译器优化决策决定。

**`volatile`** 表示对象可能被编译器无法感知的外部因素改变，要求每次都从内存读取或写入，常见于内存映射寄存器、信号处理等场景。它不等于线程同步，不能替代 `std::atomic`。

### 6. `struct` 和 `class` 有什么区别？

在 C++ 中，`struct` 和 `class` 的能力几乎一样，都可以有构造函数、析构函数、成员函数、继承、虚函数和访问控制。默认差异只有两个：

| 对比项 | `struct` | `class` |
|--------|----------|---------|
| 默认成员访问权限 | `public` | `private` |
| 默认继承访问权限 | `public` | `private` |

工程习惯上，`struct` 常用于简单数据聚合或值对象，`class` 常用于有封装、有不变量、有行为的对象。

```cpp
struct Point {
    double x = 0;
    double y = 0;
};

class Account {
public:
    explicit Account(int balance) : balance_(balance) {}

    void deposit(int value) {
        balance_ += value;
    }

private:
    int balance_ = 0;
};
```

如果一个类型需要维护内部状态一致性，例如余额不能随意被外部修改，就更适合用 `class` 并把成员变量设为 `private`。

## 二、对象模型和面向对象

### 7. 构造函数、析构函数、拷贝构造、拷贝赋值分别什么时候调用？

**构造函数** 在对象创建时调用，用于初始化对象状态。  
**析构函数** 在对象生命周期结束时调用，用于释放资源。  
**拷贝构造函数** 用已有对象创建新对象时调用。  
**拷贝赋值运算符** 用已有对象给另一个已存在对象赋值时调用。

```cpp
class Buffer {
public:
    Buffer() = default;

    Buffer(const Buffer& other) {
        // 拷贝构造：用 other 创建一个新对象
    }

    Buffer& operator=(const Buffer& other) {
        if (this != &other) {
            // 拷贝赋值：当前对象已经存在
        }
        return *this;
    }

    ~Buffer() {
        // 释放资源
    }
};
```

特殊成员函数常用 **三/五/零法则** 来理解：

1. **三法则**：如果类需要自定义析构函数、拷贝构造、拷贝赋值中的一个，通常三个都要考虑，因为它往往持有资源。
2. **五法则**：C++11 之后还要考虑移动构造和移动赋值。
3. **零法则**：优先使用标准库类型和智能指针管理资源，让编译器生成特殊成员函数，业务类尽量不手写析构和拷贝控制。

### 8. 深拷贝和浅拷贝有什么区别？

浅拷贝只复制对象的成员值，如果成员里有裸指针，复制的是地址，两个对象会指向同一块资源。深拷贝会重新申请资源并复制资源内容，使两个对象拥有独立资源。

```cpp
class BadString {
public:
    explicit BadString(const char* text) {
        size_ = std::strlen(text);
        data_ = new char[size_ + 1];
        std::strcpy(data_, text);
    }

    ~BadString() {
        delete[] data_;
    }

private:
    char* data_ = nullptr;
    std::size_t size_ = 0;
};
```

上面的类如果不自定义拷贝构造和拷贝赋值，编译器生成的默认拷贝会直接复制 `data_` 指针，导致两个对象析构时重复 `delete[]` 同一块内存。

更现代的写法是避免裸指针：

```cpp
class GoodString {
public:
    explicit GoodString(std::string text) : data_(std::move(text)) {}

private:
    std::string data_;
};
```

使用 `std::string` 后资源由标准库对象管理，默认拷贝、移动和析构通常就是正确的。

### 9. 什么是移动语义？为什么需要右值引用？

移动语义用于把临时对象或即将销毁对象中的资源“转移”给另一个对象，避免昂贵的深拷贝。右值引用 `T&&` 是移动语义的基础。

```cpp
class Buffer {
public:
    Buffer(std::size_t size) : size_(size), data_(new int[size]) {}

    ~Buffer() {
        delete[] data_;
    }

    Buffer(Buffer&& other) noexcept
        : size_(other.size_), data_(other.data_) {
        other.size_ = 0;
        other.data_ = nullptr;
    }

    Buffer& operator=(Buffer&& other) noexcept {
        if (this != &other) {
            delete[] data_;
            size_ = other.size_;
            data_ = other.data_;
            other.size_ = 0;
            other.data_ = nullptr;
        }
        return *this;
    }

private:
    std::size_t size_ = 0;
    int* data_ = nullptr;
};
```

移动后，被移动对象必须仍然处于 **可析构、可赋值** 的有效状态，但它的业务值通常不再有意义。标准库容器在扩容时，如果元素的移动构造是 `noexcept`，更倾向于移动元素；如果移动可能抛异常，为保证异常安全，可能退回拷贝。

### 10. 什么是虚函数？虚函数表是怎么工作的？

虚函数用于实现运行时多态。基类通过 `virtual` 声明函数，派生类可以 `override` 重写。当通过基类指针或引用调用虚函数时，实际调用哪个实现由对象的真实类型决定。

```cpp
class Shape {
public:
    virtual ~Shape() = default;
    virtual double area() const = 0;
};

class Circle : public Shape {
public:
    explicit Circle(double radius) : radius_(radius) {}

    double area() const override {
        return 3.14159 * radius_ * radius_;
    }

private:
    double radius_ = 0;
};

void printArea(const Shape& shape) {
    std::cout << shape.area() << std::endl;
}
```

典型实现中，含虚函数的类对象里会有一个隐藏的 **虚表指针（vptr）**，指向类对应的 **虚函数表（vtable）**。虚表中保存虚函数入口地址。调用 `shape.area()` 时，程序通过对象的 vptr 找到 vtable，再找到对应槽位里的函数指针并调用。

需要注意：

1. 虚函数调用有一次间接跳转，通常比普通函数调用略慢，但多数业务代码中不是瓶颈。
2. 构造函数不能是虚函数，因为对象构造期间动态类型还没有完全形成。
3. 析构函数在作为基类使用时通常要声明为 `virtual`，否则通过基类指针删除派生类对象会导致未定义行为。

### 11. 为什么基类析构函数通常要是虚函数？

如果一个类会被当作多态基类使用，即可能通过基类指针删除派生类对象，那么基类析构函数必须是虚函数。

```cpp
class Base {
public:
    virtual ~Base() = default;
};

class Derived : public Base {
public:
    ~Derived() override {
        // 释放 Derived 自己持有的资源
    }
};

Base* obj = new Derived();
delete obj; // 先调用 Derived::~Derived，再调用 Base::~Base
```

如果 `Base` 的析构函数不是虚函数，`delete obj` 只会按静态类型 `Base*` 处理，派生类析构函数可能不会执行，导致资源泄漏或未定义行为。

但并不是所有类都要虚析构。如果一个类不打算作为基类，可以标记 `final` 或保持非虚析构，避免虚表开销。只要类有虚函数并作为多态基类使用，析构函数就应该是 `virtual`。

### 12. 重载、重写、隐藏有什么区别？

| 概念 | 发生位置 | 判断依据 | 例子 |
|------|----------|----------|------|
| 重载 overload | 同一作用域 | 函数名相同，参数列表不同 | `print(int)` 和 `print(string)` |
| 重写 override | 基类和派生类 | 派生类重新实现基类虚函数 | `Derived::draw()` 重写 `Base::draw()` |
| 隐藏 hiding | 基类和派生类 | 派生类声明同名函数，隐藏基类同名函数 | 参数不同也会隐藏 |

```cpp
class Base {
public:
    virtual void f(int) {}
    void g(int) {}
};

class Derived : public Base {
public:
    void f(int) override {}
    void g(double) {}
};
```

`Derived::g(double)` 会隐藏 `Base::g(int)`。如果希望把基类同名函数引入派生类作用域，可以使用 `using`：

```cpp
class Derived : public Base {
public:
    using Base::g;
    void g(double) {}
};
```

实际开发中建议重写虚函数时总是加 `override`。这样函数签名写错时，编译器能直接报错，避免以为重写了，实际却变成隐藏或新函数。

### 13. 多继承和虚继承解决什么问题？

多继承允许一个类同时继承多个基类，但会带来二义性和菱形继承问题。

```cpp
class A {
public:
    int value = 0;
};

class B : public A {};
class C : public A {};
class D : public B, public C {};
```

`D` 中会有两份 `A` 子对象，一份来自 `B`，一份来自 `C`。访问 `D::value` 时会产生二义性。

虚继承可以让最底层派生类只保留一份公共基类子对象：

```cpp
class B : virtual public A {};
class C : virtual public A {};
class D : public B, public C {};
```

虚继承能解决菱形继承中的重复基类问题，但对象布局更复杂，访问虚基类成员可能需要额外间接寻址。工程上应谨慎使用多继承：接口类多继承较常见，带状态的实现类多继承要特别小心。

### 14. 什么是对象切片？

对象切片指把派生类对象按值赋给基类对象时，派生类独有部分被“切掉”。

```cpp
class Animal {
public:
    virtual ~Animal() = default;
    virtual std::string name() const {
        return "Animal";
    }
};

class Dog : public Animal {
public:
    std::string name() const override {
        return "Dog";
    }
};

Dog dog;
Animal animal = dog; // 对象切片
```

`animal` 是一个真正的 `Animal` 对象，不再是 `Dog`。即使 `name()` 是虚函数，`animal.name()` 也只会走 `Animal` 的实现。

避免对象切片的方法：

1. 多态对象通过指针或引用传递，例如 `Animal&`、`Animal*`。
2. 所有权交给智能指针，例如 `std::unique_ptr<Animal>`。
3. 如果确实需要值语义，可以实现虚克隆函数 `clone()`。

## 三、内存管理和 RAII

### 15. `new/delete` 和 `malloc/free` 有什么区别？

| 对比项 | `new/delete` | `malloc/free` |
|--------|--------------|---------------|
| 所属语言 | C++ 运算符 | C 标准库函数 |
| 类型安全 | 返回具体类型指针 | 返回 `void*` |
| 构造/析构 | 会调用构造和析构 | 不会调用构造和析构 |
| 失败行为 | 默认抛 `std::bad_alloc` | 返回 `nullptr` |
| 重载 | 可以重载 | 不能按类重载 |

```cpp
class User {
public:
    User() {
        std::cout << "constructor\n";
    }

    ~User() {
        std::cout << "destructor\n";
    }
};

User* u1 = new User();                         // 分配内存并调用构造函数
delete u1;                                     // 调用析构函数并释放内存

void* raw = std::malloc(sizeof(User));         // 只分配内存
std::free(raw);                                // 只释放内存
```

如果用 `malloc` 分配 C++ 对象，需要 placement new 手动构造，再显式调用析构函数，普通业务中很少这么写。

还要注意配对规则：

1. `new` 对应 `delete`
2. `new[]` 对应 `delete[]`
3. `malloc` 对应 `free`

混用释放函数属于未定义行为。

### 16. 什么是 RAII？为什么它是 C++ 的核心思想？

RAII 是 Resource Acquisition Is Initialization，意思是资源获取即初始化。它的核心是：**把资源生命周期绑定到对象生命周期**，在构造函数中获取资源，在析构函数中释放资源。

```cpp
class File {
public:
    explicit File(const char* path) {
        file_ = std::fopen(path, "r");
        if (file_ == nullptr) {
            throw std::runtime_error("open file failed");
        }
    }

    ~File() {
        if (file_ != nullptr) {
            std::fclose(file_);
        }
    }

    File(const File&) = delete;
    File& operator=(const File&) = delete;

private:
    std::FILE* file_ = nullptr;
};
```

RAII 的好处：

1. 自动释放资源，减少泄漏。
2. 异常安全，即使中途 `throw`，栈展开时局部对象析构函数仍会执行。
3. 代码路径更简单，不需要每个分支都手动释放。
4. 所有权表达清晰，例如 `std::unique_ptr` 表示独占所有权。

C++ 中的 `std::lock_guard`、`std::unique_lock`、`std::unique_ptr`、`std::shared_ptr`、`std::vector`、`std::string` 都体现了 RAII。

### 17. `unique_ptr`、`shared_ptr`、`weak_ptr` 分别适合什么场景？

**`std::unique_ptr`** 表示独占所有权，不允许拷贝，只能移动。它开销小，语义清晰，应该作为默认首选。

```cpp
std::unique_ptr<User> user = std::make_unique<User>();
```

**`std::shared_ptr`** 表示共享所有权，内部维护引用计数。最后一个 `shared_ptr` 销毁时释放对象。

```cpp
std::shared_ptr<User> a = std::make_shared<User>();
std::shared_ptr<User> b = a; // 引用计数增加
```

**`std::weak_ptr`** 不增加引用计数，用来观察 `shared_ptr` 管理的对象，常用于打破循环引用。

```cpp
std::weak_ptr<User> weak = a;
if (auto strong = weak.lock()) {
    // 对象仍然存活
}
```

使用注意事项：

1. 能用 `unique_ptr` 就不要用 `shared_ptr`，共享所有权会增加理解成本和运行时开销。
2. `shared_ptr` 的引用计数操作通常是线程安全的，但被管理对象本身不是自动线程安全。
3. `make_shared` 通常比 `shared_ptr<T>(new T)` 更推荐，因为它能减少一次分配，并且异常安全。
4. 循环引用必须用 `weak_ptr` 或重新设计所有权关系解决。

### 18. `shared_ptr` 循环引用是怎么产生的？

如果两个对象互相持有对方的 `shared_ptr`，引用计数永远不会归零，析构函数不会执行。

```cpp
struct Child;

struct Parent {
    std::shared_ptr<Child> child;
};

struct Child {
    std::shared_ptr<Parent> parent;
};

auto parent = std::make_shared<Parent>();
auto child = std::make_shared<Child>();
parent->child = child;
child->parent = parent;
```

当外部的 `parent` 和 `child` 变量离开作用域时，两个对象内部还互相强持有，引用计数都不为 0。

解决方式是把非拥有关系改成 `weak_ptr`：

```cpp
struct Child {
    std::weak_ptr<Parent> parent;
};
```

判断标准是所有权：父对象通常拥有子对象，子对象只是知道父对象，不应该延长父对象生命周期，因此子到父适合用 `weak_ptr`。

### 19. 野指针、悬空指针、内存泄漏分别是什么？

**野指针** 是未初始化或指向未知地址的指针：

```cpp
int* p;
*p = 10; // 未定义行为
```

**悬空指针** 是曾经指向有效对象，但对象已经释放或生命周期结束：

```cpp
int* p = new int(10);
delete p;
*p = 20; // p 已经悬空
```

**内存泄漏** 是动态申请的内存失去释放路径：

```cpp
void leak() {
    int* p = new int(10);
    // 忘记 delete
}
```

现代 C++ 的解决思路不是到处手动 `delete`，而是：

1. 局部对象优先放栈上。
2. 动态对象优先使用 `std::unique_ptr`。
3. 共享所有权才使用 `std::shared_ptr`。
4. 容器使用 `std::vector`、`std::string` 等标准库对象。
5. 调试时配合 AddressSanitizer、LeakSanitizer、Valgrind、Xcode Memory Graph 等工具。

### 20. 什么是未定义行为？

未定义行为是指 C++ 标准没有规定程序结果，编译器可以做任何处理。它不是“运行时报错”，也不是“一定崩溃”，而是程序已经失去语言层面的正确性保证。

常见未定义行为包括：

1. 数组越界访问。
2. 解引用空指针或悬空指针。
3. 有符号整数溢出。
4. 重复释放内存。
5. 使用未初始化变量。
6. 数据竞争。
7. `new[]` 后用 `delete` 释放。

未定义行为很危险，因为优化编译器会基于“未定义行为不会发生”的假设进行优化。一段 Debug 下看起来正常的代码，Release 下可能完全不同。C++ 工程质量很大程度上取决于能否通过类型、所有权、工具和规范减少未定义行为。

## 四、STL、模板和泛型

### 21. `vector`、`list`、`deque`、`map`、`unordered_map` 怎么选择？

| 容器 | 底层特点 | 适合场景 |
|------|----------|----------|
| `std::vector` | 连续内存，尾部增删快，随机访问快 | 默认首选，缓存友好 |
| `std::list` | 双向链表，任意位置插入删除快 | 需要频繁在中间插入删除且已有迭代器 |
| `std::deque` | 分段连续内存，两端增删快 | 队列、双端队列 |
| `std::map` | 红黑树，有序，查找 O(log n) | 需要按 key 有序遍历 |
| `std::unordered_map` | 哈希表，平均 O(1) 查找 | 快速 key-value 查询，不要求有序 |

多数情况下 `vector` 是默认答案，因为连续内存带来更好的缓存局部性。`list` 理论上中间插入删除是 O(1)，但如果还要先查找位置，整体未必快；链表节点分散在堆上，缓存命中率也差。

选择容器时不要只背时间复杂度，还要考虑：

1. 是否需要保持顺序。
2. 是否需要随机访问。
3. 插入删除发生在头部、尾部还是中间。
4. 数据量大小。
5. 内存连续性和缓存友好性。
6. 迭代器失效规则。

### 22. `vector` 扩容时发生什么？迭代器为什么会失效？

`std::vector` 使用连续内存。当容量不够时，需要申请一块更大的内存，把旧元素移动或拷贝到新内存，再释放旧内存。

```cpp
std::vector<int> values;
values.reserve(2);
values.push_back(1);
values.push_back(2);

auto it = values.begin();
values.push_back(3); // 可能扩容
// it 可能已经失效
```

扩容后，元素地址改变，原来的指针、引用、迭代器都可能指向旧内存，因此失效。

优化方式：

1. 已知大概数量时提前 `reserve()`。
2. 不要长期保存 `vector` 元素指针或迭代器，尤其是在后续还会插入元素时。
3. 需要稳定地址时，可以考虑 `std::deque`、`std::list`，或存储智能指针。

常见失效规则：

| 容器 | 插入后失效情况 |
|------|----------------|
| `vector` | 扩容时全部失效；未扩容时插入点之后失效 |
| `deque` | 中间插入通常会使迭代器失效 |
| `list` | 插入删除通常只影响被删除元素 |
| `map` | 插入不影响已有迭代器，删除只影响被删元素 |
| `unordered_map` | rehash 会使迭代器失效 |

### 23. `map` 和 `unordered_map` 的区别是什么？

`std::map` 通常基于红黑树，元素按 key 有序排列，查找、插入、删除复杂度是 O(log n)。  
`std::unordered_map` 基于哈希表，元素无序，平均 O(1) 查找，但最坏情况下可能退化。

选择建议：

1. 需要有序遍历、范围查询、找前驱后继时，用 `map`。
2. 只需要快速通过 key 查 value，且 key 有良好哈希函数时，用 `unordered_map`。
3. 数据量小的时候，复杂度不是唯一因素，`vector` 线性查找可能因为缓存友好反而更快。
4. `unordered_map` 要关注哈希质量、负载因子和 rehash 成本。

`unordered_map` 平均 O(1) 但不稳定，原因是哈希冲突不可避免，多个 key 可能落到同一个桶中；如果哈希函数差或遭遇恶意输入，桶内查找成本会升高。

### 24. 模板的本质是什么？和虚函数多态有什么区别？

模板是 C++ 的泛型机制，本质上是在编译期根据具体类型生成代码。

```cpp
template <typename T>
T maxValue(T a, T b) {
    return a > b ? a : b;
}

int a = maxValue(1, 2);          // 生成 int 版本
double b = maxValue(1.0, 2.0);   // 生成 double 版本
```

模板多态是 **编译期多态**，虚函数是 **运行时多态**。

| 对比项 | 模板 | 虚函数 |
|--------|------|--------|
| 决定时机 | 编译期 | 运行时 |
| 开销 | 可内联，通常无虚调用开销 | 有间接调用开销 |
| 类型要求 | 编译期可见 | 通过共同基类抽象 |
| 代码体积 | 可能膨胀 | 共享一套虚调用接口 |
| 适合场景 | 容器、算法、数值计算 | 插件化、运行时替换、多态对象 |

模板的优点是性能好、类型安全、抽象能力强；缺点是错误信息复杂、编译时间增加、实现通常要放在头文件中。

### 25. 什么是完美转发？

完美转发用于在模板函数中保持实参的左值/右值属性，把参数原样转发给另一个函数。核心工具是 **万能引用** 和 `std::forward`。

```cpp
void handle(const std::string& text) {
    std::cout << "lvalue\n";
}

void handle(std::string&& text) {
    std::cout << "rvalue\n";
}

template <typename T>
void wrapper(T&& value) {
    handle(std::forward<T>(value));
}

std::string name = "Tom";
wrapper(name);              // 保持左值
wrapper(std::string("Bob")); // 保持右值
```

`T&&` 在模板类型推导中不一定是右值引用，它可能绑定左值，也可能绑定右值，因此常被称为万能引用或转发引用。`std::forward<T>` 会根据 `T` 的推导结果决定转成左值还是右值。

如果在 `wrapper` 中直接写 `handle(value)`，无论外部传进来的是左值还是右值，`value` 作为有名字的变量都是左值，会丢失右值属性。

### 26. Lambda 的捕获方式有哪些？有什么风险？

Lambda 常见捕获方式：

```cpp
int x = 10;
int y = 20;

auto a = [x]() { return x; };       // 按值捕获 x
auto b = [&x]() { return x; };      // 按引用捕获 x
auto c = [=]() { return x + y; };   // 默认按值捕获
auto d = [&]() { return x + y; };   // 默认按引用捕获
```

按值捕获会复制变量，Lambda 内使用的是捕获时的值。默认情况下按值捕获的变量不可修改，如果需要修改副本，要加 `mutable`：

```cpp
auto counter = [count = 0]() mutable {
    return ++count;
};
```

按引用捕获不会复制变量，Lambda 内访问原对象，因此要特别注意生命周期：

```cpp
std::function<int()> makeCallback() {
    int value = 42;
    return [&]() {
        return value; // 错误：返回后 value 已经销毁
    };
}
```

异步场景中尤其要避免默认按引用捕获。更安全的做法是显式捕获需要的变量，并确保对象生命周期清晰。

### 27. `std::function` 和函数指针有什么区别？

函数指针只能保存普通函数或无捕获 Lambda 的入口地址。

```cpp
int add(int a, int b) {
    return a + b;
}

int (*fp)(int, int) = add;
```

`std::function` 是通用可调用对象包装器，可以保存普通函数、成员函数适配结果、函数对象、有捕获 Lambda 等。

```cpp
int base = 10;
std::function<int(int)> f = [base](int value) {
    return base + value;
};
```

代价是 `std::function` 有类型擦除开销，某些捕获对象较大时还可能产生堆分配。性能敏感的模板代码中，可以直接使用模板参数接收 callable：

```cpp
template <typename Callback>
void run(Callback&& callback) {
    callback();
}
```

函数指针轻量但能力弱；`std::function` 灵活但有额外开销；模板 callable 性能最好但会增加模板复杂度和编译成本。

## 五、并发、异常和性能

### 28. C++ 中创建线程和线程同步有哪些常用工具？

现代 C++ 标准库提供了基础并发工具：

| 工具 | 作用 |
|------|------|
| `std::thread` | 创建线程 |
| `std::mutex` | 互斥锁 |
| `std::lock_guard` | RAII 加锁，作用域结束自动解锁 |
| `std::unique_lock` | 更灵活的锁，可延迟加锁、手动解锁 |
| `std::condition_variable` | 条件变量，用于线程等待和通知 |
| `std::future` / `std::promise` | 异步结果传递 |
| `std::async` | 启动异步任务 |
| `std::atomic` | 原子变量 |

示例：

```cpp
std::mutex mutex;
std::vector<int> values;

void append(int value) {
    std::lock_guard<std::mutex> lock(mutex);
    values.push_back(value);
}
```

使用锁时优先使用 RAII 包装，避免异常或提前返回导致忘记解锁。条件变量等待时要用谓词，防止虚假唤醒：

```cpp
std::condition_variable cv;
std::mutex mutex;
bool ready = false;

void waitReady() {
    std::unique_lock<std::mutex> lock(mutex);
    cv.wait(lock, [] { return ready; });
}
```

### 29. 什么是数据竞争？`atomic` 能解决所有线程安全问题吗？

如果两个线程并发访问同一内存位置，至少一个是写操作，并且没有同步，就产生数据竞争。C++ 中数据竞争属于未定义行为。

```cpp
int count = 0;

void increase() {
    ++count; // 多线程下数据竞争
}
```

可以用互斥锁：

```cpp
std::mutex mutex;
int count = 0;

void increase() {
    std::lock_guard<std::mutex> lock(mutex);
    ++count;
}
```

也可以用原子变量：

```cpp
std::atomic<int> count = 0;

void increase() {
    count.fetch_add(1, std::memory_order_relaxed);
}
```

但 `atomic` 不能解决所有线程安全问题。它只能保证单个原子对象上的操作具备原子性和指定内存序。如果一个不变量涉及多个变量，仍然需要锁或更高层同步结构。

```cpp
// 即使 x 和 y 都是 atomic，也不能自动保证 x <= y 这样的组合不变量
std::atomic<int> x = 0;
std::atomic<int> y = 0;
```

简单计数、状态标志可以考虑 `atomic`；复杂临界区、多个变量一致性、容器修改，优先用锁或串行队列。

### 30. C++ 异常有什么优缺点？析构函数里能抛异常吗？

C++ 异常可以把错误处理和正常流程分离，适合构造失败、深层调用错误传播等场景。配合 RAII，异常路径上局部对象会自动析构，资源仍能释放。

缺点：

1. 控制流不如返回码直观。
2. 跨模块、跨语言边界传播异常风险高。
3. 某些工程为了二进制体积、性能可控性或平台规范会禁用异常。
4. 异常安全要求较高，需要保证对象状态在异常后仍一致。

析构函数里不应该抛异常。尤其是在栈展开过程中，如果另一个异常已经在传播，析构函数再抛异常会调用 `std::terminate()`。

```cpp
class Resource {
public:
    ~Resource() noexcept {
        // 析构函数默认应当不抛异常
    }
};
```

释放资源失败时，析构函数里通常只能记录日志或吞掉错误；如果调用者必须感知失败，应提供显式 `close()`、`commit()` 等方法返回错误。

### 31. 什么是异常安全？常见级别有哪些？

异常安全关注的是操作抛出异常后程序是否仍保持正确状态。

| 级别 | 含义 |
|------|------|
| 无异常保证 | 抛异常后对象可能处于不可用状态 |
| 基本保证 | 不泄漏资源，对象仍有效，但状态可能改变 |
| 强保证 | 操作要么成功，要么回滚到调用前状态 |
| 不抛保证 | 操作承诺不抛异常，通常标记 `noexcept` |

典型强保证可以用 copy-and-swap 实现：

```cpp
class Holder {
public:
    Holder& operator=(Holder other) {
        swap(other);
        return *this;
    }

    void swap(Holder& other) noexcept {
        data_.swap(other.data_);
    }

private:
    std::vector<int> data_;
};
```

参数 `other` 先拷贝成功后再交换。如果拷贝失败，当前对象不变；交换通常是 `noexcept`，因此赋值具备强异常保证。

### 32. C++ 性能优化通常从哪些方向入手？

性能优化不要只盯着语法微调，应该先定位瓶颈，再选择手段。

常见方向：

1. **算法复杂度**：O(n²) 改成 O(n log n) 往往比微优化更有效。
2. **内存分配**：减少频繁 `new/delete`，使用对象池、`reserve()`、栈对象、复用缓冲区。
3. **缓存局部性**：连续内存通常比链表更快，结构体布局会影响缓存命中。
4. **拷贝成本**：使用移动语义、引用传参、`string_view`、`span` 等避免不必要复制。
5. **虚调用和类型擦除**：热路径中过度使用虚函数、`std::function` 可能有间接调用成本。
6. **锁竞争**：缩小临界区，减少共享状态，使用分片锁、无锁结构或任务串行化。
7. **编译优化**：开启合适优化级别，使用 LTO、PGO 等。

性能优化应该先用 profiler 找热点，再优化；没有数据支撑的优化容易让代码更复杂却没有收益。

## 六、iOS 项目中的 C++ 常见面试题

### 1. iOS 项目中为什么会用 C++？

iOS 项目使用 C++ 的常见原因：

1. **跨平台复用**：算法、音视频、图像处理、游戏逻辑、IM 协议、加密压缩等核心模块可以在 iOS、Android、桌面端复用。
2. **性能和控制力**：C++ 对内存布局、对象生命周期、SIMD、线程和底层库调用有更强控制。
3. **复用已有生态**：接入 FFmpeg、OpenCV、Skia、Ceres、TensorFlow Lite、游戏引擎等 C/C++ 库。
4. **隔离业务和平台**：核心能力用 C++ 写，平台层用 Objective-C、Swift 做 UI、系统 API 和生命周期适配。

典型架构是：

```text
Swift / Objective-C UI 层
        ↓
Objective-C++ 适配层（.mm）
        ↓
C++ Core / Engine / SDK
        ↓
C / POSIX / 系统库
```

iOS 里写 C++ 不是为了替代 Swift/Objective-C，而是把适合跨平台、性能敏感、平台无关的部分沉到 C++。

### 2. Objective-C++ 的 `.mm` 文件有什么作用？

`.mm` 表示 Objective-C++ 源文件，允许在同一个文件中同时写 Objective-C 和 C++。它通常用于桥接平台层和 C++ 核心层。

```objc
// ImageProcessorWrapper.mm
#import "ImageProcessorWrapper.h"
#include "ImageProcessor.hpp"

@implementation ImageProcessorWrapper {
    std::shared_ptr<ImageProcessor> _processor;
}

- (instancetype)init {
    self = [super init];
    if (self) {
        _processor = std::make_shared<ImageProcessor>();
    }
    return self;
}

- (UIImage *)process:(UIImage *)image {
    // UIImage -> C++ buffer -> UIImage
    return image;
}

@end
```

但要注意：Objective-C 的公开头文件如果被 Swift 或普通 `.m` 文件引用，最好不要直接暴露 C++ 类型。否则所有引用方都可能被迫改成 Objective-C++，Swift 也无法直接理解 Objective-C 头文件里的 C++ 成员。

更好的做法是用 PIMPL 或私有扩展隐藏 C++ 实现：

```objc
// PlayerWrapper.h
@interface PlayerWrapper : NSObject
- (void)play;
@end
```

```objc
// PlayerWrapper.mm
#include "PlayerCore.hpp"

@implementation PlayerWrapper {
    std::shared_ptr<PlayerCore> _core;
}
@end
```

公开 API 面向 Objective-C/Swift，内部实现再转到 C++。

### 3. Swift 如何调用 C++？实际项目中怎么选方案？

Swift 已经提供 C++ interoperability 能力，可以让 Swift 直接导入部分 C++ API；不过 C++ 语言特性很复杂，不是所有 C++ 写法都适合直接暴露给 Swift。实际工程中常见两种方案：

1. **Swift → Objective-C++ Wrapper → C++**：最稳妥，适合大型项目、复杂 C++ 类型、需要兼容旧版本 Xcode 或需要稳定 API 的 SDK。
2. **Swift 直接调用 C++**：适合接口简单、类型可映射、团队熟悉 Swift/C++ 互操作规则的模块。

Objective-C++ 包装层的优势是能把复杂 C++ 类型转换成 Swift 友好的 Objective-C 类型：

| C++ 类型 | 包装层常转成 |
|----------|--------------|
| `std::string` | `NSString *` |
| `std::vector<T>` | `NSArray *` 或 `NSData *` |
| `std::map` | `NSDictionary *` |
| `std::optional<T>` | 可空对象或带错误码结果 |
| C++ 异常 | `NSError`、返回码、Result 风格对象 |

如果直接使用 Swift/C++ interoperability，要认真阅读官方限制，避免把异常、复杂模板、重载过多、所有权不清晰的 C++ API 直接暴露给 Swift。工程上通常会为 Swift 设计一层更薄、更稳定、更像 Swift 的接口。

根据 Swift.org 文档，Swift 5.9 及以上支持 C++ interoperability；Apple 平台使用 libc++，混编代码需要使用一致的 C++ 标准库。官方支持不少常见 C++ 函数、类、结构体和部分标准库类型，但右值引用参数、部分依赖模板、`std::function`、`std::variant` 等仍有限制。Swift 可以直接调用一部分 C++ API，但复杂工程里 Objective-C++ wrapper 仍然很常见。

### 4. 为什么不建议在 Objective-C 头文件里暴露 C++ 标准库类型？

例如：

```objc
// 不推荐
@interface SearchEngine : NSObject
- (std::vector<std::string>)search:(NSString *)keyword;
@end
```

问题包括：

1. 普通 `.m` 文件无法编译 C++ 语法，引用这个头文件会失败。
2. Swift 通过 Bridging Header 导入 Objective-C 头文件时，对 C++ 类型支持有限，容易造成桥接困难。
3. `std::vector`、`std::string` 的 ABI、编译选项、标准库实现会成为公开接口的一部分。
4. 调用方被迫知道 C++ 细节，平台层和核心层耦合变重。

推荐写法是公开 Objective-C 友好的类型：

```objc
@interface SearchEngine : NSObject
- (NSArray<NSString *> *)search:(NSString *)keyword;
@end
```

内部再在 `.mm` 中转换：

```objc
std::vector<std::string> result = engine.search(keyword.UTF8String);
NSMutableArray<NSString *> *array = [NSMutableArray arrayWithCapacity:result.size()];
for (const auto& item : result) {
    [array addObject:[NSString stringWithUTF8String:item.c_str()]];
}
```

### 5. iOS 中 C++ 全局对象和静态对象有什么启动风险？

C++ 全局对象、命名空间作用域对象、函数内局部静态对象都可能涉及初始化时机。

```cpp
std::string globalName = loadName(); // 全局动态初始化
```

在 iOS App 启动中，C++ 全局对象的动态初始化通常发生在 `main()` 之前，属于 pre-main 阶段的一部分。风险包括：

1. **增加启动耗时**：复杂构造函数、文件读取、锁、线程创建、网络配置都会阻塞启动。
2. **初始化顺序问题**：不同翻译单元中全局对象初始化顺序不可靠，容易出现 A 依赖 B 但 B 尚未初始化。
3. **崩溃定位困难**：pre-main 阶段崩溃堆栈和上下文可能不如业务代码清晰。
4. **预热启动统计干扰**：iOS 预热启动场景下，pre-main 工作可能和用户点击时机不完全一致。

更推荐的方式是延迟初始化：

```cpp
Config& sharedConfig() {
    static Config config; // C++11 起局部静态变量初始化线程安全
    return config;
}
```

但局部静态变量的构造也不能太重，只是把初始化推迟到首次使用。真正耗时的工作应该显式放到合适的业务阶段异步执行。

### 6. C++ 异常能不能穿过 Objective-C、Swift 或 C 回调边界？

不建议，也通常不应该让 C++ 异常跨语言边界传播。

例如 C++ 抛出的异常穿过 Objective-C 方法、C 函数指针回调、系统 framework 回调、Swift 调用栈，行为可能不可控，甚至直接 `terminate`。工程上应该在边界处捕获异常并转换成平台层能理解的错误形式。

```objc
- (BOOL)runWithError:(NSError **)error {
    try {
        _core->run();
        return YES;
    } catch (const std::exception& e) {
        if (error != nil) {
            *error = [NSError errorWithDomain:@"CoreError"
                                         code:-1
                                     userInfo:@{NSLocalizedDescriptionKey: @(e.what())}];
        }
        return NO;
    } catch (...) {
        if (error != nil) {
            *error = [NSError errorWithDomain:@"CoreError"
                                         code:-2
                                     userInfo:@{NSLocalizedDescriptionKey: @"Unknown C++ exception"}];
        }
        return NO;
    }
}
```

很多 iOS C++ SDK 会选择禁用异常，统一使用返回码、错误对象或 `Result` 风格 API。C++ 内部可以按团队规范使用异常，但跨 Objective-C/Swift/C 边界前必须消化掉。

### 7. C++ 对象和 Objective-C 对象互相持有时要注意什么？

主要注意 **生命周期、线程、循环引用**。

常见模式一：Objective-C 包装器持有 C++ 对象。

```objc
@implementation EngineWrapper {
    std::unique_ptr<Engine> _engine;
}
@end
```

这里要注意 Objective-C property 对 C++ 类型支持有限，实际项目中更常见的是在 `.mm` 的 ivar 中直接声明 C++ 成员，让 Objective-C++ 编译器负责构造和析构。

常见模式二：C++ 回调 Objective-C 对象。不能让 C++ 长期保存裸的 Objective-C 对象指针而不明确所有权。可以根据需要使用 `__weak`、`__unsafe_unretained`、block 包装或显式注册/注销。

```objc
__weak id<EngineDelegate> weakDelegate = self.delegate;
_engine->setCallback([weakDelegate](int code) {
    id<EngineDelegate> delegate = weakDelegate;
    if (delegate != nil) {
        [delegate engineDidFinish:code];
    }
});
```

如果 C++ lambda 持有 Objective-C block 或对象，也要避免形成 `OC 对象 -> C++ 对象 -> callback -> OC 对象` 的循环。

### 8. iOS 中 C++ 回调切回主线程应该怎么做？

C++ 核心层通常不应该直接依赖 UIKit，但 Objective-C++ 适配层可以把回调切回主线程。

```objc
_engine->setCallback([](Result result) {
    dispatch_async(dispatch_get_main_queue(), ^{
        // 更新 UI 或通知 Objective-C/Swift 层
    });
});
```

如果希望 C++ Core 保持跨平台，可以抽象一个调度器接口：

```cpp
class Dispatcher {
public:
    virtual ~Dispatcher() = default;
    virtual void post(std::function<void()> task) = 0;
};
```

iOS 侧实现：

```objc
class MainQueueDispatcher : public Dispatcher {
public:
    void post(std::function<void()> task) override {
        dispatch_async(dispatch_get_main_queue(), ^{
            task();
        });
    }
};
```

这样 C++ 核心只知道“投递任务”，不知道 GCD、主线程或 UIKit，跨平台边界更干净。

### 9. iOS 项目接入 C++ 静态库或动态库要注意什么？

常见注意点：

1. **架构切片**：真机和模拟器架构不同，现代分发推荐使用 XCFramework。
2. **标准库一致性**：iOS 使用 libc++，避免混入旧的 libstdc++ 依赖。
3. **Bitcode 历史包袱**：老项目可能还保留 Bitcode 配置，要确认第三方库构建设置一致。
4. **符号可见性**：SDK 对外只导出必要符号，减少冲突和二进制体积。
5. **C++ ABI**：不要轻易把 STL 类型暴露为跨库公开 ABI，尤其是 SDK 对外接口。
6. **异常和 RTTI**：确认库和主工程编译选项一致，避免一边开启一边关闭造成行为不一致。
7. **资源和依赖**：动态库、framework、bundle 资源路径要按 iOS 沙盒和包结构处理。

对于 SDK 设计，比较稳的做法是对外暴露 C 接口或 Objective-C 接口，内部实现用 C++。这样 ABI 更稳定，也更容易被 Swift、Objective-C、其他语言调用。

### 10. `extern "C"` 在 iOS C++ 混编中有什么用？

C++ 支持函数重载，因此编译器会对函数名做 name mangling，把参数类型等信息编码进符号名。C 不支持函数重载，符号名更直接。`extern "C"` 用于告诉 C++ 编译器按 C 规则导出符号。

```cpp
extern "C" int add(int a, int b);
```

常见用途：

1. 给 C、Objective-C、Swift、动态加载逻辑提供稳定符号。
2. 写跨语言 SDK 的 C API。
3. 避免 C++ name mangling 导致 `dlsym` 找不到符号。

如果头文件需要同时被 C 和 C++ 使用，常见写法是：

```cpp
#ifdef __cplusplus
extern "C" {
#endif

int add(int a, int b);

#ifdef __cplusplus
}
#endif
```

注意：`extern "C"` 只能改变符号链接规则，不能让 C 直接理解 C++ 类、模板、引用、异常等语言特性。

### 11. 如何设计一个适合 iOS 调用的 C++ SDK 接口？

建议遵循几个原则：

1. **边界简单**：对外尽量使用 C、Objective-C 或 Swift 友好的类型，不直接暴露复杂模板。
2. **所有权明确**：谁创建、谁释放、是否线程安全、回调在哪个线程执行，都写清楚。
3. **错误可表达**：不要让异常跨边界，使用返回码、`NSError`、Result 对象或错误回调。
4. **ABI 稳定**：对外 C API 或 Objective-C API 通常比 C++ 类 ABI 更稳定。
5. **生命周期可控**：提供显式 `start`、`stop`、`close`，避免在析构里做太重的工作。
6. **线程模型清晰**：内部线程、外部调用线程、回调线程要有明确约定。

C 风格句柄接口示例：

```cpp
#ifdef __cplusplus
extern "C" {
#endif

typedef struct EngineHandle EngineHandle;

EngineHandle* EngineCreate(void);
void EngineDestroy(EngineHandle* handle);
int EngineStart(EngineHandle* handle);
int EngineStop(EngineHandle* handle);

#ifdef __cplusplus
}
#endif
```

内部可以把 `EngineHandle` 映射到真实 C++ 对象。这样对外 ABI 稳定，对内仍然能使用现代 C++。

### 12. iOS 中 C++ 代码如何排查崩溃和内存问题？

常用工具和方法：

1. **崩溃日志符号化**：确认 dSYM 完整，C++ 符号可能需要 demangle 才容易阅读。
2. **Address Sanitizer**：检查越界、use-after-free、重复释放等问题。
3. **Thread Sanitizer**：检查数据竞争。
4. **Undefined Behavior Sanitizer**：检查部分未定义行为。
5. **Instruments Allocations/Leaks**：观察内存增长、泄漏和对象生命周期。
6. **Xcode Memory Graph**：主要适合 Objective-C/Swift 对象图，也可辅助观察包装层循环引用。
7. **日志和断言**：C++ 核心层保留关键状态日志，Debug 下使用 `assert` 或内部检查。

C++ 崩溃常见原因：

1. Objective-C 对象释放后，C++ 回调仍然访问它。
2. C++ 对象析构后，异步线程还在使用裸指针。
3. `std::vector` 扩容导致保存的元素指针失效。
4. 多线程读写容器没有同步。
5. C++ 异常穿过系统回调边界。
6. `new/delete`、`malloc/free` 混用。

排查时建议先确认崩溃线程、崩溃地址、最近一次对象生命周期变化，再结合 ASan/TSan 缩小范围。

### 13. iOS 启动优化中为什么会关注 C++？

iOS 启动的 pre-main 阶段会执行动态库加载、符号绑定、Objective-C Runtime 初始化以及各种初始化函数。C++ 对启动的影响主要包括：

1. **全局对象动态初始化**：复杂 C++ 全局对象会在 `main()` 前构造。
2. **静态库代码膨胀**：模板实例化、内联代码过多可能增加二进制体积。
3. **虚函数和 RTTI 元数据**：会增加部分只读数据、重定位和符号信息。
4. **第三方 C++ 动态库数量**：动态库越多，dyld 处理成本越高。
5. **初始化函数**：某些库通过 constructor 或全局单例提前做大量工作。

优化建议：

1. 清理不必要的全局对象和 constructor。
2. 把重初始化延迟到首次使用或首帧之后。
3. 合并或静态链接非必要动态库。
4. 开启 Dead Code Stripping、LTO 等合适优化。
5. 检查模板导致的二进制膨胀。
6. 用 Instruments App Launch、`DYLD_PRINT_STATISTICS` 等方式观察启动阶段成本。

### 14. C++ 代码和 ARC 管理的 Objective-C 对象混用时有什么坑？

ARC 管理 Objective-C 对象，C++ 管理 C++ 对象，两套生命周期模型可以共存，但边界要清晰。

常见坑：

1. C++ 析构函数中访问已经释放的 Objective-C 对象。
2. C++ lambda 捕获 `self` 导致循环引用。
3. C++ 后台线程回调 Objective-C 对象时，没有切回正确线程。
4. 在 `.hpp` 里引入 Foundation/UIKit，导致跨平台核心层污染。
5. Objective-C 对象和 C++ 智能指针互相强持有。

比较稳的做法：

1. Objective-C++ wrapper 负责对象转换和线程切换。
2. C++ core 不直接依赖 UIKit。
3. 回调中弱持有 Objective-C 对象。
4. 销毁 wrapper 时先注销 C++ 回调，再释放 C++ core。
5. 所有跨线程回调都明确队列和生命周期。

## 参考资料

- [Swift.org - C++ Interoperability](https://www.swift.org/documentation/cxx-interop/)
- [Swift.org - Using Swift with C++](https://www.swift.org/documentation/cxx-interop/project-build-setup/)
- [Swift.org - Supported Features and Constraints of C++ Interoperability](https://www.swift.org/documentation/cxx-interop/status/)
- [Swift.org - Safely Mixing Swift and C/C++](https://www.swift.org/documentation/cxx-interop/safe-interop/)
