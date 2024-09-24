过程调用
=========

运行时栈是直接由处理器管理的内存数组，用于跟踪过程返回地址、参数、局部变量、和其他过程相
关数据。在32位模式下，栈指针寄存器 ESP 保存着32位的栈位置地址。我们一般很少直接操作 ESP，
而是使用 CALL、RET、PUSH、POP 等指令间接操作。

ESP 总是保存最新的栈顶元素的地址，PUSH 指令将 ESP 向低位置偏移一个元素然后将新元素压入
新的栈顶，POP 获取当前栈顶元素的一份拷贝然后将 ESP 向高位置增加一个元素的偏移。栈可用来
临时保存寄存器的值，当执行 CALL 指令时 CPU 会将当前过程的返回地址保存到栈中，当调用过程
时可以将额外的参数放到栈中，另外栈还是保存过程局部变量的场所。

使用 PROC 汇编命令来定义一个过程，除了程序的起始过程，都需要使用 RET 指令让处理器返回到
主调函数的调用位置之后继续执行后续指令。 ::

    func PROC
        ...
        ret
    func ENDP

过程中可以定义标签，代码标签只在过程内部可见，代码标签一般用于分支或循环指令的跳转目标。
但是可以定义全局的代码标签，使用双冒号定义。但对于程序设计来说，从当前过程跳转到外部不是
一个好主意。过程会自动返回和跳转运行时栈，如果将控制权直接转移到外部，运行时栈很容易遭到
破坏。

调用过程时，CALL 指令将当前过程的返回位置的地址压入栈中，并将被调过程的第一条指令的地址
写入指令指针 EIP。但过程返回时，RET 指令会将返回地址出栈保存到指令指针 EIP 寄存器中。嵌
套过程调用是指被调过程在返回之前后调用了另一个过程，甚至可以是递归调用，即在当前调用链中
继续调用了调用链中已经调用过的过程。

在 PROC 声明中可以使用 USES 让汇编器自动保存和恢复过程内部使用的寄存器，USES 告诉汇编器
两件事：第一，在过程开始处生成 PUSH 指令将指定的寄存器保存到栈中；第二，在过程返回前生成
POP 指令将指定的寄存器从栈中恢复。但是，不能对返回值寄存器进行保存和恢复，因为返回值寄存
器需要保存过程的返回值，不能被覆盖成原来的值。也不能在使用 EBP 访问栈参数时使用 USES，因
为 USES 插入的 PUSH 指令在建立栈帧基址 EBP 之前，导致 EBP 访问栈参数的偏移不准确。

栈帧是栈的一个区域保存传递的参数、过程返回地址、局部变量、保护的寄存器，它通过按照以下步
骤创建：

1. 任何需要通过栈传递的参数，压入栈
2. 调用一个过程，使得过程的返回地址压到栈中
3. 当过程开始执行，EBP 寄存器压入栈中
4. 将当前 ESP 的值保存到 EBP，这样 EBP 就可以作为过程参数的基指针
5. 如果有局部变量，ESP 会被减去对应的偏移为变量留下保存空间
6. 如果任何寄存器需要保存，会被压入栈中

栈帧的结构直接由程序的内存模型以及选择的过程调用约定决定。对于32位 Windows API，需要将
所有的参数都传递到栈中，而64位程序同时使用寄存器和栈传递参数。多年来，微软在32位程序中包
含了一种名为 fastcall 的参数传递约定。顾名思义，通过在调用子程序之前简单地将参数放入寄存
器中，可以获得一些运行时效率。替代方案是将参数推入堆栈，执行速度会较慢。通常用于参数的寄
存器包括 EAX、EBX、ECX 和 EDX，较少使用 EDI 和 ESI。不幸的是，这些相同的寄存器也用于保
存诸如循环计数器和计算中的操作数等数据值。因此，任何用作参数的寄存器在过程调用之前必须先
压入堆栈，分配过程参数的值，并在过程返回后恢复到它们的原始值。所有额外的压栈和出栈操作不
仅会造成代码混乱，而且往往会抵消我们希望通过使用寄存器参数获得的性能优势！此外，程序员必
须非常小心，确保每个寄存器的 PUSH 都与其适当的 POP 匹配，即使在代码中有多个执行路径。例
如，在以下代码中，如果第 8 行的 EAX 等于 1，则程序将不会在第 17 行返回给其调用者，因为
有三个寄存器值还留在栈上，ret 出栈的不是真正的返回地址。 ::

     1:     push    ebx                 ; save register values
     2:     push    ecx
     3:     push    esi
     4:     mov     esi,OFFSET array    ; starting OFFSET
     5:     mov     ecx,LENGTHOF array  ; size, in units
     6:     mov     ebx,TYPE array      ; doubleword format
     7:     call    dump                ; display memory
     8:     cmp     eax,1               ; error flag set?
     9:     je      error_exit          ; exit with flag set
    10: 
    11:     pop     esi                 ; restore register values
    12:     pop     ecx
    13:     pop     ebx
    14:     ret
    15: error_exit:
    16:     mov     edx,offset error_msg
    17:     ret

