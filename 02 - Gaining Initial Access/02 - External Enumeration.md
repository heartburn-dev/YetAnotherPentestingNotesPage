## External Enumeration 

This is all about mapping the external attack surface. This section is under construction and will be completed at a later date.

The primary tool I personally use is Nmap, though alternatives such as MassScan and RustScan are available that are supposedly exponentially quicker. I cannot verify this but it makes sense since Nmap is single-threaded.

### SNMP 

```bash
sudo apt-get install snmp-mibs-downloader
sudo download-mibs
snmpwalk -v 1 -c public <IP> NET-SNMP-EXTEND-MIB::nsExtendOutputFull
```