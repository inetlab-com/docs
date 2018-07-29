---
uid: DeliveryReceipt
---

Delivery Receipt
================

Receipt format
---------------

Often you want to get delivery status for SMS message. SMPP protocol provides ability to request delivery receipt in submitted PDU. There are two ways how you can do it with the component.

```cs
submitSm.RegisteredDelivery = 1;
``` 

or

```cs
   IList<SubmitSmResp> responses = client.Submit(
         SMS.ForSubmit()
         .Text("Test").From("1111").To("79171234567")
         .DeliveryReceipt()
        );
```


As the result SMPP server will deliver receipt to client application. On the client side it can be received using @Inetlab.SMPP.SmppClient.evDeliverSm event. Delivery receipt format is SMSC vendor specific, but typical format is

**id:IIIIIIIIII sub:SSS dlvrd:DDD submit date:YYMMDDhhmm done date:YYMMDDhhmm stat:DDDDDDD err:E Text: . . . . . . . . .**

In the library this short message text is represented as @Inetlab.SMPP.Common.Receipt class.

It has following properties:

**MessageId** - The message ID allocated to the message by the SMSC when originally submitted. You can get it from @Inetlab.SMPP.PDU.SubmitSmResp or @Inetlab.SMPP.PDU.SubmitMultiResp.

**Submitted** - Number of short messages originally submitted. This is only relevant when the original message was submitted to a distribution list within @Inetlab.SMPP.PDU.SubmitMulti.

**Delivered** - Number of short messages delivered to distribution list with SubmitMulti.

**SubmitDate** - The time and date at which the short message was submitted.

**DoneDate** - The time and date at which the short message reached it’s final state.

**ErrorCode** - Network specific error code or an SMSC error code for the attempted delivery of the message.

**Text** - The first 20 characters of the short message.

**State** - The final status of the message. The value could be on of the following.

| State         | Description
|---------------|------------------------------------
| Delivered     |Message is delivered to destination 
| Expired       |Message validity period has expired 
| Deleted       |Message has been deleted 
| Undeliverable |Message is undeliverable 
| Accepted      |Message is in accepted state (i.e. has been manually read on behalf of the subscriber by customer service) 
| Unknown       |Message is in invalid state 
| Rejected      |Message is in a rejected state 


How to connect submitted message with delivery receipt
------------------------------------------------------

SMS message in SMPP protocol actually is represented as one or many PDUs. When text is longer that 140 octets library sends text as concatenated SMS parts (PDU). One part can be represented as @Inetlab.SMPP.PDU.SubmitSm class or @Inetlab.SMPP.PDU.SubmitMulti class.

Before sending @Inetlab.SMPP.PDU.SubmitSm or @Inetlab.SMPP.PDU.SubmitMulti PDU you need to assign @Inetlab.SMPP.PDU.SmppPDU.Sequence number to it.

```cs
IList<SubmitSm> list = SMS.ForSubmit()
                .From(_config.ShortCode, AddressTON.NetworkSpecific, AddressNPI.Unknown)
                .To(message.PhoneNumber)
                .Text(message.Text)
                .DeliveryReceipt()
                .Create(_client);

            foreach (SubmitSm sm in list)
            {
                sm.Sequence = _client.SequenceGenerator.NextSequenceNumber();
                SaveSequenceForMessage(message.Id, sm.Sequence);
            }

            _client.SubmitAsync(list);
```

At the same time you need to store *sm.Sequence* to the database. For one *message.Id* you need to store several *sm.Sequence*.

In response to @Inetlab.SMPP.PDU.SubmitSm PDU your application receives @Inetlab.SMPP.PDU.SubmitSmResp PDU in the event Inetlab.SMPP.SmppClient.evSubmitComplete. For @Inetlab.SMPP.PDU.SubmitMulti PDU the event @Inetlab.SMPP.SmppClient.evSubmitMultiComplete should be used.
In response PDU you receive same @Inetlab.SMPP.PDU.SmppPDU.Sequence and @Inetlab.SMPP.PDU.SubmitSmResp.MessageId that server has generated for it. 

```cs
        private void OnSubmitComplete(object sender, SubmitSmResp data)
        {
            SaveServerMessageId(data.Sequence, data.MessageId);
        }
```


When you receive delivery receipt in the event @Inetlab.SMPP.SmppClient.evDeliverSm,  server sends same @Inetlab.SMPP.Common.Receipt.MessageId which you can use for updating status of submitted SMS text.

```cs
        private void OnEvDeliverSm(object sender, DeliverSm data)
        {
            if (data.Receipt != null)
            {
                ChangeMessageStatus(data.Receipt.MessageId, data.Receipt.State);
            }
        }
```

SMS Text is considered as delivered when all sms parts are in delivered state.


For this purpose you can create 2 tables in the database. 

1. for all outgoing SMS messages

|**outgoing_messages**  | field description 
|-----------------------|------------------------------
| *messageId*           |id of the message 
| *messageText*         |long message text

2. for all PDUs generated for each message

|**outgoing_message_parts** | field description
|---------------------------|----------------------------------------
| *messageId*               | reference to messageId field in the *outgoing_messages* 
| *sessionId*               | any unique id generated when SmppClient connects to the server. sequenceNumber is unique only in one SMPP session. 
| *sequenceNumber*          | number generated before sending PDU 
| *serverMessageId*         | message id received from the server. 
| *status*                  | status received in the delivery receipt 




