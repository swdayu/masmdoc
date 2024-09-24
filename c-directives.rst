汇编命令
=========

**ALIAS**
    定义函数的一个别名，ALIAS <alias> = <actual-name>，尖括号是必须的。

**ASSUME**
    开启对寄存器值的错误检查，仅32位MASM可用。

**COMMENT**
    将分隔字符之间以及同行的文本当作注释，COMMENT delimeter ⟦text⟧ delimiter ⟦text⟧

**ECHO message**
    将 message 显式到标准输出，与 %OUT 相同。

**%OUT**
    见 ECHO。

**END**
    标记一个模块的结束，32位MASM还可以可选的设置程序的入口点：END ⟦entry⟧。

**.FPO(cdwLocals, cdwParams, cbProlog, cbRegs, fUseBP, cbFrame)**
    控制输出到 .debug$F 分段的调试记录的输出。

**INCLUDE filename**
    包含一个文件，如果文件名包含反斜杠、分号、大于、小于、单引号、双引号，必须包含在尖括
    号内。

**INCLUDELIB libraryname**
    通知链接器当前模块需要链接 libraryname 库，如果库名称包含以上特殊字符必须包含在尖括
    号内。

**OPTION option-list**
    启用和禁用汇编器的特性，包括 CASEMAP、DOTNAME、NODOTNAME、EMULATOR、NOEMULATOR、
    EPILOGUE、EXPR16、EXPR32、LJMP、NOLJMP、M510、NOM510、NOKEYWORD、NOSIGNEXTEND、
    OFFSET、OLDMACROS、NOOLDMACROS、OLDSTRUCTS、NOOLDSTRUCTS、PROC、PROLOGUE、
    READONLY、NOREADONLY、SCOPED、NOSCOPED、SEGMENT、SETIF2。

    OPTION PROC: PRIVATE 可以使所有的过程默认都是私有的。

**OPTION LANGUAGE: language**
    设置语言特性比如调用约定，可用的 language 包括：C、STDCALL、BASIC、SYSCALL、PASCAL、
    FORTRAN。其中 BASIC、SYSCALL、PASCAL、FORTRAN 不能在 .MODEL FLAT 内存模型下使用。

**OPTION AVXENCODING: preference**
    选择 AVX 指令的编码偏好，可用的 preference 包括：PREFER_FIRST、PREFER_VEX、
    PREFER_VEX3、PREFER_EVEX、NO_EVEX。

**POPCONTEXT context**
    恢复当前的 context，context 可以是 ASSUMES（仅32位MASM）、RADIX、LISTING、CPU（
    仅32位MASM）、ALL。

**PUSHCONTEXT**
    保存当前的 context。

**.RADIX expr**
    设置后续数值的基数，例如： ::

        .RADIX 8
        mov ax, 173

**.SAFESEH identifier**
    注册一个函数作为结构化异常处理函数，仅32位MASM可用。identifier 只能是本地定义的 PROC
    或 EXTRN PROC，不能是一个 LABEL。.SAFESEH 需要 ml.exe 使用 /safeseh 命令行选项。

**MMWORD**
    类型名，qword 是一个64位无符号整型，而 mmword 相当于 __m64。

**XMMWORD**
    类型名，用于 XMM 指令的128位操作数，相当于 __m128。

**YMMWORD**
    类型名，用于 AVX 指令的256位操作数，相当于 __m256。

**COMM definition, ...**
    创建一个通用的变量，通用变量是由链接器分配的，并且不能初始化。这也意味着不能依赖这种
    变量的位置和顺序。每个 definition 有以下形式： ::

        ⟦language-type⟧ ⟦NEAR | FAR⟧ label:type⟦:count⟧

    其中 language-type、NEAR、FAR 仅在 32 位 MASM 中合法。lable 是变量的名字，type
    是类型或者一个整数表示变量的字节大小，count 声明多少个数据对象，默认是 1。例如创建
    一个 512 个字节的通用数组变量： ::

        COMM array:BYTE:512

**EXTERN ⟦language-type⟧ name ⟦(altid)⟧ : type, ...**
    定义一个或多个名称为 name 类型为 type 的外部变量、标签、或符号。类型可以是 ABS，表
    示导入名为 name 的常量。language-type 仅在 32 位 MASM 中合法。

**EXTERNDEF ⟦language-type⟧ name:type, ...**
    如果 name 定义在模块中，相当于 PUBLIC；否则相当于 EXTERN。如果 name 没有被引用，会
    忽略该 EXTERNDEF。

