# 02 - Analyse réseau

- On se connecte en tant que level02.
```
level02@SnowCrash:~$ ls -l
total 12
----r--r-- 1 flag02 level02 8302 Aug 30  2015 level02.pcap
```


- Un fichier [pcap](https://en.wikipedia.org/wiki/Pcap) nous a été laissé. En l'examinant avec [Wireshark](https://en.wikipedia.org/wiki/Wireshark), nous pouvons voir qu'un paquet contient le mot `Password:`, et le paquet qui suit contient ceci:
>`f t _ w a n d r 'DEL' 'DEL' 'DEL' N D R e l 'DEL' L 0 L 'ENTER'`

- `ft_waNDReL0L` est peut-être notre token.
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