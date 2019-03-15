# erlang中的unicode

## unicode 实现方式
实现对Unicode字符集的支持是一个持续的过程。 Erlang增强建议（EEP）10概述了Unicode支持的基础知识，并在二进制文件中指定了一个默认编码，所有Unicode感知模块将来都要处理。

以下概述了迄今为止所做的工作：
- EEP10中描述的功能在Erlang / OTP R13A中实现
- Erlang / OTP R14B01增加了对Unicode文件名的支持，但它并不完整，默认情况下在没有为文件名编码提供保证的平台上禁用。
- 使用Erlang / OTP，R16A支持UTF-8编码的源代码，许多应用程序的增强功能支持Unicode编码的文件名，并且在许多情况下支持UTF-8编码的文件。使用```file:consult/1```可以读取utf-8文件，在io系统中也有更多对于utf-8编码的支持
- 在Erlang / OTP 17.0中，Erlang源文件的编码默认值已切换为UTF-8。
- 在Erlang / OTP 20.0中，原子(atom)和函数(function)可以包含Unicode字符。 模块名称，应用程序名称和节点名称仍受限于ISO Latin-1范围。

为unicode中的规范化表单添加了支持，字符串模块现在处理utf8编码的二进制文件。

本节概述了当前的Unicode支持，并提供了一些使用Unicode数据的方法。

## 理解unicode
在Erlang中使用Unicode支持的经验清楚地表明，理解Unicode字符和编码并不像人们期望的那样容易。 该领域的复杂性和标准的含义需要彻底理解很少有人想到的概念。

此外，Erlang实现需要理解对于许多（Erlang）程序员来说从来不是问题的概念。 要理解和使用Unicode字符，您需要彻底研究该主题，即使您是一位经验丰富的程序员也是如此。

作为一个例子，考虑在大写和小写字母之间进行转换的问题。 例如，阅读标准会让您意识到在所有脚本中没有简单的一对一映射

在德语中，字母“ß”（尖锐的s）是小写的，但大写的等号是“SS”。

在希腊语中，字母“Σ”有两种不同的小写形式，“ς”在单词最终位置，“σ”在别处。

在土耳其语中，点缀和无点“i”都以小写和大写形式存在。

西里尔语“我”通常没有小写形式。

没有大写概念的语言

因此，转换函数不仅必须一次知道一个字符，而且可能知道整个句子，要翻译的自然语言，输入和输出字符串长度的差异等等。 Erlang / OTP目前没有特定于语言处理的Unicode大写/小写功能，但公开可用的库解决了这些问题。

另一个例子是重音字符，其中相同的字形有两个不同的表示。瑞典字母“ö”就是一个例子。 Unicode标准有一个代码点，但你也可以把它写成“o”后面跟着“U + 0308”（结合分音符，简化意思是最后一个字母上面有“¨”）。它们具有相同的字形，用户感知的字符。它们在大多数情况下都是相同的，但具有不同的表示形式。例如，MacOS X转换所有文件名以使用Combining Diaeresis，而大多数其他程序（包括Erlang）尝试通过执行相反的操作来隐藏它，例如列出目录。无论如何，通常很重要的是规范化这些字符以避免混淆。

示例列表可以很长。当程序仅考虑一种或两种语言时，需要一种不需要的知识。在构建通用标准时，人类语言和脚本的复杂性无疑使这成为一项挑战。在程序中正确支持Unicode需要付出努力。

## 什么是Unicode
Unicode是定义所有已知，活动或死亡脚本的代码点（数字）的标准。原则上，任何语言中使用的每个符号都有一个Unicode代码点。 Unicode代码点由Unicode Consortium定义和发布，Unicode Consortium是一个非营利组织。

在整个计算领域，对Unicode的支持正在增加，因为当在全球环境中使用程序时，一个共同字符集的好处是压倒性的。除了标准的基础，所有脚本的代码点，一些编码标准是可用的。

了解编码和Unicode字符之间的区别至关重要。 Unicode字符是根据Unicode标准的代码点，而编码是表示此类代码点的方式。编码只是表示的标准。例如，UTF-8可用于表示Unicode字符集的非常有限的部分（例如ISO-Latin-1）或完整的Unicode范围。它只是一种编码格式。

只要所有字符集限制为256个字符，每个字符就可以存储在一个字节中，因此对于字符或多或少只有一个实际编码。在一个字节中编码每个字符非常常见，甚至没有命名编码。对于Unicode系统，有超过256个字符，因此需要一种常用的方法来表示这些字符。表示代码点的常用方法是编码。这对程序员来说意味着一个全新的概念，即字符表示的概念，这在以前是没有问题的。

不同的操作系统和工具支持不同的编码。例如，Linux和MacOS X选择了UTF-8编码，它与7位ASCII向后兼容，因此影响用简单英语编写的程序。 Windows支持UTF-16的限制版本，即所有代码平面，其中字符可以存储在一个单独的16位实体中，其中包括大多数生活语言。

以下是分布最广的编码：

### 字面表示（Bytewise representation）

这不是一个正确的Unicode表示，而是用于Unicode标准之前的字符的表示。 它仍可用于表示Unicode标准中的字符代码点，数字<256，与ISO Latin-1字符集完全对应。 在Erlang中，这通常表示为latin1编码，这有点误导，因为ISO Latin-1是字符代码范围，而不是编码。

### UTF-8
根据代码点，每个字符存储在一到四个字节中。 编码向后兼容7位ASCII的字节表示，因为所有7位字符都以UTF-8存储在一个字节中。 代码点127之外的字符存储在更多字节中，使第一个字符中的最高有效位表示多字节字符。 有关编码的详细信息，RFC是公开可用的。

请注意，UTF-8与128到255的代码点的字节表示不兼容，因此ISO Latin-1字节表示通常与UTF-8不兼容。

