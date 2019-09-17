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
