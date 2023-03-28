# Page 4

Get a Kerberos ticket

```bash
getTGT.py <domain_name>/<username>:<password>
export KRB5CCNAME=$(pwd)/<username>.ccache # set the TGT ticket
smbclient.py -k @<computer_name>
```

How does **getTGT** works ?

How does **smbclient.py** works ?



You can unset the ticket by doing `unset KRBCCNAME`



```
// Some code
```