### UTF-16
此编码与UTF-8有许多相似之处，但基本单位是16位数。 这意味着所有字符至少占用两个字节，而一些高字符占用四个字节。 声称使用UTF-16的某些程序，库和操作系统仅允许可以存储在一个16位实体中的字符，这通常足以处理活动语言。 由于基本单元不止一个字节，因此会出现字节顺序问题，这就是UTF-16同时存在于big-endian和little-endian变体中的原因。

在Erlang中，适用时支持完整的UTF-16范围，例如unicode模块和位语法。

### UTF-32
最直接的表现。 每个字符都存储在一个32位数字中。 对于一个字符，不需要转义或任何可变数量的实体。 所有Unicode代码点都可以存储在一个32位实体中。 与UTF-16一样，存在字节顺序问题。 UTF-32既可以是big-endian，也可以是little-endian。

### UCS-4
基本上与UTF-32相同，但没有一些由IEEE定义的Unicode语义，并且很少用作单独的编码标准。 对于所有正常（也可能是异常）的使用，UTF-32和UCS-4是可互换的。

Unicode标准中未使用某些数字范围，某些范围甚至被视为无效。 最值得注意的无效范围是16＃D800-16＃DFFF，因为UTF-16编码不允许对这些数字进行编码。 这可能是因为UTF-16编码标准从一开始就希望能够在一个16位实体中保存所有Unicode字符，但随后进行了扩展，在Unicode范围内留下了一个漏洞来处理向后兼容性。

代码点16＃FEFF用于字节顺序标记（BOM），并且不鼓励在其他上下文中使用该字符。 但它有效，因为字符“ZWNBS”（零宽度非破坏空间）。 BOM用于识别预先不知道此类参数的程序的编码和字节顺序。 BOM比预期的更少使用，但可以更广泛地传播，因为它们为程序提供了对某个文件的Unicode格式进行有根据的猜测的方法。

## Unicode支持领域
为了支持Erlang中的Unicode，已经解决了各个方面的问题。 本节将在本用户指南的后面简要介绍每个区域。

### 表示
要在Erlang中处理Unicode字符，需要列表和二进制文件中的通用表示。 EEP（10）以及随后在Erlang / OTP R13A中的初始实现确定了Erlang中Unicode字符的标准表示。

### 操纵
Unicode字符需要由Erlang程序处理，这就是库函数必须能够处理它们的原因。 在某些情况下，功能已添加到现有接口（因为字符串模块现在可以处理具有任何代码点的字符串）。 在某些情况下，添加了新功能或选项（如io模块，文件处理，unicode模块和位语法）。 今天，Kernel和STDLIB中的大多数模块以及VM都支持Unicode。

### 文件I / O.
到目前为止，I / O是Unicode最成问题的领域。 文件是存储字节的实体，编程的传说一直是将字符和字节视为可互换的。 对于Unicode字符，如果要将数据存储在文件中，则必须确定编码。 在Erlang中，您可以打开带有编码选项的文本文件，以便您可以从中读取字符而不是字节，但您也可以打开字节I / O的文件。

Erlang I / O系统的设计（或至少使用过）是指您希望任何I / O服务器处理任何字符串数据的方式。 但是，使用Unicode字符时不再是这种情况。 Erlang程序员现在必须知道数据最终的设备的功能。 此外，Erlang中的端口是面向字节的，因此，如果没有先将其转换为选择的编码，则无法将任意字符串（Unicode）字符发送到端口。

### 终端I / O.
终端I / O比文件I / O稍微容易一些。 输出用于人类阅读，通常是Erlang语法（例如，在shell中）。 存在任何Unicode字符的语法表示而不显示字形（而是写为\ x {HHH}）。 因此，即使终端本身不支持整个Unicode范围，也可以显示Unicode数据。
### 文件名
根据底层操作系统和文件系统，文件名可以以不同方式存储为Unicode字符串。这可以通过程序相当容易地处理。当文件系统的编码不一致时会出现问题。例如，Linux允许使用任何字节序列命名文件，留给每个程序来解释这些字节。在使用这些“透明”文件名的系统上，必须通过启动标志通知Erlang文件名编码。默认为字节解释，这通常是错误的，但允许解释所有文件名。

如果在不是默认值的平台上启用Unicode文件名转换（+ fnu），“原始文件名”的概念可用于处理错误编码的文件名。

### 源代码编码
Erlang源代码支持UTF-8编码和字节编码。 Erlang / OTP R16B中的默认值是逐字（latin1）编码。它在Erlang / OTP 17.0中被改为UTF-8。您可以通过文件开头的以下注释来控制编码：
```
%%  -  *  - 编码：utf-8  -  *  - 
```
这当然要求您的编辑器也支持UTF-8。同样的注释也由诸如file：consult / 1，发布处理程序等函数解释，因此您可以使用UTF-8编码将源目录中的所有文本文件都包含在内。

### 语言
使用UTF-8的源代码还允许您编写字符串文字，函数名称和包含代码点> 255的Unicode字符的原子。模块名称，应用程序名称和节点名称仍限制在ISO Latin-1范围内。您使用类型/ utf8的二进制文字也可以使用大于255的Unicode字符表示。使用7位ASCII以外的字符的模块名称或应用程序名称可能会导致文件命名方案不一致的操作系统出现问题，并且可能会损害可移植性，所以不推荐。

EEP 40建议该语言也允许变量名中的Unicode字符> 255。是否实施该EEP还有待确定。

## 标准Unicode表示
在Erlang中，字符串是整数列表。直到Erlang / OTP R13定义为在ISO Latin-1（ISO 8859-1）字符集中编码的字符串，即代码点的代码点，Unicode字符集的子范围。

因此，字符串的标准列表编码很容易扩展，以处理整个Unicode范围。 Erlang中的Unicode字符串是一个包含整数的列表，其中每个整数都是有效的Unicode代码点，表示Unicode字符集中的一个字符。

ISO Latin-1中的Erlang字符串是Unicode字符串的子集。

