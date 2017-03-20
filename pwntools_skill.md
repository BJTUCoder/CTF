## pwntools 的奇技淫巧

### set arch info 
context(arch = 'x86_64', os = 'linux', endian  = 'little')

### set info/debug 
* context.log_level = 'debug'
* context.log_level = 'info'

### set env of elf
```python
env = {'LD_PRELOAD':'/home/peak/CTF//easiestprintf/libc.so.6'}
if DEBUG:
    r = process('./EasiestPrintf', env=env)
else:
    r = remote('202.120.7.210', 12321)
   
```

### debug program from exp
    print proc.pidof(p)
    raw_input('gdb attach')



### demo of memu 
```python

def new_note(x):
    p.recvuntil("Your choice: ")
    p.send("2\n")
    p.recvuntil("Length of new note: ")
    p.send(str(len(x))+"\n")
    p.recvuntil("Enter your note: ")
    p.send(x)

def delete_note(x):
    p.recvuntil("Your choice: ")
    p.send("4\n")
    p.recvuntil("Note number: ")
    p.send(str(x)+"\n")

def list_note():
    p.recvuntil("Your choice: ")
    p.send("1\n")

def edit_note(x,y):
    p.recvuntil("Your choice: ")
    p.send("3\n")   
    p.recvuntil("Note number: ")
    p.send(str(x)+"\n")   
    p.recvuntil("Length of note: ")
    p.send(str(len(y))+"\n")   
    p.recvuntil("Enter your note: ")
    p.send(y)
    
```
> p.sendline("1")  == p.send("1\n")
> p.sendlineafter("Command: ", "1") == p.recvuntil("Command: ")/p.sendline("1")