**PUBLIC  ⟦language-type⟧ name, ...**
    使每个指定名称的变量、标签、符号在所有模块中都可见。language-type 仅在 32 位 MASM
    中合法。

处理器
-------

**.386**
    仅32位MASM可用，启用 80386 处理器非特权指令，禁用所有后续版本引入的指令。同时也启用
    了 80387 指令。
**.386P**
    仅32位MASM可用，启用所有的 80386 处理器指令，包括特权指令。禁用所有后续版本引入的指
    令。同时也启用了 80387 指令。
**.387**
    仅32位MASM可用，启用 80387 指令。
**.486**
    仅32位MASM可用，启用 80486 处理器非特权指令。
**.486P**
    仅32位MASM可用，启用 80486 处理器所有指令，包括特权指令。
**.586**
    仅32位MASM可用，启用 Pentium 处理器非特权指令。
**.586P**
    仅32位MASM可用，启用 Pentium 处理器所有指令，包括特权指令。
**.686**
    仅32位MASM可用，启用 Pentium Pro 处理器非特权指令。
**.686P**
    仅32位MASM可用，启用 Pentium Pro 处理器所有指令，包括特权指令。
**.K3D**
    仅32位MASM可用，启用 K3D 指令。
**.MMX**
    仅32位MASM可用，启用 MMX 或 SIMD 指令。
**.XMM**
    仅32位MASM可用，启用 Streaming SIMD Extension（SSE）指令。

分段
-----

**.MODEL memory-model ⟦, language-type⟧ ⟦, stack-option⟧**
    仅32位MASM可用，初始化程序的内存模型。memory-model 指定代码和数据指针的大小，32位
    仅支持 FLAT，16位支持 TINY、SMALL、COMPACT、MEDIUM、LARGE、HUGE、FLAT。language-type
    指定调用和命名约定，32位仅支持 C 和 STDCALL，16位支持 C、BASIC、FORTRAN、PASCAL、
    SYSCALL、STDCALL。stack-option 仅16位可用，可以是 NEARSTACK、FARSTACK。
**.DOSSEG**
    仅32位MASM可用，根据 MS-DOS 分段约定对分段排序，代码段第一，然后是不在 DGROUP 中的
    段，然后是 DGROUP 中的段。DGROUP 中的顺序为，不是 BSS 或 STACK 的段，然后是 BSS
    段，最后是 STACK 段。
**.STARTUP**
    仅32位MASM可用，生成程序起始代码。
**.EXIT ⟦expr⟧**
    仅32位MASM可用，生成程序结束代码，返回 expr。
**.STACK ⟦size⟧**
    仅32位MASM可用，定义程序栈段，同时可以设置栈的字节大小，默认是 1024 个字节。.STACK
    自动关闭结束 stack 语句。
**.FARDATA ⟦name⟧**
    仅32位MASM可用，远初始化数据段（FAR_DATA 或者 name）。
**.FARDATA? ⟦name⟧**
    仅32位MASM可用，远未初始化数据段（FAR_BSS 或者 name）。
**.CODE ⟦name⟧**
    代码段的开始，TINY、SMALL、COMPACT、FLAT 模型的默认代码段名称为 _TEXT，其他模型的
    默认名称为 moduelname_TEXT。
**.CONST**
    只读常量数据段（名称为 CONST）。
**.DATA**
    初始化数据段（名称为 _DATA）。
**.DATA?**
    未初始化数据段（名称为 _BSS）。

过程
-----

**name PROTO**
    过程的原型声明，具体语法如下： ::

        label PROTO ⟦distance⟧ ⟦language-type⟧ ⟦, parameter ⟦:tag⟧ ...⟧

    属性 distance 仅32位MASM可用，用于16位内存模型，表示 NEAR 或 FAR 调用。属性 language-type
    仅32位MASM可用，设置调用和命名约定，支持的约定有：

    - 32 位 FLAT 模型：C、STDCALL
    - 16 位内存模型：C、BASIC、FORTRAN、PASCAL、SYSCALL、STDCALL

    属性 parameter 指定过程的参数，属性 tag 指定参数的类型。例如： ::

        func PROTO NEAR C, args:WORD, arg1:VARARG

