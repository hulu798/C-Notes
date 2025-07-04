# `ctype.h`头文件

> 作用
> ==**字符分类:**==判断一个字符是否属于特定的字符类别，例如字母、数字、空白字符等<br>==**字符转化:**==将大写字母转换为小写字母，或者将小写字母转换为大写字母

## 字符分类

![image-20250223125800254](https://gitee.com/hulu135289/Typora/raw/master/img/20250223125808210.png)

> [!important]
>
> 这些函数接受一个字符作为参数,如果该字符属于某指定的类别，就返回一个非零值(真);否则,返回0（假）

```c
#include<stdio.h>
#include<ctype.h>

int main()
{
	char a;
	while((a=getchar())!='\n')
	{
		if(isalpha(a))
			putchar(a+1);
		else
			putchar(a);
	}
	return 0;
}          //输入hehwahfehad ajk ajkd2'1' /p41[4   输出ifixbigfibe bkl bkle2'1' /q41[4
```

> 以上是一个输入`putchar`一行将字母转换为$ASCII$的下一个字符输出,如果不是字母照常输出的程序

## 字符转换

![image-20250223130301336](https://gitee.com/hulu135289/Typora/raw/master/img/20250223130301422.png)

```
to lower转换为小写
to upper转换为大写
```

