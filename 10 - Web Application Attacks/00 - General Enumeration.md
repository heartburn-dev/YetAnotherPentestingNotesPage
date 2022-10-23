## Overview

This section is under construction.

### Directory Bustin'

```bash
# Search for words/directories and output file content. Ignore SSL cert. 2 threads. Check for backups of discovered files.
feroxbuster -u https://test.com -w /usr/share/seclists/Discovery/Web-Content/raft-large-words-lowercase.txt -k -t 2 -o ferox.out -B

# Find directories at the requested IP with .aspx appended to the wordlist, ignore 302/404
gobuster dir -u https://10.10.1000.1000/ -w /usr/share/seclists/Discovery/Web-Content/raft-large-words.txt  -o gobuster.out -k -t 5 -x .aspx -b 302,404
```

### Subdomain Hunting

```bash
ffuf -c -u https://FUZZ.test.com -w subdomains.txt -o subdomains.out
```

```bash
assetfinder example.com 
```


### Wordpress

Brute Force
```bash
wpscan --url <url> --passwords <passwordlist> --usernames <username>
```

Standard Enumeration
```bash
wpscan --url <url> --api-token <api-token>
```

Enumerate plugins
```bash
wpscan --url [url] --enumerate [p/vp/ap/t/vt/at] --plugins-detection aggressive
```

Enumerate Users
```bash
wpscan --url [target URL] --enumerate u
```