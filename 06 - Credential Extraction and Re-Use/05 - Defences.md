## Defending Against Credential Attacks

### Windows Defender Credential Guard

With Windows Defender Credential Guard in place, LSASS cannot be read from or written to. This stops attacks such as pass-the-hash and over-pass-the-hash. However, it does only protect LSASS and not the SAM hive, not service account credentials being stored. Furthermore, this cannot be enabled on a Domain Controller!

### Protected Users Groups

Members of the protected user group have stronger defences in regard to credential theft attacks. The users in this group cannot use CredSSP or WDigest, resulting in cleartext credentials not being stored anywhere. Their NTLM is not cached in LSASS. Furthermore, Kerberos does not allow them to use anything but AES keys for their secret.