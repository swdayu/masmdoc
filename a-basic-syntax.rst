基本语法
=========

整数常量
--------

::

    sign digits [radix]
    sign = ["+" | "-"] .

基数后缀，如果不指定基数默认是十进制：

* h   - 十六进制，必须以数字开头以与标识符区别
* o/q - 八进制
* d/t - 十进制
* b/y - 二进制
* r   - 编码的实数

整数表达式
----------

算术操作： ::

    ()    括号            优先级1
    + -   正负一元操作     优先级2
    * /   乘除            优先级3
    MOD   取模            优先级3
    + -   加减            优先级4

例如： ::

    4 + 5 * 2
    12 - 1 MOD 5
    -5 + 2
    (4 + 2) * 6

实数常量
--------

::

    [sign] integer "." [integer] [exponent]
    exponent = "E" sign integer .

例如： ::
    
    2.
    +3.0
    -44.2E+05
    26.E5

编码的实数，是用32位十六进制整数表示的对应编码的单精度浮点。例如 +1.0 的二进制表示为：
0011 1111 1000 0000 0000 0000 0000 0000，可以用如下的编码的实数表示： ::

    3F800000r

字符和字符串
------------

::

    'A' "d"
    'ABC' 'X' "Good night, Gracie" 'Say "Good night," Gracie'

标识符
-------

标识符可以定义变量、常量、过程、或代码标签。标识符可以包含 1 到 247 个字符，大小写不敏
感，第一个字符必须是字母、下划线、@、?、$，后续字符还可以是数字，标识符不能与汇编器的保
留字相同。一般应该避免使用 @ 以及下划线开头的标识符，因为它们是被汇编器和高级语言保留使
用的标识符前缀。

可以使用 -Cp 命令行选项使得所有关键字和标识符是大小写敏感的。

保留字
------

1. 指令助记符
2. 寄存器名称
3. 汇编命令
4. 属性，例如 BYTE 和 WORD
5. 操作符
6. 预定义符号，例如 @data

保留字包括类型标识符以及： ::

    BASIC C FAR FAR16 FORTRAN NEAR NEAR16 PASCAL STDCALL SYSCALL VARARG

寄存器名称包括： ::

    AH AL AX BH BL BP BX CH CL CR0 CR2 CR3 CS CX DH DI DL DR0 DR1
    DR2 DR3 DR6 DR7 DS DX EAX EBP EBX ECX EDI EDX ES ESI ESP FS GS
    SI SP SS ST TR3 TR4 TR5 TR6 TR7

预定义符号包括：

**$**
    当前地址位置计数器的值。
**?**
    用于数据声明，表示数据分配但未初始化。
**@@:**
    定义仅在 label1 和 lable2 之间可见的代码标签，lable1 是代码段的开始或者前一个 @@:
    标签，label2 是代码段的结束或者下一个 @@: 标签。见 @B 和 @F。
**@B**
    前一个 @@: 标签的地址位置。
**@F**
    下一个 @@: 标签的地址位置。
**@CatStr(string1{, string2})**
    行函数用于拼接一个或多个字符串，返回一个字符串结果。
**@code**
    文本宏，代码段的名称。
**@CodeSize**
    TINY、SMALL、COMPACT、FLAT 模型是 0，MEDIUM、LARGE、HUGE 模型是 1。
**@Cpu**
    一个比特掩码指定处理器模式。
**@CurSeg**
    文本宏，当前分段的名称。
**@data**
    默认数据组的名称，在 FLAT 内存模型下展开为 FLAT，在其他内存模型下展开为 DGROUP，是
    一个文本宏。
**@DataSize**
    TINY、SMALL、MEDIUM、FLAT 模型是 0，COMPACT、LARGE 模型是 1，HUGE 模型是 2。
**@Date**
    文本宏，mm/dd/yy 格式的系统时间。
**@Environ(envvar)**
    宏函数，环境变量 envvar 的值。
**@fardata**
    文本宏，.FARDATA 汇编命令定义的分段名称。