而使用栈传递参数，提供了不需要寄存器的一种灵活方法，在调用过程前只需简单的将参数压入栈中，
不需要考虑寄存器： ::

    push TYPE array
    push LENGTHOF array
    push OFFSET array
    call dump

下面是 C 语言函数的一个例子： ::

    int addtwo(int x, int y) {
        return x + y;
    }

其对应的32位汇编如下： ::

    y_param EQU [ebp + 12]
    x_param EQU [ebp + 8]

    addtwo PROC
        push    ebp
        mov     ebp,esp
        mov     eax,y_param
        add     eax,x_param
        pop     ebp
        ret
    addtwo ENDP

    caller PROC
        push    y_param
        push    x_param
        call    addtwo
        add     esp,8   ; 调用者需要负责清理参数
        ret
    caller ENDP

分配局部变量的例子： ::

    y_param EQU [ebp + 12]
    x_param EQU [ebp + 8]
    a_local EQU DWORD PTR [ebp - 4]
    b_local EQU DWORD PTR [ebp - 8]

    addtwo PROC
        push    ebp
        mov     ebp,esp
        sub     esp,8       ; 预留局部变量的空间
        mov     a_local,10  ; 初始化局部变量
        mov     b_local,20  ; 初始化局部变量
        mov     eax,y_param
        add     eax,x_param
        mov     esp,ebp     ; 清理局部变量
        pop     ebp
        ret
    addtwo ENDP

LEA、ENTER、LEAVE 指令
----------------------

LEA 指令返回一个间接操作数的地址，间接操作数包含一个或多个寄存器，它们的偏移在运行时计算。
例如下面的 C 语言函数： ::

    void make_array() {
        char mystr[30];
        for (int i = 0; i < 30; ++i)
            mystr[i] = '*';
    }

等价的汇编语言会分配 32 个字节的局部变量的空间（对齐到四字节边界），并将地址赋给 ESI，该
地址就是一个间接操作数的地址： ::

    make_array PROC
        push    ebp
        mov     ebp,esp
        sub     esp,32
        lea     esi,[ebp-30]        ; 加载地址
        mov     ecx,30              ; 循环计算
    L1: mov     BYTE PTR [esi],'*'  ; 依次赋值
        inc     esi                 ; 地址加 1
        loop    L1
        add     esp,32
        pop     ebp
        ret
    make_array ENDP

不能使用 OFFSET 取地址，因为 [ebp-30] 的地址在编译时是未知的，下面是错误的代码： ::

    mov esi,OFFSET [ebp-30] ; 错误

ENTER 指令自动创建过程的栈帧，它保存 EBP 并分配局部变量的空间，具体的：

1. 将 EBP 压到栈中（push ebp）
2. 将 EBP 值设为当前栈帧的基址（mov ebp,esp）
3. 给局部变量预留空间（sub esp,numbytes）

ENTER 指令由两个参数，第一个是要预留的局部变量字节数（需要是四字节的倍数），第二个是嵌套
等级，在刚进入过程时嵌套等级是 0，每嵌套一个语句块加 1，详细内容见 Intel 指令文档。因此，
ENTER 8,0 相当于： ::

    push ebp
    mov ebp,esp
    sub esp,8

与 ENTER 指令相对于的是 LEAVE，LEAVE 指令结束一个过程的栈帧，即将 ESP 寄存器恢复到栈帧
基址处已清理局部变量，并且弹出 EBP，下面的代码是等价的： ::

    func PROC
        enter 8,0
        ...
        leave
        ret
    func ENDP

    func PROC
        push ebp
        mov ebp,esp
        sub esp,8
        ...
        mov esp,ebp
        pop ebp
        ret
    func ENDP

