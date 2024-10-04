编译选项
=========

微软 C++ 编译器工具集可以单独下载和安装，如果你已经安装了 Visual Studio，那么这些工具都
已经有了。如果你只想安装工具集，只需在 Visual Studio Installer 中选择 Desktop development
with C++ 和相关的库，它会安装一个 platform toolset，其中包括 C/C++ 编译器、链接器、汇
编器、和其他构建工具。它们有 x86 和 x64 两套编译器和工具，可以用来构建 x86、x64、ARM、
ARM64 目标平台的代码。

可以用 set 命令查看当前开发命令行环境中设置的环境变量，比如打开以下命令行环境 "x64 Native
Tools Command Prompt for VS 2022"，用 set 可以看到： ::

    COMPUTERNAME=HOME
    USERNAME=localhost
    ComSpec=C:\WINDOWS\system32\cmd.exe
    ExtensionSdkDir=C:\Program Files (x86)\Microsoft SDKs\Windows Kits\10\ExtensionSDKs
    EXTERNAL_INCLUDE=
        C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.38.33130\include;
        C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.38.33130\ATLMFC\include;
        C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Auxiliary\VS\include;
        C:\Program Files (x86)\Windows Kits\10\include\10.0.22621.0\ucrt;
        C:\Program Files (x86)\Windows Kits\10\\include\10.0.22621.0\\um;
        C:\Program Files (x86)\Windows Kits\10\\include\10.0.22621.0\\shared;
        C:\Program Files (x86)\Windows Kits\10\\include\10.0.22621.0\\winrt;
        C:\Program Files (x86)\Windows Kits\10\\include\10.0.22621.0\\cppwinrt
    INCLUDE=
        C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.38.33130\include;
        C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.38.33130\ATLMFC\include;
        C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Auxiliary\VS\include;
        C:\Program Files (x86)\Windows Kits\10\include\10.0.22621.0\ucrt;
        C:\Program Files (x86)\Windows Kits\10\\include\10.0.22621.0\\um;
        C:\Program Files (x86)\Windows Kits\10\\include\10.0.22621.0\\shared;
        C:\Program Files (x86)\Windows Kits\10\\include\10.0.22621.0\\winrt;
        C:\Program Files (x86)\Windows Kits\10\\include\10.0.22621.0\\cppwinrt
    LIB=
        C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.38.33130\ATLMFC\lib\x64;
        C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.38.33130\lib\x64;
        C:\Program Files (x86)\Windows Kits\10\lib\10.0.22621.0\ucrt\x64;
        C:\Program Files (x86)\Windows Kits\10\\lib\10.0.22621.0\\um\x64
    LIBPATH=
        C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.38.33130\ATLMFC\lib\x64;
        C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.38.33130\lib\x64;
        C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.38.33130\lib\x86\store\references;
        C:\Program Files (x86)\Windows Kits\10\UnionMetadata\10.0.22621.0;
        C:\Program Files (x86)\Windows Kits\10\References\10.0.22621.0;
        C:\Windows\Microsoft.NET\Framework64\v4.0.30319
    WindowsLibPath=
        C:\Program Files (x86)\Windows Kits\10\UnionMetadata\10.0.22621.0;
        C:\Program Files (x86)\Windows Kits\10\References\10.0.22621.0
    WindowsSdkBinPath=
        C:\Program Files (x86)\Windows Kits\10\bin\
    WindowsSdkDir=
        C:\Program Files (x86)\Windows Kits\10\
    NUMBER_OF_PROCESSORS=20
    OS=Windows_NT
    PATHEXT=.COM;.EXE;.BAT;.CMD;.VBS;.VBE;.JS;.JSE;.WSF;.WSH;.MSC
    Platform=x64

相关的开发环境脚本位于以下目录中，其中 vcvarsall.bat 可以根据提供的参数生成对应的环境，
可以使用 /help 查看帮助： ::

    C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Auxiliary\Build
    vcvarsall.bat [architecture] [platform_type] [winsdk_version] [-vcvars_ver=vcversion] [spectre_mode]

其中架构可以是： ::

    x86         - x86 32-bit native, build x86
    x86_x64     - x64 on x86 cross, build x64
    x86_arm     - ARM on x86 cross, build ARM
    x86_arm64   - ARM64 on x86 cross, build ARM64
    x64         - x64 64-bit native, build x64
    x64_x86     - x86 on x64 cross, build x86
    x64_arm     - ARM on x64 cross, build ARM
    x64_arm64   - ARM64 on x64 cross, build ARM64