**@fardata?**
    文本宏，.FARDATA? 汇编命令定义的分段名称。
**@FileCur**
    文本宏，当前文件名称。
**@FileName**
    文本宏，被汇编的主文件的 base 名称。
**@InStr([position,] string1, string2)**
    宏函数，在 string1 中的位置 position 开始查找 string2，返回找到的位置或者没有找到
    返回 0。
**@Interface**
    语言参数的信息。
**@Line**
    当前文件的源文件行号。
**@Model**
    TINY 内存模式 1，SMALL 2，COMPACT 3，MEDIUM 4，LARGE 5，HUGE 6，FLAT 7。
**@SizeStr(string)**
    宏函数，返回字符串的长度。
**@stack**
    文本宏，near stacks 是 DGROUP，far stacks 是 STACK。
**@SubStr(string, position [, length])**
    宏函数，返回从 string 位置 position 开始的，对应长度的子字符串。
**@Time**
    文本宏，hh:mm:ss 形式的 24 小时格式的系统时间。
**@Version**
    文本宏，MASM 的版本号，整数。
**@WordSize**
    16为分段是 2，32为分段是 4。

汇编命令
--------

汇编命令可以定义变量、宏、过程等等。命令名称大小写不敏感，例如 .data、.DATA、.Data 是
相同的。

下面展示汇编命令和指令的区别，其中 DWORD 汇编命令告诉汇编器为双字变量预留空间： ::

    MyVar   DWORD 26
    mov     eax, MyVar

汇编命令的另外一个重要用途是定义段或分区，.data 指定包含变量的程序区域，.text 指定包含
程序指令的分区，.stack 可以定义程序栈段并设置字节大小。

指令
-----

一个指令包含几个基本部分： ::

    [label:] prefix mnemonic [operands] [";" comment]

标签（label）是作为指令和数据位置标记的标识符。放置在指令之前的标签意味着该指令的地址。
同样，放置在变量之前的标签意味着该变量的地址。

数据标签：数据标签标识变量的位置，提供了一种方便的方式来在代码中引用变量。例如，以下定义
了一个名为 count 的变量： ::

    count DWORD 100

汇编器为每个标签分配一个数字地址。可以在一个标签后面定义多个数据项。在以下示例中，array
定义了第一个数字（1024）的位置。其他数字紧跟在内存中： ::

    array DWORD 1024, 2048
          DWORD 4096, 8192

代码标签：程序的代码区域中的标签（指令所在位置）必须以冒号（:）字符结尾。代码标签用作跳
转和循环指令的目标。例如，以下 JMP（跳转）指令将控制权转移到由名为 target 的标签标记的
位置，创建一个循环： ::

    target:
        mov ax, bx
        ...
        jmp target

可以在一个程序中多次使用一个相同的代码标签，只要这个标签在当前封闭的过程内部是唯一的。代
码标签可以与指令共用同一行，也可以单独一行： ::

    L1: mov ax, bx
    L2:

您可以为一些指令添加前缀关键字，这些关键字设置了指令编码的选项。REP、REPE、REPZ、REPNE
和 REPNZ 关键字与字符串指令一起使用，以在单个指令中执行 memcpy 或 strlen 类型的操作。
LOCK 关键字使对内存操作数的某些操作成为原子操作。还可以将它与 XACQUIRE 和 XRELEASE 关
键字结合使用，在支持的处理器上进行硬件锁消除（HLE，Hardware Lock Elision），这在某些情
况下允许更高的事务并行性。

剩余的前缀控制如何编码 AVX 指令。AVX 指令使用 VEX 前缀进行编码，该前缀出现在操作码之前。
它取代了某些字节指令前缀和操作码引导字节。许多 AVX 指令也是 AVX-512 指令，使用 EVEX 前
缀进行编码，后者支持更多选项。MASM 尝试尽可能紧凑地编码指令，但这些关键字允许更多地控制
使用特定指令的编码。它们还用于强制生成 AVX 形式的指令，例如 vex vpdpbusd 指定 VPDPBUSD
指令的 AVX-VNNI 形式，而不是 AVX512-VNNI 形式。当 AVX 指令没有显式指定前缀关键字时，所
选的编码取决于当前的 AVX 编码设置。OPTION AVXENCODING 指令允许更改此设置。

