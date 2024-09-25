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

在 x86 平台上，所有参数在传递时都被扩展到32位宽度，并且以从右到左的顺序压入栈中。返回值
也会扩展到32位宽度通过 EAX 寄存器返回，除了 8 字节的结构体会通过 EDX:EAX 寄存器对返回，
更大的结构体数据通过指针返回，该指针保存在 EAX 寄存器中。另外非 POD 的结构体数据也通过
指针返回。POD（Plain Old Data）代表的是简单的旧数据，可以理解为是与 C 兼容的简单的数据
类型，拷贝一个 POD 就拷贝了该数据的所有比特并且可以是未初始化的，C++ 里面定义了构造函数
或虚函数的类就不是 POD 类型。POD 类型没有虚函数、基类、用户定义构造函数、拷贝构造函数、
赋值操作符、析构函数。

如果在浮点协处理器上编写汇编过程，必须保护浮点控制字寄存器，并且清除掉协处理器寄存器栈，
除非返回了一个 float 或 double 浮点值，该返回值通过 ST(0) 寄存器返回。

如果函数使用了 EBX、EBP、ESI、EDI，编译器会生成 prolog 和 epilog 代码来保存和恢复这些
寄存器。如果要定义函数自己的 prolog 和 epilog 代码，需要使用裸函数。

使用 naked 属性声明的函数，编译器不会生成前导（prolog）或后置（epilog）代码，这使得能够
使用内联汇编编写自己的自定义前导/后置序列。Naked 函数是一种高级特性，它使得能声明一个从
非 C/C++ 上下文调用的函数，因此对参数的位置或哪些寄存器需要保护有不同的假设。例如中断处
理程序。这个特性对于虚拟设备驱动程序（VxD）的编写者特别有用。

裸函数有一些规则和限制：

1. 不允许使用 return 语句，必须手动返回。
2. 不允许使用结构化异常处理和 C++ 异常处理机制，因为它们需要在函数的栈帧上进行展开。
3. 由于 setjmp 需要在栈帧上回滚，任何形式的 setjmp 都是禁止的。
4. 禁止使用 _alloca 函数，因为 naked 函数不处理栈帧。
5. 为了确保在前导序列之前不出现局部变量的初始化代码，函数作用域内不允许有初始化的局部变
   量。特别是，不允许在函数作用域内声明 C++ 对象。然而，可以在嵌套作用域中初始化数据。
6. 不推荐使用帧指针优化（通过 /Oy 编译器选项），但对于 naked 函数它是被自动禁止的。
7. 不能在函数词法作用域内声明 C++ 类对象，但可以在嵌套的块中声明对象。
8. 使用 /clr 编译时会忽略 naked 关键字。
9. 对于 __fastcall naked 函数，如果在 C/C++ 代码中引用了寄存器参数，序言代码应该将该寄
   存器的值存储到该变量的栈位置。

裸函数仅用于 x86 和 ARM，在 x64 上不可用。一个裸函数的示例如下： ::

    __declspec(naked) int __fastcall power(int i, int j) {
        // calculates i^j, assumes that j >= 0

        // prolog
        __asm {
            push ebp
            mov ebp, esp
            sub esp, __LOCAL_SIZE
            // store ECX and EDX into stack locations allocated for i and j
            mov i, ecx
            mov j, edx
        }

        {
            int k = 1;   // return value
            while (j-- > 0)
                k *= i;
            __asm {
                mov eax, k
            };
        }

        // epilog
        __asm {
            mov esp, ebp
            pop ebp
            ret
        }
    }

Visual C/C++ 编译器支持的其他调用约定包括：

**__cdecl**
    调用者清理栈参数，参数从右到左入栈
**__stdcall**
    被调函数清理栈参数，参数从右到左入栈
**__fastcall**
    被调函数清理栈参数，前两个通过寄存器传递，额外的参数从右到左通过栈传递
**__thiscall**
    被调函数清理栈参数，this 指针通过 ECX 传递，额外参数从右到左通过栈传递
**__vectorcall**
    被调函数清理栈参数，前几个通过寄存器传递，额外参数从右到左通过栈传递

一些调用约定是过时的 __pascal、__fortran、__syscall，这些已经不再支持。另外，所有仅从
托管代码调用的虚拟函数应使用 __clrcall 调用约定。但是，这种调用约定不能用于原生代码可以
调用的函数。__clrcall 修改符是 Microsoft 特有的。当托管函数调用虚托管函数或通过指针调用
托管函数时，使用 __clrcall 可以提高性能。入口点是分开的、编译器生成的函数。如果一个函数
既有原生又有托管的入口点，其中一个将是实际的函数，包含函数实现，另一个函数则是单独的函数
（一个内联函数），它调用实际的函数并允许公共语言运行时执行 PInvoke。当标记一个函数为
__clrcall 时，表明函数实现必须是 MSIL，并且不会生成原生入口点函数。在获取原生函数的地址
时，如果没有指定 __clrcall，编译器将使用原生入口点。__clrcall 表明该函数是托管的，无需
从托管过渡到原生。在这种情况下，编译器将使用托管入口点。

