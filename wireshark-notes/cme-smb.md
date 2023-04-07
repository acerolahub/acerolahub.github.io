# Cme smb

```
cme smb 192.168.56.1/24
ARP request: Who has 192.168.56.0 ? Tell me <IP_source>
ARP answer : It's me <IP_dst>, you can find me on <MAC_dst>

1st attempt
TCP: [SYN,ACK] negociation

Request
SMB > NetBIOS > IPV4 : Negotiate Protocol Request
    Dialect: NT LM 0.12 

When the recipient doesn't support that th<at dialect
TCP: [RST,ACK]
```

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://techcommunity.microsoft.com/t5/storage-at-microsoft/smb-is-dead-long-live-smb/ba-p/1185401" %}



```
2nd attempt
TCP: [SYN,ACK] negociation

Request
SMB > NetBIOS > IPV4 : Negotiate Protocol Request
    Dialects: NT LM 0.12 and SMB 2.xxx

When the recipient found an acceptable dialect, here it's SMB2
SMB2 > NetBIOS > IPv4 : Negotiate Protocol Response
    Signature enabled or not
    Signature required or not
    Dialect: SMB2 wildcard (0x02ff) what does it means ?

```

**0x2FFF SMB2 wildcard** revision number; indicates that the server implements SMB 2.1 or future dialect revisions and expects the client to send a subsequent SMB2 Negotiate request to negotiate the actual SMB 2 Protocol revision to be used. The wildcard revision number is sent only in response to a multi-protocol negotiate request with the "SMB 2.???" dialect string.

{% embed url="https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-smb2/63abf97c-0d09-47e2-88d6-6bfa552949a5" %}

Here the SMB header of the client is SYNC and not ASYNC

{% embed url="https://hatsoffsecurity.com/2018/01/11/smbv2-sync-header-explained/" %}

{% embed url="https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-smb2/fb188936-5050-48d3-b350-dc43059638a4" %}



{% embed url="https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-smb2/e14db7ff-763a-4263-8b10-0c3944f52fc5" %}



{% embed url="https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-smb2/63abf97c-0d09-47e2-88d6-6bfa552949a5" %}

{% embed url="https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-smb2/b39f253e-4963-40df-8dff-2f9040ebbeb1" %}

And because of frame 17, we have this:

{% embed url="https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-smb2/7fd079ca-17e6-4f02-8449-46b606ea289c" %}





{% embed url="https://4sysops.com/archives/the-smb-protocol-all-you-need-to-know/" %}

```

So 2nd negotiation
V2 is cool but what about V3 ?
SMB > NetBIOS > IPV4 : Negotiate Protocol Request
    Dialects: NT LM 0.12 and SMB 2.xxx

When the recipient found an acceptable dialect, here it's SMB2
SMB2 > NetBIOS > IPv4 : Negotiate Protocol Request with a Preauth Hash this time
    Dialects: 
        SMB 2.0.2
        SMB 2.1
        SMB 3.0
SMB2 > NetBIOS > IPv4 : Negotiate Protocol Response
TCP: ACK

```



<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>



![](../.gitbook/assets/image.png)
