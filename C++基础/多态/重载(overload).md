#### 函数重载

* 作用与同一个范围内（同一个类、同一个命名空间）
* 函数名必须相同
* 参数列表必须不同（个数不同、类型不同、参数排列顺序不同等）
* 函数的返回类型可以相同也可以不同
* 仅仅返回类型不同不足以成为函数重载



c++代码在编译的时候会根据参数列表对重载的函数重命名，例如`void Swap(int a, int b)`会被重命名为`_Swap_int_int`，`void Swap(float x, float y)`会被重命名为`_Swap_float_float`（不同的编译器有不同的重命名方式，这里仅仅举例说明，实际情况可能并非如此）。当发生函数调用时，编译器会根据传入的实参去逐个匹配，以选择对应的函数，如果匹配失败，编译器就会报错，即**多态的静态绑定**。



#### 运算符重载

* 不能重载的运算符

  ```
  .			//成员访问运算符
  ?:			//条件运算符
  ::			//作用于运算符
  sizeof
  ```



* 怎么实现运算符的重载？

  ​        类的成员函数或友元函数（类外的普通函数）。

  ​        成员函数和友元函数的使用场合：一般情况下，建议一元运算符使用成员函数，二元运算符使用友元函数。

  1、运算符的操作需要修改类对象的状态，则使用成员函数。如需要做左值操作数的运算符（如=，+=，++）

  2、运算时，有数和对象的混合运算，必须使用友元

  3、二元运算符中，第一个操作数为非对象时，必须使用友元函数。如输入输出运算符<< 和 >>

具体规则如下：

| 运算符                                      | 建议使用    |
| ---------------------------------------- | ------- |
| 所有一元运算符                                  | 成员函数    |
| =    ()    []    ->                      | 必须是成员函数 |
| +=    -=    /=    *=    ^=    &=    !=    %=    >>=    <<= | 成员函数    |
| 所有其他二元运算符，例如： +   -   *   /              | 友元函数    |
| <<    >>                                 | 必须是友元函数 |



* 参数和返回值

  ​        当参数不会被改变，一般按const引用来进行传值（若使用成员函数来重载，函数也为const）。

  ​        对于返回值的决定：

  1、如果返回值可能出现在=号左边，则只能作为左值，返回非const引用

  2、如果返回值只能出现在=号右边，则只需作为右值，返回const型引用或const型值。

  3、如果返回值既可能出现在=号左边或右边，则其返回值必须作为左值，返回非const引用。



* 双目运算符重载实例：

  ```c++
  //+ 和 - 运算符的重载：
  class Point    
  {    
  private:    
      int x;   
  public:    
      Point(int x1)  
      {   x=x1;}    
      Point(Point& p)     
      {   x=p.x;}  
      const Point operator+(const Point& p);//使用成员函数重载加号运算符  
      friend const Point operator-(const Point& p1,const Point& p2);//使用友元函数重载减号运算符  
  };    
    
  const Point Point::operator+(const Point& p)  
  {  
      return Point(x+p.x);  
  }  
    
  Point const operator-(const Point& p1,const Point& p2)  
  {  
      return Point(p1.x-p2.x);  
  }


  //调用
  Point a(1);    
  Point b(2);  
  a+b;  //正确，调用成员函数  
  a-b;  //正确，调用友元函数  
  a+1;  //正确，先调用类型转换函数，把1变成对象，之后调用成员函数  
  a-1;  //正确，先调用类型转换函数，把1变成对象，之后调用友元函数  
  1+a;  //错误，调用成员函数时，第一个操作数必须是对象，因为第一个操作数还有调用成员函数的功能  
  1-a;  //正确，先类型转换 后调用友元函数  
  ```

  1、由于+和-都是出现在=号的右边，如c=a+b，即会返回一个右值，可以返回const型值

  2、后几个表达式讨论的就是，数和对象混合运算符的情况，一般出现这种情况，常使用友元函数

  3、隐式调用：obj1 + obj2

  ​      显式调用：obj1.operator+(OBJ obj2)——成员函数

  ​			  operator+(OBJ obj1, OBJ obj2)——友元函数

  ​      执行时，隐式调用和显式调用都会调用operator+()