编译命令行选项格式如下： ::

    CL [option...] file... [option | file]... [lib...] [@command-file] [/link link-opt...]

CL 可以用来编译和链接源代码，生成可执行程序、库、或者 DLL。CL 的选项可以用 / 或者 - 开
头，选项的名字除了 /HELP 选项都是大小写敏感的。文件 file 可以是源文件、.obj 目标文件、
库文件，CL 会编译源文件，并将目标文件和库文件传递给链接器。命令行的最大长度为 1024 个字
符，具体由操作系统限定。

CL 可以接受用反斜杠（\）或者斜杠（/）分隔的路径，路径如果包含空格必须用双引号引起。如果
路径没有提供驱动器名称，CL 认为是当前驱动器，如果文件没有提供路径 CL 认为文件位于当前目
录。CL 根据文件后缀名决定怎么处理文件，源文件 .c/.cxx/.cpp 会进行编译，其他文件包括
.obj/.lib/.def 等，会交由链接器处理。

CL 返回 0 表示成功，返回非零表示出错。可能的错误可以在 winerror.h 或者 ntstatus.h 中查
看，或者使用 errlook.exe 工具查看，错误号需要转成十六进制进行查看。命令行选项可以通过额
外的文件 command-file 提供，该文件中的选项可以有多行，但是一个选项必须在同一行。

CL 会自动编译然后进行链接，除非使用了下面的选项进行打断。编译器产生 Common Object File
Format (COFF) 的目标文件 .obj，链链接器产生可执行文件 .exe 或者动态链接库 .dll 文件。

常用选项
---------

**/c**
    只进行编译不链接
**/E /EP /P**
    只进行预处理
**/Zg**
    产生函数原型
**/Zs**
    检查语法
**/? /help**
    打印帮助
**/J**
    默认 char 类型是 unsigned
**/utf-8**
    将源代码字符集和可执行字符集都设成 utf-8
**/MP[n]**
    最多使用 n 个进程进行编译，范围 1 到 65536。仅用于编译，不用于链接。
**/nologo**
    不显示版权信息，会传递 /nologo 给链接器
**/showIncludes**
    让编译器输出包含的头文件的列表，这个列表还包含嵌套包含的头文件。显式包含文件名，输出
    到 stderr。
**/sourceDependencies filename**
    将源文件的依赖以 json 格式输出到对应的文件，输出的依赖文件名称使用绝对路径输出。包含
    头文件和嵌套的头文件，如果使用了 /Yu 还包含使用的 PCH，直接导入或嵌套导入的模块以及
    头文件单元的名称和路径。
**/Tc<source file>**
    将文件当作 .c 源文件
**/TC**
    将所有文件当作 .c 源文件
**/Tp<source file>**
    将文件当作 .cpp 源文件
**/TP**
    将所有文件当作 .cpp 源文件
**/V<string>**
    设置版本字符串
**/Yc<file>**
    创建 .PCH 文件
**/Yd**
    将调试信息放在每个 .obj 中，已过时
**/Wall**
    启用所有警告
**/w**
    禁用所有警告
**/W<n> /W0~4**
    设置警告等级，默认 n=1
**/WX**
    将警告视为错误
**/WL**
    启用单行诊断
**/wd<n>**
    禁止警告n
**/we<n>**
    将警告n视为错误
**/wo<n>**
    发出一次警告 n
**/w1~4<n>**
    为n设置警告等级 1 ~ 4
**/sdl[-]**
    开启更多的安全功能和警告，启用推荐的 Security Development Lifecycle（SDL）检查。
    会开启包含 /GS 提供的安全检查，并且会覆盖选项 /GS- 的设置。

调试选项
---------

当使用调式选项时，编译器会产生函数和变量的符号名称、类型信息、和代码位置来用于调试器的调
试。这些调试信息可以包含在目标 .obj 文件中，或者与可执行文件分离的单独的 .pdb 文件中。默
认编译器不会产生调试信息。

