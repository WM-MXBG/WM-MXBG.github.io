---
title: 【VS Code】“The ‘clang-format‘ command is not available. Please check your clang-format.executable“解决
data: 2021-11-13 12:33:35
tags: 
- solution
---

在VS Code中对C语言进行代码格式化的时候发现’clang-format'插件一直在提示报错：

```
The 'clang-format' command is not available. Please check your clang-format.executable user setting and ensure it is installed.
```
在网上找了很多方法，其中很多都在说配置文件.clang-format，但是我没整明白，最后在dynamsoft上得到了解决方案。
下载 [Clang for Windows (32-bit)](http://llvm.org/releases/3.7.0/LLVM-3.7.0-win32.exe) or [Clang for Windows (64-bit)](http://llvm.org/releases/3.7.0/LLVM-3.7.0-win64.exe).

安装完之后将 %LLVM% \bin 添加到系统路径中.（这步在安装的时候有选项可以直接选择安装到路径中）

之后就可以用快捷键 Alter+Shift+F 在VS Code里进行格式化操作了。

参考链接：
https://www.dynamsoft.com/codepool/vscode-format-c-code-windows-linux.html
