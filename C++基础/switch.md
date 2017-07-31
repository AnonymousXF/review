```c++
链接：https://www.nowcoder.com/questionTerminal/269bd2347181496a81d8af411f011db5
来源：牛客网

#include<stdio.h>
main()
{ 
    int s=0,n;
    for(n=0; n<4; n++)
    { 
        switch(n)
        { 
           default:s+=4;
           case 1:s+=1;
           case 2:s+=2;
           case 3:s+=3;
        }
    }
    printf ("%d\n",s);
}
```



第一次循环，n=0，没有匹配的case，从default开始执行，由于没有break语句，后面的case会继续执行，因此，第一次循环：s = 4 + 1 + 2 + 3 =10

第二次循环，n=1，从case 1开始执行，由于没有break，后面的语句会顺序执行，因此，第二次循环：s = 10 + 1 + 2 + 3 =16

第三次循环，n=1，从case 2开始执行，由于没有break，后面的语句会顺序执行，因此，第三次循环：s = 16 +  2 + 3 =21

第四次循环，n=1，从case 3开始执行，因此，第四次循环：s = 21 + 3 =24

最后输出为24.

