# 堆内存越界 栈内存越界

## 基本概念对比

![image-20250720142534782](https://gitee.com/hulu135289/Typora/raw/master/img/20250720142534876.png)

| 特性 | 堆内存越界 | 栈内存越界 |
|------|-----------|-----------|
| **内存区域** | 动态分配的堆区 | 函数调用栈区 |
| **分配方式** | malloc/new等显式分配 | 自动变量隐式分配 |
| **生存周期** | 程序员手动管理 | 函数调用周期 |
| **增长方向** | 向上增长(低→高地址) | 向下增长(高→低地址) |

## 内存布局与保护机制

### 堆内存布局
```
[堆管理器元数据]
[已分配块1] [元数据] [已分配块2] [元数据] [空闲空间]
├─────────── 连续的大块内存页面 ──────────┤
│          通常具有相同的读写权限          │
```

### 栈内存布局  
```
[高地址] 栈底
├─ 函数A的栈帧 [局部变量][返回地址][参数]
├─ 函数B的栈帧 [局部变量][返回地址][参数]  
├─ 函数C的栈帧 [局部变量] ← 当前位置
└─ [保护页/未分配区域] 
[低地址] 栈顶增长方向
```

## 越界行为对比

### 堆内存越界特点

#### ✅ 不容易立即崩溃
```c
int *p = malloc(40);  // 分配10个int
p[15] = 100;  // 越界写入，通常不会立即段错误
p[20] = 200;  // 继续越界，可能还是"安全"的
// 原因：仍在同一个可写内存页面内
```

#### ⚠️ 破坏数据完整性
- **破坏其他变量数据**：覆盖相邻分配的内存
- **破坏堆管理元数据**：损坏malloc/free的控制信息
- **链表结构损坏**：堆内存管理通常使用链表

#### 🕰️ 延迟暴露问题
- 问题在越界时发生，但在后续操作时才暴露
- 常见触发点：`realloc()`, `free()`, 程序退出
- Windows: `RtlValidateHeap` 检测到堆损坏
- Linux: `glibc` 检测到 `corruption`



![image-20250720142411689](https://gitee.com/hulu135289/Typora/raw/master/img/20250720142418871.png)

### 栈内存越界特点

#### 💥 容易立即崩溃

```c
void func() {
    int arr[10];
    arr[1000000] = 5;  // 很可能立即段错误
}
// 原因：快速超出栈空间边界，碰到未分配或保护页面
```

#### 🎯 影响程序流程控制
- **覆盖返回地址**：导致程序跳转到错误位置
- **覆盖函数参数**：影响程序逻辑
- **栈帧破坏**：影响函数调用链

#### ⚡ 立即或近期暴露
- 通常在函数返回时暴露（返回地址被修改）
- 或在访问时立即段错误（超出栈边界）

## 典型错误场景

### 堆内存越界场景

#### 场景1：数组边界检查缺失
```c
int *data = malloc(10 * sizeof(int));
for (int i = 0; i <= 10; i++) {  // 错误：应该是 i < 10
    data[i] = i;  // data[10] 越界
}
// 现象：可能不立即崩溃，在后续 realloc,free(data) 时报错
```

#### 场景2：字符串缓冲区溢出
```c
char *buffer = malloc(20);
strcpy(buffer, "这是一个超过20字节的很长字符串");  // 越界
// 现象：破坏相邻内存，程序继续运行但数据损坏
```

#### 场景3：动态数组扩容错误
```c
// 你的原始代码就是这种情况
for (int *p = *data + arrLen; p >= *data + location; p--)
    *(p + 1) = *p;  // 写入 *data + arrLen + 1，超出数组边界
```

### 栈内存越界场景

#### 场景1：局部数组越界
```c
void func() {
    int arr[100];
    arr[150] = 5;  // 可能覆盖其他局部变量或返回地址
}
// 现象：函数返回时可能崩溃或跳转到错误地址
```

#### 场景2：递归过深
```c
void recursive_func(int n) {
    int large_array[1000];  // 每次递归消耗大量栈空间
    if (n > 0) recursive_func(n - 1);
}
// 现象：栈空间耗尽，Stack Overflow
```

#### 场景3：缓冲区溢出攻击
```c
void vulnerable_func(char *input) {
    char buffer[64];
    strcpy(buffer, input);  // 如果input超过64字节
}
// 现象：可能覆盖返回地址，被恶意利用
```

## 检测与调试对比

### 堆内存越界检测

#### 编译时工具
```bash
# AddressSanitizer - 最推荐
gcc -fsanitize=address -g test.c

# Valgrind (Linux)
valgrind --tool=memcheck ./test

# Visual Studio (Windows)
# 启用 /RTC1 运行时检查
```

#### 运行时检测
```c
// Windows Debug Heap
#ifdef _DEBUG
_CrtSetDbgFlag(_CRTDBG_ALLOC_MEM_DF | _CRTDBG_LEAK_CHECK_DF);
#endif

// 手动边界检查
#define SAFE_MALLOC(size) safe_malloc(size, __FILE__, __LINE__)
```

### 栈内存越界检测

#### 编译器保护
```bash
# 栈保护 (Stack Canary)
gcc -fstack-protector-strong test.c

# 栈不可执行保护
gcc -Wl,-z,noexecstack test.c
```

#### 静态分析
```bash
# 编译器警告
gcc -Wall -Wextra -Warray-bounds test.c

# 静态分析工具
cppcheck --enable=all test.c
```

## 错误信息对比

### 堆内存越界错误信息

#### Windows
```
HEAP[test.exe]: Heap block at 0x00000000 modified at 0x00000000 past requested size of 40
ntdll.dll!RtlValidateHeap detected heap corruption
```

![image-20250720142927503](https://gitee.com/hulu135289/Typora/raw/master/img/20250720142927638.png)

#### Linux

```
*** Error in `./test': corrupted size vs. prev_size: 0x0000000000000000 ***
malloc(): memory corruption
free(): invalid pointer
```

#### AddressSanitizer
```
ERROR: AddressSanitizer: heap-buffer-overflow on address 0x60200000eff4
WRITE of size 4 at 0x60200000eff4 thread T0
    #0 0x4007a8 in add test.c:48
```

### 栈内存越界错误信息

#### Segmentation Fault
```
Segmentation fault (core dumped)
Program received signal SIGSEGV, Segmentation fault.
```

#### Stack Smashing Detection
```
*** stack smashing detected ***: ./test terminated
Aborted (core dumped)
```

#### Windows DEP
```
Access violation - code c0000005 (!!! second chance !!!)
The instruction at 0x00000000 referenced memory at 0x00000000
```

## 预防策略对比

### 堆内存越界预防

#### 编程习惯
```c
// 1. 边界检查
if (index >= 0 && index < array_size) {
    array[index] = value;
}

// 2. 安全的内存操作
strncpy(dest, src, sizeof(dest) - 1);
dest[sizeof(dest) - 1] = '\0';

// 3. 智能指针 (C++)
std::vector<int> vec(10);  // 自动边界检查
std::unique_ptr<int[]> ptr(new int[10]);
```

#### 工具辅助
- 使用 `valgrind`, `AddressSanitizer`
- 启用 Debug Heap
- 定期的内存完整性检查

### 栈内存越界预防

#### 编程习惯
```c
// 1. 合理的数组大小
#define MAX_SIZE 1000
int arr[MAX_SIZE];

// 2. 检查递归深度  
void recursive_func(int n, int depth) {
    if (depth > MAX_RECURSION_DEPTH) return;
    recursive_func(n - 1, depth + 1);
}

// 3. 使用动态分配替代大型局部数组
int *large_array = malloc(LARGE_SIZE * sizeof(int));
```

#### 编译器保护
- 启用栈保护 (`-fstack-protector`)
- 启用边界检查警告 (`-Warray-bounds`)
- 使用安全的库函数

## 性能影响对比

### 堆内存越界检测成本
- **AddressSanitizer**: 2-3x 内存开销，2x 运行时间
- **Valgrind**: 10-50x 运行时间开销
- **Debug Heap**: 轻微性能影响，主要是内存开销

### 栈内存越界检测成本
- **Stack Canary**: 几乎无性能影响
- **编译时检查**: 无运行时开销
- **DEP/NX保护**: 无明显性能影响

## 总结要点

### 堆内存越界
- **特点**: 温和但危险，延迟暴露，难以调试
- **后果**: 数据损坏，程序逻辑错误，安全漏洞
- **检测**: 需要专门工具，运行时开销较大
- **预防**: 严格的边界检查，使用安全的内存管理

### 栈内存越界  
- **特点**: 激烈但直接，通常立即暴露
- **后果**: 程序崩溃，控制流劫持，安全攻击
- **检测**: 编译器内置支持，开销较小
- **预防**: 合理的栈使用，编译器保护机制

### 共同建议
1. **开发阶段**: 启用所有可用的检测工具
2. **测试阶段**: 使用内存检测工具进行充分测试  
3. **生产环境**: 保留必要的运行时保护
4. **代码审查**: 重点关注内存边界操作
5. **安全编程**: 使用安全的库函数和编程模式