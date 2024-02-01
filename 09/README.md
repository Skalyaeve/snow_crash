# 09 - Chiffrement de César

- On se connecte en tant que level09.
```
level09@SnowCrash:~$ ls -l
total 12
-rwsr-sr-x 1 flag09 level09 7640 Mar  5  2016 level09
----r--r-- 1 flag09 level09   26 Mar  5  2016 token
```

```
level09@SnowCrash:~$ ./level09
You need to provied only one arg.
```

```
level09@SnowCrash:~$ ./level09 token
tpmhr
```

```
level09@SnowCrash:~$ cat token
f4kmm6p|=pnDBDu{
```

```
level09@SnowCrash:~$ ./level09 aaaa
abcd
```


- Nous pouvons accéder au contenu du token, mais il est crypté. Lorsqu'on exécutons le binaire level09 avec `aaaa` en paramètre, nous obtenons `abcd`. Peut-être que le contenu du token à été crypté par le binaire.


- L'encryption semble assez simple, au premier abord il semble que chaque caractère de la chaîne résultante soit la somme de sa `valeur ASCII` + `sa position dans la chaîne`.


- Faisons la substitution pour chaque caractère du token:

| Caractère | ASCII | Position | Résultat | Résultat |
|-------|-------|-------|-------|-------|
| f     | 102   | 0     | 102   | f     |
| 4     | 52    | 1     | 51    | 3     |
| k     | 107   | 2     | 105   | i     |
| m     | 109   | 3     | 106   | j     |
| m     | 109   | 4     | 105   | i     |
| 6     | 54    | 5     | 49    | 1     |
| p     | 112   | 6     | 106   | j     |
| \|    | 124   | 7     | 117   | u     |
| =     | 61    | 8     | 53    | 5     |
| \x82  | 130   | 9     | 121   | y     |
| \x7f  | 127   | 10    | 117   | u     |
| p     | 112   | 11    | 101   | e     |
| \x82  | 130   | 12    | 118   | v     |
| n     | 110   | 13    | 97    | a     |
| \x83  | 131   | 14    | 117   | u     |
| \x82  | 130   | 15    | 115   | s     |
| D     | 68    | 16    | 52    | 4     |
| B     | 66    | 17    | 49    | 1     |
| \x83  | 131   | 18    | 113   | q     |
| D     | 68    | 19    | 49    | 1     |
| u     | 117   | 20    | 97    | a     |
| {     | 123   | 21    | 102   | f     |
| \x7f  | 127   | 22    | 105   | i     |
| \x8c  | 140   | 23    | 117   | u     |
| \x89  | 137   | 24    | 113   | q     |


- On obtient `f3iji1ju5yuevaus41q1afiuq`.
```
level09@SnowCrash:~$ su flag09
Password:f3iji1ju5yuevaus41q1afiuq
Don't forget to launch getflag !
```

```
flag09@SnowCrash:~$ getflag
Check flag.Here is your token : s5cAJpM8ev6XHw998pRWG728z
```

```
flag09@SnowCrash:~$ su level10
Password:s5cAJpM8ev6XHw998pRWG728z
level10@SnowCrash:~$
```