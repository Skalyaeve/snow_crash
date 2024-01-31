# 01 - Hash DES

- On se connecte en tant que level01.
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


- Rien d'intéressant ne nous a été laissé, mais si nous regardons le contenu du fichier  `/etc/passwd`:
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


- Nous pouvons voir `flag01:42hDRfypTqqnw:3001:3001::/home/flag/flag01:/bin/bash`

- `42hDRfypTqqnw` est notre token, mais il est hashé, du coup:
```
$ echo 42hDRfypTqqnw > pass
```

```
$ john pass
...
Loaded 1 password hash (descrypt, traditional crypt(3) [DES 256/256 AVX2])
...
Proceeding with wordlist:/usr/share/john/password.lst
abcdefg          (?)
...
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
