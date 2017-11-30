Message Composer: How to combine concatenated messages
======================================================

SMS message with long text is split to small parts (segments). In GSM Standard maximal length of the one short message is 140 bytes. 
Inetlab.SMPP library provides an ability to combine all parts back into full message text. This job does @Inetlab.SMPP.Common.MessageComposer class.

@Inetlab.SMPP.Common.MessageComposer supports all types of PDUs: @Inetlab.SMPP.PDU.SubmitSm, @Inetlab.SMPP.PDU.SubmitMulti, @Inetlab.SMPP.PDU.DeliverSm. 
You should invoke @Inetlab.SMPP.Common.MessageComposer.AddMessage method in each event handler for received PDU. @Inetlab.SMPP.Common.MessageComposer will store PDU in memory and wait for last segment or just raise @Inetlab.SMPP.Common.MessageComposer.evFullMessageReceived event when PDU has no concatenation parameters.

When @Inetlab.SMPP.Common.MessageComposer didn't receive last segment for a long time it raises @Inetlab.SMPP.Common.MessageComposer.evFullMessageTimedout event. Default timeout is 60 secodns.


    private readonly MessageComposer _composer = new MessageComposer();

    _composer.evFullMessageReceived += OnFullMessageReceived;
    _composer.evFullMessageTimedout += OnFullMessageTimedout;

    private void client_evDeliverSm(object sender, DeliverSm data)
    {
       _messageComposer.AddMessage(data);
    }

    private void OnFullMessageTimedout(object sender, MessageEventHandlerArgs args)
    {
        _log.Info(string.Format("Incomplete message received from {0}", args.Parts[0].SourceAddr));
    }

    private void OnFullMessageReceived(object sender, MessageEventHandlerArgs args)
    {
        _log.Info(string.Format("Full message received from {0}: {1}",args.Parts[0].SourceAddr, args.Text));
    }


@Inetlab.SMPP.Common.MessageComposer provides also methods for detecting last segment and getting full message:


    _client.evDeliverSm += (sender, data) =>
               {
                   composer.AddMessage(data);
                   if (composer.IsLastSegment(data))
                   {
                       receivedText = composer.GetFullMessage(data);
                   }
               };
