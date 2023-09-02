# level00

- We login as user level00, we ar looking for a token (a password) allowing us to open a terminal as user flag00, in order to execute the command `/bin/getflag` as user flag00. First, let's try to see if there's something belonging to user flag00.
```
level00@SnowCrash:~$ find / -user flag00 2>/dev/null | grep -v "/proc/*"
/usr/sbin/john
/rofs/usr/sbin/john

level00@SnowCrash:~$ ls -l /usr/sbin/john
----r--r-- 1 flag00 flag00 15 Mar  5  2016 /usr/sbin/john

level00@SnowCrash:~$ cat /usr/sbin/john
cdiiddwpgswtgt
```

- So, `/usr/sbin/john` is the only file belonging to user flag00, it's probably the token, let's try.
```
level00@SnowCrash:~$ su flag00
Password: cdiiddwpgswtgt
su: Authentication failure
```

- It doesn't work, the token is probably encrypted. Let's try using a decryption tool, [dcode.fr](https://www.dcode.fr/cipher-identifier) suggère [un chiffre affine](https://fr.wikipedia.org/wiki/Chiffre_affine) et propose le mot de passe 'nottoohardhere'
```
level00@SnowCrash:~$ su flag00
Password: nottoohardhere
Don't forget to launch getflag !

flag00@SnowCrash:~$ getflag
Check flag.Here is your token : x24ti5gi3x0ol2eh4esiuxias

flag00@SnowCrash:~$ su level01
Password: x24ti5gi3x0ol2eh4esiuxias
level01@SnowCrash:~$
```