VEX2、VEX3、VEX 和 EVEX 选项在 Visual Studio 2019 版本 16.7 及更高版本中可用。

- REP：按 (E)CX 中的计数重复字符串操作
- REPE REPZ：在相等时重复字符串操作，它受 (E)CX 中的计数限制
- REPNE REPNZ：在不相时重复字符串操作，它受 (E)CX 中的计数限制
- LOCK：以原子方式对内存操作数执行操作
- XACQUIRE：开始 HLE 事务，它最常与 LOCK 前缀一起使用
- XRELEASE：完成 HLE 事务，它最常与 LOCK 前缀一起使用
- VEX：使用 VEX 前缀对 AVX 指令进行编码
- VEX2：使用 2 字节 VEX 前缀对 AVX 指令进行编码
- VEX3：使用 3 字节 VEX 前缀对 AVX 指令进行编码
- EVEX：使用 EVEX 前缀对 AVX 指令进行编码

一些 AVX-512 指令允许指定更多选项。这些选项包括：掩码（Masking），零掩码（Zero-Masking），
内嵌广播（Embedded Broadcast），内嵌舍入（Embedded Rounding），异常抑制（Exception
Suppression）。

- 掩码允许只对向量中的选定元素应用操作。这个选项是通过在目标操作数之后放置一个掩码寄存器
  （从 {k1} 到 {k7}）来控制的。如果掩码寄存器后面跟着 {z}，则目的所有未选定元素都被设置
  为零。这种替代方案被称为零掩码。
- 内嵌广播允许将内存中的标量值应用于向量的所有元素。这个选项是通过在内存操作数中添加元素
  大小和关键字 BCST 来启用的，这与使用 PTR 进行正常内存引用类似。
- 内嵌舍入控制单个浮点指令的舍入模式，而无需设置和重置全局舍入模式。它通过在指令后面跟随
  用大括号括起来的舍入模式来启用。启用时，它还抑制了仅对该指令的所有异常。不进行舍入的浮
  点指令也可以使用类似的选项来抑制所有异常。

使用 AVX-512 选项的示例如下： ::

    vaddps xmm1 {k1}, xmm2, xmm3            ; merge-masking
    vsubps ymm0 {k4}{z}, ymm1, ymm2         ; zero-masking
    vmulps zmm0, zmm1, dword bcst scalar    ; embedded broadcast
    vdivps zmm0, zmm1, zmm2 {rz-sae}        ; embedded rounding
    vmaxss xmm1, xmm2, xmm3 {sae}           ; suppress all exceptions

舍入模式有：

- rn-sae：四舍五入至最近的偶数，抑制所有异常
- rz-sae：向零舍入（即截断），抑制所有异常
- rd-sae：向下舍入（向负无穷大方向），抑制所有异常
- ru-sae：向上舍入（向正无穷大方向），抑制所有异常
- sae：抑制所有异常（不需要舍入）

汇编语言指令可以有零到三个操作数，每个操作数可以是一个寄存器、内存操作数、常量表达式、或
者输入输出端口。一个内存操作数实际上引用的是一个内存位置的地址，因此内存操作数可以用一个
变量的名称，或者包括在方括号内的寄存器表示。一个变量名称隐含的就是数据的地址。

每个指令只允许某些类型的操作数，除 MOVS 和 CMPS 指令外，只有一个操作数可以是内存引用，
所有其他操作数都必须是寄存器引用或常量。

NOP 指令
---------

NOP（无操作）指令是你可以编写的最安全（也是最无用）的指令。它占用 1 字节的程序存储空间，
并且不执行任何操作。编译器和汇编器有时使用它来将代码对齐到偶数地址边界。在以下示例中，第
一条 MOV 指令生成了三个字节的机器代码。NOP 指令将第三条指令的地址对齐到双字边界（4 的偶
数倍）： ::

    00000000    66 8B C3    mov ax,bx
    00000003    90          nop         ; 对齐下一条指令
    00000004    8B D1       mov edx,ecx

