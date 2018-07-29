Hello World
==============

        public static async Task SendHelloWorld()
        {
            using (SmppClient client = new SmppClient())
            {
                if (await client.Connect(new DnsEndPoint("localhost", 7777, AddressFamily.InterNetwork)))
                {
                    BindResp bindResp = await client.Bind("1", "2");

                    if (bindResp.Header.Status == CommandStatus.ESME_ROK)
                    {
                       var submitResp = await client.Submit(
                            SMS.ForSubmit()
                                .From("111")
                                .To("222")
                                .Coding(DataCodings.UCS2)
                                .Text("Hello World!"));

                        if (submitResp.All(x => x.Header.Status == CommandStatus.ESME_ROK))
                        {
                            client.Logger.Info("Message has been sent.");
                        }
                    }

                    await client.Disconnect();
                }
            }
        }