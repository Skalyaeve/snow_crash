# level01

- We login as user level01.
```
level01@SnowCrash:~$ ls -la
total 12
dr-x------ 1 level01 level01  100 Mar  5  2016 .
d--x--x--x 1 root    users    340 Aug 30  2015 ..
-r-x------ 1 level01 level01  220 Apr  3  2012 .bash_logout
-r-x------ 1 level01 level01 3518 Aug 30  2015 .bashrc
-r-x------ 1 level01 level01  675 Apr  3  2012 .profile
```

```
level01@SnowCrash:~$ find / -user flag01 2>/dev/null | grep -v /proc/*
```


- Nothing interesting has been left to us, but if we look at the content of the file `/etc/passwd`:
```
level01@SnowCrash:~$ cat /etc/passwd
...
level13:x:2013:2013::/home/user/level13:/bin/bash
level14:x:2014:2014::/home/user/level14:/bin/bash
flag00:x:3000:3000::/home/flag/flag00:/bin/bash
flag01:42hDRfypTqqnw:3001:3001::/home/flag/flag01:/bin/bash
flag02:x:3002:3002::/home/flag/flag02:/bin/bash
...
```


- We can see `flag01:42hDRfypTqqnw:3001:3001::/home/flag/flag01:/bin/bash`, maybe `42hDRfypTqqnw` is our token:
```
level01@SnowCrash:~$ su flag01
Password:42hDRfypTqqnw
su: Authentication failure
```


- It doesn't work, the token may be encrypted. [Dcode.fr](https://www.dcode.fr/cipher-identifier) couldn't find the password, so maybe the password is actually hashed. Let's see what [John the Ripper](https://en.wikipedia.org/wiki/John_the_Ripper) thinks about this.
```
$ echo 42hDRfypTqqnw > pass
```

```
$ john --show pass
?:abcdefg

1 password hash cracked, 0 left
```

```
level01@SnowCrash:~$ su flag01
Password:abcdefg
Don't forget to launch getflag !
```

```
flag01@SnowCrash:~$ getflag
Check flag.Here is your token : f2av5il02puano7naaf6adaaf
```

```
flag01@SnowCrash:~$ su level02
Password:f2av5il02puano7naaf6adaaf
level02@SnowCrash:~$
```