仅当字符串包含<256的代码点时，才能通过使用例如erlang：iolist_to_binary / 1将其直接转换为二进制文件，或者可以直接发送到端口。如果字符串包含大于255的Unicode字符，则必须决定编码，并使用unicode将字符串转换为首选编码的二进制文件：characters_to_binary / 1,2,3。字符串通常不是字节列表，因为它们是在Erlang / OTP R13之前，它们是字符列表。字符通常不是字节，它们是Unicode代码点。

二进制文件比较麻烦。出于性能原因，程序通常将文本数据存储在二进制文件而不是列表中，主要是因为它们更紧凑（每个字符一个字节而不是每个字符两个字，如列表的情况）。使用erlang：list_to_binary / 1，可以将ISO Latin-1 Erlang字符串转换为二进制字符串，有效地使用逐字节编码：每个字符一个字节。这对于那些有限的Erlang字符串很方便，但不能用于任意Unicode列表。

由于UTF-8编码广泛传播并在7位ASCII范围内提供了一些向后兼容性，因此它被选为Erlang二进制文件中Unicode字符的标准编码。

只要Erlang中的库函数处理二进制文件中的Unicode数据，就会使用标准二进制编码，但在外部通信时当然不会强制执行。存在函数和位语法以对二进制文件中的UTF-8，UTF-16和UTF-32进行编码和解码。但是，处理二进制文件和Unicode的库函数通常只处理默认编码。

字符数据可以从许多来源组合，有时可以混合使用字符串和二进制文件。 Erlang长期以来一直有iodata或iolists的概念，其中二进制文件和列表可以组合起来表示一个字节序列。同样，支持Unicode的模块通常允许二进制文件和列表的组合，其中二进制文件具有以UTF-8编码的字符，并且列表包含表示Unicode代码点的二进制文件或数字：

```
unicode_binary() = binary() with characters encoded in UTF-8 coding standard

chardata() = charlist() | unicode_binary()

charlist() = maybe_improper_list(char() | unicode_binary() | charlist(),
  unicode_binary() | nil())
```
模块unicode甚至支持包含除UTF-8之外的其他编码的二进制文件的类似混合，但这是允许与外部数据进行转换的特殊情况：

```
external_unicode_binary() = binary() with characters coded in a user-specified
  Unicode encoding other than UTF-8 (UTF-16 or UTF-32)

external_chardata() = external_charlist() | external_unicode_binary()

external_charlist() = maybe_improper_list(char() | external_unicode_binary() |
  external_charlist(), external_unicode_binary() | nil())
```
## 基本语言支持
从Erlang / OTP R16开始，Erlang源文件可以用UTF-8或逐字（latin1）编码编写。 有关如何声明Erlang源文件的编码的信息，请参阅epp（3）模块。 从Erlang / OTP R16开始，可以使用Unicode编写字符串和注释。 从Erlang / OTP 20开始，也可以使用Unicode编写原子和函数。 仍必须使用ISO Latin-1字符集中的字符命名模块，应用程序和节点。 （语言中的这些限制与源文件的编码无关。）

### 位语法
位语法包含用于处理三种主要编码中的二进制数据的类型。 这些类型命名为utf8，utf16和utf32。 utf16和utf32类型可以是big-endian或little-endian变体：

```
<<Ch/utf8,_/binary>> = Bin1,
<<Ch/utf16-little,_/binary>> = Bin2,
Bin3 = <<$H/utf32-little, $e/utf32-little, $l/utf32-little, $l/utf32-little,
$o/utf32-little>>,
```
为方便起见，可以使用以下（或类似）语法在二进制文件中使用Unicode编码对文字字符串进行编码：

```
Bin4 = <<"Hello"/utf16>>,
```

### 字符串和字符文字
对于源代码，有一个扩展语法\ OOO（反斜杠后跟三个八进制数）和\ xHH（反斜杠后跟x，后跟两个十六进制字符），即\ x {H ...}（反斜杠后跟x ，后面是左大括号，任意数量的十六进制数字和一个终止右大括号）。 即使源文件的编码是按字节顺序（latin1），这也允许在字符串中字面输入任何代码点的字符。

在shell中，如果使用Unicode输入设备，或者使用UTF-8中存储的源代码，$可以直接跟随生成整数的Unicode字符。 在以下示例中，输出Cyrillic的代码点：

```
7> $с.
1089
```
### 启发式字符串检测
在某些输出函数和shell中返回值的输出中，Erlang尝试以启发式方式检测列表和二进制文件中的字符串数据。 通常，您会在以下情况下看到启发式检测：
```
1> [97,98,99].
"abc"
2> <<97,98,99>>.
<<"abc">>    
3> <<195,165,195,164,195,182>>.
<<"åäö"/utf8>>
```

这里shell检测包含可打印字符的列表或包含按字节或UTF-8编码的可打印字符的二进制文件。但什么是可打印的角色？一种观点是Unicode标准认为可打印的任何内容，也可根据启发式检测进行打印。结果是，几乎所有整数列表都被视为字符串，并且打印了各种字符，也可能是您的终端在其字体集中缺少的字符（导致一些未被认可的通用输出）。另一种方法是使其向后兼容，以便仅使用ISO Latin-1字符集来检测字符串。第三种方法是让用户确切地确定要将哪些Unicode范围视为字符。

从Erlang / OTP R16B开始，您可以分别通过提供启动标志+ pc latin1或+ pc unicode来选择ISO Latin-1范围或整个Unicode范围。为了向后兼容，latin1是默认的。这仅控制启发式字符串检测的完成方式。预计将来会添加更多范围，从而可以根据与用户相关的语言和区域定制启发式方法。

以下示例显示了两个启动选项：