**/Z7**
    目标文件包含所有的符号调试信息，这些目标文件和由这些目标文件创建的库文件的大小都显著
    比较大。不会生成 PDB 文件，但是可以 /DEBUG 链接选项仍然可以从这些文件或对应的库文件
    生成出 PDB 文件。
**/Zi**
    产生一个独立的 PDB 文件，这些调试信息不会包含在目标文件或者可执行文件中，使用 /Zi 不
    影响代码的优化。/Zi 会设置 /DEBUG 链接选项。
**/ZI**
    不能与 /GL 一起使用。与 /Zi 相同，但是会产生支持 Edit and Continue 调试特性的 PDB
    文件。
**/Zc:inline**
    从 COMDAT 分区中移除掉未被引用的数据和函数，或仅内部使用的符号。

代码优化
---------

**/favor:<blend|AMD64|INTEL64|ATOM>**
    针对处理器优化
**/O1**
    创建最小代码，相当于 /Og /Os /Oy /Ob2 /GF /Gy
**/O2**
    创建最快代码，相当于 /Og /Oi /Ot /Oy /Ob2 /GF /Gy，选项 /Ox 相当于/Oi /Ot /Oy
    /Ob2
**/Ob<n> /Ob0~3**
    控制内联扩展，默认 n=0：

    - 0 禁止内联
    - 1 仅允许声明为 inline/__inline/__forceinline 和 C++ 定义在类声明中的函数
    - 2 允许对任何函数内联，只要没有标记为不允许内联
    - 3 执行更强的内联

**/Od**
    禁用优化（默认），最快编译速度和简单调试信息
**/Og**
    启用全局优化，已过时
**/Oi[-]**
    生成内部函数（intrinsic functions），这种函数没有函数调用开销。这些函数包括 abs
    _disable _enable fabs _inp _inpw labs _lrtol _lrotr memcmp memcpy memset
    _outp _outpw _rotl _rotr strcat strcmp strcpy strlen _strset。下面这些函数直接
    将参数传给浮点芯片而不是程序栈：acos asin cosh fmod pow sinh tanh。这些函数由
    intrinsic 形式，如果打开了 /Oi 和 /fp:fast，atan atan2 cos exp log log10 sin
    sqrt tan。
**/Os**
    偏向代码空间
**/Ot**
    偏向代码速度
**/Ox**
    启用大多数的速度优化，选项 /O2 的子集，不包括 /GF 和 /Gy
**/Oy[-]**
    忽略 frame pointer（仅 x86），不在程序栈中创建 frame pointer。可以加快函数调用速
    度，因为不需要准备和移除 frame pointer，而且也多了一个可用的通用寄存器。编译器会自
    动检查需要使用 fp 的地方，例如 _alloca/setjmp/结构化异常处理。

代码生成
---------

**/arch:<IA32|SSE|SSE2|AVX|AVX2|AVX512>**
    最小的 CPU 架构需求，IA32/SSE/SSE2 仅 x86
**/GR[-]**
    启用 C++ RTTI
**/GX[-]**
    启用 C++ EH，与 /EHsc 相同
**/EHs**
    启用 C++ EH，没有 SEH 异常
**/EHa**
    启用 C++ EH，包含 SEH 异常
**/EHc**
    extern "C" 默认为 nothrow
**/EHr**
    始终生成 noexcept 运行时终止检查
**/fp:<contract|except[-]|fast|precise|strict>**
    选择浮点模型
**/Gd /Gr /Gv /Gz**
    调用约定，默认是 /Gd 使用 __cdecl 调用约定除了 C++ 成员函数；/Gr 使用 __fastcall
    调用约定；/Gv 使用 __vectorcall 调用约定；/Gz 使用 __stdcall 调用约定。
**/GF**
    启用只读字符串池，消除重复的字符串，指向相同的字符串共享一个指针。最多只能创建 65536
    个地址区，每个唯一识别的字符串会创建一个地址区。如果你由多于 65536 个字符串，需要使
    用 /bigobj 来创建更多的地址区。
**/Gy[-]**
    启用函数级（function-level）链接，指向同一个函数共享一个指针。