* 单目运算符重载实例：

  ```c++
  //++和--运算符的重载
  class Point    
  {    
  private:    
      int x;   
  public:    
      Point(int x1)  
      {   x=x1;}    
      Point operator++();//成员函数定义自增  
      const Point operator++(int x); //后缀可以返回一个const类型的值  
      friend Point operator--(Point& p);//友元函数定义--  
      friend const Point operator--(Point& p,int x);//后缀可以返回一个const类型的值  
  };    
    
  Point Point::operator++()//++obj  
  {  
      x++;  
      return *this;  
  }  
  const Point Point::operator++(int x)//obj++  
  {  
      Point temp = *this;  
      this->x++;  
      return temp;  
  }  
  Point operator--(Point& p)//--obj  
  {  
      p.x--;  
      return p;  
           //前缀形式(--obj)重载的时候没有虚参,通过引用返回*this 或 自身引用,也就是返回变化之后的数值  
  }  
  const Point operator--(Point& p,int x)//obj--  
  {  
      Point temp = p;  
      p.x--;  
      return temp;  
           // 后缀形式obj--重载的时候有一个int类型的虚参, 返回原状态的拷贝  
  }  


  //调用
  Point a(1);  
  Point b(2);  
  a++;//隐式调用成员函数operator++(0),后缀表达式  
  ++a;//隐式调用成员函数operator++(),前缀表达式  
  b--;//隐式调用友元函数operator--(0),后缀表达式  
  --b;//隐式调用友元函数operator--(),前缀表达式  
  cout<<a.operator ++(2);//显式调用成员函数operator ++(2)，后缀表达式  
  cout<<a.operator ++();//显式调用成员函数operator ++()，前缀表达式  
  cout<<operator --(b,2);//显式调用友元函数operator --(2)，后缀表达式  
  cout<<operator --(b);//显式调用友元函数operator --()，前缀表达式
  ```

  1、前后缀仅从函数名无法区分，只能由参数区分，这里引入一个虚参数int x，x可以是任意整数。

  2、 前置：++a

  ​       返回原对象本身，返回值可以作为左值，即函数结果可以作为左值，则要返回一个非const类型的值。

  ​	后置：a++

  ​	返回原对象的一个副本（拷贝的临时变量），返回值不能出现在等号的左边（临时变量不能作为左值），函数结果只能做右值，则要返回一个const类型的值。

  3、隐式调用：obj1-- 或 --obj

  ​      显式调用：obj1.operator--()——成员函数，前置

  ​			 obj1.operator--(0)——成员函数，后置

  ​			 operator--(OBJ obj1)——友元函数

  ​			 operator--(OBJ obj1, int x)——友元函数

  ​      执行时，隐式调用和显式调用都会调用operator--()





* 重载下表运算符[]

  ```c++
  class Point    
  {    
  private:    
      int x[5];   
  public:    
      Point()  
      {  
          for (int i=0;i<5;i++)  
          {  
              x[i]=i;  
          }  
      }   
      int& operator[](int y);  
  };    
  int& Point::operator[](int y)  
  {  
      static int t=0;  
      if (y<5)  
      {  
          return x[y];  
      }  
      else  
      {  
          cout<<"下标出界";  
          return t;  
      }     
  }  

  //调用
  Point a;  
  for (int i=0;i<10;i++)  
  {  
           cout<<a[i]<<endl;//无论i下标是否越界，每当使用a[i]时，都会调用[]的重载，这里返回值做为右值  
  }  
  a[0]=10;  //这里返回值作为左值
  ```

  **重载[]的目的：对象[x]类似于数组名[x]；可以对下标越界做出判断**

  1、只能使用成员函数重载

  2、函数名：operator[] (参数列表)

  3、参数列表：有且仅有一个参数，表示下标的值，通常为整型，但是也可以为字符串（当作下标）

  4、显式调用：Obj[arg]        对象[下标]

  ​      隐式调用：obj.operator[] (arg)

  5、返回类型：

  ​	返回成员实际类型的引用（例如 int&）。

  ​	因为返回值可以做左值和右值，应该不使用返回值为const类型。但是，为了能访问const对象，下标运算符重载有非const和const两个版本。

  ​	在这里使用int&  Point::operator[] (int y)，是因为返回的值可以做左值，也可以做右值，则必须使用返回引用