```
$ erl +pc latin1
Erlang R16B (erts-5.10.1) [source] [async-threads:0] [hipe] [kernel-poll:false]

Eshell V5.10.1  (abort with ^G)  
1> [1024].
[1024]
2> [1070,1085,1080,1082,1086,1076].
[1070,1085,1080,1082,1086,1076]
3> [229,228,246].
"åäö"
4> <<208,174,208,189,208,184,208,186,208,190,208,180>>.
<<208,174,208,189,208,184,208,186,208,190,208,180>>
5> <<229/utf8,228/utf8,246/utf8>>.
<<"åäö"/utf8>>
```
```
$ erl +pc unicode
Erlang R16B (erts-5.10.1) [source] [async-threads:0] [hipe] [kernel-poll:false]

Eshell V5.10.1  (abort with ^G)  
1> [1024].
"Ѐ"
2> [1070,1085,1080,1082,1086,1076].
"Юникод"
3> [229,228,246].
"åäö"
4> <<208,174,208,189,208,184,208,186,208,190,208,180>>.
<<"Юникод"/utf8>>
5> <<229/utf8,228/utf8,246/utf8>>.
<<"åäö"/utf8>>
```
在示例中，您可以看到默认的Erlang shell仅将ISO Latin1范围内的字符解释为可打印，并且仅检测包含字符串数据的那些“可打印”字符的列表或二进制文件。 包含俄语单词“Юникод”的有效UTF-8二进制文件不会打印为字符串。 当以可打印的所有Unicode字符（+ pc unicode）启动时，shell将包含可打印Unicode数据（以二进制文件，UTF-8或逐字节编码）的任何内容输出为字符串数据。

当修饰符t与~p或~P一起使用时，这些启发式也被io：format / 2，io_lib：format / 2和朋友使用：

```
$ erl +pc latin1
Erlang R16B (erts-5.10.1) [source] [async-threads:0] [hipe] [kernel-poll:false]

Eshell V5.10.1  (abort with ^G)  
1> io:format("~tp~n",[{<<"åäö">>, <<"åäö"/utf8>>, <<208,174,208,189,208,184,208,186,208,190,208,180>>}]).
{<<"åäö">>,<<"åäö"/utf8>>,<<208,174,208,189,208,184,208,186,208,190,208,180>>}
ok
```
```
$ erl +pc unicode
Erlang R16B (erts-5.10.1) [source] [async-threads:0] [hipe] [kernel-poll:false]

Eshell V5.10.1  (abort with ^G)  
1> io:format("~tp~n",[{<<"åäö">>, <<"åäö"/utf8>>, <<208,174,208,189,208,184,208,186,208,190,208,180>>}]).
{<<"åäö">>,<<"åäö"/utf8>>,<<"Юникод"/utf8>>}
ok
```
请注意，这仅影响输出中列表和二进制文件的启发式解释。 例如，无论+ pc设置如何，~ts格式序列始终输出有效的字符列表，因为程序员已明确请求字符串输出。

## 交互式Shell
交互式Erlang shell在启动到终端或在Windows上使用命令werl启动时，可以支持Unicode输入和输出。

在Windows上，正确的操作要求安装并选择合适的字体供Erlang应用程序使用。 如果您的系统上没有合适的字体，请尝试安装免费提供的DejaVu字体，然后在Erlang shell应用程序中选择该字体。

在类Unix操作系统上，终端能够在输入和输出上处理UTF-8（例如，由现代版本的XTerm，KDE Konsole和Gnome终端完成），你的语言环境设置必须是 正确。 例如，LANG环境变量可以设置如下：

```
$ echo $LANG
en_US.UTF-8
```
大多数系统在LANG之前处理变量LC_CTYPE，因此如果设置了它，则必须将其设置为UTF-8：
```
$ echo $LC_CTYPE
en_US.UTF-8
```
LANG或LC_CTYPE设置应与终端的能力一致。 Erlang没有可移植的方式向终端询问其UTF-8容量，我们必须依赖语言和字符类型设置。

要研究Erlang对终端的看法，可以在启动shell时使用调用io：getopts（）：

```
$ LC_CTYPE=en_US.ISO-8859-1 erl
Erlang R16B (erts-5.10.1) [source] [async-threads:0] [hipe] [kernel-poll:false]

Eshell V5.10.1  (abort with ^G)
1> lists:keyfind(encoding, 1, io:getopts()).
{encoding,latin1}
2> q().
ok
$ LC_CTYPE=en_US.UTF-8 erl
Erlang R16B (erts-5.10.1) [source] [async-threads:0] [hipe] [kernel-poll:false]

Eshell V5.10.1  (abort with ^G)
1> lists:keyfind(encoding, 1, io:getopts()).
{encoding,unicode}
2>
```
当（最终？）所有内容都与语言环境设置，字体一致。和终端模拟器，你可能已经找到了一种在你想要的脚本中输入字符的方法。对于测试，最简单的方法是为其他语言添加一些键盘映射，通常在桌面环境中使用一些applet。

在KDE环境中，选择KDE控制中心（个人设置）>区域和辅助功能>键盘布局。

在Windows XP上，选择“控制面板”>“区域和语言选项”，选择“选项卡语言”，然后在名为“文本服务和输入语言”的方框中单击“详细信息...”按钮。

您的环境可能提供了更改键盘布局的类似方法。如果您不熟悉，请确保您可以轻松地在键盘之间来回切换。例如，在Erlang shell中不容易使用西里尔字符集输入命令。

现在，您已设置了一些Unicode输入和输出。最简单的方法是在shell中输入一个字符串：

```
$ erl
Erlang R16B (erts-5.10.1) [source] [async-threads:0] [hipe] [kernel-poll:false]

Eshell V5.10.1  (abort with ^G)
1> lists:keyfind(encoding, 1, io:getopts()).
{encoding,unicode}
2> "Юникод".
"Юникод"
3> io:format("~ts~n", [v(2)]).
Юникод
ok
4>
```
虽然字符串可以作为Unicode字符输入，但语言元素仍然限于ISO Latin-1字符集。 只允许字符常量和字符串超出该范围：

```
$ erl
Erlang R16B (erts-5.10.1) [source] [async-threads:0] [hipe] [kernel-poll:false]

Eshell V5.10.1  (abort with ^G)
1> $ξ.
958
2> Юникод.
* 1: illegal character
2> 
```
## Unicode文件名
大多数现代操作系统都以某种方式支持Unicode文件名。有许多不同的方法可以做到这一点，默认情况下Erlang以不同的方式处理不同的方法：

