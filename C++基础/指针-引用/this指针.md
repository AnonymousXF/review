##### this指针

​	this指针是一个隐含的指针，它是指向对象本身的，表示当前对象的地址。

​	this只能在成员函数中使用。全局函数，静态函数都不能使用this。

​	在一个非静态的成员里面，this关键字就是一个指针，指向该函数的这次调用所针对的那个对象。在类a的非const成员函数里，this的类型是a* ，但this不是一个常规变量，所以不可以获取this的地址或者给它赋值。在类a的const成员函数里，this的类型是const a* ，不可以对这个对象本身进行修改。

```c++
void Date::setMonth(int mn)
{
  month = mn;
  this->month = mn;
  (*this).month = mn;
}
//括号中的三句是等价的。
```



##### 何时使用this指针

​	一种情况就是，在类的非静态成员函数中返回类对象本身的时候，我们可以使用圆点运算符(* this). ，箭头运算符this->，另外，我们也可以返回关于*this的引用，这样我们可以像输入输出流那样进行“级联”操作。

```c++
#include<iostream>  
#include<string>  
using namespace std;  
class Stu_Info_Mange  
{  
    int sno;  
    string sname;  
    int age;  
    int grade;  
public:  
    Stu_Info_Mange(int s=0,string n="",int a=0,int g=0)  
    {  
        sno=s;  
        sname=n;  
        age=a;  
        grade=g;  
    }  
    void Setsname(int sn)   //使用this指针进行赋值  
    {  
        this->sname=sn;  
    }  
    int  Setage(int a)  
    {  
        this->age=a;  
        return (*this).age; //使用this指针返回该对象的年龄  
    }  
    void print()  
    {  
        cout<<"the sname is "<<this->sname<<endl;  //显式this指针通过箭头操作符访问  
        cout<<"the sno   is "<<sno<<endl;//隐式使用this指针打印  
        cout<<"the age   is "<<(*this).age<<endl;//显式使用this指针通过远点操作符  
        cout<<"the grade is "<<this->grade<<endl<<endl;  
    }  
  
};  
int main()  
{  
    Stu_Info_Mange sim1(761,"张三",19,3);  
  
    sim1.print();      //输出信息  
  
    sim1.Setage(12);  //使用this指针修改年龄  
  
  
    sim1.print();     //再次输出  
    return 0;  
}  
```

以上的例子中，我们要设置一个学生的信息，需要对每一个相关变量所属的成员函数进行调用（名字调用设置名字的成员函数。。。），我们还可以使用this的另外一个功能，让他实现级联，方便我们调用，对此，我们对上述代码进行修改，如下：

```c++
#include<iostream>  
#include<string>  
using namespace std;  
class Stu_Info_Mange  
{  
    int sno;  
    string sname;  
    int age;  
    int grade;  
public:  
    Stu_Info_Mange(int s=0,string n="",int a=0,int g=0)  
    {  
        sno=s;  
        sname=n;  
        age=a;  
        grade=g;  
    }  
    Stu_Info_Mange &Setsname(string s)   
    //所有的相关函数，都返回该对象的引用，这样就可以实现级联，方便编码
    {  
        this->sname=s;  
        return (*this); 
    }  
    Stu_Info_Mange &Setsno(int sno)  
    {  
        this->sno=sno;  
        return *this;  
    }  
    Stu_Info_Mange &Setgrade(int grade)  
    {  
        this->grade=grade;  
        return *this;
    }  
    Stu_Info_Mange &Setage(int age)  
    {  
        this->age=age;  
        return *this;
    }  
    void print()  
    {  
        cout<<"the sname is "<<this->sname<<endl;  
        cout<<"the sno   is "<<this->sno<<endl;  
        cout<<"the age   is "<<this->age<<endl;  
        cout<<"the grade is "<<this->grade<<endl<<endl;  
  
    }  
};  
int main()  
{  
    Stu_Info_Mange sim;//  使用默认参数  
    sim.Setsname("张三").Setsno(457).Setgrade(2012).Setage(20);  
  	//级联
    //使用this指针使串联的函数调用成为可能  
  
    sim.print();  
       
    return 0;  
  
}  
```

