## linux kernel pwn 
#### 入门
* [Linux 内核漏洞利用教程（一）：环境配置](https://www.tuicool.com/articles/mYvUFfe)
* [Linux 内核漏洞利用教程（二）：两个Demo](https://www.tuicool.com/articles/eiEria)
* [Linux 内核漏洞利用教程（三）：实践 CSAW CTF 题目](https://www.tuicool.com/articles/iqqUNbI)

#### 实践
* [一道简单内核题入门内核利用](https://www.anquanke.com/post/id/86490)
* [Linux Kernel UAF](http://pwn4.fun/2017/08/15/Linux-Kernel-UAF/)
* [Exploiting Linux Kernel Heap Corruptions](http://pwn4.fun/2017/06/12/Exploiting-Linux-Kernel-Heap-Corruptions/)

#### 调试
如果要调试的话，可以在boot.sh中加入参数-gdb tcp::1234 -S，这样系统启动时会挂起等待gdb连接，gdb中用target remote :1234连接即可。
想办法添加符号表，gdb如果报错 **Remote 'g' packet reply is too long**， (gdb) set architecture i386:x86-64:intel 可以解决错误，如果还未解决删除-S试试

> 
    1 调试注意事项
    模块在编译后按照上篇文章的方法，丢进busybox，然后qemu起内核然后调试。
    由于模块并没有作为vmlinux的一部分传给gdb，因此必须通过某种方法把模块信息告知gdb，可以通过add-symbol-file命令把模块的详细信息告知gdb，由于模块也是一个elf文件，需要知道模块的.text、.bss、.data节区地址并通过add-symbol-file指定。
    模块stack_smashing.ko的这三个信息分别保存在/sys/module/stack_smashing/sections/.text、/sys/module/stack_smashing/sections/.bss和/sys/module/stack_smashing/sections/.data，由于stack_smashing模块没有bss、data节区所以只需要指定text即可。

    2 调试过程
    qemu 中设置好gdbserver后，找到模块的.text段的地址grep 0 /sys/module/stack_smashing/sections/.text。
    