强制Unicode文件命名
Windows，对于大多数常见用途，MacOS X强制对文件名进行Unicode支持。在文件系统中创建的所有文件都具有可以一致解释的名称。在MacOS X中，所有文件名都以UTF-8编码检索。在Windows中，每个系统调用处理文件名都有一个特殊的Unicode感知变体，可以产生相同的效果。这些系统上没有不是Unicode文件名的文件名。因此，Erlang VM的默认行为是在“Unicode文件名转换模式”下工作。这意味着可以将文件名指定为Unicode列表，该列表会自动转换为基础操作系统和文件系统的正确名称编码。

例如，在其中一个系统上执行文件：list_dir / 1可以返回代码点> 255的Unicode列表，具体取决于文件系统的内容。

透明文件命名
大多数Unix操作系统采用了一种更简单的方法，即不强制执行Unicode文件命名，而是按照惯例。这些系统通常对Unicode文件名使用UTF-8编码，但不强制执行。在这样的系统上，包含代码点从128到255的字符的文件名可以命名为普通的ISO Latin-1或使用UTF-8编码。由于没有强制执行一致性，Erlang VM无法对所有文件名进行一致的转换。

默认情况下，在这样的系统上，如果终端支持UTF-8，则Erlang以utf8文件名模式启动，否则以latin1模式启动。

在latin1模式下，文件名按字节编码。这允许列出系统中所有文件名的表示。但是，一个名为“Östersund.txt”的文件出现在文件：list_dir / 1中，或者作为“Östersund.txt”（如果文件名是由创建文件的程序按字节顺序ISO Latin-1编码），或者更多可能是[195,150,115,116,101,114,115,117,110,100] ]，这是一个包含UTF-8字节的列表（不是你想要的）。如果在此类系统上使用Unicode文件名转换，则file：list_dir / 1等函数将忽略非UTF-8文件名。可以使用函数文件：list_dir_all / 1检索它们，但错误编码的文件名显示为“原始文件名”。

Erlang / OTP R14B01中引入了Unicode文件命名支持。以Unicode文件名转换模式运行的VM可以处理具有任何语言或字符集名称的文件（只要它受底层操作系统和文件系统支持）。 Unicode字符列表用于表示文件名或目录名。如果列出了文件系统内容，则还会将Unicode列表作为返回值。支持在于内核和STDLIB模块，这就是为什么大多数应用程序（不明确要求文件名在ISO Latin-1范围内）都可以从Unicode支持中获益而不需要更改。

在具有强制Unicode文件名的操作系统上，这意味着您可以更轻松地符合其他（非Erlang）应用程序的文件名。您还可以处理至少在Windows上无法访问的文件名（因为其名称无法在ISO Latin-1中表示）。此外，您还避免在MacOS X上创建难以理解的文件名，因为操作系统的vfs层接受所有文件名，因为UTF-8不会重写它们。

对于大多数系统，即使使用透明文件命名，启用Unicode文件名转换也没有问题。很少有系统混合文件名编码。一致的UTF-8命名系统在Unicode文件名模式下运行良好。但是，它仍被认为是Erlang / OTP R14B01的实验，并且仍然不是此类系统的默认设置。

使用switch + fnu打开Unicode文件名转换。在Linux上，VM启动时未明确声明文件名转换模式默认为latin1作为本机文件名编码。在Windows和MacOS X上，默认行为是Unicode文件名转换。因此，file：native_name_encoding / 0默认在这些系统上返回utf8（Windows在文件系统级别上不使用UTF-8，但Erlang程序员可以安全地忽略它）。如前所述，默认行为可以使用选项+ fnu或+ fnl更改为VM，请参阅erl程序。如果以Unicode文件名转换模式启动VM，则file：native_name_encoding / 0将返回atom utf8。 Switch + fnu后面可以跟w，i或e控制如何报告错误编码的文件名。

w表示只要在目录列表中“跳过”错误编码的文件名，就会向error_logger发送警告。 w是默认值。

我的意思是默默地忽略错误编码的文件名。

e表示每当遇到错误编码的文件名（或目录名）时，API函数都会返回错误。

请注意，如果链接指向无效的文件名，则file：read_link / 1始终会返回错误。

在Unicode文件名模式下

### 关于原始文件名的说明
原始文件名与ERTS 5.8.2（Erlang / OTP R14B01）中的Unicode文件名支持一起引入。 “原始文件名”在系统中引入的原因是能够始终如一地表示在同一系统上以不同编码指定的文件名。让VM自动将不是UTF-8的文件名转换为Unicode字符列表似乎很实际，但这会打开重复的文件名和其他不一致的行为。

考虑一个目录，其中包含ISO Latin-1中名为“björn”的文件，而Erlang VM则以Unicode文件名模式运行（因此需要UTF-8文件命名）。 ISO Latin-1名称无效UTF-8，人们可能会认为自动转换，例如file：list_dir / 1是一个好主意。但是如果我们后来尝试打开文件并将名称作为Unicode列表（从ISO Latin-1文件名中神奇地转换）会发生什么？ VM将文件名转换为UTF-8，因为这是预期的编码。实际上这意味着尝试打开名为<<“björn”/ utf8 >>的文件。此文件不存在，即使它存在，也不会与列出的文件相同。我们甚至可以创建两个名为“björn”的文件，一个以UTF-8编码命名，另一个不以。如果file：list_dir / 1会自动将ISO Latin-1文件名转换为列表，我们会得到两个相同的文件名作为结果。为避免这种情况，我们必须区分根据Unicode文件命名约定（即UTF-8）正确编码的文件名和在编码下无效的文件名。通过公共函数文件：list_dir / 1，在Unicode文件名转换模式中忽略错误编码的文件名，但是通过函数文件：list_dir_all / 1，具有无效编码的文件名作为“原始”文件名返回，即作为二进制文件。