**__cdecl**

__cdecl 调用约定是 C/C++ 程序默认的调用约定，由于栈是调用者清除的，它可以传递可变个数参
数。但这也导致 __cdecl 会比 __stdcall 创建更大的可执行文件，因为每次调用一个函数都必须
有一个额外的栈清除指令。__cdecl 可以用在函数名或函数指针名称前： ::

    int __cdecl system(const char *);
    typedef bool (__cdecl *func_ptr)(void *arg, int flags, ...);

由于 __cdecl 是 C/C++ 函数默认的，因此在 x86 代码中唯一必须使用 __cdecl 的情况是当指定
了 /Gv（vectorcall）、/Gz（stdcall）、/Gr（fastcall）这些编译选项的时候。另外 /Gd 强
制使用 __cdecl 调用约定。

在 ARM 和 x64 处理器上，编译器会接受 __cdecl 声明但会忽略，这些处理器上，参数会尽量通过
寄存器传递，只有额外的参数通过栈传递。在 x64 代码中，使用 __cdecl 可以覆盖 /Gv 编译选项
的设定，使得编译器使用默认的 x64 调用约定。

函数 void __cdecl MyFunc(char c, short s, int i, double f) 调用后的结果为： ::

    函数名：_MyFunc

    ECX 没有使用
    EDX 没有使用

    程序栈：

    |  f 高字节  | ESP + 0x14
    |  f 低字节  | ESP + 0x10
    |     i     | ESP + 0x0C
    |     s     | ESP + 0x08
    |     c     | ESP + 0x04
    |  返回地址  | ESP

**__stdcall**

__stdcall 调用约定用于 Win32 API 函数。被调函数使用 ret NUM 指令负责清除栈中的参数，因
此变长参数函数会声明为使用 __cdecl 约定。使用 __stdcall 调用约定的函数必须提供函数原型。
使用 /Gz 编译选项可以将所有没有特别指定调用约定的函数都指定使用 __stdcall 调用约定。

在 ARM 和 x64 处理器上，编译器会接受 __stdcall 声明但会忽略，这些处理器上，参数会尽量通
过寄存器传递，只有额外的参数通过栈传递。

**__fastcall**

__fastcall 调用约定仅用于 x86 架构，参数列表中可以找到的前两个小于等于四字节的参数从左
到有依次通过 ECX 和 EDX 寄存器传递，所有其他参数从右到左都通过栈传递。对于类、结构体、或
联合体，不管其大小都通过栈传递。

在 ARM 和 x64 处理器上，编译器会接受 __fastcall 声明但会忽略。在 x64 芯片上，前四个参
数通过寄存器传递，额外的通过栈传递。在 ARM 芯片上，四个整数参数和八个浮点参数通过寄存器
传递，额外的通过栈传递。

函数 void __fastcall MyFunc(char c, short s, int i, double f) 调用后的结果为： ::

    函数名：@MyFunc@20

    ECX     c
    EDX     s

    程序栈：

    |  f 高字节  | ESP + 0x0C
    |  f 低字节  | ESP + 0x08
    |     i     | ESP + 0x04
    |  返回地址  | ESP

**__thiscall**

__thiscall 仅用于 x86 架构上的 C++ 类成员函数，这个不带可变个数参数的成员函数的默认调用
方式。参数从右到左通过栈传递，而 this 指针通过 ECX 寄存器传递。在 ARM 和 x64 处理器上，
编译器会接受 __thiscall 声明但会忽略。

**__vectorcall**

__vectorcall 仅使用在支持 SSE2 及以上扩展的 x86 和 x64 处理器上，使用该调用约定的函数
可以将三类参数通过寄存器传递：整型、向量类型、同类向量复合类型（HVA，Homogeneous Vector
Aggregate）。整数类型必须满足两个要求：长度必须小于等于处理器寄存器大小，转换到寄存器长
度的整数并转换回来不需要改变它的比特位表示。整数类型包括基础类型、指针、引用、小于等于处
理器寄存器长度的结构体或联合体。在 x86 平台上，更长的结构体或联合体通过传值压入栈中；而
在 x64 平台上，调用者负责分配结构体内存并只传递引用给被调函数。