LOCAL 汇编命令
--------------

LOCAL 汇编命令是 ENTER 指令的高级替换，ENTER 指令只能预留一块未命名的局部变量区域，而
LOCAL 可以命令一些列局部变量。如果使用 LOCAL，它必须立即跟在的 PROC 声明之后： ::

    func PROC
        LOCAL x:DWORD, y:BYTE, p:PTR WORD, a[10]:DWORD

下面的代码是等价的： ::

    func PROC
        LOCAL temp:DWORD
        mov eax,temp
        ret
    func ENDP

    func PROC
        push ebp
        mov ebp,esp
        sub esp,4
        mov eax,[ebp-4]
        leave
        ret
    func ENDP

32位调用约定
------------

32位 Windows 环境中主要使用的两种调用约定是 C 调用约定和 STDCALL 调用约定。C 调用约定
是 C 语言使用的约定，STDCALL 是 Windows API 使用的约定。

C 调用约定：

1. 过程参数以相反的顺序（从右到左）压入栈中
2. 调用者负责清理栈中的参数，这个规则使得 C 函数可以传递可变长个数的参数

STDCALL 调用约定：

1. 过程参数以相反的顺序（从右到左）压入栈中
2. 被调函数使用 ret NUM 指令负责清除栈中的参数

微软 x64 调用约定
-----------------

微软在 64 位程序中遵从一致的参数传递和过程调用约定，称为微软 x64 调用约定。这个约定用于
C/C++ 编译器，也用于 Windows API。以下是这个调用约定的基本行为：

1. CALL 指令会将 RSP 寄存器的值减去 8 保存返回地址
2. 过程最开头的四个参数依次通过 RCX、RDX、R8、R9 四个寄存器传递，额外的参数会按从左到右
   的顺序依次压入栈中通过栈传递
3. 如果参数少于64位不会进行零扩展，因此高位的值是不确定的
4. 如果返回值是一个小于等于64位的整数，通过 RAX 寄存器返回
5. 过程调用者需要负责分配至少 32 个字节（4 * 8）的 shadow space 栈空间，让被调过程能够
   可选将寄存器参数保存到这个区域
6. 当调用过程时，被调过程可以使用的第一个栈元素必须对齐到 16 字节地址边界，即进入被调过
   程后的 RSP + 8 必须是 16 的倍数
7. 调用者负责清除所有的参数和 shadow space 栈空间
8. 如果返回值超过64位，结果会压入栈，位置的地址通过 RCX 返回
9. 如果要保留 RAX、RCX、RDX、R8、R9、R10、R11 的值，调用者必须调用过程前将这些寄存器压
   入栈，并在调用返回后进行恢复
10. 过程如果要使用 RBX、RBP、RDI、RSI、R12、R13、R14、R15 这些寄存器，必须在使用前先
    进行保护，并在返回前恢复

下面的例子展示了在调用过程时怎样将 RSP 对齐以及预留 shadow space 空间： ::

     1: .code
     2: main PROC
     3:     sub rsp,8   ; RSP 对齐预留，如果有奇数个额外参数这里可以不预留
     4:     sub rsp,32  ; 预留 shadow space
     5:     mov rcx,1   ; 传递寄存器参数
     6:     mov rdx,2
     7:     mov r8,3
     8:     mov r9,4
     9:     push 5      ; 传递额外参数
    10:     push 6
    11:     call addsix
    12:
    13:     add rsp,40
    14:     mov ecx,0   ; 返回到操作系统
    15:     ret
    16: main ENDP

    过程 addsix 在执行第一条指令前栈的状态：
    |XXX返回操作系统XX| 0x1068 <- 进入 main 时的栈顶，main 可以使用的下一元素是对齐的
    |                | 0x1060 sub rsp,8  预留 8 个字节
    |                | 0x1058
    |                | 0x1050
    |                | 0x1048
    |                | 0x1040 sub rsp,32 预留 32 个字节
    |    参数5       | 0x1038
    |    参数6       | 0x1030
    |CALL填入返回地址 | 0x1028 <- RSP 当前栈顶指针，下一个可使用的元素是对齐的
    |                | 0x1020

如上图所示，只要向第 3 行和第 4 行那样一共预留 40 个字节的栈空间，就可以同时满足 shadow
space 和 RSP 对齐的要求。这里一个前提是，当前过程在进入时已经保证 RSP 对齐。

