Hello World
==============

     SmppClient client = new SmppClient();

     client.Connect("127.0.0.1", 7777);

     if (client.Status == Inetlab.SMPP.Common.ConnectionStatus.Open)
     {
         client.Bind("username", "password", Inetlab.SMPP.Common.ConnectionMode.Transmitter);

         if (client.Status == Inetlab.SMPP.Common.ConnectionStatus.Bound)
         {
             IList<SubmitSmResp> respList = client.Submit(
                 SMS.ForSubmit().From("8888").To("7917123456").Text("Test SMS")
                 );

             if (respList.Count > 0 && respList[0].Status == Inetlab.SMPP.Common.CommandStatus.ESME_ROK)
             {
                 Console.WriteLine("SMS has been sent");
                 foreach (SubmitSmResp resp in respList)
                 {
                     Console.WriteLine("MessageId: " + resp.MessageId);
                 }
             }

             client.UnBind();
         }

         client.Disconnect();
     }


