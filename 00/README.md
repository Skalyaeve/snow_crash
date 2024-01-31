# 00 - Chiffrement affine

- On se connecte en tant que level00, nous cherchons un token (un mot de passe) nous permettant d'ouvrir un terminal en tant que flag00, afin d'exécuter `/bin/getflag`. Donc, tout d'abord, essayons de voir s'il y a quelque chose appartenant à flag00.
```
level00@SnowCrash:~$ find / -user flag00 2>/dev/null | grep -v /proc/*
/usr/sbin/john
/rofs/usr/sbin/john
```

```
level00@SnowCrash:~$ ls -l /usr/sbin/john
----r--r-- 1 flag00 flag00 15 Mar  5  2016 /usr/sbin/john
```

```
level00@SnowCrash:~$ cat /usr/sbin/john
cdiiddwpgswtgt
```


- `/usr/sbin/john` est le seul fichier appartenant à flag00, c'est probablement notre token, essayons:
```
level00@SnowCrash:~$ su flag00
Password:cdiiddwpgswtgt
su: Authentication failure
```


- Ça ne fonctionne pas, le token est enfaite chiffré. Essayons d'utiliser un simple outil de décryptage en ligne. [Dcode.fr](https://www.dcode.fr/cipher-identifier) suggère [un chiffrement affine](https://en.wikipedia.org/wiki/Affine_cipher), et propose `nottoohardhere`.
```
level00@SnowCrash:~$ su flag00
Password:nottoohardhere
Don't forget to launch getflag !
```

```
flag00@SnowCrash:~$ getflag
Check flag.Here is your token : x24ti5gi3x0ol2eh4esiuxias
```

```
flag00@SnowCrash:~$ su level01
Password:x24ti5gi3x0ol2eh4esiuxias
level01@SnowCrash:~$
```
