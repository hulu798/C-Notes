#  `scanf()`读取输入数据

## 读取结束标志

1. 达到设置的字符宽度[^注]，读取结束（超出范围的部分放回缓冲区留给下一个格式说明符读取或者下一个`scanf()`语句）
2. 遇到空白或者不匹配的字符，这个数据类型读取结束，继续下一个数据类型读取或者下一个`scanf()`语句（不匹配的部分放回缓冲区）

> [!tip]
>
> **`scanf()`占位符前面加*可以跳过一种数据类型**,(`printf`是变成变量的形式比如说
>
> ```c
> printf("%x.yf\n",2,3,3.1415)) //让输出数据宽度等格式可以由变量决定
> ```
>
> 

==注意:==
如果输入了一个不匹配后面所有格式说明符的字符，则后面所有都不能正常读取，这是因为`scanf()`用于读取匹配的字符，不匹配的会放回缓冲区.导致对于每一个格式说明符读取的时候都首先读取的到不匹配的字符，则读取结束，不能正常读取.

### 数字类型的数据读入机制

`scanf`首先跳过前置空白直至遇到数字开始**==逐个逐个==**读取，遇到非数字字符或者达到设置的字符宽度`（e.g.%4d）`读取结束，只提取读取的内容，其余内容放回缓冲区.

对于多个格式说明符的一个`scanf`语句，空格，制表符，换行符等空白意味着这个数据读取结束，紧接着读取下一个数据，类似的跳过空白，直至读取第一个数字

**如果有非数字字符读取到视为输入有误，把这个字符放回缓冲区并且从这个字符开始读取写一个数据如果下一个转化说明仍然是数字类型,那么重复上面的步骤.**

<img src="https://gitee.com/hulu135289/helloworld/raw/master/img/20250216155544726.jpg" alt="img" style="zoom: 67%;" />

> 输入`1    2.2   3`由于`scanf`读取的全是整形数据,读取2.2的时候先读取2,小数点不能读取放回缓冲区,然后读取3,由于缓冲区有.2还没读取,使用继续读取.2,读取小数点的时候类型不匹配,读取失败…最后返回正确读取的个数为2

### 机制

`scanf`函数在处理格式字符串时，会根据不同的字符类型执行不同的操作。对于格式说明符（如`%d、%f、%s`等(<font color=red>数字和字符串的转化说明才会跳过空白</font>)），它会按照相应的数据类型去解析输入. 遇到普通字符（如空格、逗号等），它会尝试将输入中的字符与格式字符串中的普通字符进行精确匹配。不过，空格字符是一个特殊的普通字符，它的匹配规则有所不同。<u>当</u>`scanf`<u>在上面几个格式字符串中遇到空格时，它会进入一种 “跳过空白字符” 的状态</u>。从输入缓冲区开始读取字符，只要遇到的是空白字符（空格、制表符、换行符），就会持续跳过这些字符，直到遇到一个非空白字符为止。然后，它会根据后续的格式说明符来处理这个非空白字符及其后续内容。
如`scanf(“%d %d”,&a,&b)`会跳过两个输入整形数据中任意数量空格（即跳过空白）
而%d类型数据读取过程中会默认跳过空白，从而`scanf(“%d  %d”)与scanf(“%d%d”)`表达的意思相同，但是由于%c没有跳过空白的作用，从而`scanf(“%c  %c”)`与`scanf(“%c%c”)`表达意义不同。如果希望读取字符而不是空白，可以在`%c`前面加一个空格(即`scanf(" %c",&a)`)，从而可以跳过`a`字符前面所有空白.

> [!warning]
>
> <font face="黑体" color=blue size=5>进入跳过空白模式后需要读取到数据(正确和不正确读取都可以),否则程序停留在等待输入的地方</font>

## 总结

- 数字类型的转换说明会跳过空白直至遇到数字开始读入，遇到非数字字符（包括空白）或达到设定的字符长度认为本个数据读入完毕。

- `%s`类型同理，但是由于所有字符都属于字符类型，==所以`%s`读取结束只有:star:**空白**或达到设定的字符长度==。

	**注意:**`%s`遇到空格会读入结束比如说输入$hello\ world$实际上读取到的只有$hello$

- `%c`类型特殊，空白也会读取，所以如果不希望读入空白可以在%c前面加上空白.



[^注]:`printf`格式说明符%和类型之间可以填n~1~.n~2~两个数字,但是`scanf`只有%+数字+类型缩写这一种,其代表的意思是`scanf`读取的字符格式,比如说`scanf("%4s",&a)`会读取输入的前4个字符输入$hello$读取到$hell$,在比如`scanf("%3d",&a)`输入1234和12.3举例子,输入1234读取到123,输入12.3读取到12.,但是小数点不是数字匹配失败放回缓冲区,实际上读取到的是12

```c
/*输入长度设定的例子:输入1.2.345.4
输出:1.200000
 .  3  4  5  .  4*/
#include<stdio.h>
int main()
{
    double a;
    scanf("%3lf",&a);
    printf("%f\n",a);
    int b;
    while((b=getchar())!='\n')  //留意细节!=优先级比=高,不加括号先把getchar()赋值给b
        printf(" %c ",b);
    return 0;
}
```







