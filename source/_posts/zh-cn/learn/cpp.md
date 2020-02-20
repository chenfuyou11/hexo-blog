---
title: cpp
category: learn
date: 2020-02-08 16:45:08
tags:
---

Notes on Cherno C++ Tutorial

TODO: 重新格式化这篇博文

<!-- more -->

 > 写在前面: 如果你在看到一半的时候, 若出现**还没有讲到过的操作出现在示范代码中**(比如new), 知道它**基本起什么作用**即可, 在后面的episodes肯定会细讲

## Visual Studio Setup for C++

在Solution Explorer下使用Show All Files视图

默认vs把中间文件放在项目的debug文件夹下, 把最终二进制文件放在Solution文件夹下
推荐设置Output Directory为$(SolutionDir)bin\$(Platform)\$(Configuration)\
Intermediate Directory为$(SolutionDir)bin\intermediate\$(Platform)\$(Configuration)\
bin前不加\因为$(SolutionDir)变量自带backslash

## CONDITIONS and BRANCHES in C++

else if = else { if(){} }

## Loops in C++

C++的语法是自由的, 不一定循规蹈率, 比如 for循环 可以写成这样

```C++
int i = 0;
bool condition = true;
for ( ; condition; )
{
    Log("Hello World!");
    i++;
    if (!(i<5))
        condition = false;
}
```

## POINTERS in C++

指针: 指针代表的仅仅只是一个数字, 一个内存地址, 指针的数据类型一般用于标志指针对应地址上数据的数据类型.

如果我们不关心数据的数据类型, just using `void*`

例子, 分配一个8个char的空间的数组, memset填充00, 最后delete[]操作删除数组的内存

```C++
char* buffer = new char[8];
memset(buffer, 0, 8);

delete[] buffer;
```

指向指针的指针

```C++
... // 接上面的那个栗子
char** ptr = &buffer;
```

解说: 若实际运行时`ptr`储存的数据为`0x00B6FED4` (也就是 `buffer` 这个变量的地址), 用VS2019自带的Memory View查看,
**我们看到的是**指针buffer储存的数据为`f0 dd d0 00` (指针`buffer`指向的 `8个char的空间的数组` 的地址),
由于x86的设计我们从Memory View**看到的是反转的数据**, 实际内容应该是`00 d0 dd f0` (即 0x00d0ddf0), 这个地址就是 `8个char的空间的数组` 所在地.

## Reference in C++

函数使用引用类型参数可以使代码**相比指针参数更简洁**(访问变量不使用需要去引用化符号 "*")

## CLASSES vs STRUCTS in C++

Struct和Class**无本质区别**; Struct中的变量默认是Public修饰. 同时, Class可以继承Struct(但编译器会报警)

Struct更适合存储多个对象或基本数据类型

@Cherno 写Class的规范:

1. m_LogLevel的"m_"表示变量是类内部(private)变量
2. public和private可以分离, 如一部分Public修饰变量, 另一部分Public修饰函数:

   ```C++
   class Log {
   public:
       const int LogLevelError = 0;
       const int LogLevelWarning = 1;
       const int LogLevelInfo = 2;
   private:
       int m_LogLevel = LogLevelInfo;
   public:
       void SetLevel(int level)
       {
           m_LogLevel = level;
       }
   
       void Error(const char* message){
           if (m_LogLevel >= LogLevelError)
               std::cout << "[Error]:" << message << std::endl;
       }
   
       void Warn(const char* message){
           if (m_LogLevel >= LogLevelWarning)
           std::cout << "[WARNING]:" << message << std::endl;
       }
       void Info(const char* message){
           if (m_LogLevel >= LogLevelInfo)
           std::cout << "[INFO]:" << message << std::endl;
       }
   }
   ```

   可以看到这个类有两处 `public:`

## Static in C++