文件模块接受原始文件名作为输入。 open_port（{spawn_executable，...} ...）也接受它们。如前所述，open_port（{spawn_executable，...} ...）选项列表中指定的参数与文件名进行相同的转换，这意味着可执行文件也提供了UTF-8中的参数。通过将参数作为二进制文件，可以避免这种转换与文件名的处理方式一致。

在不是默认值的系统上强制使用Unicode文件名转换模式在Erlang / OTP R14B01中被认为是实验性的。这是因为初始实现没有忽略错误编码的文件名，因此原始文件名可能会在整个系统中意外传播。从Erlang / OTP R16B开始，错误编码的文件名仅由特殊函数（例如file：list_dir_all / 1）检索。由于现有代码的影响要小得多，现在支持它。 Unicode文件名转换在未来版本中应该是默认的。

即使您在没有由VM自动完成的Unicode文件命名转换的情况下运行，您也可以使用编码为UTF-8的原始文件名来访问和创建名称为UTF-8编码的文件。无论Erlang VM启动的模式如何，强制执行UTF-8编码在某些情况下都是个好主意，因为使用UTF-8文件名的惯例正在传播。


### 关于MacOS X的注意事项
MacOS X的vfs层以激进的方式强制执行UTF-8文件名。较旧版本通过拒绝创建符合非UTF-8的文件名来实现此目的，而较新版本使用序列“％HH”替换违规字节，其中HH是十六进制表示法中的原始字符。由于默认情况下在MacOS X上启用了Unicode转换，因此唯一可以解决此问题的方法是使用标志+ fnl启动VM或以字节顺序（latin1）编码使用原始文件名。如果使用包含127到255字符的字节编码的原始文件名来创建文件，则无法使用与创建文件相同的名称打开该文件。除了使文件名保持正确的编码外，此行为没有任何补救措施。

MacOS X重新组织文件名，以便重音符表示等使用“组合字符”。例如，字符ö表示为代码点[111,776]，其中111是字符o，776是特殊重音字符“Combining Diaeresis”。否则这种规范化Unicode的方式很少使用。 Erlang在检索时以相反的方式规范化这些文件名，因此使用组合重音的文件名不会传递给Erlang应用程序。在Erlang中，文件名“björn”被检索为[98,106,246,114,110]，而不是[98,106,117,776,114,110]，尽管文件系统可以有不同的思维方式。在访问文件时重新组合重音符号的规范化，因此Erlang程序员通常可以忽略这一点。

### 环境和参数中的Unicode
环境变量及其解释的处理方式与文件名相同。 如果启用了Unicode文件名，则环境变量以及Erlang VM的参数应为Unicode格式。

如果启用了Unicode文件名，则对os：getenv / 0,1，os：putenv / 2和os：unsetenv / 1的调用将处理Unicode字符串。 在类Unix平台上，内置函数将UTF-8中的环境变量转换为Unicode字符串或从Unicode字符串转换，可能代码点> 255.在Windows上，使用环境系统API的Unicode版本，代码点> 255 被允许。

在类Unix操作系统上，如果启用了Unicode文件名，则参数应为UTF-8而不进行转换。

## Unicode-Aware模块
Erlang / OTP中的大多数模块都是Unicode不知道的，因为它们没有Unicode概念，也不应该有。通常，它们处理非文本或面向字节的数据（例如gen_tcp）。

处理文本数据的模块（例如io_lib和string有时会受到转换或扩展，以便能够处理Unicode字符。

幸运的是，大多数文本数据都存储在列表中，并且范围检查很稀疏，因此像字符串这样的模块适用于Unicode字符串，几乎不需要转换或扩展。

但是，某些模块已更改为明确支持Unicode。这些模块包括：

### unicode
unicode模块显然支持Unicode。它包含用于在不同Unicode格式之间进行转换的函数以及用于标识字节顺序标记没有这个模块，处理Unicode数据的程序很少。

### IO
io模块已与实际的I / O协议一起扩展，以处理Unicode数据。这意味着许多函数需要二进制文件为UTF-8，并且有一些修饰符可以格式化控制序列以允许输出Unicode字符串。
### file group user
整个系统中的I / O服务器可以处理Unicode数据，并具有在输出或输入到设备或从设备输入时转换数据的选项。如前所示，shell模块支持Unicode终端，文件模块允许在磁盘上进行各种Unicode格式的转换。

但是，使用Unicode数据读取和写入文件最好不要使用文件模块，因为它的接口是面向字节的。使用io模块最好读取或写入使用Unicode编码（如UTF-8）打开的文件。

### re
re模块允许将Unicode字符串作为特殊选项进行匹配。由于库以二进制文件中的匹配为中心，因此Unicode支持以UTF-8为中心。

### wx
图形库wx广泛支持Unicode文本。

字符串模块适用于Unicode字符串和ISO Latin-1字符串，但依赖于语言的函数字符串除外：uppercase / 1和string：lowercase / 1。对于当前形式的Unicode字符，这两个函数永远不能正常工作，因为在案例之间转换文本时需要考虑语言和区域设置问题。在国际环境中转换案例是OTP尚未解决的一个大问题。

## 文件中的Unicode数据
虽然Erlang可以处理多种形式的Unicode数据，但并不意味着任何文件的内容都可以是Unicode文本。外部实体（如端口和I / O服务器）通常不支持Unicode。

端口始终是面向字节的，因此在发送您不确定按字节编码到端口的数据之前，请确保以适当的Unicode编码对其进行编码。有时这意味着只有部分数据必须编码为例如UTF-8。某些部分可以是二进制数据（如长度指示符）或其他不能进行字符编码的部分，因此不存在自动转换。

I / O服务器的行为略有不同。无论编码选项如何，连接到终端（或stdout）的I / O服务器通常都可以处理Unicode数据。当人们期望现代环境但是在写入古老的终端或管道时不想崩溃时，这很方便。

