SubmitMulti. Send message to multiple destinations
==================================================

The @Inetlab.SMPP.PDU.SubmitMulti command is used to submit SMPP message for delivery to multiple
recipients or to one or more Distribution Lists. 

Recipients can be specified with multiple invocation of method @Inetlab.SMPP.Builders.ISubmitMultiBuilder.To(System.String)

    Client.SubmitAsync(SMS.ForSubmitMulti()
                 .ServiceType("test")
                 .Text("Test Test")
                 .From("MyService")
                 .To("1111")
                 .To("2222")
                 .To("3333")
                 );

this can be done from phone numbers collection


    var pduBuilder = SMS.ForSubmitMulti()
                    .ServiceType("test")
                    .Text("Test Test")
                    .From("MyService");
                    
    foreach (string phoneNumber in phoneNumbers)
    {
        pduBuilder.To(phoneNumber);
    }



another possibility is to create @Inetlab.SMPP.Common.DestinationAddress list

    List<DestinationAddress> destList = new List<DestinationAddress>();

    destList.Add(new DestinationAddress("11111111111", 0, 1));
    destList.Add(new DestinationAddress("22222222222", 0, 1));

    IList<SubmitMultiResp> submitResponses = client.Submit(SMS.ForSubmitMulti()
       .ServiceType("test")
       .Text("Test Test")
       .From("MyService")
       .ToDestinations(destList)
    );


@Inetlab.SMPP.PDU.SubmitMultiResp can be received as return value in synchronous method @Inetlab.SMPP.SmppClient.Submit(Inetlab.SMPP.PDU.SubmitMulti) and with event @Inetlab.SMPP.SmppClient.evSubmitMultiCompleted.
When response received it means SMPP server stored message for further delivery to recipient.

@Inetlab.SMPP.PDU.SubmitMulti message for destination address is accepted by SMPP server only when you receive @Inetlab.SMPP.Common.CommandStatus.ESME_ROK in all responses in then result list IList<@Inetlab.SMPP.PDU.SubmitMultiResp>
and destination address does not exist in @Inetlab.SMPP.PDU.SubmitMultiResp.UnsuccessAddresses of response.  



