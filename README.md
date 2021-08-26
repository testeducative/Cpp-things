# Cpp-things
# const用法
## const含义
常类型是指使用类型修饰符const说明的类型，常类型的变量或对象的值是不能被更新的
## const作用
1.定义const常量
2.类型检查
const常量有数据类型而宏常量（define）没有数据类型只进行字符替换，所以编译器会对前者进行类型安全检查
```c++
void f(const int i){};//传入的参数如果类型检查不匹配会提示
```
3.保护被修饰的变量
如果在程序里修改const常量，编译器会报错
4.可以节省空间，避免不必要的内存分配
-  const定义常量从汇编的角度来看，只是给出了对应的内存地址，而不是像`#define`一样给出的是立即数。
-   const定义的常量在程序运行过程中只有一份拷贝，而`#define`定义的常量在内存中有若干个拷贝。
## const使用
1.定义常量
以下两种定义形式本质上是相同的，注意声明const常量时必须初始化。
```c++
TYPE const ValueName = value;
const TYPE ValueName = value;
```
注：const默认为局部变量，非const变量默认为extern，将const改为extern之后，编译器会分配内存，并且可以不进行初始化。
2.与指针相关的四种const用法：
```c++
const char * a; //指向const对象的指针或者说指向常量的指针。
char const * a; //同上
char * const a; //指向类型对象的const指针。或者说常指针、const指针。
const char * const a; //指向const对象的const指针。
```
（1）指向常量的指针
a是一个指向char类型的const指针，const定义的是a指向的对象类型而不是a本身，所以a可以不用赋初值，但不能通过a修改所指对象的值。同时也不能用void```*```指针保存const对象的地址，必须加const
```c++
*a = 10;//error

const int p = 10;
const void * vp = &p;
void *vp = &p; //error
``` 
还有一个重点是，允许把非const对象的地址赋值给const对象的指针，但如果要修改指针所指向的对象，就不能用当前指针修改了。
（2）常指针
const指针必须进行初始化，且值不能改变
（3）指向常量的常指针
注：还有一种特别的区别方法，沿着星号画一条线
如果const位于*左侧，则const是用来修饰指针所指向的变量，即指针指向为常量；
如果const位于星号右侧，const就是修饰指针本身，即常指针
## 函数中使用const
1.传递过来的参数或参数指针在函数内不可以改变（没有意义，Var本来就是形参）
```c++
void function(const int Var)
```
2.参数指针所指内容为常量不可变
```c++
void function(const char* Var)
```
3.参数为引用，为了增加效率同时防止修改
```c++
void function(const A &a)
```
对于非内部数据类型的参数而言，象void func(A a) 这样声明的函数注定效率比较低。因为函数体内将产生A 类型的临时对象用于复制参数a，而临时对象的构造、复制、析构过程都将消耗时间。

为了提高效率，可以将函数声明改为void func(A &a)，因为“引用传递”仅借用一下参数的别名而已，不需要产生临时对象。

> 但是函数void func(A &a) 存在一个缺点：  
>   
> “引用传递”有可能改变参数a，这是我们不期望的。解决这个问题很容易，加const修饰即可，因此函数最终成为 void func(const A &a)。

以此类推，是否应将void func(int x) 改写为void func(const int &x)，以便提高效率？完全没有必要，因为内部数

据类型的参数不存在构造、析构的过程，而复制也非常快，“值传递”和“引用传递”的效率几乎相当。

> 小结：  
> 1.对于非内部数据类型的输入参数，应该将“值传递”的方式改为“const 引用传递”，目的是提高效率。例如将void func(A a) 改为void func(const A &a)。  
>   
> 2.对于内部数据类型的输入参数，不要将“值传递”的方式改为“const 引用传递”。否则既达不到提高效率的目的，又降低了函数的可理解性。例如void func(int x) 不应该改为void func(const int &x)。

以上解决了两个面试问题：

-   如果函数需要传入一个指针，是否需要为该指针加上const，把const加在指针不同的位置有什么区别；
-   如果写的函数需要传入的参数是一个复杂类型的实例，传入值参数或者引用参数有什么区别，什么时候需要为传入的引用参数加上const。

>const修饰函数返回值

（1）const int function();
本身没有意义，函数返回值本身就是赋值给其他变量
（2）const int* function()
指针指向的内容不变
（3）int *const function()
指针本身不可变
## 类中使用const
使用const关键字进行说明的成员函数，称为常成员函数。只有常成员函数才有资格操作常量或者常对象，没有使用const关键字进行说明的成员函数不能用来操作常对象。（类中的const成员变量必须通过初始化列表初始化）：
```c++
class Apple{
private:
    int people[100];
public:
    Apple(int i); 
    const int apple_number;
};

Apple::Apple(int i):apple_number(i)
{

}
```

const对象只能访问const成员函数，非const对象可以访问任意的成员函数
# static用法
## 静态变量
1.函数中的静态变量
当函数变量为static时，空间将在程序的生命周期内分配（程序内多次调用，只分配一次空间）
2.类中的静态变量
类中的静态变量由对象共享，正因为如此，静态变量不能使用构造函数初始化
```c++
#include<iostream> 
using namespace std; 

class Apple 
{ 
public: 
	static int i; 
	
	Apple() 
	{ 
		// Do nothing 
	}; 
}; 

int main() 
{ 
Apple obj1; 
Apple obj2; 
obj1.i =2; 
obj2.i = 3; 
	
// prints value of i 
cout << obj1.i<<" "<<obj2.i; 
} 
```
上面的程序试图根据不同对象创造多个静态变量副本，但结果并不能实现。
## 静态成员
1.类对象为静态
像变量一样，对象也会存在到程序生命周期。
```c++
#include<iostream> 
using namespace std; 

class Apple 
{ 
public: 
	static int i; 
	
	Apple() 
	{ 
		// Do nothing 
	}; 
}; 

int main() 
{ 
Apple obj1; 
Apple obj2; 
obj1.i =2; 
obj2.i = 3; 
	
// prints value of i 
cout << obj1.i<<" "<<obj2.i; 
} 
```
输出：
```c++
Inside Constructor
Inside Constructor
End of main
```
在上面的程序中，对象为非静态，因此创建对象时会调用构造函数，当if判断语句结束后会调用析构函数，如果将对象声明为静态（在声明阶段加入static），可以看到析构函数会在main结束后才调用。
2.类中的静态函数
对于静态函数我们可以使用对象和'.'来调用静态成员函数，但建议使用类名和'::'（即范围解析运算符）来调用静态成员。
静态成员函数可以访问静态数据成员和其他静态成员函数，但无法访问类的非静态数据成员或成员函数。
> Written with [StackEdit](https://stackedit.io/).
