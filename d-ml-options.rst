汇编选项
=========

对于 x64 或 ARM64 目标，使用内联汇编或 ASM 关键字是不支持的。要将使用内联汇编的 x86 代
码移植到 x64 或 ARM64，可以将代码转换为 C/C++，使用编译器内在函数，或创建汇编语言源文件。
Microsoft C++ 编译器支持内在函数，这样能够尽可能以跨平台的方式使用特殊功能指令，例如特
权指令、位扫描或测试、原则操作等。

汇编器 ML（Microsoft Assembler）汇编和链接一个或多个汇编语言源文件，其命令行选项是大小
写敏感的。 ::

    ML [options] filelist [/link linkoptions]
    ML64 [options] filelist [/link linkoptions]

ML 和 ML64 的一些命令行选项是位置敏感的。例如，因为 ML 和 ML64 可以接受多个 /c 选项，
任何相应的 /Fo 选项必须在 /c 之前指定。 ::

    ml.exe /Fo a1.obj /c a.asm /Fo bi.obj /c b.asm

汇编器使用的环境变量如下：

- INCLUDE 指定包含文件的搜索路径
- ML 指定默认的命令行选项
- TMP 指定临时文件路径

命令行 options 可以指定以下一个或多个选项：

**/AT**
    启用 tiny-memory-model 支持，这个选项允许汇编器生成适用于 .com 格式文件的代码，这
    种格式是 MS-DOS 应用程序常用的格式。同时，它也会启用对违反 .com 格式要求的代码构造
    的错误消息。对 ml64.exe 不可用。
**/Bl<linker>**
    使用一个替换的链接器。
**/c**
    仅汇编，不链接。
**/coff**
    产生通用目标文件格式（COFF）类型的目标文件，这个选项对于 Win32 汇编语言开发是必需的。
    对 ml64.exe 不可用。
**/Cp**
    保留所有用户标识符的大小写。
**/Cu**
    将所有标识符到映射成大写形式（默认选项）。对 ml64.exe 不可用。
**/Cx**
    保留公共和外部符号的大小写。
**/D<name>[=value]**
    定义一个名为 name 的文本宏，如果没有指定 value 内容为空，如果内容包含空格字符必须
    用引号引起。
**/Sf**
    将第一次汇编的输出添加到列表文件中。
**/Sa**
    开启列表文件所有的可用信息。
**/Sl<width>**
    设置列表文件每行的宽度，范围是 60~255 或 0，默认是 0。与 PAGE width 相同。
**/Sn**
    当产生列表文件时关掉符号表。
**/Sp<length>**
    设置列表文件每页的行数，范围是 10~255 或 0，默认是 0。与 PAGE length 相同。
**/Ss<text>**
    与 SUBTITLE text 相同。
**/St<text>**
    与 TITLE text 相同。
**/Sx**
    在列表文件中显式条件为假的条件编译指令的内容。
**/EP**
    输出预处理后的列表文件到 STDOUT，见 /Sf。
**/ERRORREPORT [NONE|PROMPT|QUEUE|SEND]**
    已过时，错误报告由 Windows Error Reporting (WER) 设置控制。
**/F <hexnum>**
    设置栈大小为 hexnum 字节，相当于 /link /STACK:<number>。值必须是十六进制，hexnum
    之前必须由一个空格。
**/Fe<filename>**
    指定可执行文件。
**/Fl[filename]**
    指定汇编列表文件，见 /Sf。
**/Fm[filename]**
    指定一个链接器 map 文件。
**/Fo<filename>**
    指定目标文件。
**/FPi**
    用于为浮点算术（仅混合语言）生成仿真器修复（fix-ups）。对 ml64.exe 不可用。
**/Fr[filename]**
    生成一个源浏览器（source browser）文件 .sbr，该文件包含了汇编源代码的结构信息，可
    以被工具解析查看代码的层次结构和关系。
**/FR[filename]**
    生成一个扩展形式的 .sbr 文件。
**/Gc**
    指定使用 FORTRAN 或 Pascal 风格的调用约定和命名规则。这个选项影响函数调用时的参数
    传递已经函数名的修饰方式。等价于 OPTION LANGUAGE:PASCAL。对 ml64.exe 不可用。
**/Gd**
    指定使用 C 风格的调用约定和命名规则，等价于 OPTION LANGUAGE:C。对 ml64.exe 不可用。
