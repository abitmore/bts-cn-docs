# 使用 Visual Studio 2015 编译 BitShares-Core
> 作者: abitmore

## 0.环境准备

- 64 位 Windows 系统，比如 Windows Server 2012 R2 Standard edition


## 1. 安装 Visual Studio 2015 with Update 1

注：
- 用 VS 2015 Update 3 编译会有问题； https://github.com/bitshares/bitshares-core/issues/389
- 本人没试过其他版本


### 1.1 下载 VS 2015 Update 1

http://download.microsoft.com/download/5/7/A/57A99666-126E-42FA-8E70-862EDBADD215/vs2015.1.com_enu.iso

Visual Studio Community 2015 with Update 1 (x86 and x64) – DVD (English)

SHA1：FB5AE6B57BDC495AFB29646AFCA088756363A263

### 1.2 使用虚拟光驱软件加载上述 iso 文件

如果没有虚拟光驱软件，可以用 WinCDEmu ，下载地址 http://wincdemu.sysprogs.org/download/

### 1.3 安装 VS 2015

安装时选 C++


## 2. 编译 OpenSSL

### 2.0 参考资料

http://p-nand-q.com/programming/windows/building_openssl_with_visual_studio_2013.html

http://developer.covenanteyes.com/building-openssl-for-visual-studio/

https://www.npcglib.org/~stathis/blog/precompiled-openssl/

http://blog.csdn.net/fireroll/article/details/51242518

注：虽然上面一些链接里提供已经编译好的 OpenSSL 库供下载，但本人在使用过程中碰到过问题，最后还是自己从源码编译解决。


### 2.1 下载安装 active perl

https://www.activestate.com/activeperl/downloads


### 2.2 下载安装 NASM

http://www.nasm.us/

http://www.nasm.us/pub/nasm/releasebuilds/2.13.01/win64/nasm-2.13.01-installer-x64.exe

如果不装 NASM ，编译 OpenSSL 时会报 ml64 找不到的错（虽然也有其他方式解决，这里不详述）。


### 2.3 下载 OpenSSL 源码

注：可以使用 OpenSSL 1.0.1 或者 1.0.2 ，本文以 1.0.1u 为例。不能用 OpenSSL 1.1.0 。

https://www.openssl.org/source/

https://www.openssl.org/source/openssl-1.0.2l.tar.gz

https://www.openssl.org/source/old/1.0.1/openssl-1.0.1u.tar.gz

下载后解压，比如源代码目录为 C:\bts\openssl-1.0.1u （不要有多层嵌套目录）


### 2.4 编译

运行 VS2015 x64 Native Tools Command Prompt

注：它实际是个快捷方式，在这里：
```
C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Visual Studio 2015\Visual Studio Tools\Windows Desktop Command Prompts
```
内容是
```
%comspec% /k ""C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\vcvarsall.bat"" amd64
```
然后在打开的命令行界面里执行下面的命令（具体目录请根据实际情况修改）：
```
set PATH=C:\Program Files\NASM;%PATH%

c:
cd C:\bts\openssl-1.0.1u
perl Configure VC-WIN64A --prefix=C:\bts\openssl-1.0.1u-x64-release-static
ms\do_win64a
nmake -f ms\nt.mak
nmake -f ms\nt.mak install
```
碰到的问题及解决方式：
- 如果因为 PATH 原因，找不到 NASM 的问题，添加 PATH 后，需要执行 nmake clean 命令清除之前生成的临时文件，或者删除源码，重新解压，
  然后从头开始编译步骤，否则会碰到 asm 临时文件为空的问题
- https://stackoverflow.com/questions/31595869/how-to-resolve-the-module-machine-type-x86-conflicts-with-target-machine-type

编译完成后，会生成一个 C:\bts\openssl-1.0.1u-x64-release-static 目录，里面是编译好的库文件


## 3. 编译 Boost

http://www.boost.org/

https://sourceforge.net/projects/boost/files/boost/1.57.0/

https://sourceforge.net/projects/boost/files/boost/1.57.0/boost_1_57_0.zip/download

注：
- 只支持 1.57 - 1.60 ，这里使用 1.57.0 。（ 1.60 碰到过命令行参数解析问题）
- 网上也有编译好的库供下载，但本人下载使用中也碰到问题，后来还是通过自己编译解决

源码解压到 C:\bts\boost_1_57_0 （不要有多层嵌套目录）

运行 VS2015 x64 Native Tools Command Prompt 然后在里面执行：
```
c:
cd C:\bts\boost_1_57_0
bootstrap
b2 architecture=x86 address-model=64 --build-type=complete --toolset=msvc-14.0 --threading=multi --variant=release release stage
```


