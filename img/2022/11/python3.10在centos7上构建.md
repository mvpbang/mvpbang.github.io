---
title: "python3.10在centos7上构建"
toc: true
hiddenFromHomePage: false
date: 2022-11-13T19:14:09+08:00
draft: false
tags: ["python","deploy"]
categories: ["python"]
---

在centos7上编译安装py3.10,用到openssl库

<!--more-->

# env
- centos7.8
- Python-3.10.7.tgz

#link
- https://www.jianshu.com/p/c3c8003d2760

## 1.下载Python3.10.7
- https://www.python.org/ftp/python
- https://www.python.org/ftp/python/3.10.7/

## 2.requirement 
```
yum install -y gcc gcc-c++ make autoconf

yum insall -y zlib-devel bzip2-devel ncurses-devel sqlite-devel readline-devel  libffi-devel
```
> require openssl-1.1.1+,需要自编译


## 3.install openssl
- https://www.openssl.org/source/
- https://www.openssl.org/source/openssl-1.1.1s.tar.gz

```
./config -fPIC --prefix=/usr/local/openssl
make -s -j4
make -s install -j4

注释：
--prefix：指定安装目录
-fPIC:编译openssl的静态库
```

## 4.building-py
```
tar zxf Python-3.10.7.tgz   && cd Python-3.10.7

//view configure help
./configure --help
...
  --with-system-ffi       build _ctypes module using an installed ffi library,
                          see Doc/library/ctypes.rst (default is
                          system-dependent)
...
  --with-openssl=DIR      root of the OpenSSL directory
  --with-openssl-rpath=[DIR|auto|no]
                          Set runtime library directory (rpath) for OpenSSL
                          libraries, no (default): don't set rpath, auto:
                          auto-detect rpath from --with-openssl and

//编译过程提示错误，因此需要安装openssl-1.1.1+
Failed to build these modules:
_hashlib              _ssl                                     

Could not build the ssl module!
Python requires a OpenSSL 1.1.1 or newer

//编译构建
./configure -q --prefix=/usr/local/python3.10.7 --with-openssl=/usr/local/openssl --with-openssl-rpath=auto

make -s -j4  && make -s -j4 install

py3.10.7/lib/python3.10/site-packages  ## pip安装的包路径
py3.10.7/bin/xxx   //部分whl安装后可执行文件会在这里存在
```

## 5.env && yum
```
vim /etc/profile
export PATH=/usr/local/python3.10.7/bin:$PATH
source /etc/profile

//struct
bin
include
lib
share

bin/   #原生的Python,默认的Python还是Python 2.7.5

#调用Python
#!/usr/bin/env python3.10
```

# error
```
//error
zipimport.ZipImportError: can't decompress data; zlib not available
cd Modules/zlib
./configure
make install  //缺少则编译
or
yum install -y zlib-devel

apt-get install zlib1g-dev   //ubuntu16.04

//ModuleNotFoundError: No module named '_ctypes'
01 install libffi-devel
02 enable openssl and version
03 again buiding py3.10
```