文件可以具有编码选项，使其通常可由io模块使用（例如{encoding，utf8}），但默认情况下打开为面向字节的文件。文件模块是面向字节的，因此只能使用该模块写入ISO Latin-1字符。如果要将Unicode数据输出到除latin1之外的其他编码的文件（字节编码），请使用io模块。有些令人困惑的是，使用file：open（Name，[read，{encoding，utf8}]）打开的文件无法使用file：read（File，N）正确读取，但使用io模块检索来自它的Unicode数据。原因是文件：read和file：write（和friends）纯粹是面向字节的，应该是，因为这是逐字节访问文本文件以外的文件的方法。与端口一样，您可以通过“手动”将数据转换为选择的编码（使用unicode模块或位语法）将编码数据写入文件，然后在逐字节（latin1）编码文件上输出。


*** 推荐 ***
将file模块用于为逐字节访问而打开的文件（{encoding，latin1})

使用任何其他编码访问文件时使用io模块（例如{encoding，uf8}）

从文件读取Erlang语法的函数识别编码：注释，因此可以在输入时处理Unicode数据。 将Erlang术语写入文件时，建议您在适用时插入此类注释：
```
$ erl +fna +pc unicode
Erlang R16B (erts-5.10.1) [source]  [async-threads:0] [hipe] [kernel-poll:false]

Eshell V5.10.1  (abort with ^G)
1> file:write_file("test.term",<<"%% coding: utf-8\n[{\"Юникод\",4711}].\n"/utf8>>).
ok
2> file:consult("test.term").   
{ok,[[{"Юникод",4711}]]}
```

##总结
Unicode支持由命令行开关，一些标准环境变量和您正在使用的OTP版本控制。 大多数选项主要影响Unicode数据的显示方式，而不影响标准库中API的功能。 这意味着Erlang程序通常不需要关注这些选项，它们更适合于开发环境。 可以编写Erlang程序，使其无论系统类型或有效的Unicode选项如何都能正常工作。

以下是影响Unicode的设置的摘要：

### LANG和LC_CTYPE环境变量
### +pc {unicode|latin1}参数
### +fn{l|u|a} [{w|i|e}] 参数
### epp:default_encoding/0

## Recipes
从Unicode开始时，经常会遇到一些常见问题。 本节介绍一些处理Unicode数据的方法。

字节顺序标记
识别文本文件中的编码的常用方法是首先在文件中放置字节顺序标记（BOM）。 BOM是以与剩余文件相同的方式编码的代码点16＃FEFF。 如果要读取这样的文件，则前几个字节（取决于编码）不是文本的一部分。 此代码概述了如何打开被认为具有BOM的文件，并设置文件编码和位置以进行进一步的顺序读取（最好使用io模块）。

请注意，代码中省略了错误处理：

```
open_bom_file_for_reading(File) ->
    {ok,F} = file:open(File,[read,binary]),
    {ok,Bin} = file:read(F,4),
    {Type,Bytes} = unicode:bom_to_encoding(Bin),
    file:position(F,Bytes),
    io:setopts(F,[{encoding,Type}]),
    {ok,F}.
```
函数unicode：bom_to_encoding / 1标识来自至少四个字节的二进制的编码。 它返回适合于设置文件编码的术语，BOM的字节长度，以便可以相应地设置文件位置。 请注意，函数文件：position / 2始终适用于字节偏移，因此需要BOM的字节长度。


要打开文件进行编写并首先放置BOM更简单：

```
open_bom_file_for_writing(File,Encoding) ->
    {ok,F} = file:open(File,[write,binary]),
    ok = file:write(File,unicode:encoding_to_bom(Encoding)),
    io:setopts(F,[{encoding,Encoding}]),
    {ok,F}.
```
在这两种情况下，文件最好使用io模块进行处理，因为该模块中的函数可以处理超出ISO Latin-1范围的代码点。

### 格式化I / O.
在读取和写入支持Unicode的实体（如为Unicode转换打开的文件）时，您可能希望使用io模块或io_lib模块中的函数设置文本字符串的格式。 出于向后兼容性原因，这些函数不接受任何列表作为字符串，但在使用Unicode文本时需要特殊的转换修饰符。 修饰符是t。 当应用于控制格式化字符串中的字符时，它接受所有Unicode代码点并期望二进制文件为UTF-8：
```
1> io:format("~ts~n",[<<"åäö"/utf8>>]).
åäö
ok
2> io:format("~s~n",[<<"åäö"/utf8>>]).
Ã¥Ã¤Ã¶
ok

```

显然，第二个io：format / 2给出了不需要的输出，因为UTF-8二进制文件不在latin1中。为了向后兼容，非前缀控制字符s期望二进制文件中的按字节编码的ISO Latin-1字符和仅包含<256的代码点的列表。

只要数据始终是列表，修饰符t就可以用于任何字符串，但是当涉及二进制数据时，必须注意正确选择格式化字符。字节编码的二进制文件也被解释为字符串，即使使用~ts也会打印，但它可能会被误认为是有效的UTF-8字符串。因此，如果二进制文件包含按字节编码的字符而不是UTF-8，则避免使用~ts控件。

函数io_lib：format / 2的行为类似。它被定义为返回深度字符列表，输出可以很容易地转换为二进制数据，以便通过简单的erlang：list_to_binary / 1在任何设备上输出。使用转换修饰符时，列表可以包含不能存储在一个字节中的字符。对erlang的调用：list_to_binary / 1然后失败。但是，如果要与之通信的I / O服务器支持Unicode，则仍可以直接使用返回的列表：

```
$ erl +pc unicode
Erlang R16B (erts-5.10.1) [source] [async-threads:0] [hipe] [kernel-poll:false]

Eshell V5.10.1 (abort with ^G)
1> io_lib:format("~ts~n", ["Γιούνικοντ"]).
["Γιούνικοντ","\n"]
2> io:put_chars(io_lib:format("~ts~n", ["Γιούνικοντ"])).
Γιούνικοντ
ok
```

