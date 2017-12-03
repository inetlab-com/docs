Getting Started with MM7Client
==============================

MM7 is the interface between MMSC and a value-added service provider (VASP).
The MM7 interface is used to send MMS from 3rd party providers (e.g., a bank sending a statement or an advertiser sending publicity).


Send MMS from SMIL file
-----------------------

     MM7Client client = new MM7Client(url);

     client.AuthId = authId;
     client.AuthSecret = authSecret;
     client.VASPID = vaspid;
     client.VASID = vasid;
     client.SenderAddress = senderAddress;
     client.ServiceCode = serviceCode;


  * **url** - http or https URL of MMSC
  * **authId** - username for Basic authentication on the remote MMS Relay/Server.
  * **authSecret** - password for Basic authentication on the remote MMS Relay/Server.
  * **vaspid** - your identifier as VASP (Value Added Service Provider)
  * **vasid** - identifier of your application
  * **senderAddress** - the address of the message originator.
  * **serviceCode** - information supplied for billing purposes 


    SubmitRequest req = client.CreateSubmitRequest();

    req.Recipients.To.Add("+79171234567");

    req.MessageClass = MessageClass.Personal;
    req.DeliveryReport = true;
    req.Subject = "Test";
    req.Priority = Priorities.Normal;
    req.ChargedParty = ChargedParty.Sender;
    req.ContentHref =  "/mms/mm7/mm7client";
    req.TransactionID = DateTime.Now.Ticks.ToString();

    try
    {
      // Submit MMS Message as VASP
      SubmitResponse resp = client.Submit(smilFilePath, req);
      // Check response status
      if (resp.Status.StatusCode == "1000")
      {
         MessageBox.Show(
             "MMS message has been sent successully",
             "Server response",
              MessageBoxButtons.OK, MessageBoxIcon.Information);
      }
    }
    catch (MM7Exception ex)
    {
       MessageBox.Show(
             ex.Message + Environment.NewLine +
             ex.Status.Details,
             "Server response error",
             MessageBoxButtons.OK, MessageBoxIcon.Error);
    }
    catch (WebException ex)
    {
        MessageBox.Show(
             ex.Message,
             "Server connection error",
             MessageBoxButtons.OK, MessageBoxIcon.Error);
    }
