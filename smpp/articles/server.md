SMPP Server FAQ
================

How to send message to connected client
---------------------------------------

In following code target client is selected and @Inetlab.SMPP.PDU.DeliverSm message is sent to this client.

  
    SmppServerClient client = server.ConnectedClients[0];
    
    client.Deliver(SMS.ForDeliver()
              .From(message.PhoneNumber)
              .To(message.ClientNumber)
              .Text(message.Text)
              );
  

In production environment client selection will be more smart than array index of @Inetlab.SMPP.SmppServer.ConnectedClients.

You can also use asynchronous method @Inetlab.SMPP.Common.SmppServerClient.DeliverAsync(Inetlab.SMPP.PDU.DeliverSm) and event @Inetlab.SMPP.Common.SmppServerClient.evDeliverComplete which should be hooked up to the handler method when client bound with the server.

Example of how to send messages out from the server on a bind
-------------------------------------------------------------

    Server.evClientBind += (sender, client, pdu) =>
       {
          if (ClientAllowed(client, pdu))
          {
              //Check if bound client can receive messages
              if (client.BindingMode == ConnectionMode.Transceiver || client.BindingMode == ConnectionMode.Receiver)
              {
                  //Start messages delivery
                  ThreadPool.QueueUserWorkItem(state => DeliverMessages(client, pdu));


                  //Start sending delivery reciepts
                  ThreadPool.QueueUserWorkItem(state => DeliverReceipts(client, pdu));
              }
          } else {
              pdu.Response.Status = CommandStatus.ESME_RBINDFAIL;
          }
       };
       

      private void DeliverMessages(SmppServerClient client, Bind pdu)
      {
          var messages = _query.GetMessagesForClient(pdu.SystemId, pdu.SystemType);
          foreach (var message in messages)
          {
              var pduBuilder = SMS.ForDeliver()
                  .From(message.PhoneNumber)
                  .To(message.ClientNumber)
                  .Text(message.Text);

              foreach (var deliverPdu in pduBuilder.Create(client))
              {
                  client.DeliverAsync(deliverPdu);
              }
          }
      }
       
     private void DeliverReceipts(SmppServerClient client, Bind pdu)
     {
         var messages = _query.GetDeliveryReceiptsForClient(pdu.SystemId, pdu.SystemType);

         foreach (var message in messages)
         {
             client.DeliverAsync(
                          SMS.ForDeliver()
                         .From(message.SourceAddr, message.SourceAddrTon, message.SourceAddrNpi)
                         .To(message.DestAddr, message.DestAddrTon, message.DestAddrNpi)
                         .Receipt(new Receipt
                                  {
                                      DoneDate = DateTime.Now,
                                      State = MessageState.Delivered,
                                      MessageId = message.MessageId,
                                      ErrorCode = "0",
                                      SubmitDate = message.SubmitDate,
                                      Text = message.Text
                                  })
                         );

        }
     }