Basically just to `cut to the chase` (切入正题/长话短说),
static outside of class means that the linkage of that symbol that you declare to be static is going to be internal meaning. It;s only going to be visible to that translation unit that you've defined it in.

Whereas a static variable inside a class or struct means that variable is actually going to share memory with all of the instances of the class meaning that basically across all instances that you create of that class or struct, there's only going to be one instance of that static variable
and a similar thing applies to static methods in a class there is no instance of that class being passed into that method. (Cherno讲的是背后的原理)

第一种情况翻译: 由于translation unit中的(不是class中的)全局变量/函数是全局可见的, 所以加上static让全局变量只在它所在的translation unit可见. 否则在不同的translation unit声明相同符号的变量将在链接阶段报重复定义错误. 类似于translation unit的private修饰

@Cherno 的命名规范:

s_开头代表变量为静态 `static int s_Variable = 5;`

***

尽量在头文件使用static变量, 因为它只是简单将内容复制到cpp文件

### 局部Static类型变量

使变量在局部可见的同时有static的性质

```c++
void Function()
{
	static int i = 0;
	i++;
	std::cout << i << std::endl;
}

int main()
{
	Function();
	Function();
	Function();
	Function();
}
```

### 经典的Singleton类

Singleton类在整个程序中只有一个实例

```C++
// 第一种实现
class Singleton
{
private:
	static Singleton* s_Instance;
    Singleton() {}; // 这一行作用阻止类被实例化, 后面构造函数会讲
public:
	static Singleton& Get() { return *s_Instance; };
	void Hello() { std::cout << "Hello" << std::endl; };
};

Singleton* Singleton::s_Instance = nullptr;

int main()
{
	Singleton::Get().Hello();
```

```C++
// 或者将对象实例放在局部静态变量中(好处: 相比上一种更少的代码量)
class Singleton
{
private:
    Singleton() {}; // 这一行作用阻止类被实例化, 后面构造函数会讲
public:
	static Singleton& Get() 
	{
		static Singleton instance;
		return instance;
	};
	void Hello() { std::cout << "Hello" << std::endl; };
};

int main()
{
	Singleton::Get().Hello();
}
}
```

## Enum in C++

枚举用来管理标识符, 增强代码可读性, 本质是Integer(可指定which types of integer you want to be)
如
(模式是32位Integer, 这里用unsigned char只有8位可以节约内存)

```C++
enum Example : unsigned char
{
    A, B, C
};
```

使用enum后的Log类

```C++
class Log
{
public:
    enum Level
    { // LevelError = 0代表从零开始递进, 当然默认就是0
        LevelError = 0, LevelWarning, LevelInfo
    };
private:
    Level m_LogLevel = LevelInfo;
public:
    void SetLevel(Level level)
    {
        m_LogLevel = level;
    }

    void Error(const char* message) {
        if (m_LogLevel >= LevelError)
            std::cout << "[Error]:" << message << std::endl;
    }

    void Warn(const char* message) {
        if (m_LogLevel >= LevelWarning)
            std::cout << "[WARNING]:" << message << std::endl;
    }
    void Info(const char* message) {
        if (m_LogLevel >= LevelInfo)
            std::cout << "[INFO]:" << message << std::endl;
    }
};

int main() {
    Log log;
    log.SetLevel(Log::LevelWarning);
    log.Error("Hello");
    log.Warn("Hello");
    log.Info("Hello");
}
```

## Constructors in C++

You have to manually initialize all of your primitive types otherwise they will be set to whatever was left over in that memory.

禁止实例化类, 只需要把构造函数设为Private, 或者删除构造函数
If you did not want people creating instances...

```C++
class Log
{
Private:
    Log() {} // One way
public:
    Log() = delete; // Another way

    static void Write()
    {

    }
};

int main()
{
    // Only Write() can be invoke
    Log::Write();

    // Now you cannot access the constructor
    Log l;
}
```

## Destructors in C++

折构函数可以被这样调用, 但是基本上不会用到, It's weired