x86 处理器被设计为能够更快地从偶数双字地址加载代码和数据。

注释
-----

注释是程序编写者向阅读源代码的人传达程序设计信息的重要方式。通常在程序列表的顶部包括以下
信息：程序目的的描述，创建和/或修改程序的人员姓名，程序的创建和修订日期，关于程序实现的
技术注释。

注释可以通过两种方式指定：单行注释，以分号字符（;）开始。在同一行中分号后的所有字符都被
汇编器忽略。块注释，以 COMMENT 指令和一个用户指定的符号开始。汇编器将忽略所有随后的文本
行，直到出现相同的用户指定符号。例如： ::

    COMMENT !
        这是一条注释。
        这也是一条注释。
    !

我们还可以使用任何其他符号： ::

    COMMENT &
        这是一条注释。
        这也是一条注释。
    &

当然，在整个程序中提供注释是很重要的，特别是在代码的意图不明显的地方。这有助于其他程序员
或未来的你自己理解代码的功能和结构，也便于调试和维护。

代码示例
---------

一个简单的32位汇编程序代码： ::

    TITLE Adds Two 32-bit Intergers (AddTwo.asm)
    .386
    .MODEL FLAT,STDCALL
    .STACK 4096
    ExitProcess PROTO,exitcode:DWORD

    .DATA
    sum DWORD 0

    .CODE
    main PROC
        MOV EAX,5
        ADD EAX,6
        MOV sum,EAX

        INVOKE ExitProcess,0
    main ENDP
    END main

TITLE 汇编命令将整行多标记为注释，可以在这一行放置任何东西。以分号开始到行结束的内容会被
汇编器忽略，可以用作注释。INCLUDE 汇编命令用来包含另一个文件的内容。.code 表明代码段的
开始。PROC 汇编命令表示一个过程的开始，这里过程的名称为 main。后面都是指令助记符以及用
法。ExitProcess 是 Windows 提供的退出函数，用来终止程序。ENDP 汇编命令用来结束 main
过程。

汇编命令 .CODE 标记程序代码区的开始，该分区包含可执行指令。一般情况下，在 .CODE 的下一
行就是程序入口点的声明，并且通常这个入口点程序名为 main。程序入口点是程序执行时最先执行
的第一条指令的位置。

最后，END 汇编命令表示程序的结束，并且还可以指定程序的入口点。可以在 END 之后添加更多的
程序行，但是这些都会被汇编器忽略，因此可以在这里添加任何东西。

定义数据
---------

数据定义的语法如下： ::

    [name] directive initializer {, initializer}

例如： ::

    count DWORD 12345       ; 名字只是一个标记地址的标签
    value BYTE ?            ; 可以使用问号明确不初始化
    list  BYTE 10,20,30,40  ; 可以有多个初始化值
          BYTE 50,60,70,80  ; 名字是可选的
          BYTE 81,82,83,84
    list2 BYTE 0AH,20H,'A'
    list3 BYTE 10, 32, 41H

数据类型汇编命令： ::

    BYTE SBYTE      无符号和有符号字节
    WORD SWORD      无符号和有符号双字节
    DWORD SDWORD    无符号和有符号四字节，DWORD还可以式保护模式下的近指针
    FWORD           六字节整数或保护模式远指针
    QWORD           八字节整数
    TBYTE           十字节整数
    REAL4           四字节单精度浮点
    REAL8           八字节双精度浮点
    REAL10          十字节双精度扩展浮点

定义字符串： ::

    str1 BYTE "Good afternoon",0
    str2 BYTE 'Good night',0
    str3 BYTE "Welcome to the Encryption Demo program "
         BYTE "created here.",0dh,0ah
         BYTE "If you wish to modify this program, please "
         BYTE "send me a copy.",0dh,0ah,0

