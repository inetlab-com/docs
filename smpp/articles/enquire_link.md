Enquire Link
============

@Inetlab.SMPP.PDU.EnquireLink is SMPP command that used to check communication between ESME and SMSC.

To enable this feature you need to set following property:

```cs
    _client.EnquireLinkInterval = TimeSpan.FromSeconds(30);     
```

@Inetlab.SMPP.Common.SmppClientBase.EnquireLinkInterval defines a delay after last received PDU. It means that library won't send EnquireLink when early it receives any other PDU from remote side.