Unicode字符串作为Unicode列表返回，因为Erlang shell使用Unicode编码（并且所有Unicode字符都被认为是可打印的），因此可以识别它。 Unicode列表是函数io：put_chars / 2的有效输入，因此可以在任何支持Unicode的设备上输出数据。 如果设备是终端，如果编码为latin1，则以格式\ x {H ...}输出字符。 否则以UTF-8（对于非交互式终端：“oldshell”或“noshell”）或任何适合正确显示角色的东西（对于交互式终端：常规shell）。

因此，您始终可以将Unicode数据发送到standard_io设备。 但是，如果将编码设置为除latin1之外的其他内容，则文件仅接受超出ISO Latin-1的Unicode代码点。

### UTF-8的启发式识别
虽然强烈建议在处理之前知道二进制数据中的字符编码，但这并不总是可行的。 在典型的Linux系统中，存在UTF-8和ISO Latin-1文本文件的混合，并且文件中很少有任何BOM来识别它们。

UTF-8的设计使得在解码为UTF-8时，具有超出7位ASCII范围的数字的ISO Latin-1字符很少被认为是有效的。 因此，通常可以使用启发式方法来确定文件是否为UTF-8或者是否使用ISO Latin-1（每个字符一个字节）进行编码。 unicode模块可用于确定数据是否可以解释为UTF-8：

```
heuristic_encoding_bin(Bin) when is_binary(Bin) ->
    case unicode:characters_to_binary(Bin,utf8,utf8) of
	Bin ->
	    utf8;
	_ ->
	    latin1
    end.
```

如果您没有文件内容的完整二进制文件，则可以通过文件进行分块并逐个检查。 函数unicode：characters_to_binary / 1,2,3的返回元组{incomplete，Decoded，Rest}派上用场。 从文件读取的一个数据块的不完整的休息被预先添加到下一个块，因此我们在读取UTF-8编码的字节块时避免了字符边界的问题：

```
heuristic_encoding_file(FileName) ->
    {ok,F} = file:open(FileName,[read,binary]),
    loop_through_file(F,<<>>,file:read(F,1024)).

loop_through_file(_,<<>>,eof) ->
    utf8;
loop_through_file(_,_,eof) ->
    latin1;
loop_through_file(F,Acc,{ok,Bin}) when is_binary(Bin) ->
    case unicode:characters_to_binary([Acc,Bin]) of
	{error,_,_} ->
	    latin1;
	{incomplete,_,Rest} ->
	    loop_through_file(F,Rest,file:read(F,1024));
	Res when is_binary(Res) ->
	    loop_through_file(F,<<>>,file:read(F,1024))
    end.
```
另一个选择是尝试以UTF-8编码读取整个文件，看它是否失败。 这里我们需要使用函数io：get_chars / 3来读取文件，因为我们必须读取代码点> 255的字符：

```
heuristic_encoding_file2(FileName) ->
    {ok,F} = file:open(FileName,[read,binary,{encoding,utf8}]),
    loop_through_file2(F,io:get_chars(F,'',1024)).

loop_through_file2(_,eof) ->
    utf8;
loop_through_file2(_,{error,_Err}) ->
    latin1;
loop_through_file2(F,Bin) when is_binary(Bin) ->
    loop_through_file2(F,io:get_chars(F,'',1024)).
```

### UTF-8字节列表
由于各种原因，您有时可以获得UTF-8字节列表。 这不是Unicode字符的常规字符串，因为每个列表元素不包含一个字符。 相反，您将获得二进制文件中的“原始”UTF-8编码。 通过首先将每个字节的字节转换为二进制文件，然后将UTF-8编码字符的二进制文件转换回Unicode字符串，可以轻松将其转换为正确的Unicode字符串：

```
utf8_list_to_string(StrangeList) ->
  unicode:characters_to_list(list_to_binary(StrangeList)).
```
### 双UTF-8编码
使用二进制文件时，您可以获得可怕的“双UTF-8编码”，其中奇怪的字符在您的二进制文件或文件中编码。 换句话说，您可以获得第二次编码为UTF-8的UTF-8编码二进制文件。 常见的情况是您逐字节读取文件，但内容已经是UTF-8。 如果您然后将字节转换为UTF-8，例如使用unicode模块，或者通过写入使用选项{encoding，utf8}打开的文件，您将输入文件中的每个字节编码为UTF-8，而不是 原始文本的每个字符（一个字符可以用很多字节编码）。 除了确定哪种数据以哪种格式编码，并且永远不会将UTF-8数据（可能逐字节地从文件中读取）转换为UTF-8之外，没有真正的补救措施。

到目前为止，最常见的情况是，当您获得UTF-8列表而不是正确的Unicode字符串，然后将它们转换为二进制或文件中的UTF-8：

```
wrong_thing_to_do() ->
  {ok,Bin} = file:read_file("an_utf8_encoded_file.txt"),
  MyList = binary_to_list(Bin), %% Wrong! It is an utf8 binary!
  {ok,C} = file:open("catastrophe.txt",[write,{encoding,utf8}]), 
  io:put_chars(C,MyList), %% Expects a Unicode string, but get UTF-8
                          %% bytes in a list!
  file:close(C). %% The file catastrophe.txt contains more or less unreadable
                 %% garbage!
```
在将二进制文件转换为字符串之前，请确保您知道它包含的内容 如果不存在其他选项，请尝试启发式：

```
if_you_can_not_know() ->
  {ok,Bin} = file:read_file("maybe_utf8_encoded_file.txt"),
  MyList = case unicode:characters_to_list(Bin) of
    L when is_list(L) ->
      L;
    _ ->
      binary_to_list(Bin) %% The file was bytewise encoded
  end,
  %% Now we know that the list is a Unicode string, not a list of UTF-8 bytes
  {ok,G} = file:open("greatness.txt",[write,{encoding,utf8}]), 
  io:put_chars(G,MyList), %% Expects a Unicode string, which is what it gets!
  file:close(G). %% The file contains valid UTF-8 encoded Unicode characters!
```