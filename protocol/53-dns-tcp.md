# 53 - dns TCP

Usually 53 is used for UDP DNS.

When it's used for TCP DNS, a zone transfer might be possible

nslookup \[-type=(A | PTR)] \[host] server # return a domain name if host=IP and we query A or PTR record

{% embed url="https://www.hostinger.fr/tutoriels/ptr-reverse-ip-lookup" %}

Domain Zone transfer

dig axfr @\<SERVER\_DNS> \<DOMAIN\_NAME>

