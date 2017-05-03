    栈溢出

    No NX, No Canary: 直接将shellcode置于缓冲区，然后从返回地址跳到shellcode处

    NX, No Canary:直接覆盖返回地址，rop构造

    x86: 栈构造

    x86_64: 栈与寄存器构造、万能gadget

    NX, Canary

    泄露栈cookie

    观察溢出是否可以覆盖指针，造成任意地址写（修改__stack_chk_fail函数got表）

    在栈cookie检查前控制PC指针

    堆

    堆利用的方式最多，最复杂，默认大部分保护打开：https://github.com/shellphish/how2heap

    漏洞类型分类

    堆溢出

     通过溢出可以改写下一个chunk的pre size、size、FD和BK，可以结合unlink或者fastbin attack

    Double Free

    多结合unlink, 通过修改free got表

    Use After Free

    多用于泄露地址

    off-by-one

     chunk overlapping

      off-by-one overwrite allocated

      off-by-one overwrite freed

      off-by-one null byte

     unlink

      off-by-one small bin

      off-by-one large bin

    利用方法分类

    house_of_einherjar

    house_of_force

    house_of_lore

    house_of_spirit

    unsafe_unlink

    Format String

    No ALSR，Partial RELRO, No PIE: 任意地址改（堆，栈，got表，libc随意）

    ALSR，Partial RELRO

     No PIE: 泄露栈，libc地址，然后修改got表

    PIE

     通过泄露栈中地址，如调用lib_start_main时的参数，泄露程序地址，推算got表

     根据ELF的魔术字7f 45 4c 46进行爆破，内存地址一页一页往后翻，多适用于32位

    ALSR，Full RELRO:

    可泄露栈地址：泄露libc，覆盖返回地址构造rop

    不可泄露栈地址： 泄露libc地址

     atexit注册的函数列表

     tls_dtor等在exit时会被调用的函数

     修改stdout虚表指针

     修改__free_hook/__malloc_hook

     覆盖__kernel_vsycall

    整数溢出

    一般结合堆溢出辅助利用
