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
    设置后续数值的基数，例如：.RADIX 8      mov ax, 173
**.SAFESEH identifier**
    注册一个函数作为结构化异常处理函数，仅32位MASM可用。identifier 只能是本地定义的 PROC
    或 EXTRN PROC，不能是一个 LABEL。.SAFESEH 需要 ml.exe 使用 /safeseh 命令行选项。
**MMWORD**
    类型名，qword 是一个64位无符号整型，而 mmword 相当于 __m64。
**XMMWORD**
    类型名，用于 XMM 指令的128位操作数，相当于 __m128。
**YMMWORD**
    类型名，用于 AVX 指令的256位操作数，相当于 __m256。

处理器：

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