**/Gz**
    指定使用 __stdcall 风格的调用约定和命名规则，等价于 OPTION LANGUAGE:STDCALL。对
    ml64.exe 不可用。
**/Gy[-]**
    为链接器隔离函数，启用函数级（function-level）链接，指向同一个函数共享一个指针。
**/H<number>**
    限制外部名称的有效字符数，默认为 31 个字符。对 ml64.exe 不可用。
**/omf**
    生成目标模块文件格式（OMF）类型的目标文件，该选项暗含了 /c 选项。ml.exe 不支持链接
    OMF 对象文件。对 ml64.exe 不可用。
**/safeseh**
    标记目标文件：要么不包含异常处理，要么包含的异常处理都声明为 .SAFESEH。对 ml64.exe
    不可用。
**/Ta<filename>**
    汇编后缀名不是 .asm 的源文件。
**/w**
    相当于 /W0 /WX。
**/W<level>**
    设置警告等级，level 可以是 0、1、2、3。
**/WX**
    将警告当成错误。
**/X**
    忽略 INCLUDE 环境变量。
**/Zd**
    在目标文件中生成行号信息。
**/Zf**
    将所有符号都标记为公开的。
**/ZH:MD5**
    使用 MD5 来 checksum 调试信息。
**/ZH:SHA_256**
    使用 SHA256 来 checksum 调试信息，在 Visual Studio 2022 版本 17.0 及以后时默认
    的选项。
**/Zi**
    在目标文件中生成 CodeView 信息。CodeView 是微软提供的一种调试信息格式，它允许程序
    员在调试器中查看和操作程序的符号信息，如变量名、函数名和源代码行号。
**/Zm**
    使能 M510 选项，即启用对 MASM 5.1 的最大兼容性支持。该选项用于确保在更新的汇编器版
    本中编写的代码能够与较旧版本的 MASM 兼容。对 ml64.exe 不可用。
**/Zp[alignment]**
    使用指定的字节边界对结构打包，alignment 可以是 1、2、4。
**/Zs**
    仅执行语法检查。
**/help**
    显式命令行语法和选项。
**/nologo**
    不打印工具版权信息。
**/quiet**
    不打印汇编期间的信息。
**/?**
    显式命令行语法和选项。

链接选项
---------

汇编选项 /link 之后可以包含传递给链接器的链接选项。链接器的完整命令行格式如下： ::

    link [options] [files] [@commandfile]

链接器可以将目标文件和库文件，链接成可以执行程序或者 DLL。LINK 不会根据后缀名来判断文件
的类型，而是实际检查文件内容来确定。链接器的输入文件可以是 .obj，.netmodule，.lib，.exp，
.def，.pdb，.res，.exe，.txt，.ilk。链接器会先处理选项，然后处理文件。每个选项以/或者-
字符开始，有些选项有用:分隔的参数，选项中不允许出现空格或者tab字符，除了/COMMENT可以使用
引用的字符串。选项的名称和它的关键字，以及文件名称参数都不是大小写敏感的，但是作为参数的
标识符是大小些敏感的。输入文件如果不提供后缀名，链接器默认它为 .obj 后缀来查找文件，但是
链接器不会根据后缀名来判断文件类型。链接器返回0表示成功，返回其他错误表示失败。

链接器可以接受 COFF 标准库文件和 COFF 导入库文件，两者通常使用 .lib 后缀。标准库文件是
包含目标文件的使用 LIB 工具创建的库。导入库文件（import libraries）包含导出信息，可以
使用 LINK 或者 LIB 工具创建。使用 LINK 创建导入库文件，可以参考 /dll 选项。

传给 LINK 的库文件，要么是通过文件参数，要么通过默认库参数 /defaultlib。LINK 首先通过
查寻命令行指定的库文件来解除外部引用，然后使用 /defaultlib 指定的库，然后使用 obj 文件
中保存的默认库名称。

使用 /Zi 编译的 .obj 文件包含了 pdb 文件名，你不需要提供的目标文件的 pdb 名称给链接器，
链接器使用 .obj 中内嵌的名称找到 pdb 文件。同样可调式的库文件在链接时对应的 pdb 文件必
须要存在。链接器也是用 pdb 文件来保存 .exe 或者 .dll 文件的调试信息。链接器会在绝对路径
或者 obj 所在路径查找 obj 对应的 pdb 文件。