someClass.~someClass();

## Inheritance in C++

没啥好说的, 唯一要注意的是继承的类的大小将是: 父类所有的变量总和 + 自己声明变量总和

Use colon to inerit a class

```C++
class Sub_Class : Main_Class
{

}
```

## Virtual Function in C++

为什么要有Virtual函数

```C++
class Entity
{
public:
    std::string GetName() { return "Entity"; }  
};

class Player : public Entity
{
private:
    std::string m_Name;
public:
    Player(const std::string& name)
        : m_Name(name) {}

    std::string GetName() { return m_Name; }
};

int main()
{
    Entity* entity = new Entity();
    std::cout << entity->GetName() << std::endl;

    Player* player = new Player("Cherno");
    Eneity* entity2 = player;
    std::cout << entity2->GetName() << std::endl;
}
```

输出:

```
Entity
Entity
```

发现问题了吗, 第二个输出本应是"Cherno"

实际上当指针类型是父类Entity时, 通过该指针调用子类Player类的重载函数GetName()并没有被调用, 而是父类定义的内容, 这会导致许多问题



C++11引入了Override关键字, 它不是必须的, 但能够避免发生拼写错误并能增强代码的可读性

正确示范:

```C++
class Entity
{
public:
    // 加上了virtual
    virtual std::string GetName() { return "Entity"; }  
};

class Player : public Entity
{
private:
    std::string m_Name;
public:
    Player(const std::string& name)
        : m_Name(name) {}

    // 加上了override
    std::string GetName() override { return m_Name; }
};

int main()
{
    Entity* entity = new Entity();
    std::cout << entity->GetName() << std::endl;

    Player* player = new Player("Cherno");
    Eneity* entity2 = player;
    std::cout << entity2->GetName() << std::endl;
}
```

总结:

If you want to override a function you have to mark the base function in the base class as virtual.

不重要的补充: 

1. 使用virtual可以减少dynamic dispatch这种操作的使用 (运行时修改对象的vtable).

2. 很不幸virtual它不属于免费的午餐, 首先使用它会需要额外的Vtable空间以让我们能够dispatch to the correct function that includes a member pointer in the actual base class that points to the Vtable. 第二, 我们每次调用virtual函数我们都要通过那个table来决定which function to actually map to.

    但是基本上区别并不明显, 所以能用就用. 除非你的程序要跑在一个性能真的非常非常差的嵌入式设备上.


## Interface (Pure virtual method)

在Printable中创建一个GetClassName的赋值为0的函数, 它就是一个接口

这个类也不能被直接实例化, 需要实现此方法的子类才能工作

```C++
class Printable{
public:
    virtual std::string GetClassName() = 0;
}
```

简单的示例

```C++

class Player : public Entity, Printable // 子类可以继承多个接口类
{
public:
    std::string GetClassName() override { return "Player"; }
}

void Print(Printable* obj) // 通过这样的函数, 我们只需要实现这个方法, 该函数就会调用我们的实现
{
    std::cout << obj->GetClassName() << std::endl;
}

void main()
{
    Player player = new Player();
    Print(player);

    Print(new Player); // 不要这么用, 会导致内存泄漏
}

```

## Visibility in C++

The default visibility of a Class would actually be private; if its a Struct then it would be public by default.

private标记的内容只有本类能访问, 子类也不行, 但是朋友类(friend class)可以, 会在以后视频中讲到.

protected标记是在private基础上, 子类可以访问

## Arrays in C++

### Raw Arrays

举个例子`int example[5]`

数组名称 example 是一个指针, 指向 example[0] 的所在地址

请注意, 不能使用超出数组下标的操作(如`example[-1] = 0`), 那意味着访问不在当前数组许可范围内的内存; 在debug模式下它会报错, 但在release下它可能会造成不可预料的后果

还可以用指针的方式访问数组

