---
layout: post
title: "总结一下python打包库pyinstaller打包的一些问题"
subtitle: '勤动手，更要多动脑'
author: "Yufx"
header-style: text
tags:
  - pyinstaller，cx-freeze，python3
---
> python常用打包库有pyinstaller and cx-freeze
> 两种我都尝试过，最近一次比较符合预期的还是要属cx-freeze了。我是用的python版本是**3.7**，建议还是用***3.6***吧，3.7还是有许多bug.安装pyinstaller`pip install pyinstaller`安装cx-freeze命令行敲入`pip install cx_freeze`

----------

下面分别介绍pyinstaller和cx-freeze两种打包方式：

一. pyinstaller打包。`pyinstaller myexe.py`
	> pyinstaller的菜单如下：

`-h, --help	显示此帮助消息并退出`

`-v, --version	显示程序版本信息并退出。`

`--distpath DIR	放置捆绑应用的位置（默认值：./ did）`

`--workpath WORKPATH	在哪里放置所有临时工作文件，.log，.pyz等（默认值：./ build）`

`-y, --noconfirm	替换输出目录（默认值：SPECPATH / dist / SPECNAME）而不要求确认`

`--upx-dir UPX_DIR	UPX实用程序的路径（默认：搜索执行路径）`

`-a, --ascii	不包括unicode编码支持（默认值：如果可用，则包含）`

`--clean	在构建之前清理PyInstaller缓存并删除临时文件。`

`--log-level LEVEL	构建时控制台消息中的详细信息量。LEVEL可能是DEBUG，INFO，WARN，ERROR，CRITICAL（默认值：INFO）之一。`

	pyinstaller打包常用的：
	-D, --onedir	创建包含可执行文件的单文件夹包（默认）
	-F, --onefile	创建一个文件捆绑的可执行文件。
	--specpath DIR	用于存储生成的spec文件的文件夹（默认值：当前目录）
	-n NAME, --name NAME	要分配给捆绑应用程序和规范文件的名称
（默认值：第一个脚本的基本名称）
	比如我要打包一个无cmd窗口的，只有一个捆绑的exe的包:
直接进入要打包的myexe的文件夹目录`ctrl+shift+右键单击`
再执行`pyinstaller myexe.py -D -F`就等结束吧。

**打包后的可执行程序**存放在打包文件所再目录，会生成一个dist和bulid的文件夹，程序在dist里面。

**值得注意的是**python的程序目录最好不要有中文路径，不然找问题容易忽略路径的问题。想要exe可移植还需要注意的就是目标电脑上要安装`vc_redist.x64.exe`这个插件。我是64位win10.


> 二. 通过cx-freeze打包： 

> 
> 1. `cxfreeze D:\change\hello.py --target-dir D:\change\changedexe` 命令解释：hello.py 是你要打包的主文件、启动文件。后面的路径是exe存放的路径。
> 打包成一个可直接安装的程序，安装打开即可。
> 2. 在script文件夹建一个`setup.py`.
> 	   	   
		import os,sys 
        os.environ['TCL_LIBRARY']="C:\\python3\\Python36\\tcl\\tcl8.6"
        os.environ['TK_LIBRARY']="C:\\python3\\Python36\\tcl\\tk8.6"
        from cx_Freeze import setup, Executable
        include_files = ["C:\\python3\\Python36\\DLLs\\tcl86t.dll",
		"C:\\python3\\Python36\\DLLs\\tk86t.dll",
		'C:\\Users\\yfx\\Desktop\\ccc\\1.mp3',
		'C:\\Users\\yfx\\Desktop\\ccc\\1.png',
		'C:\\Users\\yfx\\Desktop\\ccc\\love.py',
		'C:\\Users\\yfx\\Desktop\\ccc\\simkai.ttf']
        # options = { 'build_exe': { 'excludes': ['gtk', 'PyQt5', 'Tkinter'], 'packages': [], 'includes': [], } }  
        base = None
        if sys.platform == "win32":
            base = "Win32gui"
        options = { 'build_exe': { 'excludes': ['PyQt5','Tkinter'], 'packages': [],
		 'include_files': include_files, } }
        executables = [Executable('C:\\Users\\yfx\\Desktop\\ccc\\love.py',base=base,targetName = 'love.exe')]#, icon = 'C:\\Users\\yfx\\Desktop\\aaa\\1.ico
        setup(name='yfx_1', version = '1.0', description ='', options=options, executables = executables)
**其中第二行和第三行的环境必须导入**，其次，需要导入`pyqt4`或者`pyqt5`的库，include_filse里面是打包程序的项目文件等等。这里懒得写那么详细了，应该都看得懂了。

>      接着在script路径下进入cmd执行`python setup.py bdist_msi`，最后在script路径生成一个dist和build文件夹，dist里就有安装程序。在build里面可以调试，因为你在使用过程中一定会遇到很多问题。报错了就在build进入`cmd`，直接执行exe程序，会看到是什么错。最终，恭喜你打包成功，可以将自己的程序移植到别人的电脑，而无需安装python.

----------
总结一下，想要打包成功。需要注意的有：
1. python版本3？3.5？3.7
2. 电脑系统，32位还是6位，有无安装`vc_redist.x64.exe`
3. 打包项目的路径一定不要有中文