## 4. 下载 CMake

https://cmake.org/download/

https://cmake.org/files/v3.9/cmake-3.9.4-win64-x64.zip

下载 zip 文件后解压到 C:\bts\cmake-3.9.4-win64-x64 （不要有多层嵌套目录）


## 5. 下载 Doxygen （可选）

Doxygen 不是必须，其用处是生成文档及在线帮助，比如在 cli_wallet 里面可以用 gethelp 命令看命令的参数说明。

http://www.stack.nl/~dimitri/doxygen/download.html

http://ftp.stack.nl/pub/users/dimitri/doxygen-1.8.13.windows.x64.bin.zip

下载后解压到 C:\bts\doxygen-1.8.13.windows.x64.bin （不要有多层嵌套目录）


## 6. 下载安装 git

https://git-scm.com/download/win


## 7. 下载 BitShares-Core 源码

从开始菜单找到并运行 Git Bash ，在打开的命令行界面里，执行如下命令：
```
cd /c/bts
git clone https://github.com/bitshares/bitshares-core
cd bitshares-core
git checkout 2.0.170710
git submodule update --init --recursive
```
注：
- 具体目录请根据实际情况修改
- 2.0.170710 是本文编写时 bitshares-core 的最新稳定版本，如果需要编译其他版本，请相应修改


## 8. 运行 CMake

### 8.1 创建一个文件 C:\bts\setenv_x64.bat 内容如下
```
@echo off
set GRA_ROOT=C:\bts
set OPENSSL_ROOT=%GRA_ROOT%\openssl-1.0.1u-x64-release-static
set OPENSSL_ROOT_DIR=%OPENSSL_ROOT%
set OPENSSL_INCLUDE_DIR=%OPENSSL_ROOT%\include
set BOOST_ROOT=%GRA_ROOT%\boost_1_57_0
set CMAKE_ROOT=%GRA_ROOT%\cmake-3.9.4-win64-x64
set DOXYGEN_ROOT=%GRA_ROOT%\doxygen-1.8.13.windows.x64.bin
set PATH=%BOOST_ROOT%\lib;%CMAKE_ROOT%\BIN;%DOXYGEN_ROOT%;%PATH%
```
### 8.2 运行 VS2015 x64 Native Tools Command Prompt 然后在里面执行：
```
c:
cd C:\bts
setenv_x64.bat
cmake-gui
```
会弹出 cmake 的界面。

- Where is the source code: 输入或者选择 bitshares-core 源代码目录 C:/bts/bitshares-core
- Where to build the binaries: 输入或者选择编译输出目录，比如 C:/bts/bin

点击 Configure 按钮。

如果提示编译输出目录不存在，点 Yes 创建目录

在弹出框中，
第一个下拉框 Specify the generator for this project 选 Visual Studio 14 2015 Win64

第二个输入框 Optional toolset to use (argument to -T) 留空
下面的单选框，选 Use default native compilers

点 Finish ，然后等一会， Generate 按钮会亮起

点 Generate ，然后等一会， Open Project 按钮会亮起

点 Open Project ，会打开 Visual Studio


## 9. 编译 bitshares-core

Visual Studio 中，
上部工具栏里编译类型默认是 Debug ，修改为 Release
另一个选项模式默认是 x64 ，不需要修改

界面右边 Solution Explorer 中，往下滚动找到 cli_wallet 和 witness_node，分别右键点击，选 build

编译完成后，会生成可执行文件：
- C:\bts\bin\programs\witness_node\Release\witness_node.exe
- C:\bts\bin\programs\cli_wallet\Release\cli_wallet.exe


## 10. 其他

- 上述编译出的 witness_node.exe 和 cli_wallet.exe 可以复制到其他电脑使用，但需要同时 msvcp140.dll 和 vcruntime140.dll 两个文件
  复制到同一个目录，它们在 C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\redist\x64\Microsoft.VC140.CRT\ 下。
  也可以通过静态链接的方式，或者安装 redistributable 的方式解决，本文不详述。

- 如果使用 cli_wallet.exe 连接使用 wss 的 API 服务器，需要指定包含服务器根证书的 PEM 文件，
  参考 https://github.com/bitshares/bitshares-core/issues/314 ，
  比如，可下载 https://curl.haxx.se/ca/cacert.pem 保存为 c:\bts\cacert.pem ，然后执行：
```
  set SSL_CERT_FILE=c:/bts/cacert.pem
  cli_wallet -s wss://bitshares.openledger.info/ws
```