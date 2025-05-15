---
title: Python 环境搭建和工具使用
date: 2025-05-15 20:15
venue: Tianjin
collection: publications
category: Python
permalink: /publication/Python 环境搭建和工具使用-2025-05-15
excerpt: Python 环境搭建和工具使用
---
# 一、Python 环境搭建和工具使用（课时： 1）
### 1. python、pip、anaconda/conda 安装
#### python 介绍和安装
Python是一种广泛使用的解释型、高级编程、通用型编程语言，由‌吉多·范罗苏姆于1991年创造。Python的设计哲学强调代码的可读性和简洁的语法，尤其是使用空格缩进划分代码块，而非使用大括号或者关键词。
python 官网：https://www.python.org/
python 手册：https://docs.python.org/3.12/index.html
python 安装：（建议稳定版本）
    1）windows 系统
    · 官网下载 python 安装包，解压安装。
    · 配置环境变量：我的电脑右键->属性->高级环境变量->系统变量->在 Path 中增加 python 路径
    2）Mac 系统
    Mac 系统本身自带系统支持的 python，但是版本号可能不同，根据自己的需求升级
    · Mac 电脑自带的软件安装程序 Homebrew ，brew install python
    · 配置环境变量，vim .bashrc

#### pip 介绍和安装
pip 手册：https://pip.pypa.io/en/latest/
pip 是用来安装 python 工具包的软件，它通过下载远程 python 库中的包安装到本地。pip 默认使用的是 Python Package Index（PyPI）作为包的源，而 PyPI 的服务器位于国外。因此，在国内使用 pip 安装 Python 包时，可能会因为网络延迟、不稳定或者防火墙限制等原因导致安装失败或速度极慢。为了解决这个问题，我们可以将 pip 的源切换到国内的镜像站点，这些站点通常会提供更快的下载速度和更稳定的连接。
配置国内 pip 源：https://www.jb51.net/python/320303r58.htm
pip 的常用命令：
    1）pip install 安装依赖的 python 包
    pip install SomePackage            # latest version
    pip install SomePackage==1.0.4     # specific version
    pip install 'SomePackage>=1.0.4'   # minimum version
    pip install -r requirements.txt    # 安装文件 requirements.txt 中包含的所有包
    2）pip uninstall 卸载安装的 python 包
    pip uninstall SomePackage
    3) pip list 显示所有已安装的 python 包
    --outdated 显示当前包的最新版本
    4) pip search
    pip search "query" # 在仓库中查找满足 query 的 python 包
    5）pip show 显示安装包的详细信息
    6）pip freeze > requirements.txt 导出环境中安装的所有包到 requirements.txt 文件中

#### python 虚拟环境
Python 虚拟环境是一种用于隔离和管理项目所需的 ‌Python 解释器及其‌依赖库的工具。‌ 它允许在同一台机器上同时运行多个项目，并且每个项目都可以有不同的依赖库和 Python 版本。虚拟环境通过创建一个独立的 Python 运行环境，使得不同项目之间的依赖关系相互隔离，避免冲突。

#### anaconda、miniconda 和 conda
Anaconda是一款开源的Python和R语言的发行版本，其主要目的是为数据科学、机器学习、大数据处理和科学计算提供一个集成的开发环境。Anaconda集成了大量常用的数据科学库和工具，并附带了强大的环境管理和包管理功能，使得Python环境的配置和维护变得简单高效。
Anaconda的核心组件包括：
    Conda：Anaconda的包和环境管理工具。它支持多版本Python环境的创建和管理，并且可以跨平台运行。
    Python和R：Anaconda预装了Python和R语言的解释器。
    数据科学库：Anaconda预装了包括NumPy、Pandas、Matplotlib、Scikit-learn、TensorFlow等常用的数据科学库。
    Jupyter Notebook：一个广泛使用的交互式笔记本环境，用于数据分析和可视化。
Miniconda：
    Miniconda是Anaconda的轻量级版本，只包含了Python和Conda，以及它们的依赖项。
    Miniconda的优点是其小巧且快速，用户可以自行选择并安装他们需要的软件包。
Conda：是一个开源的包管理系统和环境管理系统

· miniconda 的安装
miniconda 安装手册：https://docs.anaconda.com/miniconda/miniconda-install/
· conda 的使用
conda 的使用手册：https://docs.conda.io/projects/conda/en/latest/user-guide/getting-started.html
    1）创建环境
    conda create -n <ENV_NAME>
    conda create -n <ENV_NAME> python=<VERSION> <PACKAGE>=<VERSION>
    conda rename -n ENVNAME NEWENVNAME # 环境重命名
    conda remove -n ENVNAME --all # 删除环境
    conda export ENVNAME > ENV.yml # 导出环境信息
    conda env create -n ENVNAME --file ENV.yml # 使用环境信息文件创建环境
    2）显示所有 conda 环境
    conda info --envs
    3）使用指定 conda 环境
    conda activate <ENV_NAME>
    conda deactivate <ENV_NAME>
    4）查找 conda 包
    conda search <PACKAGE>
    5）安装 conda 包
    conda install (--name <ENV_NAME>) <PACKAGE>(=<VERSION>)
    conda uninstall <PACKAGE>  # 卸载包
    6）显示所有安装的包
    conda list
    7）conda 版本
    conda --version
    conda info
    8）conda 升级
    conda update conda
    9）conda 环境中如何安装 Pypi 包
    使用 conda 在当前环境安装 pip，配置国内源，pip install 安装需要的包

### 2. pycharm 和 vscode 的使用
软件编辑器和集成开发环境 IDE
#### pycharm
1）安装
下载地址：https://www.jetbrains.com/pycharm/download/?section=windows
2）汉化，安装 chinese 插件
3）设置字体
4）创建项目p
5）代码编辑
6）代码调试
#### vscode
1）下载安装 vscode
下载地址：https://code.visualstudio.com/Download
python 环境配置手册：https://code.visualstudio.com/docs/python/python-quick-start
2）安装 python 插件，支持 python 语言
3）安装 code runner 插件，支持代码运行
4）指定 python 解析器，使用 conda 虚拟环境
5）vscode 主题和字体设置

### 3. jupyter 安装和使用
1）介绍
jupyter lab 是一个 web 应用，可以在界面上直接输入代码并执行，方便操作和可视化。
2）安装
使用 pip 安装说明：https://jupyter.org/install
本教程基于 conda 虚拟环境，安装方式 conda install jupyter notebook
将环境写入 kernel 中：python -m ipykernel install --user --name=<ENV_NAME>
3）启动
jupyter notebook (--port <port_number>) (--no-browser)
4）修改默认的文件保存路径
jupyter notebook --generate-config  # 生成默认配置文件
vim ~/.jupyter/jupyter_notebook_config.py
查找并修改：c.NotebookApp.notebook_dir = '指定文件保存路径'
5）使用
jupyter 使用手册：https://docs.jupyter.org/en/latest/running.html
知乎文章：https://zhuanlan.zhihu.com/p/33105153