# x86从实模式到保护模式

此仓库为书籍<<x86汇编语言从实模式到保护模式>>各个章节习题的尝试解答与知识点总结.  
比如, `8_1.asm`对应第8章检测点1解答程序, `8_1.md`对应第8章检测点1问答题的解答, `c08.md`对应第8章最后章节练习题的解答.

## 标志寄存器

| | |
|:-:|:-:|
| OF| (有符号数)是否溢出|
| DF| 方向|
| IF| 允许(1)或关闭中断|
| SF| 符号标志, 结果为负时置1, 否则置0|
| ZF| 为0置1|
| PF| 奇偶标志, 为偶置1|
| CF| (无符号数)进位标识符, 最高位产生进位为1, 否则为0|

## Chapter 5

- 标号: 后面可以添加:, 也可以不添加. 可以由`字母`, `数字`, `_ $ # @ ~ . ?`组成, 但必须以`字母` `.` `_` `?` `@`中的任意一个开头.
- 数据声明: DB, DW, DD, DQ, 数据之间可以用`,`隔开, 声明数据的大小不能超过相应的指令最大数字, 比如DB指令允许的最大数字为`0xff`

## Chapter 7

- 声明字符串与字符: 用`' '`括起来, 比如 `db 'a'`, `db '1+2+3+...+100='`
- 8086四个段寄存器: ds(数据段), cs(代码段), es(附加段), ss(堆栈段)
- div指令
    - 16位除以8位: 被除数必须在AX中, 除数可以由8位的通用寄存器或者内存单元提供. 商在AL中, 余数在AH中. 
    - 32位除以16位, 高16位在DX中, 低16位在AX中, 除数可以由16位通用寄存器或者内存单元提供. 商在AX中, 余数在DX中.
- 8086 push只支持字, 80386以后支持双字, 四字.
- CF: Carry flag, 用于无符号数的溢出判断, 最高位(比如对于ax来说就是第16位)产生进位后置1
- OF: Overflow flag, 用于有符号数的溢出判断, 次高位产生进位后置1, 比如0x7fff + 1 = 0x8000, OF置1(正数变负数) 

## Chapter 9

- **实模式**下的中断向量表要求(处理器要求, 而不是操作系统)集中放在内存中0x00000开始的地方, 到0x003ff结束, 共1KB空间. (Interrupt Vector Table, IVT)  
- 栈对于整个程序来讲是非常重要的, 要修改栈, 不仅要修改SS, 还要修改SP, 处理器在设计的时候规定修改SS的时候此条指令与下条指令之间不接受中断, 以此来防止刚修改完SS就要处理中断的情况, 因为处理中断的开始需要push来保护现场, 所以这种情况是绝对不允许出现的, 理应在修改SS后立马跟上一条修改SP的指令.  
- 实模式下中断向量表中的中断号对应的物理地址为中断号乘以4, 原因是一个中断入口占用两个字, 也就是32位.
- 中断大概有三种, 一为外部硬件中断, 由连接在CPU上的NMI与INTR引脚来控制, 其中NMI(Non Maskable Interrupt)为不可屏蔽中断, INTR引脚的输入来自8259芯片, 此芯片有8个引脚来指定相应的中断(IR0~IR8)并可以级联. 二是内部中断, 比如除法的除数为0, 在处理器内部发生中断. 三是软中断, 由`int`指令控制,  格式大致分为`int3`, `int imm8`, `into`, int3为断点中断, 常用于调试. int imm8可以查表得到相应的功能, into为溢出中断, 如果执行时OF为1, 则产生4号中断, 否则do nothing.  
- 软中断中比较重要的是BIOS中断, 这些中断是在计算机加电之后BIOS程序执行期间建立的, 也就是在加载主引导扇区之间就可以使用了.

