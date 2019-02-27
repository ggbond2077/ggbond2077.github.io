---
layout: post
title: 编译安装tesseeract
categories: [技术] 
description: 编译安装tesseeract
keywords: linux
---


Tesseract是一个开源的[光学字符识别](https://en.wikipedia.org/wiki/Optical_character_recognition)（OCR）引擎。  做python的作业，在验证码识别这一块需要用到这个引擎。   有root权限可以使用yum 或 apt-get直接安装编译好的二进制文件，自动解决一堆依赖。可参考：[https://github.com/tesseract-ocr/tesseract/wiki](https://github.com/tesseract-ocr/tesseract/wiki) 同样有root权限可以按照：[https://github.com/tesseract-ocr/tesseract/wiki/Compiling ](https://github.com/tesseract-ocr/tesseract/wiki/Compiling)  提供的方法，先sudo yum  install好依赖库。再编译安装tesseract.     现在是在老师服务器上没有root权限.  年少无知又非要把环境配好。 那就编译安装在自己的目录下吧。 依赖列表：

*   A compiler for C and C++: GCC or Clang  编译器一般有
*   GNU Autotools: autoconf, automake, libtool  编译前的自动构建工具，一般也有
*   autoconf-archive  ---这个是个坑
*   pkg-config   不知道
*   [Leptonica](http://www.leptonica.org/)
*   libpng, libjpeg, libtiff  zlib  一些图片处理库


在用户根目录创建一个local文件夹，里面分别创建好bin,lib,include,share文件夹.  作为用户自己的库和软件安装目录,编辑.bashrc，添加环境变量设置 PATH=$PATH:$HOME/local/bin   以Lepotonica为例：  **_Leptonica _is a pedagogically-oriented open source site containing software that is broadly useful for image processing and image analysis applications.** 找到源码包地址，下载解压，编译，安装。

```bash
    wget http://www.leptonica.org/source/leptonica-1.74.4.tar.gz
    tar -zxvf leptonica-1.74.4.tar.gz
    cd leptonica-1.74.4
    ./configure --prefix='$HOME/local' CFLAGS="-L$HOME/local/lib -I$HOME/local/include"  
    #prefix参数指定安装路径，CFLAGS可以指定头文件和库查找路径
    make; make install
```

其它的都一样.缺什么就装什么吧。   最后安装tesseract，这是官方提供的方法： To install it in $HOME/local using Leptonica libraries also installed in $HOME/local:

```bash
    ./autogen.sh
    LIBLEPT_HEADERSDIR=$HOME/local/include ./configure \
      --prefix=$HOME/local/ --with-extra-libraries=$HOME/local/lib
    make install
```

如果没有问题，恭喜成功。。。 下面是我遇到的问题： 1. 在安装tesseract 执行./configure时：

```bash
    ./configure: line 4193: syntax error near unexpected token `-mavx,'
    ./configure: line 4193: `AX_CHECK_COMPILE_FLAG(-mavx, avx=true, avx=false)'
```
   

  在#647 中能找到：[https://github.com/tesseract-ocr/tesseract/issues/647](https://github.com/tesseract-ocr/tesseract/issues/647) 回答：“You should install autoconf-archive .” 看来时autoconf-archive的原因。  已经装了？？？ 这货在/local/share/aclocal里面。 一堆m4后缀的文件：m4 是一个宏处理器.将输入拷贝到输出,同时将宏展开. 宏可以是内嵌的也可以是用户定义的. 除了可以展开宏,m4还有一些内建的函数,用来引用文件,执行Unix命令,整数运算,文本操作,循环等. m4既可以作为编译器的前端也可以单独作为一个宏处理器. 现在的问题是怎么引用这些文件? 

  ./configure --help 没有。 许久找到了这篇讲自动编译的文章：[https://jin-yang.github.io/post/linux-package.html](https://jin-yang.github.io/post/linux-package.html) 有这样一句话："aclocal，将在 configure.ac 同一目录下生成 aclocal.m4，在扫描 configure.ac 的过程中，将第三方扩展和开发者自己编写的宏定义复制进去；这样，autoconf 在遇到不认识的宏时，就会从 aclocal.m4 中查找".     的确编译是生成了aclocal.m4文件。 看来的想办法把/local/share/aclocal里的m4引用进去。 
  
   打开autogen.sh : 81行有echo "Running aclocal" aclocal -I config. 应该是这儿引用的宏。操作我看不懂  .所以怎么指定加入自己的宏？ aclocal --help 有:  -I DIR add directory to search list for .m4 files    --install copy third-party files to the first -I directory   --system-acdir=DIR    directory holding third-party system-wide files 感觉是这几个参数。。。试一试 ..    aclocal -I m4 --install  --system-acdir=$HOME/local/share/aclocal   再./autogen.sh  ./configure  奇迹般的跑起来了。 
	
  
  因此 解决办法就是： 在 autogen.sh : 81行后面添加 aclocal -I m4 --install  --system-acdir=$HOME/local/share/aclocal    保存退出。再安装即可。     2. 执行的时候 报的错：
  
```
    Tesseract Open Source OCR Engine v3.05.00dev with Leptonica
    Error in pixReadMemJpeg: function not present
    Error in pixReadMem: jpeg: no pix returned
    Error during processing.
```


没有安装 libgif   libjpeg  libpng  libtiff  zlib  这几个库。 下载编译安装之后。再重新编译安装一次Lepotonica 就行.     就这些了。。。
