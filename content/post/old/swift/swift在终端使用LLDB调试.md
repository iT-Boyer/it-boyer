---
title: swift在终端使用LLDB调试
date: 2018-10-01 21:02:49
updated: 2018-10-17 19:47:48
comments: true
tags: [swift]
categories:
- 学习笔记
keywords: 
permalink: 
copyright: 
password: 
top:   
---
[Using the LLDB Debugger](https://swift.org/getting-started/#using-the-lldb-debugger)
使用LLDB调试器一步一步地运行Swift程序，通过设置`断点`调试运行状态。
1. 创建一个名`Factorial.swift`，定义了一个`factorial(n:)`函数，并打印调用该函数的结果:
```sh
func factorial(n: Int) -> Int {
    if n <= 1 { return n }
    return n * factorial(n: n - 1)
}

let number = 4
print("\(number)! is equal to \(factorial(n: number))")
```
2. swiftc命令
运行swiftc命令`-g`选项生成`swift`调试信息,在目录中生存可执行的Factorial文件：
```sh
$ swiftc -g Factorial.swift
$ ls
Factorial.dSYM
Factorial.swift
Factorial*
```
3. 使用lldb启动Factorial文件
通过LLDB调试器命令`lldb`运行:
```sh
$ lldb Factorial
(lldb) target create "Factorial"
Current executable set to 'Factorial' (x86_64).
```
4. 设置断点
使用`breakpoint set` (`b`) 命令在`factorial(n:)`函数的第2行中设置一个断点，每次执行函数时中断进程:
```sh
(lldb) b 2
Breakpoint 1: where = Factorial`Factorial.factorial (Swift.Int) -> Swift.Int + 12
```
5. 运行调试
使用`run` (`r`)命令运行进程。进程在`factorial(n:)`函数的调用位置停止。
```sh
(lldb) r
Process 40246 resuming
Process 40246 stopped
* thread #1: tid = 0x14dfdf, 0x0000000100000e7c Factorial`Factorial.factorial (n=4) -> Swift.Int + 12 at Factorial.swift:2, queue = 'com.apple.main-thread', stop reason = breakpoint 1.1
frame #0: 0x0000000100000e7c Factorial`Factorial.factorial (n=4) -> Swift.Int + 12 at Factorial.swift:2
   1    func factorial(n: Int) -> Int {
-> 2        if n <= 1 { return n }
   3        return n * factorial(n: n - 1)
   4    }
   5
   6    let number = 4
   7    print("\(number)! is equal to \(factorial(n: number))")
```
Use the print (p) command to inspect the value of the n parameter.
```sh
(lldb) p n
(Int) $R0 = 4
```
The print command can evaluate Swift expressions as well.
```sh
(lldb) p n * n
(Int) $R1 = 16
```
Use the `backtrace` (`bt`) command to show the frames leading to `factorial(n:)` being called.
```sh
(lldb) bt
* thread #1: tid = 0x14e393, 0x0000000100000e7c Factorial`Factorial.factorial (n=4) -> Swift.Int + 12 at Factorial.swift:2, queue = 'com.apple.main-thread', stop reason = breakpoint 1.1
    * frame #0: 0x0000000100000e7c Factorial`Factorial.factorial (n=4) -> Swift.Int + 12 at Factorial.swift:2
    frame #1: 0x0000000100000daf Factorial`main + 287 at Factorial.swift:7
    frame #2: 0x00007fff890be5ad libdyld.dylib`start + 1
    frame #3: 0x00007fff890be5ad libdyld.dylib`start + 1
```
Use the `continue` (`c`) command to resume the process until the breakpoint is hit again.
```sh
(lldb) c
Process 40246 resuming
Process 40246 stopped
* thread #1: tid = 0x14e393, 0x0000000100000e7c Factorial`Factorial.factorial (n=3) -> Swift.Int + 12 at Factorial.swift:2, queue = 'com.apple.main-thread', stop reason = breakpoint 1.1
frame #0: 0x0000000100000e7c Factorial`Factorial.factorial (n=3) -> Swift.Int + 12 at Factorial.swift:2
   1    func factorial(n: Int) -> Int {
-> 2        if n <= 1 { return n }
   3        return n * factorial(n: n - 1)
   4    }
   5
   6    let number = 4
   7    print("\(number)! is equal to \(factorial(n: number))")
```
Use the `print` (`p`) command again to inspect the value of the n parameter for the second call to` factorial(n:)`.
```sh
(lldb) p n
(Int) $R2 = 3
```
6. 取消断点
Use the `breakpoint disable` (`br di`) command to disable all breakpoints and the `continue` (`c`) command to have the process run until it exits.
```sh
(lldb) br di
All breakpoints disabled. (1 breakpoints)
(lldb) c
Process 40246 resuming
4! is equal to 24
Process 40246 exited with status = 0 (0x00000000)
```
