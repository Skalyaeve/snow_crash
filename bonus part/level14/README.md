# level14

- We login as user level14.
```
level14@SnowCrash:~$ ls -la
total 12
dr-x------ 1 level14 level14  100 Mar  5  2016 .
d--x--x--x 1 root    users    340 Aug 30  2015 ..
-r-x------ 1 level14 level14  220 Apr  3  2012 .bash_logout
-r-x------ 1 level14 level14 3518 Aug 30  2015 .bashrc
-r-x------ 1 level14 level14  675 Apr  3  2012 .profile
```

```
level14@SnowCrash:~$ find / -user flag14 2>/dev/null
```

```
level14@SnowCrash:~$ find / -group flag14 2>/dev/null
```

```
level14@SnowCrash:~$ cat /etc/passwd
...
level14:x:2014:2014::/home/user/level14:/bin/bash
flag00:x:3000:3000::/home/flag/flag00:/bin/bash
...
flag13:x:3013:3013::/home/flag/flag13:/bin/bash
flag14:x:3014:3014::/home/flag/flag14:/bin/bash
```


- Nothing.. Yet we need to retrieve flag14. Normally, it's the `getflag` command that gives us our flag depending on the user who executes it.
One way or another, the `getflag` binary must be able to provide the flags, so either the flags are stored somewhere in the ISO and the command is used to retrieve them, or they are directly written in the source code of the binary.


- Let's take a closer look at this binary using [GDB](https://en.wikipedia.org/wiki/GNU_Debugger).
```
level14@SnowCrash:~$ whereis getflag
getflag: /bin/getflag
```

```
level14@SnowCrash:~$ gdb /bin/getflag
```

```
...
(gdb) info functions
All defined functions:

Non-debugging symbols:
...
0x080487be  afterSubstr
0x08048843  isLib
0x08048946  main
...
```

```
(gdb) disas main
Dump of assembler code for function main:
HUGE CODE

(gdb) q
```


- Let's save time, we can use [Ghidra](https://en.wikipedia.org/wiki/Ghidra) to get an interpretation of the source code of this binary.
```c
undefined4 main(void)
{
    ...
    lVar2 = ptrace(PTRACE_TRACEME,0,1,0);
    if (lVar2 < 0) {
        /* Si le programme est déjà en train d'être suivi par un autre processus (ex: GDB),
        ptrace retournera -1, le programme affiche un message d'erreur et sort. */
    }
    else {
        pcVar7 = getenv("LD_PRELOAD");
        if (pcVar7 == (char *)0x0) {
            iVar9 = open("/etc/ld.so.preload",0);
            if (iVar9 < 1) {
                iVar4 = syscall_open("/proc/self/maps",0);
                if (iVar4 == -1) {
                    /* Si un chargement dynamique de bibliothèque est en cours,
                    le programme affiche un message d'erreur et sort.*/
                }
                else {
                    do {
                        do {
                            /* Autre verification contre l'injection de bibliothèque
                            et de code dans la mémoire du processus*/
                        } while (!bVar1);

                        iVar9 = isLib(local_114,(int)&DAT_08049068);
                        if (iVar9 != 0) {
                            fwrite("Check flag.Here is your token : ",1,0x20,stdout);

                            pFVar8 = stdout;
                            _Var5 = getuid();
                            // --- A ce moment, '_Var5' sera utilisé pour savoir quel flag afficher.

                            if (_Var5 == 0xbbe) {
                                pcVar7 = ft_des("H8B8h_20B4J43><8>\\ED<;j@3");
                                fputs(pcVar7,pFVar8);
                            }
                            else if (_Var5 < 0xbbf) {
                                if (_Var5 == 0xbba) {
                                    pcVar7 = ft_des("<>B16\\AD<C6,G_<1>^7ci>l4B");
                                    fputs(pcVar7,pFVar8);
                                }
                                else if (_Var5 < 0xbbb) {
                                    if (_Var5 == 3000) {
                                        pcVar7 = ft_des("I`fA>_88eEd:=`85h0D8HE>,D");
                                        fputs(pcVar7,pFVar8);
                                    }
                                    else if (_Var5 < 0xbb9) {
                                        if (_Var5 == 0)
                                            fwrite("You are root are you that dumb ?\n",1,0x21,stdout);
                                        else
                                            fwrite("\nNope there is no token here for you sorry. Try again :)",1,0x38, stdout);
                                    }
                                }
                                ...
                            }
                            ...
                        }
                        ...
                    } while (pcVar7 != (char *)0x0);
                    ...
                }
                ...
            }
            else {
                /* Si des bibliothèques ont été préchargées en utilisant la variable
                d'environnement le fichier /etc/ld.so.preload, le programme affiche un message d'erreur et on sort. */
            }
            ...
        }
        else {
            /* Si des bibliothèques ont été préchargées en utilisant la variable
            d'environnement LD_PRELOAD, le programme affiche un message d'erreur et on sort. */
        }
        ...
    }
    /* Autre verifications (stack overflow, ...)*/
}
```


- The binary contain various protections ([DLL injection](https://en.wikipedia.org/wiki/DLL_injection), [buffer overflow](https://en.wikipedia.org/wiki/Buffer_overflow_protection), ...), but the flags are accessible to us in the same way as the previous level.
We just need to monitor the program's execution using [GDB](https://en.wikipedia.org/wiki/GNU_Debugger) and we should be able to easily retrieve our flag.


- We just need a breakpoint should be placed after the `ptrace(PTRACE_TRACEME, 0, 1, 0)` and `getuid()` returns in order to modify the return of these functions, so:
```
level14@SnowCrash:~$ cat /etc/passwd | grep flag14
flag14:x:3014:3014::/home/flag/flag14:/bin/bash
```

```
level14@SnowCrash:~$ gdb /bin/getflag
```

```
...
(gdb) disas main
Dump of assembler code for function main:
...
   0x08048989 <+67>:    call   0x8048540 <ptrace@plt>
   0x0804898e <+72>:    test   %eax,%eax
...
   0x08048afd <+439>:   call   0x80484b0 <getuid@plt>
   0x08048b02 <+444>:   mov    %eax,0x18(%esp)
...
End of assembler dump.
```

```
(gdb) b*0x0804898e
Breakpoint 1 at 0x804898e
(gdb) b*0x08048b02
Breakpoint 2 at 0x8048b02
```

```
(gdb) r
Starting program: /bin/getflag

Breakpoint 1, 0x0804898e in main ()
(gdb) set $eax=0
(gdb) n
Single stepping until exit from function main,
which has no line number information.

Breakpoint 2, 0x08048b02 in main ()
(gdb) set $eax=3014
(gdb) n
Single stepping until exit from function main,
which has no line number information.

Check flag.Here is your token : 7QiHafiNa3HVozsaXkawuYrTstxbpABHD8CPnHJ
0xb7e454d3 in __libc_start_main () from /lib/i386-linux-gnu/libc.so.6

(gdb) q
```

```
level14@SnowCrash:~$ su flag14
Password:7QiHafiNa3HVozsaXkawuYrTstxbpABHD8CPnHJ
Congratulation. Type getflag to get the key and send it to me the owner of this livecd :)
```

```
flag14@SnowCrash:~$ getflag
Check flag.Here is your token : 7QiHafiNa3HVozsaXkawuYrTstxbpABHD8CPnHJ
```

```
flag14@SnowCrash:~$ su level15
Unknown id: level15
```