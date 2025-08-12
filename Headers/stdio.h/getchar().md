# `getchar()`

## 1.作用

==吸收一个字符并且返回这个字符`ASCII`码值的整形==

比如

```c
int a=getchar();
printf("%d",a);//输入A,输出65
```



## 2.返回值

`getchar()`返回值用`int`类型变量接收,不用`char`接收的原因

- 用`int`接收的合理性

`getchar()`返回值类型是`int`类型,用`int`类型变量接收可以保证前后一致. 但是我们说过`int`和`char`类型差别只在位数,没有浮点数类型差别不大😕😕(只要不溢出问题不大)

- ### **解释为什么`char`接收返回值不行**

`C`语言中有一个宏`EOF`定义为-1,在终端输入`ctrl+z`即为输入`end of file`表示文章末尾

`getchar()`读取字符的时候遇到`EOF`结束读取或者已经读取了一个字符,所以需要`EOF`表示文章结束的信号

如果用`char`接收返回值

- #### **如果`char`类型是`unsigned char`**

	`unsigned char`范围是0~255,这个时候不能表示-1,即不能得到文章结束的标志

- #### **如果是`signed char`类型**

	在扩展`ASCII`字符中取值由`0~127`增加到`0~255`,这个时候如果用`signed char`接收会出现某个字符转换为`signed char`类型后为-1,这个时候可能导致文章不正常接收

综上所述,`getchar()`返回值只能用`int`类型变量接收

<img src="https://gitee.com/hulu135289/helloworld/raw/master/img/20250215210333813.png" alt="循环条件getchar()!=EOF" style="zoom:50%;" />

## 3.结束输入的信号

>`getchar`读取到`EOF`(对应宏定义为-1)会认为是文章结束的信号,Windows系统ctrl+Z输入`EOF`信号

> [!warning]
>
> **Windows系统**要求`Ctrl+Z`必须位于行首才能触发EOF。如果在行中间输入`Ctrl+Z`，终端不会立即结束输入，而是将其视为普通字符或忽略
> **Linux**系统`ctrl+D`为文本结束信号,同理需要位于行首输入`ctrl+D`才会认为输入文本结束信号
