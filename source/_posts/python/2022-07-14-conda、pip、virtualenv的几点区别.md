---
title: conda、pip、virtualenv的几点区别
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2022-07-14 19:33:37
subtitle:
tags:
  - python
categories:
  - Python
---

### **conda**

首先，**conda是一个通用的包管理器**，意思是什么语言的包都可以用其进行管理，自然也就包括Python了。在安装Anaconda或者Miniconda时，会对conda进行一同安装。其中Anaconda作为Python的发行版，相当于在Python的基础上自带了常用第三方库，而Miniconda则相当于是一个conda环境的安装程序，只包含了conda及其依赖项，这样就可以减少一些不需要的第三方库的安装，所以Miniconda所占用的空间很小。

### pip

Pip同conda一样，也是一个包管理器，并且是Python官方认可的包管理器。其中pip的含义是Pip Installs Packages。最常用于安装在Python包索引（PyPI, Python Package Index https://pypi.python.org/pypi）上发布的包。因此，在通过conda list命令查看当前环境下已安装的包时，通过pip的源是pypi。

​    ![在这里插入图片描述](1.png)

### conda和pip安装库的区别

在Anaconda中，**无论在哪个环境下**，只要通过conda install xxx的方式安装的库都会放在Anaconda的pkgs目录下，如:E:\python\anaconda\pkgs\numpy-1.18.1-py36h48dd78f_1。这样的好处就是，当在某个环境下已经下载好了某个库，再在另一个环境中还需要这个库时，就可以直接从pkgs目录下将该库复制至新环境（将这个库的Lib\site-packages中的文件复制到当前新环境下Lib中的第三方库中，也即Lib\site-packages中，这个过程相当于通过pip install xxx进行了安装）而不用重复下载。

### conda和pip卸载库的区别

pip是在**特定的环境**中进行库的安装，所以卸载库也是一样的道理，通过pip uninstall xxx就可以将该环境下Lib\site-packages中对应的库进行卸载了。

如果通过conda uninstall xxx删除当前环境下某个库时，删除的只是当前环境下site-packages目录中该库的内容，它的效果和通过pip uninstall xxx是一样的。如果再到另一个环境中通过conda install xxx下载这个库，则还是通过将pkgs目录下的库复制到当前环境。若要清空这个pkgs下的已下载库，可以通过命令conda clean -h进行实现。

### conda和virtualenv的区别

virtualenv和conda都可以做包隔离，类似java中的maven管理，实现一个项目一个独立的环境，简单的包隔离，用virtualenv就可以实现，它本质就是创建一个文件夹，作为一个独立的环境，运行文件夹bin下的激活脚本就进入这个独立的环境，在这个环境中，所有运行的python命令，安装的脚本都是在这个目录下的，是独立全局的python环境的。但有一个缺点就是virtualenv使用哪个版本的pip命令就会安装到当前目录的bin下，那么用virtualenv创建的虚拟环境就是这个版本的环境，是不能实现版本切换的，换句话说创建出的所有环境都是同一个python版本，要想实现多版本的环境就得用conda，很好的工具，可以详细学习一下。
