Version 0.9.2 02.01.2018
- fixed: MM7Client should process SOAP Fault instead throwing of "Unknown server response". 

Version 0.9.1 19.11.2016
- small fixes in demo application

Version 0.9.0 11.03.2016
- added support of Basic and Digest authentication in  MM7Client and MM7Server classes.
- added ability to change ServerName in MM7Server.

Version 0.8.2 07.03.2016
- add ability to get request parameters for the event handlers in standalone MM7Server 

Version 0.8.1 06.03.2016
- new logging classes
- improved MIME classes
- added ability to use Evaluation version as Full version after adding a license file to the project as embedded resource.
  You can update nuget package of the library in your project and use always latest version.

Version 0.7.2 06.06.2014
- fixed missing MM7Version in DeliverRequest

Version 0.7.1 30.10.2013
- added workaround for non-standard error responses

Version 0.7.0 27.08.2012
- added ability to send DRM content

Version 0.6.3 22.08.2012
- addition of DeliveryCondition, ApplicID, ReplyApplicID, AuxApplicInfo, DRMContent fields to SubmitRequest

Version 0.6.2 17.08.2012
- fix threading issue in MIME classes

Version 0.6.1 15.08.2012
- adapt XML serialization to schema 5.3.0

Version 0.6.0 27.07.2012
- added support of DeliveryReport and Read-Reply report 

Version 0.5.12 31.01.2012
- added ability to send SOAPAction HTTP header
- process server responses without Content-Length HTTP header

Version 0.5.11 02.11.2011
- handle GET requests to MMS server

Version 0.5.10 06.09.2011
- improved parsing HTTP headers

Version 0.5.9 16.05.2011
- added methods that helps to send custom DeliverRequest.

Version 0.5.8 06.12.2010
- fixed charset of transfered text file within SMIL message

Version 0.5.7 01.11.2010
- added ability to change MM7Version and MM7 Schema version
- added ContentClass element for SubmitReq

Version 0.5.6 06.09.2010
- added Submit method for sending MMS message from SMIL file

Version 0.5.5 25.08.2010
- adapted to some vendor specific responses
- fixed basic authentication
- for class MM7Client added new method Submit with SubmitRequest parameter

Version 0.5.4 12.04.2010
- adapted to some vendor specific requests

Version 0.5.3 08.04.2010
- added trace of MMSServer

Version 0.5.2 10.09.2009
- fixed From was empty in MMSMessage 

Version 0.5.1 08.09.2009
- fixed TimeStamp parsing

Version 0.5.0 26.08.2009
- added ability to process request in ASHX files

Version 0.4.6 24.08.2009
- fixed server.Stop() bug

Version 0.4.5 05.08.2009
- added ability to change Content-Transfer-Encoding for MMSParts

Version 0.4.4 14.06.2009
- improved MM7Client

Version 0.4.3 16.04.2009
- added support of multipart.mixed MMS messages
- added "Inetlab.MMS.MM7.Switch" to enable detailed information about requests and responses

Version 0.4.2 05.03.2009
- added Timeout property to MM7Client

Version 0.4.1 28.02.2009
- added xml declaration to request and response

Version 0.4 27.02.2009
- fixed SenderAnddress type in the SenderIdentification

Version 0.3 01.10.2008
- added MM7Client.ServiceCode property
- allow to specify Charged Party in the Submit method.