## IDA 分析


##脚本
```python
#!/usr/bin/env python
# coding=utf-8
import gdb

strlen = 30
charsets = "0123456789-_qwertyuioplkjhgfdsazxcvbnmQWERTYUIOPLKJHGFDSAZXCVBNM{}!"
targetnum = [0xd0, 0x71, 0xe6, 0x32, 0xf, 0x3a, 0x9, 0x2e, 0xf8, 0xa1, 0xb6, 0x52, 0xde, 0xcd, 0x65, 0x72, 0x52,\
        0x9f, 0x4f, 0xb9, 0xf4, 0x72, 0x76, 0xc1, 0x34, 0x35, 0xee, 0xf7, 0xda, 0x50]
flagstr =[0 for n in range(strlen)]
gin = 0

class Cal(gdb.Command):
    def __init__(self):
        super(Cal,self).__init__("tocal",gdb.COMMAND_USER)

    def getreg(self, register): 
        r = register.lower()
        regs = gdb.execute("info registers %s" % r, to_string=True)
        if regs:
            regs = regs.splitlines()
            if len(regs) > 1:
                return None
            else:
                result = int(str(regs[0].split()[1]), 0)
                return result
        return None

    def get_status(self):
        status = "UNKNOWN"
        out = gdb.execute("info program", to_string=True)
        for line in out.splitlines():
            if line.startswith("It stopped"):
                if "signal" in line: # stopped by signal
                    status = line.split("signal")[1].split(",")[0].strip()
                    break
                if "breakpoint" in line: # breakpoint hit
                    status = "BREAKPOINT"
                    break
            if "not being run" in line:
                status = "STOPPED"
                break
        return status

    def invoke(self, arg, from_tty):
        global gin      
        print(charsets[gin] * strlen)
        gdb.execute('set pagination off')
        gdb.execute('b *0x484e1b')
        gdb.execute('r')
        #for index in range(len(charsets)):
        while 1:           
            totry = charsets[gin] * strlen
            print(totry)
            tmparr = [0 for n in range(strlen)]
            for cy in range(strlen):
                runstat = self.get_status()  
                if runstat == "BREAKPOINT":
                    rax_v = self.getreg("rax")
                    tmparr[cy] = rax_v
                    gdb.execute('c')
                elif runstat == "STOPPED":
                    gin += 1
                    gdb.execute('r')
                    break
                else:
                    #gdb.execute('c')  
                    pass                 
            #print(tmparr)

            for an in range(strlen):
                if tmparr[an] == targetnum[an] and gin > 0:
                    flagstr[an] = charsets[gin -1]

            #print(flagstr)
            print(''.join(str(flagstr[n]) for n in range(len(flagstr))))

Cal()

```