**name PROC**
    标记过程的开始，过程可以通过 CALL 指令或者 INVOKE 汇编命令调用。具体语法如下： ::

        label PROC ⟦distance⟧ ⟦language-type⟧ ⟦PUBLIC|PRIVATE|EXPORT⟧ ⟦<prologuearg>⟧
            ⟦USES reglist⟧ ⟦, parameter ⟦:tag⟧ ...⟧ ⟦FRAME ⟦:ehandler-address⟧⟧
            statements
        label ENDP

    属性 ⟦distance⟧ 和 ⟦language-type⟧ 仅32位MASM可用。⟦FRAME ⟦:ehandler-address⟧⟧
    仅64位MASM可用，会使汇编器在 .pdata 分区生成一个对应该过程的函数表条目，并在 .xdata
    分区生成栈展开信息，这些内容用于函数的结构化异常处理的展开。当使用了 FRAME 属性，后
    面必须跟随一个 .ENDPROLOG 汇编命令。例如： ::

        _text SEGMENT
        func PROC FRAME
            push r10
            .pushreg r10
            push r15
            .pushreg r15
            push rbx
            .pushreg rbx
            push rsi
            .pushreg rsi
            .endprolog
            ; rest of func ...
            ret
        func ENDP
        _text ENDS
        END

**name ENDP**
    标记过程的结束。

**INVOKE expr ⟦, argument ...⟧**
    仅32位MASM可用，每个参数可以是一个表达式、寄存器对、或一个地址表达式（使用 ADDR 开
    头的表达式），ADDR 只能在 INVOKE 中使用，表示传地址，但地址必须是在编译时已知的

x64 命令
---------

**.ALLOCSTACK size**
    大小 size 必须是 8 的倍数。该汇编命令扩展了 PROC FRAME 声明，可以指定一个函数帧怎
    样展开。仅用于 prologue 内部，会根据指定大小生成一个 UWOP_ALLOC_SMALL 或者一个
    UWOP_ALLOC_LARGE。 ::

        text SEGMENT
        PUBLIC func
        PUBLIC func_UW
        func_UW PROC NEAR
            ; exception/unwind handler body
            ret 0
        func_UW ENDP
        func PROC FRAME: func_UW
            sub rsp,16
            .allocstack 16
            .endprolog
            ; function body
            add rsp,16
            ret 0
        func ENDP
        text ENDS
        END

**.ENDPROLOG**
    标记 prologue 声明的结束，prologue 声明仅用于 PROC FRAME 和 .ENDPROLOG 之间。

**.PUSHFRAME ⟦CODE⟧;;**
    生成一个 UWOP_PUSH_MACHFRAME 展开代码条目，如果指定了 CODE 关键字，该条目给定修饰
    符 1，否则修饰符为 0。该命令可以指定一个函数帧怎样展开，仅用于 prologue 内部。

**.PUSHREG register**
    生成一个 UWOP_PUSH_NONVOL 展开代码条目，该命令可以指定一个函数帧怎样展开，仅用于
    prologue 内部。寄存器可以是 RAX、RCX、RDX、RBX、RDI、RSI、RBP、R8、R9、R10、R11、
    R12、R13、R14、R15。以下示例展示了怎样压入非易变（non-volatile）寄存器： ::

        _text SEGMENT
        func PROC FRAME
            push r10
            .pushreg r10
            push r15
            .pushreg r15
            push rbx
            .pushreg rbx
            push rsi
            .pushreg rsi
            .endprolog
            ; rest of function ...
            ret
        func ENDP
        _text ENDS
        END

**.SAVEREG reg, offset**
    为指定寄存器和偏移生成一个 UWOP_SAVE_NONVOL 或者 UWOP_SAVE_NONVOL_FAR 展开代码条
    目。该命令可以指定一个函数帧怎样展开，仅用于 prologue 内部。

**.SAVEXMM128 xmmreg, offset**
    为指定 XMM 寄存器和偏移生成 UWOP_SAVE_XMM128 或 UWOP_SAVE_XMM128_FAR 展开代码条
    目。该命令可以指定一个函数帧怎样展开，仅用于 prologue 内部。offset 必须是 16 的倍
    数。

**.SETFRAME reg, offset**
    生成 UWOP_SET_FPREG 展开代码条目，并填充展开信息中的帧寄存器字段和偏移。offset 必
    须是 16 的倍数，并且小于等于 240。该命令可以指定一个函数帧怎样展开，仅用于 prologue
    内部。以下示例展示了怎样使用帧指针： ::

        .code
        func PROC FRAME
            push rbp
            .pushreg rbp
            sub rsp,010h
            .allocstack 010h
            mov rbp,rsp
            .setframe rbp,0
            .endprolog
            ; modify the sp outside of the prologue (similar to alloca)
            sub rsp,060h

            ; we can unwind from the following AV because of the frame pointer
            mov rax,0
            mov rax,[rax] ; AV!

            add rsp,060h
            add rsp,010h
            pop rbp
            ret
        func ENDP
        END