使用的链接器的版本必须比输入文件使用的工具集合版本一样或者更新，使用 /GL 编译选项编译的
静态库文件和目标文件，以及使用 /LTCG 链接选项链接产生的文件，都是版本不兼容的，必须使用
相同的 visual studio 版本。所有使用 /GL 和 /LTCG 生成的库文件和目标文件，在编译和链接
时必须都使用相同版本的工具集，只要有一个不一样就会报 C1047 错误。

链接器默认输出 exe 文件，如果指定了 /dll 选项输出一个 dll 文件，输出的文件名可以通过
/out 选项指定。在增量链接模式下，链接器会产生一个 .ilk 文件用于后续链接。如果链接器创建
一个包含导出信息的文件（通常是一个 dll），它还会产生一个 lib 文件，除非在构建过程中使用
了一个 exp 文件。你可以通过 /implib 选项控制这个 lib 文件名。

导出文件 .exp 包含导出的函数和数据信息，当 LIB 创建一个导入库时同时会创建一个导出文件。
在创建 dll 文件时，如果提供了 exp 文件，链接器不会再创建一个 lib 文件，因为它认为这个
lib 文件已经存在了。

**/nologo**
    不打印版权信息。
**/align[:number]**
    指定可执行文件中每个分区的对齐方式，必须是2的幂，默认是4096。除非是编写的是一个设备
    驱动，一般不需要更改这个信息。可以使用 /section 选项修改特定区的对齐方式。
**/section:name,[[!]{D|E|K|P|R|S|W}][,ALIGN=number]**
    修改分区属性。
**/debug[:{FASTLINK|FULL|NONE}]**
    调试器会产一个 pdb 文件来包含调试信息，不指定参数相当于 /debug:full。当指定该选项，
    /incremental 会被同时指定，还会将 /opt 的默认值 ref 和 icf 改成 noref 和 noicf，
    如果你需要这些选项必须在 /debug 选选项之后指定 /opt:ref 和 /opt:icf。不可能创建一
    个包含调试信息的 exe 或者 dll，调试信息总是包含在 obj 或者 pdb 文件中。
**/debugtype:[CV|PDATA|FIXUP]**
    指定调试信息包含的数据。
**/def:filename**
    传递一个模块定义文件 .def 给链接器。
**/defaultlib:library**
    添加一个默认库文件，如果不添加参数会忽略所有的默认库文件。
**/nodefaultlib[:library]**
    忽略对应的默认库文件
**/delay:UNLOAD /delay:NOBIND**
    控制 DLL 的延迟加载。
**/delayload:dllname**
    对特定的 DLL 进行延迟加载。
**/dll**
    创建一个 DLL 文件，DLL 文件包含了其他程序使用的导出符号，有三种导出方式，安装推荐顺
    序依次为：源代码中使用 __declspec(dllexport)；.def 文件中使用 EXPORTS 语句；链接
    器中使用 /export 选项。
**/implib:filename**
    指定 import library 的输出路径。
**/libpath:dir**
    库文件搜索路径。
**/include:symbol**
    将符号添加到符号表。
**/driver[:UPONLY|:WDM]**
    创建一个内核模式的驱动文件。
**/entry:function**
    设置exe或者dll的启动地址。
**/noentry**
    创建 resource-only 的 DLL。
**/export:entryname[,@ordinal[,NONAME]][,DATA]**
    导出一个函数。
**/functionpadmin**
    创建一个可热更新的 image。
**/heap:reserve[,commit]**
    设置 heap 的大小，以字节为单位。默认预留大小为 1MB，默认每次分配大小是 4KB。
**/stack:reserve[,commit]**
    设置栈的大小，是对 exe 有效，当生成 dll 是会被忽略。默认预留大小是 1MB，默认每次分
    配大小是 4KB。可以通过 editbin 来修改 exe 文件的栈大小。
**/kernel**
    创建一个内核模式的二进制文件。
**/largeaddressaware[:NO]**
    应用程序支持大于 2G 的地址空间。
**/ltcg[:{INCREMENTAL|NOSTATUS|STATUS|OFF}]**
    链接时代码生成，使用该选项执行全程序优化，或者创建 PGO（profile-guided 优化）指令，
    或者执行训练（training），或者进行 profile-guided 优化。
**/ltcgout:[pathname]**
    指定 /ltcg:INCREMENTAL 的输出 .iobj 格式文件名称。
