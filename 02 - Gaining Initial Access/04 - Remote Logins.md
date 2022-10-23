## Remote Login Methods

Often, we'll obtain credentials and have to use them to login to a remote Windows target. There are multiple ways to perform this, but the following are the ones I've used primarily in labs. 

### Impacket

```bash
# Login with an NTLM hash
impacket-psexec 'matrix/neo@10.10.10.10' -hashes aad3b435b51404eeaad3b435b51404ee:669b12a3bac275251170afbe2c5de8c2

# Use WMI to login with credentials
impacket-wmiexec 'matrix/neo:Passwr0rd1@10.10.10.10' 

# Use smb to login as a local user with a hash
impacket-smbexec 'matrix/neo@10.10.10.10' -hashes aad3b435b51404eeaad3b435b51404ee:669b12a3bac275251170afbe2c5de8c2
```

### Evil Winrm

```bash
proxychains evil-winrm -i 10.10.10.10 -u Administrator -p "Passw0rd"
```

### xfreerdp 

```bash
xfreerdp /u:Neo /p:Passowrd! /v:10.10.10.10 /cert-ignore
```