**/GL[-]**
    启用整个程序优化，如果你使用了 /GL 和 /c 编译程序，需要使用 /LTCG 链接选项创建链接
    文件。不能与 /ZI 一起使用。有了所有模块的信息，编译器可以：

    1. 跨越函数调用边界优化寄存器的使用；
    2. 更好的跟踪全局数据的修改，减少加载和存入的次数；
    3. 跟踪指针解引用对元素的修改，减少所需的加载存入次数；
    4. 内联一个函数即使这个函数是在另外一个模块定义的；

    使用 /GL 产生的 .obj 文件不能用于 editbin 和 dumpbin。当前版本用 /GL 生成的文件通
    常不能被后面版本的 Visual Studio 和工具集读取。除非你提供了你的用户可能使用的所有版
    本的 .lib 文件，因此不推荐发布用 /GL 产生的 .obj 生成的 .lib 文件。由 /GL 和预编译
    头文件产生的 .obj 文件，不应该用来构建 .lib 文件，除非这个 .lib 文件是在相同的机器
    上用来与其他 /GL 产生的 .obj 一起进行链接。.obj 文件中的预编译头文件信息在链接时时
    需要的。
**/Gw[-]**
    启用整个程序全局数据优化。将全局数据都放到 COMDAT 区，如果 /Gw 和 /GL 都启用了，链
    接器会使用全程序优化来比较多个目标文件的 COMDAT 区，去掉未引用的全局数据或者合并只读
    的相同内容的全局数据，这样可以减少可执行文件的大小。如果是单独编译和链接，可以使用
    /OPT:REF 链接选项去除为引用的用 /Gw 编译的目标文件中的全局数据，可以使用 /OPT:ICF
    和 /LTCG 来合并相同的用 /Gw 编译的目标文件中的全局数据。
**/GS[-]**
    开启 buffer 安全检查，默认是打开的，可以使用 /GS- 关闭。
**/GR[-]**
    启用运行时信息（RTTI）

输出文件
--------

**/FA[c][s][u]**
    为每个 C/C++ 源文件创建汇编列表文件，默认只包含汇编代码，如果使用了 [c] 还包含机器
    代码，如果使用了 [s] 还包含源代码，如果使用了 u 表示最终的汇编文件使用 uft-8 编码。
    默认的文件后缀名是 .asm，如果包含了机器码后缀名为 .cod。可以使用 /Fa 选项修改名称和
    后缀名以及目录。
**/Fa<file>**
    默认为每个源文件生成一个 source.asm 文件。directory/filename.extension 将汇编文件
    输出到对应目录的对应后缀的对应文件，只能在编译单个的源文件时才有效。
**/Fd<file>**
    为 /Z7 /Zi /ZI 提供 program database file (PDB) 的文件名。如果不提供 /Fd 默认名
    称是 VCx0.pdb。
**/Fe<file> /Fe: <file>**
    为编译器提供可执行文件或者 DLL 文件
**/Fm<file>**
    告诉链接器生成一个与可执行文件或 DLL 文件对应的映射文件，默认映射文件的后缀名为 .MAP。
    如果使用了 /c 这个选项没有效果，可以使用链接选项 /MAP。
**/Fo“<file>” /Fo:“<file>”**
    设置输出的目标文件名称，默认后缀名为 .obj
**/Fp<file>**
    预编译头文件名称
**/Fi<file>**
    为 /P 预处理提供预处理输出文件名称
**/FI<file>**
    使预处理器处理指定的头文件，相当于包含这个头文件，可以使用这个选项多次来包含多个头文
    件。

预处理选项
----------

**/C**
    不删除注释