**/machine:{ARM|ARM64|ARM64EC|EBC|X64|X86}**
    指定目标平台。
**/map:filename**
    创建 map 文件。
**/mapinfo:EXPORTS**
    包含指定的信息到 map 文件，EXPORTS 包含导出的函数。
**/merge:from=to**
    合并 from 区合并到 to 区，from 区会被移除，例如 /merge:.rdata=.text。
**/natvis:filename**
    将可视化调试信息文件（nativis 文件）内嵌到 pdb 文件中。
**/opt:{REF|NOREF} /opt:{ICF[=iterations]|NOICF} /opt:{LBR|NOLBR}**
    控制链接优化，REF 去除未引用的函数和数据，ICF 去除冗余的 COMDATs 区的数据，iteratioins
    默认是 1 表示遍历符号是否重复的次数。ICF 可以合并相等的数据和函数，但会修改出现在栈
    跟踪中的函数名，而且不可能在一些函数打单步调试点，也不能产出一些数据的值，产生的代码
    是一致的，但是会影响消息信息。LBR 仅用于 ARM 代码。
**/order:@filename**
    以可指定的顺序将各个 COMDAT 分区放入 image，可以将函数和他调用的函数放到一起或者经
    常调用的函数放到一起，这种技术称为 swap tuning 或者 paging 优化。
**/out:filename**
    指定输出文件名称。
**/pdb:filename**
    创建一个 pdb 文件，当指定了 /debug 选项是链接器会创建 pdb 文件，如果没有指定 /debug
    该选项会被忽略。pdb 文件最大可以到 2GB。
**/pdbaltpath:pdb_file_name**
    这个选项不改变 pdb 的名称，只是让链接器修改二进制文件中保存的 pdb 路径。
**/pdbstripped:pdb_file_name**
    产生额外的一个不包含私有符号的 pdb 文件。
**/pgd:filename**
    指定一个 pgd 文件用于 profile-guided 优化。
**/profile**
    产生一个输出文件可用于性能剖析器。
**/release**
    在 exe 文件头部设置 checksum，操作系统需要设备驱动的 checksum，来确保你的设备驱动
    兼容未来的操作系统。该选项默认设置当使用 /subsystem:NATIVE 选项的时候。
**/subsystem:sys[,major[.minor]]**
    其中 sys 可以是 BOOT_APPLICATION、CONSOLE、EFI_APPLICATION、EFI_BOOT_SERVICE_DRIVER、
    EFI_ROM、EFI_RUNTIME_DRIVER、NATIVE、POSIX、WINDOWS。该选项告诉操作系统怎样运行
    exe 文件，如果定义了 main 或者 wmain 那么 CONSOLE 是默认值，NATIVE 是内核驱动模式，
    如果添加了 /driver:WDM 那么 NATIVE 是默认值，POSIX 是 Windows NT 的 POSIX 子系统，
    WINDOWS 程序不需要一个控制台，如果定义了 WinMain 或者 wWinMain 那么 WINDOWS 是默
    认值。
**/useprofile**
    使用 profile-guided 优化信息来创建一个优化的 image。
**/verbose[:{CLR|ICF|INCR|LIB|REF|SAFESEH|UNUSEDDELAYLOAD|UNUSEDLIBS}]**
    打印链接器信息。
**/version:major[.minor]**
    设置 exe 和 dll 文件中的版本头部信息，可以使用 dumpbin /headers 来查看这些信息。
    major 和 minor 的值可以是 0 到 65535。
**/wholearchive /wholearchive:library**
    包含指定或所有静态库中的每个目标文件，默认链接器只链接被可执行程序引用的目标文件。该
    选项使链接器认为这个静态库中目标文件相当于在命令行中指定的一样。该选项可以让链接器重
    新导出静态库中的所有符号，这样可以保证你的库代码、资源、以及元数据到包含到了最终的二
    进制文件中。
**/incremental[:NO]**
    默认链接器工作在增量链接模式。
**/ilk:[pathname]**
    指定增量链接的输出 ilk 文件路径，如果没有使用该选项，会自动在当前路径生成对应的 .ilk
    文件。
**/ignore:warning[,warning]**
    忽略特定的链接警告，默人链接器会通报所有的警告。
**/WX[:NO] /WX[:nnnn[,nnnn...]]**
    将链接警告当成错误。
