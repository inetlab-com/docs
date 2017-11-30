Getting Started with Inetlab.SMPP
=================================

Connecting to SMPP server
-------------------------

For connecting to SMPP server you need to get username and password from your mobile network operator.

    SmppClient client = new SmppClient();
    client.Connect("smpp.server.com", 7777);
    if (client.Status == ConnectionStatus.Open) 
    {
        _log.Info("Connected to SMPP server");
        client.Bind("username", "password", ConnectionMode.Transceiver);
        if (client.Status == ConnectionStatus.Bound) 
        { 
            _log.Info("Bound with SMPP server");
        }
    }          

In @Inetlab.SMPP.PDU.Bind method you can specify @Inetlab.SMPP.Common.ConnectionMode. There are 3 modes you can use:
  - @Inetlab.SMPP.Common.ConnectionMode.Transmitter - allows to send short messages to the SMSC and to receive the corresponding SMPP responses from the SMSC
  - @Inetlab.SMPP.Common.ConnectionMode.Receiver - allows to send short messages to receive short messages from the SMSC and to return the corresponding SMPP message responses to the SMSC
  - @Inetlab.SMPP.Common.ConnectionMode.Transceiver - allows to send messages to the SMSC and receive messages from the SMSC over a single SMPP session.

Default ConnectionMode is @Inetlab.SMPP.Common.ConnectionMode.Transceiver.

If you use same SMPP account in several applications you must bind only one application in ConnectionMode = @Inetlab.SMPP.Common.ConnectionMode.Receiver or @Inetlab.SMPP.Common.ConnectionMode.Transceiver.
And other applications should bind with @Inetlab.SMPP.Common.ConnectionMode.Transmitter mode. Otherwise SMPP server can deliver messages to any application where you don't expect.

Sending SMS message
-------------------

For sending simple SMS message from your short code **1111** to the phone number **79171234567** and with required delivery receipt you can use fluent PDU builder:

    IList<SubmitSmResp> responses = client.Submit(
          SMS.ForSubmit()
          .Text("Test Test Test Test Test Test Test Test Test Test")
          .From("1111")
          .To("79171234567")
          .DeliveryReceipt()
         );

or the same message with old style:

    SubmitSm sm = new SubmitSm();
    sm.UserDataPdu.ShortMessage = Client.GetMessageBytes("Test Test Test Test Test Test Test Test Test Test", DataCodings.Default);
    sm.SourceAddr = "1111";
    sm.SourceAddrTon = 0;
    sm.SourceAddrNpi = 1;
    sm.DestAddr = "79171234567";
    sm.DestAddrTon = 0;
    sm.DestAddrNpi = 1;
    sm.DataCoding = DataCodings.Default;
    sm.RegisteredDelivery = 1;

    SubmitSmResp response = client.Submit(sm);

There is only one difference. First code allows you to send text with any length, but second code only up to 160 symbols. First code returns list of responses because it will split long message text on smaller concatenated parts.

Receiving SMS messages
----------------------

When client is bound with SMPP server in @Inetlab.SMPP.Common.ConnectionMode.Transceiver or @Inetlab.SMPP.Common.ConnectionMode.Receiver mode it will be able to receive SMS messages. Every time when SMPP client receives SMS message it raises @Inetlab.SMPP.SmppClient.evDeliverSm event. Event handler method should be attached before bind to server.

    client.evDeliverSm += new DeliverSmEventHandler(client_evDeliverSm);

Your code should be able to receive concatenated long SMS messages. @Inetlab.SMPP.Common.MessageComposer class helps to combine them and get full message text.
@DeliveryReceipt can also be received with this handler method.

    private readonly MessageComposer _composer = new MessageComposer(client);

    private void client_evDeliverSm(object sender, DeliverSm data)
    {
       try
       {
          //Check if we received Delivery Receipt
          if (data.MessageType == MessageTypes.SMSCDeliveryReceipt)
          {
              //Get MessageId of delivered message
              string messageId = data.Receipt.MessageId;
              MessageState deliveryStatus = data.Receipt.State;
          }
          else
          {
              // Receive incoming message and try to concatenate all parts
              if (data.Concatenation != null)
              {
                 _composer.AddMessage(data);

                 _log.Info(string.Format(
                      "DeliverSm part received : Sequence : {0} SourceAddr : {1}"+
                      " Concatenation ( {2} ) Coding : {3} Text : {4}",
                      data.Sequence, data.SourceAddr, data.Concatenation, data.DataCoding, data.MessageText));


                 if (_composer.IsLastSegment(data))
                 {
                     string fullMessage = _composer.GetFullMessage(data);
                     _log.Info("Full message: " + fullMessage);
                 }
              } 
              else  
              {
                  _log.Info("DeliverSm received : "
                        + " Sequence : " + data.Sequence
                        + " SourceAddr : " + data.SourceAddr
                        + " Coding : " + data.DataCoding
                        + " MessageText : " + data.MessageText);
              }
          }
       } 
       catch (Exception ex)
       {
          data.Response.Status = CommandStatus.ESME_RX_T_APPN;
          _log.Error("Failed to process DeliverSm", ex);
       }
    }

Possible reasons why you don't receive incoming messages:

  - SMPP account doesn't have right to receive SMS messages.
  - Wrong SMS routing configuration on SMPP server.
  - SMPP client has been bound as Transmitter.
  - SMPP client was not attached to evDeliverSm event handler.
  - SMPP account is used in two or more application. SMSC sends messages to application where @Inetlab.SMPP.PDU.DeliverSm is not expected.


