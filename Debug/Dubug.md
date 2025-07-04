[TOC]

# Vscode Debug C/C++

## ==Debug快捷键==

### 建立/删除断点:F9

配合ctrl+G加行号快速定位到对应行号建立和删除断点,

### 启动Debug:F5

点击F5会跳到第一个断点,在输入F5会跳到第二个断点,在不同部分调试很方便,如果断点在循环中,每一次循环都算一个断点,比如只有一个断点在循环内,每次按F5都会跳到下一轮循环的断点位置,经常配合F9使用,在需要跳出循环的时候清除循环体的断点在F5跳到下一个断点(调试过程可以F9建立和删除断点),配合ctrl+G快速跳转行建立/删除断点

### Step into:F11

单步执行（一行一行代码执行），如果遇到子函数，就会进入子函数，并且继续单步执行。就是每一行需要执行的代码都不跳过，一行一行进行

### Step over:F10

在单步执行的时候，如果遇到子函数，并不会进入子函数，而是把子函数当做一整步执行完成，从而继续执行函数调用位置下的代码。(遇到函数调用不会步入函数内部,而是一条语句执行完)

> [!warning]
>
> **如果子函数内部有断点,即使使用step over也会步入函数内部**,不过相对于step into,使用step over会直接跳到函数体内部的第一个断点

### step out :shift+F11

当单步执行到子函数内时，用Step out就可以执行完子函数余下部分，并返回到上一层函数

> [!warning]
>
> 如果剩下的语句有断点,会跳到这个断点停止,和上面的情况综合,即**断点一定会断**

## Open Disassembly View

![image-20250501105559464](https://gitee.com/hulu135289/Typora/raw/master/img/20250501105606721.png)

![image-20250501105701164](https://gitee.com/hulu135289/Typora/raw/master/img/20250501105701592.png)

> 通过打开反汇编视图可以查看Debug过程的反汇编指令

## Run and Debug

打开Run and Debug 侧栏:显示与运行、调试和管理调试配置设置相关的所有信息,在variables和watch可以查看和监视变量,查看调试过程中详细信息

## Debug Console

调试控制台,在此处可以查看调试过程中执行语句,也可以输出GBD调试命令控制调试,使用Debug Console可以利用GBD强大的调试功能,查看更加详尽的信息,使用方法为在调试过程中在Debug Console输入

```bash
-exec GDB调试指令 #比如打印a的地址:-exec p &a
```

[GDB官方文档](https://sourceware.org/gdb/)

## breakpoint

- **普通断点**：点击行号左侧设置，用于暂停程序执行
- **条件断点**：右键断点→编辑条件，仅在满足条件时触发（如循环中特定索引）
- **日志断点**：不暂停程序，仅输出日志（右键断点→编辑日志消息）

![image-20250505224054543](https://gitee.com/hulu135289/Typora/raw/master/img/20250505224101759.png)

**Edit breakpoint**:

- $Expression:$条件断点,表达式满足一定条件才会在断点处中断

	- 表达式必须是当前作用域内有效的布尔表达式，VSCode 会根据调试器（如 `Node.js、Python` 等）解析。

		示例：

		- 对于` JavaScript：i === 10`（当变量 i 等于 10 时暂停）
		- 对于` Python：name == "zhangsan"`（当变量 `name` 等于 `"zhangsan"` 时暂停）
		- 复杂条件：`i === 10 && j > 5`（支持 `&&、|| `等逻辑运算符）
- $Hit Count:$命中次数,输入一个数字,遇到这个断点多少次才会停下来

	- 输入一个数字或比较表达式，表示断点触发条件。

		支持的语法：

		- 精确次数：500（第 500 次命中时暂停）
		- 大于等于：>= 500（第 500 次及之后命中时暂停）
		- 大于：> 500（第 501 次及之后命中时暂停）
		- 模数：% 10（每 10 次命中时暂停）
- $Log Message:$日志断点,断点不会停止而是在调试控制台打印设定的信息

	- 输入要在调试控制台输出的消息，支持插值表达式（使用 {} 包含变量或表达式）。

		示例：

		- `JavaScript：Value of i is {i}`（输出变量 i 的值）
		- `Python：Current name: {name}`（输出变量 name 的值）
		- 复杂表达式：`Loop iteration {i}, j = {j}`（输出多个变量）

==**前两种断点在循环次数较多的时候调试起来比较方便**==