```C++
int example[5];
int* ptr = example;
for (int i = 0; i< 5; i++)
    example[i] = 2;

example[2] = 5;
// equals to *(ptr + 2) = 5;
// 因为这个指针是int类型, 所以每次+1都向后偏移4字节的内存地址(对指针进行加减操作是不同于普通的);
// 所以也可以写成:
// *(int*)((char*)ptr + 8) = 6;
// 即先以1 byte的char*进行指针加法操作, 然后再转回int*
// It is pretty wild line of code.
```

更多的, 两种不同的创建数组的方式

```C++
int example[5];
int* another = new int[5];
// 前者是创建在stack上的并且会在函数执行完后被摧毁
// 后者是创建在heap上的
// 建议在类中使用前者, 在函数中使用后者

// We need to delete using the square brackets
delete[] another;
```

**在C++中你无法动态地检查一个普通数组的大小**

```C++
int* another = new int[5];

int count = sizeof(another) / sizeof(int);
// 使用这样的方式是不靠谱的
// 由于another是个指针, 所以最终 count 的结果是1, 这显然不对
```

一个比较好的办法是管理一些记录数组大小的常量

```C++
static const int exampleSize = 5;
int example[size];
```

### C++11 Standard Arrays

好处是能很方便地检查一个数组的大小

```C++
#include <array>

int main(){
    std::array<int, 5> another;

    for (int i = 0; i < another.size(); i++)
        eanother[i] = 2;
}
```

两者区别是Standard Arrays 相比 Raw Array s有更多性能的开销(但是值得), 且更安全

@TheCherno 更喜欢用Raw Arrays, Because he like to live dangerously :)

## How Strings Work in C++ (and how to use them)

### C风格字符串

```C++
const char* name = "Cherno";
```

字符串分配的内存是fixed allocated block, 这表示想要扩充只能通过分配全新的字符串(新的地址)并删除旧的字符串来实现
同时由于这是个char指针所以这个字符串不能用delete(后面会细讲new和delete, rule of thumb is just if you don't use 'new' keyword dont't use the 'delete' keyword)

#### 细节的东西

`null termination character`: 每个字符串末尾都会有一个null结束符0(int类型, 等价于'\0'这个char类型)
比如 `char name[7] = {'C', 'h', 'e', 'r', 'n', 'o', '\0'};`
如果没有结束符, 那么当使用cout输出字符串时, 它会一直输出接下来的内存数据直到读到'\0'. (表现为输出"Cherno"然后跟着一串乱码)

**注意**: Double quote "" by default it becomes a char pointer; 翻译: "Cherno" 默认表示为类型为 char* 的数据.
为什么是 const char* ? 因为字符串本质是char数组, 而数组变量本质上储存的也就是这个数组的起始地址, 所以用 const char* 没毛病, cout对const char*有特殊对待不不断地读下去直到碰到前文所说的`null termination character`

同时, 从Memory View看到的十六进制 `cc` 大概率表示we are right outside of the bounds of our allocation.

### C++ 标准字符串

本质上是char array

```C++
#include <iostream>
#include <string> // cout没有输出string类型的overload, 如果不输出可以不用include

int main()
{
    std::string name = "Cherno";

    // std::string name = "Cherno" + " hello!"; 这种方式是不对的, 因为"Cherno"是一个 const char* 类型
    // name += " hello!"; 这种是对的, a nice easy way, because you are adding it to a string, and "+=" is overloaded in the string class to be able to let you do that.
    // 或者这样 std::string("Cherno") + " hello!";

    std::cout << name << std::endl;
}
```

简单的使用:

```C++
// 判断字符串是否包含指定文字
bool const contains = name.find("no") != std::string::npos // std::string::npos 代表没有找到返回的值

// 使用const xxx&这样将对象以只读形式传入函数, 因为字符串操作在c++中很普遍, 而每次都进行不必要的对象复制无疑很低效
void PrintString(const std::string& string)
{
    string += "h";
    std::cout << string << std::endl;
}
```