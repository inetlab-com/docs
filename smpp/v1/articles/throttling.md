Throttling error
===============


SMSC can limit number of submitted PDU for SMPP account. When allowed message limit exceeded, server returns status @Inetlab.SMPP.Common.CommandStatus.ESME_RTHROTTLED. 
To avoid throttling error you can specify number of messages per second in @Inetlab.SMPP.SmppClient. For this purpose you can set property @Inetlab.SMPP.Common.SmppClientBase.SendSpeedLimit

    //Send 10 messages per second
    client.SendSpeedLimit = 10;

    //Send 1 message every 5 seconds
    client.SendSpeedLimit = 1f/5f;