* 重载运算符()

  ```c++
  class Point    
  {    
  private:    
      int x;   
  public:    
      Point(int x1)  
      {   x=x1;}    
      const int operator()(const Point& p);  
  };    
    
  const int Point::operator()(const Point& p)  
  {  
      return (x+p.x);  
  }  

  //调用
  Point a(1);  
  Point b(2);  
  cout<<a(b);
  ```

  **重载()的目的：对象() 类似于 函数名(x)**

  1、只能使用成员函数重载

  2、重载后还可以继续重载

  3、函数名：operator()(参数列表)

  4、参数列表：参数随意，根据具体情况而定

  5、显式调用：Obj(x)

  ​      隐式调用：obj.operator( )(x) 

  6、返回类型：

  ​	返回成员的实际类型随意，具体由程序员根据函数体定义。

  ​	因为返回值只能做右值，只读，应该使用返回值为const类型。

  ​

   


* 重载输入输出操作符<< 和 >>

  ```c++
  class Point    
  {    
  private:    
      int x;   
  public:    
      Point(int x1)  
      {   x=x1;}   
      friend ostream& operator<<(ostream& cout,const Point& p);//使用友元函数重载<<输出运算符  
      friend istream& operator>>(istream& cin,Point& p);//使用友元函数重载>>输出运算符  
  };    
  ostream& operator<<(ostream& cout,const Point& p)  
  {  
      cout<<p.x<<endl;  
      return cout;  
  }  
  istream& operator>>(istream& cin,Point& p)  
  {  
      cin>>p.x;  
      return cin;  
  }  

  //调用
  Point a(1);  
  Point b(2);  
  cin>>a>>b;  
  cout<<a<<b<<endl; 
  ```

  1、重载方式：**只能使用友元函数重载，返回值和参数都使用引用。**

  2、函数名：

  ​	输出流： operator<<(参数表)

  ​	输入流： operator>>(参数表)

  3、参数列表：**固定，两个参数均为引用。**

  ​	输出流： 必须是两个参数：输出流ostream& 和 对象

  ​                        第一个操作数cout，定义在文件iostream中，是标准类类型ostream的对象的引用。

  ​                        如：ostream& cout,const Point& p

  ​       输入流：必须是两个参数：输入流istream& 和 对象

  ​                       第一个操作数是cin，定义在文件iostream，是标准类类型istream的对象的引用

  ​                       如：instream& cin,const Point& p

  4、函数调用：

  ​	输出流： 显式调用：cout<<对象

  ​                        隐式调用： operator<<(cout，对象)

  ​	输入流：显式调用：cin>>对象

  ​                        隐式调用： operator>>(cin，对象)

  5、**返回类型：固定。**（使用引用，满足连续输出）

  ​	输出流： 返回ostream&

  ​                        如：ostream& operator<<(ostream& cout,const Point& p)

  ​       输入流：返回：istream&

  ​                        如：istream& operator>>(istream& cin,Point& p)

  6、**为什么输入输出操作符的重载必须是友元函数？**

  ​	因为成员函数要求是有对象调用，第一个参数必须是函数所在的类的对象，而<< 和 >> 的第一个参数是流对象的引用，因此不能使用成员函数。