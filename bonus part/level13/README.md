# level13

- We login as user level13.
```
level13@SnowCrash:~$ ls -l
total 8
-rwsr-sr-x 1 flag13 level13 7303 Aug 30  2015 level13
```

```
level13@SnowCrash:~$ ./level13
UID 2013 started us but we we expect 4242
```


- Let's take a closer look at this binary using [GDB](https://en.wikipedia.org/wiki/GNU_Debugger).
```
(gdb) info functions
All defined functions:

Non-debugging symbols:
0x08048314  _init
...
0x08048474  ft_des
0x0804858c  main
0x080485f0  __libc_csu_init
...
```

```
(gdb) disas main
Dump of assembler code for function main:
   0x0804858c <+0>:     push   %ebp
   0x0804858d <+1>:     mov    %esp,%ebp
   0x0804858f <+3>:     and    $0xfffffff0,%esp
   0x08048592 <+6>:     sub    $0x10,%esp
   0x08048595 <+9>:     call   0x8048380 <getuid@plt>
   0x0804859a <+14>:    cmp    $0x1092,%eax
   0x0804859f <+19>:    je     0x80485cb <main+63>
   0x080485a1 <+21>:    call   0x8048380 <getuid@plt>
   0x080485a6 <+26>:    mov    $0x80486c8,%edx
   0x080485ab <+31>:    movl   $0x1092,0x8(%esp)
   0x080485b3 <+39>:    mov    %eax,0x4(%esp)
   0x080485b7 <+43>:    mov    %edx,(%esp)
   0x080485ba <+46>:    call   0x8048360 <printf@plt>
   0x080485bf <+51>:    movl   $0x1,(%esp)
   0x080485c6 <+58>:    call   0x80483a0 <exit@plt>
   0x080485cb <+63>:    movl   $0x80486ef,(%esp)
   0x080485d2 <+70>:    call   0x8048474 <ft_des>
   0x080485d7 <+75>:    mov    $0x8048709,%edx
   0x080485dc <+80>:    mov    %eax,0x4(%esp)
   0x080485e0 <+84>:    mov    %edx,(%esp)
   0x080485e3 <+87>:    call   0x8048360 <printf@plt>
   0x080485e8 <+92>:    leave
   0x080485e9 <+93>:    ret
End of assembler dump.
```


- We can see that the return from `<getuid@plt>` is compared with the hexadecimal value 1092 (4242 in decimal). If the two values are not equal, the program will call `<printf@plt>` with the string that starts at address 0x80486c8.
```
(gdb) x/s 0x80486c8
0x80486c8:       "UID %d started us but we we expect %d\n"
```


- However, if the comparison is indeed equal, the program will call the `<ft_des>` function with the parameter being the string that starts at the address 0x80486ef. After that, the program will printf the string that begins at the address 0x8048709.
```
(gdb) x/s 0x80486ef
0x80486ef:       "boe]!ai0FB@.:|L6l@A?>qJ}I"
```

```
(gdb) x/s 0x8048709
0x8048709:       "your token is %s\n"
```


- One can assume that `<ft_des>` will convert `boe]!ai0FB@.:|L6l@A?>qJ}I` into the token we need to log in as user flag13. Therefore, the setuid and setgid permission bits are unnecessary here, as we can retrieve our token directly in gdb since the program's code contains the token in encrypted form.
```
(gdb) b*0x0804859a
Breakpoint 1 at 0x804859a
```

```
(gdb) r
Starting program: /home/user/level13/level13
Breakpoint 1, 0x0804859a in main ()
```

```
(gdb) set $eax=4242
```

```
(gdb) n
Single stepping until exit from function main,
which has no line number information.
your token is 2A31L79asukciNyi8uppkEuSx
0xb7e454d3 in __libc_start_main () from /lib/i386-linux-gnu/libc.so.6
```

```
level13@SnowCrash:~$ su flag13
Password:2A31L79asukciNyi8uppkEuSx
su: Authentication failure
```

```
level13@SnowCrash:~$ su level14
Password:2A31L79asukciNyi8uppkEuSx
level14@SnowCrash:~$
```