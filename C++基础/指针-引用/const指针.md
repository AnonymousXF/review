#### const对象的指针和const指针

当使用带有const的指针时其实有两种意思。一种指的是你不能修改指针本身的内容，另一种指的是你不能修改指针指向的内容。

**先说指向const的指针**，它的意思是指针指向的内容是不能被修改的。

写法：const int* p; （推荐）

p是一个指针，它指向的内容是const int 类型。p本身不用初始化它可以指向任何标示符，但它指向的内容是不能被改变的。



**再说const指针**，它的意思是指针本身的值是不能被修改的。

写法： int* const p=一个地址; (因为指针本身的值是不能被修改的所以它必须被初始化）

p是一个指针，这个指针是指向int 的const指针。它指向的值是可以被改变的如*p=3。



理解这些声明的技巧在于，查看关键字const右边来确定什么被声明为常量 ，如果该关键字的右边是类型，则值是常量；如果关键字的右边是指针变量，则指针本身是常量。

```c++
const int *p1;  //the int pointed to is constant
int * const p2; // p2 is constant, it can't point to anything else
```



**还有一种情况是这个指针本身和它指向的内容都是不能被改变的**：

const int* const p=一个地址;

int const* const p=一个地址;

**总结出的规律**：指向const的指针（指针指向的内容不能被修改）const关健字总是出现在* 的左边而const指针（指针本身不能被修改）const关健字总是出现在* 的右边，那不用说两个const中间加个*肯定是指针本身和它指向的内容都是不能被改变的。

```c++
#include <iostream>
using namespace std;

int main(int argc, char *argv[])
{
    int a=3;
    int b;
    
    /*定义指向const的指针（指针指向的内容不能被修改）*/ 
    const int* p1; 
    int const* p2; 
    
    /*定义const指针(由于指针本身的值不能改变所以必须得初始化）*/ 
    int* const p3=&a; 
    
    /*指针本身和它指向的内容都是不能被改变的所以也得初始化*/
    const int* const p4=&a;
    int const* const p5=&b; 
    
     p1=p2=&a; //正确
     *p1=*p2=8; //不正确（指针指向的内容不能被修改）
    
     *p3=5; //正确
     p3=p1; //不正确（指针本身的值不能改变） 
    
     p4=p5;//不正确 （指针本身和它指向的内容都是不能被改变） 
     *p4=*p5=4; //不正确（指针本身和它指向的内容都是不能被改变） 
     
    return 0; 
}
```



#### const指针和const成员函数

可以将const关键字用于成员函数。例如：

```c++
class Rectangle

{

     pubilc:

        .....

        void SetLength(int length){itslength = length;}

        int GetLength() const {return itslength;}  //成员函数声明为常量

        .....

     private:

        int itslength;

        int itswidth;

};
```

当成员函数被声明为const时，如果试图修改对象的数据，编译器将视为错误。

如果声明了一个指向const对象的指针，则通过该指针只能调用const方法（成员函数）。

示例声明三个不同的Rectangle对象：

```c++
Rectangle* pRect = new Rectangle;

const Rectangle * pConstRect = new Rectangle;     //指向const对象

Rectangle* const pConstPtr = new Rectangle;

// pConstRect是指向const对象的指针，它只能使用声明为const的成员函数，如GetLength（）。
```