可以使用 DUP 操作进行数据重复： ::

    BYTE 20 DUP(0)          ; 20个0字节
    BYTE 20 DUP(?)          ; 20个未初始化的字节
    BYTE  4 DUP("STACK")    ; 20个字节："STACKSTACKSTACKSTACK"

定义浮点数据： ::

    rval1 REAL4 -1.2
    rval2 REAL8 3.2E-260
    rarr  REAL4 20 DUP(0.0)

定义一个变量包含另一个变量的32位地址偏移： ::

    val1 DWORD 12345678h
    val2 SDWORD -2147483648
    val3 DWORD 20 DUP(?)
    pval DWORD val3

可以使用 TBYTE 定义打包的 BCD（binary coded decimal）数据，打包的 BCD 数据用一个字节
表示一个两位的十进制数，除了最高字节。其中低 9 字节，每半个字节都表示单个十进制数字，最
高字节表示符号，80h 表示负数，00h 表示整数。因此 TBYTE 可表示的整数范围是： ::

    -999,999,999,999,999,999 ~
    +999,999,999,999,999,999

定义 TBYTE 数据时，必须使用十六进制，因为 MASM 默认将常量解析为二进制整数，而不是 BCD
整数。 ::

    intval TBYTE 8000000000000000001234h    ; 合法
    intval TBYTE -1234                      ; 非法

汇编命令 .data? 用来声明未初始化数据分区，比起放入 .data 分区的未初始化数据，.data? 可
以大量减少初始化阶段指令的大小。 ::

    .data
    small DWORD 10 DUP(0)
    .data?  ; 如果不使用会产生20000字节编译后的程序
    big   DWORD 5000 DUP(?)

符号常量
---------

符号常量是不占用存储空间也不可改变的数据。使用等号汇编命令定义整数常量： ::

    name = expression

例如： ::

    COUNT = 500
    array DWORD COUNT DUP(0)
    mov eax, COUNT
    COUNT = 100
    mov al,COUNT

一个重要的符号常量是 $，它表示当前地址位置计数器： ::

    selfptr DWORD $
    list BYTE 10,20,30,40
    SIZE = ($ - list)
    astr BYTE "This is a long string, containing"
         BYTE "any number of characters"
    SLEN = ($ - astr)
    darr DWORD 10000000h,20000000h,30000000h,40000000h
    ECNT = ($ - darr) / 4

使用 EQU 汇编命令可以将一个符号常量与整数表达式或任意文本关联，与等号汇编命令定义的符号
常量不同的是，该符号常量不能重定义： ::

    name EQU expression         ; 整数表达式
    name EQU symbol             ; 已经定义的符号常量（通过等号或EQU）
    name EQU <text>             ; 出现在尖括号内的任意文本

例如： ::

    PI EQU <3.14159>
    press_key EQU <"Press any key to continue...",0>
    prompt BYTE press_key
    matrix1 EQU 10 * 10
    matrix2 EQU <10 * 10>
    M1 WORD matrix1 ; WORD 100
    M2 WORD matrix2 ; WORD 10 * 10

使用 TEXTEQU 汇编命令也可以定义符号常量，与 EQU 类似，但该符号常量被称为是文本宏，文本
宏可以随时重定义： ::

    name TEXTEQU <text>         ; 关联任意文本
    name TEXTEQU textmacro      ; 关联另一个文本宏
    name TEXTEQU %constexpr     ; 关联一个常量整数表达式

例如： ::

    ROW_SIZE = 5
    COUNT TEXTEQU %(ROW_SIZE * 2)
    MOVE  TEXTEQU <mov>
    SETAL TEXTEQU <MOVE al,COUNT>
    SETAL   ; 被展开成 mov al,10

汇编操作符
----------

