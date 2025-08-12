# GDB 内存查看指令总结

## 1. 基本指令：`x` 命令

用于查看内存内容，语法为：`x/nfu <address>`

- **n**：显示的内存单元数量(==也就是查看多少个元素==)。
- **f**：显示格式（`x` 十六进制，`d` 十进制，`c` 字符，`s` 字符串等）。
  - `x`：十六进制（hexadecimal，默认）
  - `d`：十进制（decimal）
  - `u`：无符号十进制（unsigned decimal）
  - `o`：八进制（octal）
  - `t`：二进制（binary）
  - `a`：地址（address）
  - `c`：字符（character）
  - `f`：浮点数（float）
  - `s`：字符串（string）
- **u**：单元大小(==每个元素的大小==)：
  - `b`：1 字节 (byte)
  - `h`：2 字节 (halfword)
  - `w`：4 字节 (word)
  - `g`：8 字节 (giant)
- **address**：内存地址（如 `&a`、`$sp` 或具体地址）。

## 2. 示例

- 查看 4 个 4 字节内容（十六进制）：`x/4xw &a`
  - 显示变量 `a` 地址开始的 16 字节（4 个 4 字节单元）。
- 查看字符串：`x/s &a`
- 查看 5 个十进制数：`x/5d &a`

## 3. 记忆单元大小 (`b`, `h`, `w`, `g`)

- **联想单词**：
  - `b` → Byte (1 字节)
  - `h` → Halfword (2 字节)
  - `w` → Word (4 字节)
  - `g` → Giant (8 字节)
- **字节递增**：1-2-4-8（2^0 到 2^3）。
- **缩写**：Big Hippo Wears Glasses（`B-H-W-G`）。

## 4. 注意事项

- 确保地址有效（如 `&a` 或寄存器）。
- 非标准单元大小（如 `x/4x4`）可能无效，推荐用 `x/4xw`。
- 使用 `display` 自动显示内存：`display/4xw &a`。
	- `display /nfu <address> `方式使用
	- `info display`查看所有自动显示的内存
	- `undisplay <编号>`取消该编号的自动显示内存
- 修改内存：`set *(int*)address = value`。

