SMPP Client FAQ
===============

#### If I use SendAsync(), how do I know which DR should be matched with each sent SMS?

@Inetlab.SMPP.PDU.SubmitSmResp can be processed in @Inetlab.SMPP.SmppClient.evSubmitComplete event handler, where you know sequence number from @Inetlab.SMPP.PDU.SubmitSm and you get @Inetlab.SMPP.PDU.SubmitSmResp.MessageId for future Delivery receipt.
   
#### To send long SMS, is it handled internally (split into multiple part-SMS) in you code or must I handle UDH manually?  

Text will be split automatically when you use SMS builders. Following example covers most usage scenarios
 
    Client.Submit(SMS.ForSubmit()
         .ServiceType("test")
         .Text("Test Test Test Test Test Test Test Test Test Test")
         .From("1111")
         .To("2222")
         .DeliveryReceipt()
         .Coding(DataCodings.Default)
         );
     
#### FlashSMS, must I set this using DataCoding or do you have a Property I have not yet found?

In order to send Flash SMS you need to specify one of the following data coding in the @Inetlab.SMPP.PDU.SubmitSm class:
@Inetlab.SMPP.Common.DataCodings.UnicodeFlashSMS, @Inetlab.SMPP.Common.DataCodings.DefaultFlashSMS

#### How do I get hold of the sequence number, when doing Client.SubmitAsync()

@Inetlab.SMPP.SMS Builder has @Inetlab.SMPP.Builders.IBuilder.Create method that returns @Inetlab.SMPP.PDU.SubmitSm list with sequence numbers set to 0.
You can assign next number from the @Inetlab.SMPP.Common.SequenceGenerator and pass this PDU list to @Inetlab.SMPP.SmppClient.SubmitAsync(System.Collections.Generic.IList{Inetlab.SMPP.PDU.SubmitSm}) method.


```cs
 IList<SubmitSm> list = SMS.ForSubmit()
                .From(_config.ShortCode, AddressTON.NetworkSpecific, AddressNPI.Unknown)
                .To(message.PhoneNumber)
                .Text(message.Text)
                .DeliveryReceipt()
                .Set(s=>s.Tag = "myId")
                .Create(_client);

            foreach (SubmitSm sm in list)
            {
                sm.Sequence = _client.SequenceGenerator.NextSequenceNumber();
            }

            _client.SubmitAsync(list);
```

#### How to handle Throttling error from SMSC

SMSC can limit number of submitted PDU for SMPP account. When allowed message limit exceeded, server returns status @Inetlab.SMPP.Common.CommandStatus.ESME_RTHROTTLED.
To avoid throttling error you can specify number of messages per second in @Inetlab.SMPP.SmppClient. For this purpose you can set property @Inetlab.SMPP.Common.SmppClientBase.SendSpeedLimit

    //Send 10 messages per second
    client.SendSpeedLimit = 10;

    //Send 1 message every 5 seconds
    client.SendSpeedLimit = 1f/5f;
 
#### Example: Read messages from database and send them as fast as possible

    var messageList = _query.GetUnsentMessages();

    foreach (var message in messageList)
    {
        var pduBuilder = SMS.ForSubmit()
                        .From(_config.ShortCode)
                        .To(message.PhoneNumber)
                        .Text(message.Text);
                        
        foreach (var pdu in pduBuilder.Create(_client))
        {
            _client.SubmitAsync(pdu);
            _repository.StoreSequenceNumber(message.Id, pdu.Sequence);
        }
    }


#### How to create SubmitMulti PDUs for multiply receivers

    var pduBuilder = SMS.ForSubmitMulti()
                     .From(_config.ShortCode)
                     .Text(message.Text);
                        
    foreach (string phoneNumber in message.PhoneNumbers) {
       pduBuilder.To(phoneNumber);
    }
     
    IList<SubmitMulti> pduList = pduBuilder.Create(_client);
