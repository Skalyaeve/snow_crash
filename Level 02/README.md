# Level 02

- We login as user level02.
```
level02@SnowCrash:~$ ls -l
total 12
----r--r-- 1 flag02 level02 8302 Aug 30  2015 level02.pcap
```


- [A pcap file](https://en.wikipedia.org/wiki/Pcap) was left for us. Upon examining it with [Wireshark](https://en.wikipedia.org/wiki/Wireshark), we can see that a packet contains the characters `Password:`. The packets that follow contain the following keyboard inputs.
>`f t _ w a n d r 'DEL' 'DEL' 'DEL' N D R e l 'DEL' L 0 L 'ENTER'`


- `ft_waNDReL0L` might be our token to flag02 user.
```
level02@SnowCrash:~$ su flag02
Password:ft_waNDReL0L
Don't forget to launch getflag !
```

```
flag02@SnowCrash:~$ getflag
Check flag.Here is your token : kooda2puivaav1idi4f57q8iq
```

```
flag02@SnowCrash:~$ su level03
Password:kooda2puivaav1idi4f57q8iq
level03@SnowCrash:~$
```