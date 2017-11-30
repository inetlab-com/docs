===== Getting Started with MM7Server =====

==== HTTP Handler in ASP.NET application ====

In order to receive MMS messages from MMSC, you have to create mms.ashx file in your ASP.NET application with content bellow.
//MM7Server// has several events that will raise as soon as appropriate MM7 message received.
In event handlers you can save content or do any other processing.
After adding event handlers you need to invoke //ProcessRequest// method, that is doing all magic with requests from MMSC and raises events.

<code csharp>
public class mms : IHttpHandler {
    
    public void ProcessRequest (HttpContext context) 
    {
        Inetlab.MMS.MM7Server server = new Inetlab.MMS.MM7Server();
        server.DeliverRequest += new Inetlab.MMS.MM7.DeliverEventHandler(server_DeliverRequest);
        server.DeliveryReport += new Inetlab.MMS.MM7.SubmitEventHandler(server_DeliveryReport);
        server.ProcessRequest(context);
    }

    void server_DeliverRequest(object sender, Inetlab.MMS.MM7.DeliverReqEventArgs e)
    {
        // Save all MMS message parts to the DeliveredMMS folder
        string path = Path.Combine("c:\\tmp\\DeliveredMMS", e.Message.Header.TransactionId);
        Directory.CreateDirectory(path);
        foreach (MMSPart part in e.Message.Parts)
        {
            part.Save(Path.Combine(path, part.GetFileName()));
        }
    }
    
    void server_DeliveryReport(object sender, Inetlab.MMS.MM7.DeliveryReportRequestEventArgs e)
    {
        if(e.Request.MMStatus == DeliveryStatus.Retrieved)
        {
            //MMS Message was retrieved
        }
    }


    public bool IsReusable {
        get { return false; }
    }

}
</code>