向量类型是一个浮点类型（float 或 double），或者是一个 SIMD 向量类型（例如 __m128、
__m256）。同类向量复合类型（HVA）是有最多四个相同向量类型成员的复合类型，HVA 类型的字节
对齐要求与它的成员类型相同。例如下面三个成员的结构体，其对齐要求是 32 字节： ::

    typedef struct {
        __m256 x;
        __m256 y;
        __m256 z;
    } hva3;

声明为 __vectorcall 的函数不能有变长个数参数，且必须提供原型。声明为 __vectorcall 的
成员函数，其 this 指针相当于第一个整数参数会通过寄存器传递。使用 /Gv 编译选项可以强制模
块内的函数都使用 __vectorcall 调用约定，但不会应用到成员函数、有调用约定声明冲突的函数、
有可变个数参数的函数、以及 main 函数。

在 ARM 处理器上，编译器会接受 __vectorcall 声明但会忽略。在 ARM64EC 上，__vectorcall
不支持会被编译器拒绝。

以上是在 x86 和 x64 上都相同的 __vectorcall 调用约定内容，不同的部分在下面介绍。这里先
介绍 x86 相关的部分。在 x86 上，对于 32 位整数类型参数遵循 __fastcall 约定，并利用 SSE
向量寄存器处理向量类型和 HVA 类型。

从左到右在参数列表中找到的前两个整数类型的参数分别放在 ECX 和 EDX 中。隐藏的 this 指针
被视为第一个整数类型的参数，并在 ECX 中传递。前六个向量类型的参数通过 SSE 向量寄存器 0
到 5 按值传递，根据参数大小对应 XMM 或 YMM 寄存器。

从左到右的前六个向量类型参数按值传递到 SSE 向量寄存器中，浮点类型和 __m128 类型在 XMM
寄存器中传递，__m256 类型在 YMM 寄存器中传递。对于通过寄存器传递的向量类型参数，不分配
影子栈空间（shadow space）。第七个和随后的向量类型参数通过引用传递在调用者分配的内存中。
编译器错误 C2719 的限制不适用于这些参数，可以为向量参数指定对齐要求。

在为向量参数分配寄存器之后，HVA 参数的成员数据按升序分配到未使用的向量寄存器中，只要有足
够的寄存器可用于整个 HVA。如果没有足够的寄存器可用，则 HVA 参数通过引用传递在调用者分配
的内存中。不会为 HVA 参数分配栈影子空间。

__vectorcall 函数的结果尽可能按值在寄存器中返回。整数类型的结果，包括 4 字节或更小的结
构体或联合体，按值在 EAX 中返回。8 字节或更小的整数类型结构体或联合体按值在 EDX:EAX 中
返回。向量类型的结果根据大小在 XMM0 或 YMM0 中按值返回。HVA 结果的每个数据元素根据元素
大小在寄存器 XMM0:XMM3 或 YMM0:YMM3 中按值返回。其他结果类型通过引用返回在调用者分配的
内存中。

x86 实现的 __vectorcall 遵循调用者从右到左推送参数到栈上的约定，被调用函数在返回之前清
理栈。只有在寄存器中没有放置的参数才被推送到栈上。

x86 上的 __vectorcall 示例如下： ::

    typedef struct {
        __m128 a[2];
    } hva2;

    typedef struct {
        __m256 a[4];
    } hva4;

    // XMM0 传递 a
    // XMM1 传递 b
    // YMM2 传递 c
    // XMM3 传递 d
    // YMM4 传递 e
    // 返回值使用 XMM0 返回
    __m128 __vectorcall
    example1(__m128 a, __m128 b, __m256 c, __m128 d, __m256 e) {
        return d;
    }

    // ECX  传递 a
    // EDX  传递 c，参数 g 被压入栈中
    // XMM0 传递 b
    // XMM1 传递 d
    // YMM2 传递 e
    // XMM3 传递 f
    // 返回值使用 YMM0 返回
    __m256 __vectorcall
    example2(int a, __m128 b, int c, __m128 d, __m256 e, float f, int g) {
        return e;
    }

    // ECX  传递 a
    // EDX  传递 c，参数 d 和 e 被压入栈中
    // XMM0~XMM1 传递 b
    // 返回值使用 XMM0 返回
    __m128 __vectorcall example3(int a, hva2 b, int c, int d, int e) {
        return b.a[0];
    }

    // ECX  传递 a
    // EDX  传递 e
    // XMM0 传递 b
    // XMM1 传递 d
    // YMM2~YMM5 传递 c
    // 返回值使用 XMM0 返回
    float __vectorcall example4(int a, float b, hva4 c, __m128 d, int e) {
        return b;
    }

    // ECX  传递 a
    // EDX  传递 c，参数 e 被压入栈中
    // XMM0~XMM1 传递 b
    // YMM2~YMM5 传递 d
    // 返回值使用 EAX 返回
    int __vectorcall example5(int a, hva2 b, int c, hva4 d, int e) {
        return c + e;
    }

    // ECX  传递 b 的指针
    // YMM0 传递 c
    // XMM1~XMM2 传递 a
    // XMM3~XMM4 传递 d
    // 返回值使用 YMM0~YMM3 返回
    hva4 __vectorcall example6(hva2 a, hva4 b, __m256 c, hva2 d) {
        return b;
    }

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


