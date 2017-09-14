##### C++写函数时参数为指针或引用类型时应给指针或引用加const

如果函数参数是指针，且仅作输入用，则必须在类型前面加上const，以用来防止该指针在函数体内被意外修改。

举个例子：

void print(char *str)

{

printf("%s\n", str);

}

const char *str = "hello world";

print(str);

上面的代码会报错

当参数是char *时不能接受const char *类型的参数，只能接受char数组或动态分配的数组。而当参数类型是const char *时，可以接受char *、const char *;也就是提高了代码的参数的可接受范围。

同理引用也是如此，但const引用比const指针更为强大，初始化的过程中可以采用任意的对象,const对象，非const对象，甚至其他类型的数据。const引用支持隐式类型转换。而指向const的指针则不能，只能指向同一类型的数据，但是可以采用强制类型转换，初始化或者赋值过程中对数据类型没有要求，可以是const对象的地址，也可以是非const对象的地址。

**const引用和指向const对象的指针都是使自己指向的对象不能修改**，采用const的指针或者引用就能避免原始数据修改。