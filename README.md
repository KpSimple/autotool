# autotool

一、GNU构建系统和autotools 

GNU开源软件的安装三部曲configure->make->make install，这就是GNU构建系统。  
这个过程中， 首先需要有一个 configure 脚本，用来生成 Makefile 文件，然后才能使用Makefile文件进行代码的编译。  
那么为什么要使用configure文件去生成Makefile文件？  
最早的时候，程序员完成源代码开发以后，代码包里面一般会附带相应的 Makefile 文件。然后就可以 make && make install 来编译工程。当时并不需要这个运行 configure 的步骤。
但是如果一个程序被用的多了以后 ，可能需要被安装到不同的平台上使用。这个时候，在不同的平台上编译时，一方面可能需要对 Makefile 文件进行调整 (最常见的例子就是：不同的平台使用不同的编译器)。另外一方面，可能需要用一个替代函数来替换当前平台所不支持的函数 (例如：有的平台上不支持strdup这个调用)，需要在程序里面给每个平台写#define。
为了避免手工做这些调整，人们开始写 configure 脚本来自动做这些调整工作 (现在在 make 之前先运行 configure 是 GNU Code Style 标准所规定的)。  
configure 脚本一般会先检查目前的环境，然后根据代码里的各级Makefile.in和config.h.in文件生成一个config.h 文件 (里面带了各种各样的#define) ，同时会生成一个 针对当前平台的 Makefile 文件，之后，make 命令就会使用到这个 Makefile文件。  

![](https://github.com/KpSimple/autotool/blob/master/picture/makefile.png)
