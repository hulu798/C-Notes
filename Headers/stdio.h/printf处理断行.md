### `C`语言处理`printf()`代码中断行提高代码可读性



- 方法1:

	多个`printf()`输出,在每个`printf()`最后不加`\n`从而实现代码多行而在输出中不断行

- 方法2:

	用反斜杠`(\)`和enter组合断行.如果同时需要换行需要在下一行开始输入'\n'  ,但是,下一行的代码如果必须从最左端开始,需要删除缩进.否则打印字符串的时候缩进也会包含在内

- ==方法3:==

	`ANSIC`引入的字符串连接。在两个用双引号括起来的字符串之间用空白隔开，C编译器会把多个字符串看作是一个字符串。

```c
//断行示例
#include<stdio.h>
int main()
{
    printf("Here is a long string.\n");
    printf("\n");              //常字符串输出
    
    printf("Here is a");       //分开两行打印,且保证打印在同一行
    printf(" long string.\n");
    printf("\n");
    
    printf("Here is a\        
 long string.\n");           
    printf("\n");               //\+enter断行
           
    printf("Here is a"          //引号断开长字符串
          " long string.\n");
    return 0;
}
```



