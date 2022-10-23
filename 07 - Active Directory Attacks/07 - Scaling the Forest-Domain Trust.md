## Domain/Forest Trust Abuse

Domains have trust with other domains within their forest by default. Trust direction goes into the opposite direction to access. That is to say, if Domain Z trusts Domain X, Domain X has access to Domain Z. Transitivity defines whether a domain that trusts another domain inadvertantly trusts their trusted domains.

### Child to Parent Attacks

If you obtain Domain Administrator privileges in a child domain, moving to a parent domain is generally possible. Children domains in forests automatically have a 2-way, transitive trust with the parents.

```powershell
zion.matrix.local <-> matrix.local 
```

The attack uses SID history, which was designed to support migrations between companies. When a TGT is requested in the new domain, their old domains SID is added to the SID history of the account in the new domain. Therefore, creating a golden ticket using the `krbtgt` hash of the child domain controller with the SID of the (Enterprise Admins/Domain Admins) group will grant it those same permissions in the parent domain.

We need the domain SID of the parent domain and the child domain. In the following command, the `/sid` is the current domain and the `/sids` is the target, parent domain group RID. 512 is Domain Admins. Either use the given `/ptt` to pass the ticket into your current session or remove it and replace it with a `/ticket:<location>` to save it.

```powershell
mimikatz !kerberos::golden /user:Administrator /domain:zion.matrix.local /sid:S-1-5-21-3619591028-1129495842-3952564 /sids:S-1-5-21-839412734-235434234-6712353534-512 /aes256:3d4c2156becf2dadddd89ac551efb9d591a8aa90b2fdb13cc820d734c9d76425 /ptt
```
### Inbound Trust 

If we have members in our local domain who are members in the target domain, try to see if they are re-using passwords across domains.

```powershell
Get-DomainForeignGroupMember -Domain matrix.local
```

### Cross Forest Attacks

SID filtering stops any SID with an RID of between 500-1000 being carried over between forests when a trust key is being used. 