# 关键字`typedef`–给类型取别名

C语言中`typedef`主要的作用是给类型取别名来简化赋值类型的名称,类型越复杂`typedef`优势越大,同时可以让程序在不同平台上运行修改代码更方便,比如

```c
long a=10;   //不同平台上面long类型占用字节不同需要修改的话需要修改很多部分

typedef long LInt;  //定义了long别名,在不同平台上面只需修改typedef即可
```

**在复杂类型中`typedef`很方便**

## `typedef`使用

定义`typedef`很简单,只需在需要简化的类型正常定义前加`typedef`,然后把变量名改为简化的类型名即可

```c
//现在有一个一个函数指针:指向一个含有两个整形参数返回值为整形的函数
int (*ptr)(int ,int);
//定义typdef
typedef int (*FunctionPtr)(int ,int );
//这样就定义好了一个函数指针类型,以后所有的含有两个整形参数返回值为整形的函数指针都可以用类型FunctionPtr定义
FunctionPtr fp;
```



在比如一个数组
```c
int arr[3][4]; //正常定义
typedef int R3C4Arr[3][4];
R3C4Arr array;  //这样就可以定义3行4列的的二维数组新类型名,直接类型+名称就可以使用了
```

由此可见只要能写出定义某种类型变量的定义就可以写出`typedef`的简化形式,只需在这个变量前面加上`typedef`,变量名改为简化名即可

另外`typedef`和`#define`区别还是很大的,`#define`是预处理指令,在预处理的时候宏就已经展开了,而且`#define`只能替换,比如把A替换为20$:$`#define A 20`
`#define`只是简单的替换,

```c
#define identifier replacement  //define使用
```

- `identifier` 是宏名。

- `replacement` 是宏展开时替换的内容，可以是单个标记（token）、多个标记，或复杂表达式。

比如`#define A 20 10 230 34949 abc`预处理的时候会把A替换为后面的全部内容,仅仅是简单的替换

![image-20250421170042185](https://gitee.com/hulu135289/Typora/raw/master/img/20250421170049575.png)

这里注意的是宏名需要满足标识符命名规则:数字字母下划线,数字不能开头,比如说上面定义宏名A

![image-20250421171011304](https://gitee.com/hulu135289/Typora/raw/master/img/20250421171011498.png)



> **总结**
>
> ①先按定义变量的方法写出定义体（如：`int i;`）
>
> ②将==变量名换成新类型名==（例如：将`i`换成`Count`）
>
> ③在最前面加 `typedef`(例如：`typedef int Count`）
>
> ④然后可以用新类型名去定义变量。简单地说,就是按定义变量的方式,把变量名换上新类型名,并且在最前面加 `typedef`就声明了新类型名代表原来的类型