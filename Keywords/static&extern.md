# ` static`

`static`本意是静态的意思,用static修饰标识符可以让标识符由存储位置由堆栈区转到静态区,我们都知道栈区存储的是自动变量(局部变量和函数形参),会自动分配和释放内存,具有局部作用域,而堆区内存动态分配,手动释放,静态区的变量生命周期延长至整个程序结束才销毁释放内存,用`static`修饰标识符可以延长标识符的生命周期

- 用`static`修饰局部变量可以延长变量的生命周期,但是作用域不变,还是只能进入变量作用域才能访问,但是由于存储在静态区,变量的值会保存,即**每次进入变量的作用域会保留上一次变量的值,且初始化只会初始化一次**,因为静态变量定义是在编译阶段执行的,在编译阶段已经初始化了,后续会跳过变量的定义语句

<img src="https://gitee.com/hulu135289/Typora/raw/master/img/20250426104522900.png" alt="image-20250426104515419" style="zoom:50%;" />

> 此处用`static`修饰局部变量a,让a变为静态变量,在编译阶段就会指向`int a=1;`这条语句,此后每次调用函数都不会执行,即使只初始化一次,而且是在编译阶段(调试可以发现会直接跳过这条语句,可以验证在编译阶段执行),由于存储在静态区,会保留a的值,从而输出1 2 3 4 5

![image-20250426110645934](https://gitee.com/hulu135289/Typora/raw/master/img/20250426110646048.png)

- `static`修饰全局变量&修饰函数

	用`static`修饰具有文件作用域的变量,**如果本身标识符具有外部链接属性**,会使其链接属性由external变为internal,其他源文件不能引用这个标识符,比如**全局变量和函数**,用`static`修饰后不再具有外部链接属性,生命周期保持不变(还是存储在静态区)

**总结**:`static`修饰局部变量和全局变量效果不同,一个是改变存储位置进而改变生命周期,一个是改变链接属性

# `extern`声明来自外部的符号

注意声明来自外部的符号用`extern,`但是这个**标识符需要有外部链接属性**,示例:

```c
//add.c文件
int add(int x,int y)
{
    return x+y;
}
```

```c
//main.c
#include<stdio.h>
extern int add(int,int );
int main()
{
    printf("%d\n",add(10,20));
}
```



 