**/D<name>{=|#}<text>**
    定义宏
**/U<name>**
    移除宏定义
**/u**
    移除所有预定义的宏
**/E**
    预处理到标准输出
**/EP**
    预处理到标准输出，无行号
**/P**
    预处理到文件
**/I<dir>**
    添加到头文件搜索目录
**/PD**
    打印所有宏定义
**/X**
    忽略标准包含目录

语言相关
---------

**/await**
    启用 coroutines 扩展
**/std:<c++14|c++17|c++20|c++latest>**
    指定 c++ 标准，默认 c++14
**/std:<c11|c17|clatest>**
    指定 C 语言标准
**/premissive[-]**
    设置标准遵从模式
**/Ze**
    启用扩展，默认
**/Za**
    禁用扩展，会关闭浮点的 intrinsic 形式的函数调用
**/ZW**
    启用 WinRT 语言扩展
**/Zs**
    只进行语法检查
**/Zl**
    省略 .obj 中的默认 C 运行时库名
**/Zc:__cplusplus[-]**
    启用 __cplusplus 宏
**/Zc:__STDC__**
    启用 __STDC__ 宏

链接选项
--------

**/LD**
    创建 .DLL，会传递 /DLL 给链接器，默认使用 /MT 链接静态运行时库
**/LDd**
    创建 .DLL 调试库，会传递 /DLL 给链接器
**/LN**
    创建 .netmodule
**/F<number>**
    设置堆栈大小对其到 4 字节，栈大小默认 1MB，可以是十进制和 C 语言接受的整数。参数范围
    从 1 字节到链接器接受的最大栈大小。还可以通过 /STACK 链接器选项，或者 editbin 工具
    对 exe 文件进行操作。
**/link [link option and lib]...**
    链接选项和库
**/MD**
    与 MSVCRT.LIB 动态链接库链接，会在 obj 中设置默认库名称
**/MT**
    与 LIBCMT.LIB 静态链接库链接，会在 obj 中设置默认库名称
**/MDd**
    与 MSVCRTD.LIB 动态链接调试库链接，会在 obj 中设置默认库名称，会定义 _DEBUG
**/MTd**
    与 LIBCMTD.LIB 静态链接调试库链接，会在 obj 中设置默认库名称，会定义 _DEBUG

LIB 工具
=========

LIB 工具命令行格式： ::

    lib [options] [files]

LIB 可以用来创建或者修改 COFF（Common Object File Format）格式的 lib 库文件，或者用来
解压导出一个成员文件，或者创建一个导入 lib 库和一个导出文件。lib 的选项可以用 / 或者 -
开始，选项名字和它的关键字或者文件名参数不是大小写敏感的，但是作为参数的标识符是大小写敏
感的。

创建静态 lib 库时的输入文件：COFF 目标文件 .obj，COFF 库文件 .lib，32 位的 OMF（Object
Model Format）目标文件 .obj；该模式输出一个 COFF 库.lib。解压导出成员文件时的输入文件：
COFF 库文件 .lib；该模式输出一个目标文件。创建导入库和导出文件时的输入文件：模块定义文件
.def，COFF 目标文件 .obj，COFF 库文件 .lib，OMF 目标文件 .obj；该模式输出一个导入库
.lib 和一个导出文件 .exp。

LINK 可以使用 exp 文件创建包含导出符号的二进制程序（通常称为动态链接库 DLL），这个 DLL
使用 LIB 生成的导入库文件来解决其他程序对 DLL 中导出符号的引用。当用这种方式创建 DLL 时，
必须将所有相同的 obj 文件提供给 LINK，像当时使用 LIB 创建导入库文件时一样。因此最好只用
LIB 生成静态库，而用 LINK 生成动态库。当时两个 DLL 间接或直接依赖时，必须先用 LIB 创建
出一个导入库和导出文件，然后使用 LINK 和导出文件链接其中的一个 DLL。

**/DEF[:deffile]**
    创建一个导入库和一个导出文件，lib 名和 exp 名通过 /OUT 选项指定。有三种导出方式，按
    照推荐顺序依次为：

    1. 源代码中使用 __declspec(dllexport)
    2. .def 文件中使用 EXPORTS 语句
    3. 链接器中使用 /export 选项

**/EXPORT:entryname[=internalname][,@ordinal[,NONAME]][,DATA]**
    导出一个函数
**/EXTRACT:member**
    解压导出一个目标文件
**/INCLUDE:symbol**
    将符号添加到符号表
**/LIBPATH**
    添加库文件搜索目录
**/LIST**
    打印输出的库文件信息到标准输出，你可以使用该选项打印库文件的信息而不修改它。
**/LTCG**
    链接时代码生成
**/MACHINE:{ARM|ARM64|ARM64EC|EBC|X64|X86}**
    指定目标平台
**/NAME:filename**
    当创建导入库时，设置对应的 dll 文件的名称。
**/NODEFAULTLIB[:library]**
    忽略对应的默认库文件
**/NOLOGO**
    不打印版权信息
**/OUT:filename**
    指定输出文件的名称
**/REMOVE:object**
    忽略一个目标文件或者库文件中的对象
**/SUBSYSTEM:sys[,major[.minor]]**
    告诉操作系统怎样运行程序，sys 可以是 CONSOLE、EFI_APPLICATION、EFI_BOOT_SERVICE_DRIVER、
    EFI_ROM、EFI_RUNTIME_DRIVER、NATIVE、POSIX、WINDOWS、WINDOWSCE
**/VERBOSE**
    打印链接器信息
**/WX[:NO] /WX[:nnnn[,nnnn...]]**
    将链接警告当成错误

EDITBIN 工具
=============

EDITBIN 工具命令行格式： ::

    editbin [options] files...

EDITBIN 可以修改目标文件，可执行文件，动态链接库。选项可以以 / 或者 - 字符开头，选项名称
和它的关键字以及文件名参数都不是大小写敏感的。

**/HEAP:reserve[,commit]**
    设置堆大小
**/STACK:reserve[,commit]**
    设置栈大小
**/LARGEADDRESSAWARE**
    应用程序支持大于 2G 的地址空间
**/NOLOGO**
    不打印版权信息
**/RELEASE**
    设置 checksum 头部信息
**/SECTION:name[=newname][,attributes][alignment]**
    设置区属性信息
**/SUBSYSTEM:sys[,major[.minor]]**
    告诉操作系统怎样运行程序，sys 可以是 BOOT_APPLICATION、CONSOLE、EFI_APPLICATION、
    EFI_BOOT_SERVICE_DRIVER、EFI_ROM、EFI_RUNTIME_DRIVER、NATIVE、POSIX、WINDOWS、
    WINDOWSCE
**/VERSION:left[.right]**
    设置 exe 和 dll 文件中的版本头部信息

DUMPBIN 工具
=============

DUMPBIN 工具命令行格式： ::

    dumpbin [options] files...

DUMPBIN 可以查看目标文件，标准库文件，可执行文件，动态链接库的信息。

**/ALL**
    除了 /DISASM 外的所有信息，可以使用 /RAWDATA:NONE 去除原始的二进制数据。
**/ARCHIVEMEMBERS**
    显示库的成员文件
**/DEPENDENTS**
    打印这个库文件依赖的其他动态链接库
**/DIRECTIVES**
    打印编译器生成的 .drectve 区的信息
**/DISASM{:[BYTES|NOBYTES]}**
    打印代码区的反汇编信息，默认是 BYTES 打印指令信息和操作符操作数。
**/EXPORTS**
    打印可执行文件或者动态链接库导出的所有定义。
**/FPO**
    打印 frame pointer optimization (FPO) 记录。
**/HEADERS**
    打印文件头部和各个分区的头部信息，如果是库文件会显示每个成员文件的头部信息。
**/IMMPORTS[:file]**
    打印这个 exe 或者 dll 导入的所有 DLL 库的信息，如果指定了 file 只显示导入该 dll 的
    信息。
**/LINENUMBERS**
    打印代码行信息。
**/LINKRMEMBER[:{1|2}]**
    打印库定义的公开符号，不提供参数显示所有信息，参数 1 只显示符号，参数 2 只显示对象的
    索引并且按字母排序显示。
**/LOADCONFIG**
    打印 IMAGE_LOAD_CONFIG_DIRECTORY 结构信息。
**/NOPDB**
    不查找 pdb 文件
**/OUT:filename**
    将信息输出到文件，默认打印到标准输出。
**/PDATA**
    只适用于 RISC 处理器，打印 exceptiontables (.pdata)。
**/RANGE:virtualAddressMin[,virtualAddressMax]**
    与其他选项如 /RAWDATA /DISASM 一起使用提供打印的范围。虚拟地址可以在 map 文件中看
    到，RVA+Base。
**/RAWDATA[:{1|2|4|8|NONE[,number]}]**
    打印文件每个区的原始内容，1 是默认值显示为十六进制单字节，2 显示为十六进制双字节，4
    显示为十六进制四字节，8 显示为十六进制八字节，NONE 不信是原始内容，number 每一行显
    示多少个数据值。
**/RELOCATIONS**
    显示文件中的 relocations 信息。
**/SECTION:section**
    只打印某个分区的信息
**/SUMMARY**
    只打印分区的最少信息，包括大小信息。
**/SYMBOLS**
    打印符号表信息。
**/TLS**
    打印 IMAGE_TLS_DIRECTORY 结构信息。
