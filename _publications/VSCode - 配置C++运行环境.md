---
title: VSCode 配置 C++ 运行环境
date: 2025-05-15 20:31
venue: Tianjin
collection: publications
category: VSCode
permalink: /publication/VSCode 配置 C++ 运行环境-2025-05-15
excerpt: VSCode 配置 C++ 运行环境
---
# VSCode 配置 C++ 运行环境

### 具体操作
1. VSCode 安装
   直接通过微软官网下载安装即可。[官网链接](https://code.visualstudio.com/)

2. C++ 运行环境
   1) Mac 环境
    Mac 环境中默认已经部署好 g++ 或 clang++ 环境，通过执行 `g++ --version` 或`clang++ --version`
    2）Windows 环境（这里只做介绍，无具体实操）
    MinGW 全称 Minimalist GNU For Windows，是个精简的Windows 平台 C/C++、ADA 及 Fortran 编译器，相比 Cygwin 而言，体积要小很多，使用较为方便。MinGW 提供了一套完整的开源编译工具集，以适合 Windows 平台应用开发，且不依赖任何第三方 C 运行时库。
    
3. 需要用到的插件
   1）C/C++
   2）Code Runner
   修改配置，支持指定不同语言编译执行的命令：
   code-runner.executorMap

4. 配置文件
    1）tasks.json
    `task.json` 用来保存编译配置
    2）launch.json
    `launch.json`用来保存自定义调试参数，Ensure that the preLaunchTask value matches the label of the build task in the tasks.json file.

### 后续
配置 Python 运行环境、java 运行环境、Maven 项目运行等等

### 参考链接
官网文档：<https://code.visualstudio.com/docs/cpp/config-clang-mac>