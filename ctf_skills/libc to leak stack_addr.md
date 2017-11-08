## how to leak stack_addr from libc

libc.symbols['environ']

get the addr of char **envp

在 gdb 中打印出来 
 * $p environ
 * 0xffffbeef

然后查看  stack 100  查看程序路径所在栈地址 + 8

environ 是一個在 libc 裡面的一個 symbol，他裡面存著 stack address 指到 char** envp