在 x64 上，__vectorcall 调用约定扩展了标准的 x64 调用约定，以利用额外的寄存器。整数类
型参数和向量类型参数都根据参数列表中的位置映射到寄存器。HVA 参数被分配给未使用的向量寄存器。

当从左到右的前四个参数中有任何整数类型参数时，它们会通过与该位置相对应的寄存器传递——RCX、RDX、R8 或 R9。隐藏的 this 指针被视为第一个整数类型参数。如果前四个参数中的任何一个 HVA 参数无法传递在可用的寄存器中，则相应的整数类型寄存器中会传递调用者分配的内存的引用。第四个参数位置之后的整数类型参数通过堆栈传递。

当从左到右的前六个参数中有任何向量类型参数时，它们会根据参数位置通过值在 SSE 向量寄存器 0 到 5 中传递。浮点类型和 __m128 类型在 XMM 寄存器中传递，__m256 类型在 YMM 寄存器中传递。这与标准 x64 调用约定不同，因为向量类型是通过值而不是通过引用传递的，并且使用了额外的寄存器。为向量类型参数分配的影子栈空间固定为 8 个字节，并且不适用 /homeparams 选项。第七个和更靠后的参数位置的向量类型参数通过调用者分配的内存的引用在堆栈上传递。

在为向量参数分配寄存器之后，HVA 参数的成员数据按升序分配给未使用的向量寄存器 XMM0 到 XMM5（或对于 __m256 类型为 YMM0 到 YMM5），只要有足够的寄存器可用于整个 HVA。如果没有足够的寄存器可用，则 HVA 参数通过引用传递到调用者分配的内存中。HVA 参数的影子栈空间固定为 8 个字节，内容未定义。HVA 参数从左到右按顺序分配到参数列表中的寄存器，并且可以位于任何位置。位于前四个参数位置且未分配给向量寄存器的 HVA 参数通过相应位置的整数寄存器的引用传递。第四个参数位置之后的 HVA 参数按引用在堆栈上传递。

__vectorcall 函数的结果尽可能按值在寄存器中返回。整数类型的结果，包括 8 字节或更小的结构体或联合体，通过值在 RAX 中返回。向量类型的结果根据大小在 XMM0 或 YMM0 中按值返回。HVA 结果的每个数据元素根据元素大小在寄存器 XMM0:XMM3 或 YMM0:YMM3 中按值返回。不适合相应寄存器的结果类型通过引用返回到调用者分配的内存中。

在 x64 实现的 __vectorcall 中，堆栈由调用者维护。调用者的序言和尾声代码为被调用函数分配和清理堆栈。参数从右到左推送到堆栈上，并且为在寄存器中传递的参数分配影子栈空间。


C 函数装饰名称
--------------

C 函数的装饰名称依赖使用的调用约定，如下表。C 函数以及 C++ 中使用 extern "C" 声明的函数
都使用这一规则。但是在 64 位环境中，C 和 extern "C" 声明的函数只有在使用 __vectorcall
调用约定的情况下才进行装饰。 ::

    __cdecl         添加一个前置下划线
    __stdcall       添加一个前置下划线，并且添加一个 @ 字符后缀以及一个十进制数表示所有
                    参数的字节大小，例如函数 int func(int a, double b) 的装饰名称是
                    _func@12
    __fastcall      添加一个前置 @ 字符，并且添加一个 @ 字符后缀以及一个十进制数表示所
                    有参数的字节大小
    __vectorcall    添加两个 @ 字符后缀以及一个十进制数表示所有参数的字节大小

而对于使用 C 链接的 ARM64EC 函数（使用 C 编译或使用了 extern "C"），还会在装饰名字之前
添加一个 # 字符。

可以使用 /FA 选项在编译源文件时生成对应的 list 文件，在该文件中查找 PUBLIC 就可以看到对
应的函数装饰名称。或者使用 dumpbin /exports file.obj（或 file.lib）可以查看目标文件、
库文件中导出符号的装饰名称。还可以使用 undname docorated_name 命令将装饰名称还原成原始
名称。