::

    expr + expr     加法
    expr - expr     减法
    -expr           取负
    expr * expr     乘法
    expr / expr     除法
    expr1 [expr1]   返回 expr1 + [expr2]
    segment: expr   将 expr 的默认段修改为 segment，segment 可以是一个段寄存器，group
                    名称，分段名称，或分段表达式；expr 必须是常量。
    expr.field.field...
                    返回 expr 加上 field 的结构体内偏移
    [register].field...
                    返回寄存器指向位置加上 field 的结构体内偏移
    <text>          将 text 当成单个字面量元素
    "text"          字符串
    'text'          字符串
    !char           将 char 当成字符字面量而不是一个操作符或符号
    ;text           注释
    ;;text          宏定义内的注释
    %expr           在宏实参中将 expr 的值当成文本
    &parameter&     将 parameter 替换成它对应的实参的值
    ABS             见 EXTERNDEF 汇编命令
    ADDR            见 INVOKE 汇编命令
    expr AND expr   位与
    count DUP(initvalue,...)
                    重复声明数据
    expr EQ expr    如果相等返回 -1，不相等返回 0
    expr GE expr    大于等于返回 -1，否则返回 0
    expr GT expr    大于返回 -1，否则返回 0
    HIGH expr       低16位的高8位的值，MASM 表达式是64位值
    HIGH32 expr     高32位的值，MASM 表达式是64位值
    HIGHWORD expr   低32位的高16位值
    IMAGEREL expr   表达式的映像相对偏移，仅生成 COFF 对象文件时可用
    expr LE expr    小于等于返回 -1，否则返回 0
    LENGTH variable 数据长度
    LENGTHOF var    数据对象个数
    LOW expr        低8位的值
    LOW32 expr      低32位的值
    LOWWORD expr    低16位的值
    LROFFSET expr   表达式的偏移，与 OFFSET 类似，但生成的是一个加载器解析的偏移，允许
                    Windows 重定位代码段
    expr LT expr    小于返回 -1，否则返回 0
    MASK record/recordfieldname
                    返回比特位掩码，将 record 或 recordfieldname 位置位，其他所有位
                    清零，之后的位掩码
    expr MOD expr   取余
    expr NE expr    不相等返回 -1，否则返回 0
    NOT expr        所有比特位取反
    OFFSET expr     表达式的相对分段的偏移
    OPATTR expr     表达式的模式和作用域，低字节的值与 .TYPE 对应的值一样，高字节包含
                    额外的信息
    expr OR expr    位或
    type PTR expr   将 expr 当作类型 type
    [distance] PTR type
                    指定一个指针到类型 type
    SEG expr        返回表达式的段
    expr SHL count  左移
    .TYPE expr      见 OPATTR
    SECTIONREL expr 返回表达式的分区相对偏移，仅当生成 COFF 目标文件时可用
    SHORT label     将 label 的类型设为 short，所有跳转到 label 的值都必须时 short，
                    即在 -128 到 127 个字节范围内
    expr SHR count  右移
    SIZE var        数据的字节数
    SIZEOF var/type 数据或类型的字节数
    THIS type       返回指定类型 type 的一个操作数
    TYPE expr       返回表达式的类型
    WIDTH record/recordfieldname
                    当前的 record 或 recordfieldname 的比特位宽度
    expr XOR expr   位异或

运行时操作符： ::

    expr == expr    相等，仅用于 .IF .WHILE .REPEAT 块，在运行时求值，而不是汇编时
    expr != expr    不等
    expr > expr     大于
    expr >= expr    大于等于
    expr < expr     小于
    expr <= expr    小于等于
    expr || expr    逻辑或
    expr && expr    逻辑与
    expr & expr     位与
    !expr           逻辑非
    CARRY?          进位的状态
    OVERFLOW?       溢出的状态
    PARITY?         奇偶位的状态
    SIGN?           符号位的状态
    ZERO?           零位的状态

64位汇编程序
-------------

64位汇编器不支持 .386 .MODEL .STACK 等汇编命令，也不支持 INVOKE，PROTO 不能带参数，END
不能指定程序入口。 ::

    TITLE Adds Two Intergers in 64-bit MASM
    ExitProcess PROTO

    .data
    sum DWORD 0

    .code
    main PROC
        mov eax,5
        add eax,6
        mov sum,eax
        mov ecx,0
        call ExitProcess
    main ENDP
    END
