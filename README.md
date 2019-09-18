AUTOTOOL
===

## 一、GNU构建系统和autotools 

GNU开源软件的安装三部曲configure->make->make install，这就是GNU构建系统。  
这个过程中， 首先需要有一个 configure 脚本，用来生成 Makefile 文件，然后才能使用Makefile文件进行代码的编译。  
那么为什么要使用configure文件去生成Makefile文件？  
最早的时候，程序员完成源代码开发以后，代码包里面一般会附带相应的 Makefile 文件。然后就可以 make && make install 来编译工程。当时并不需要这个运行 configure 的步骤。  
但是如果一个程序被用的多了以后 ，可能需要被安装到不同的平台上使用。这个时候，在不同的平台上编译时，一方面可能需要对 Makefile 文件进行调整 (最常见的例子就是：不同的平台使用不同的编译器)。另外一方面，可能需要用一个替代函数来替换当前平台所不支持的函数 (例如：有的平台上不支持strdup这个调用)，需要在程序里面给每个平台写#define。  
为了避免手工做这些调整，人们开始写 configure 脚本来自动做这些调整工作 (现在在 make 之前先运行 configure 是 GNU Code Style 标准所规定的)。  
configure 脚本一般会先检查目前的环境，然后根据代码里的各级Makefile.in和config.h.in文件生成一个config.h 文件 (里面带了各种各样的#define) ，同时会生成一个 针对当前平台的 Makefile 文件，之后，make 命令就会使用到这个 Makefile文件。  

![](https://github.com/KpSimple/autotool/blob/master/picture/makefile.png)

另外，GNU的 build 系统还有一些的功能，用户在使用 configure 这个脚本的时候，可能会使用到这些功能 (最常见的就是用 --prefix 来指定安装路径， --build指定编译平台，--host用来指定目标平台，用configure --help来查看说明等等)。  
configure在执行过程中，除了生成Makefile外，还会生成的文件包括但不限于：
* config.log 日志文件
* config.cache 缓存，以提高下一次configure的速度，需通过-C来指定才会生成
* config.status 实际调用编译工具构建软件的shell脚本

![](https://github.com/KpSimple/autotool/blob/master/picture/makefile2.png)

但是，过了一段时间以后，人们发现靠人手工写这个 configure 脚本工作量太巨大了，而且以后维护这个 configure也比较麻烦：一旦发现项目在某个平台的移植性有问题，就需要更新这个 configure 脚本，比较繁琐。于是，人们就开发了 Autotools 这个工具集来自动生成 configure 和Makefile脚本。  
GNU Autotools 一般指的是3个 GNU 工具包：Autoconf，Automake 和 Libtool。  

## 二、使用流程

安装Autoconf和Automake工具包：yum install autoconf，yum install automake，包括以下工具:  
* aclocal
* autoscan
* autoheader
* automake 
* autoconf

![](https://github.com/KpSimple/autotool/blob/master/picture/autotool.png)

简单总结一下这张图，分为八步：  
1．运行autoscan命令，将configure.scan 文件重命名为configure.ac  
2．运行aclocal命令，生成aclocal.m4  
3．运行autoconf命令，生成configure  
4．运行autoheader,生成config.h.in  
5．创建Makefile.am，修改  
6．运行automake -a命令  
7．运行./confiugre脚本  

### 1、autoscan
这个命令主要用于扫描工作目录，并且生成configure.scan文件。并且configure.scan需要重命令成configure.ac，然后编辑这个配置  
下面是configure.ac标签说明  

![](https://github.com/KpSimple/autotool/blob/master/picture/autoscan.png)

一般我们需添加  
AM_INIT_AUTOMAKE([hello],[1.0]) 指定可执行名称+版本号  
AC_CONFIG_FILES([Makefile, src/Makefile]) 用于生成相应的Makefile文件  

### 2、aclocal
扫描 configure.ac 文件生成 aclocal.m4文件, 该文件主要处理本地的宏定义，它根据已经安装的宏、用户定义宏和 acinclude.m4 文件中的宏将 configure.ac 文件需要的宏集中定义到文件 aclocal.m4 中(aclocal是一个perl 脚本程序).  

### 3、autoconf
执行autoconf命令。这个命令将 configure.ac 文件中的宏展开，生成 configure 脚本。这个过程要用到aclocal.m4中定义的宏  

### 4、autoheader
执行autoheader命令。该命令生成 config.h.in 文件。该命令通常会从 "acconfig.h” 文件中复制用户附加的符号定义。

### 5、Makefile.am
创建Makefile.am文件。Automake工具会根据 configure.in 中的参量把 Makefile.am 转换成 Makefile.in 文件。最终通过Makefile.in生成Makefile文件，所以Makefile.am这个文件非常重要，定义了一些生成Makefile的规则。  
Makefile.am是一种比Makefile更高层次的规则。只需指定要生成什么目标，它由什么源文件生成，要安装到什么目录等构成。  
表一列出了可执行文件、静态库、头文件和数据文件，四种书写Makefile.am文件个一般格式。  

![](https://github.com/KpSimple/autotool/blob/master/picture/Makefile.am.png)

对于可执行文件和静态库类型，如果只想编译，不想安装到系统中，可以用noinst_PROGRAMS代替bin_PROGRAMS，noinst_LIBRARIES代替lib_LIBRARIES。  
Makefile.am还提供了一些全局变量供所有的目标体使用：

![](https://github.com/KpSimple/autotool/blob/master/picture/Makefile.am2.png)

在Makefile.am中尽量使用相对路径，系统预定义了两个基本路径： 

![](https://github.com/KpSimple/autotool/blob/master/picture/Makefile.am3.png)

### 6、automake: automake将Makefile.am中定义的结构建立Makefile.in
Automake 所需要的文件： 

![](https://github.com/KpSimple/autotool/blob/master/picture/automake.png)

用automake –a可以自动生成以下文件  

![](https://github.com/KpSimple/autotool/blob/master/picture/automake2.png)

要手动生成以下文件  
touch NEWS README AUTHORS ChangeLog  

### 7、./configure 
生成Makefile和config.h

### 8、Make
生成目标文件


