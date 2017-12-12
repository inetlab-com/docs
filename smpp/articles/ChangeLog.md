# Changelog

## [1.2.11] - 2017-12-12
### Fixed
- MessageComposer raises evFullMessageReceived sometimes two times by processing concatenated message with two parts.

## [1.2.10] - 2017-12-07
### Changed
- SmppServer returns ESME_RSUBMITFAIL when evClientSubmitSm event has no attached handler.
- SmppServer returns ESME_RBINDFAIL when evClientBind event has no attached handler.

### Fixed
- duplicate ReceiptedMessageId TLV option after serialization of DeliverSm PDU.

## [1.2.9] - 2017-11-19
### Fixed
- ESME_RTHROTTLED for first PDU when setting ReceiveSpeedLimit on SmppServerClient

## [1.2.8] - 2017-11-06
### Fixed
- Unable to reconnect from evConnected event when connection is failed.

## [1.2.7] - 2017-10-30
### Added
- Name property for classes SmppClientBase, SmppServer, that helps to define proper logger name.

## [1.2.6] - 2017-10-04
### Added
- decode receipt for IntermediateDeliveryNotification

## [1.2.5] - 2017-08-12
### Fixed
- ObjectDisposedException: Safe handle has been closed, on Mono platform when connecting to SMPP provider.

## [1.2.4] - 2017-07-03
### Changed
- SmppClientBase.WorkerThreads can be changed at any time. Library starts new threads or stops redundant threads automatically. 

### Fixed
- sequence number generation

Version 1.2.3 13.06.2017
- changed: allow to set MessageFeature property for SubmitSm instance
- fixed: source code building issue

Version 1.2.2 01.06.2017
- fixed: ReceiveSpeedLimit shouldn't be checked for Bind. 

Version 1.2.1 22.05.2017
- fixed: License detection by creating client instance.

Version 1.2 19.05.2017
- added:   new property ReceiveSpeedLimit for SmppServerClient class.  
           It allows to limit number of messages per second for connected client on SmppServer. When interval between request PDU is more than ReceiveSpeedLimit,  client receives response PDU with status ESME_RTHROTTLED.
           Default is 0 that means unlimited number of message.
- added:   new property ReceiveQueueLimit for SmppServerClient class.  
           Client receives response PDU with status ESME_RMSGQFUL, when number of messages in the receive queue for the connected client is more than ReceiveQueueLimit value. 
		   Default is 0 that means unlimited number of message.
- changed: MessageComposer doesn't depend on SmppClientBase anymore. It uses text encoding mapping from first segment PDU. This PDU should be bound with one of the client, 
           otherwise it takes default encoding mapping.

Version 1.1.29.1 14.03.2017
- fixed: Multi-part UCS-2 message with surrogate pairs not working

Version 1.1.29   27.01.2017
- renamed: property Timeout in the class SmppClientBase renamed to ResponseTimeout . Type has been changed from int to TimeSpan. Default is 100 seconds.
- removed: property NeedEnquireLink in the class SmppClientBase.
- renamed: property EnquireInterval in the class SmppClientBase renamed to EnquireLinkInterval. Type has been changed from int to TimeSpan. By default is disabled = TimeSpan.Zero. 
           Set non zero time span in order to activate automatic EnquireLink sending.
- changed: type of property ConnectionTimeout in the class SmppClientBase has been changed to TimeSpan. Default is 20 seconds.
- added:   property InactivityTimeout in the class SmppClientBase. Default is 2 minutes. Connection will be dropped when in specified period of time no PDU was received.
           InactivityTimeout doesn't work when EnquireLinkInterval is defined.
- added:   property SessionInitiationTimeout in the class SmppServer. Default is 20 seconds. 
           Specifies a period of time in which SmppServer will wait for Bind command from SmppClient after establishing a connection. 
- fixed:   EnquiryLink has not been sent with VB.NET version.

Version 1.1.28   23.01.2017
- added: ReceiveBufferSize and SendBufferSize properties for SmppClientBase to change corresponding socket options.
- added: MaxPartSize method has been added to SMS builder classes. It allows to change part size for the message. Standard size is 140 bytes.
- removed: obsolete properties from SmppClient: ShortMessageMaxBytes and UseIPv6

Version 1.1.27.6 10.01.2017
- fixed: System.ArgumentException : An item with the same key has already been added. in EnquireLink 

Version 1.1.27.5 30.12.2016
- fixed: Client stops sending EnquireLink.

Version 1.1.27.4 30.12.2016
- fixed: Obfuscator error

Version 1.1.27.3 28.12.2016
- changed: Method SmppServer.Start throws SmppException when server cannot be started
- fixed: Exception by processing PDU after failed EnquiryLinkResp in SmppServerClient

Version 1.1.27.2 27.12.2016
- fixed: Client stops sending EnquireLink when he simultaneously receives EnquiryLink from remote side. 

Version 1.1.27.1 05.12.2016
- improved: better logging by accepting connection
- fixed: cannot establish SSL connection.

Version 1.1.27 30.11.2016
- improved: extend DataCodings enumerator, EncodingMapper has also new default encodings for all GSM 7bit and UCS2 data codings.
- changed: SystemId in Bind is optional now.

Version 1.1.26.1 26.10.2016
- fixed: ignore SslPolicyErrors.RemoteCertificateNotAvailable on the server side when client connects without certificate. 

Version 1.1.26.0 26.10.2016
- fixed: SslPolicyErrors.RemoteCertificateNameMismatch when connect to the server with correct hostName
- changed: removed RemoteIP and RemotePort properties from SmppClientBase. Use client.RemoteEndPoint instead.
- changed: type of client.RemoteEndPoint to EndPoint. It can be cast to IPEndPoint or HostEndPoint.

Version 1.1.25.0 24.10.2016
- added: EnquiryLinkAsync method and evEnquireLinkComplete event
- added: Remote certificate validation events: SmppServer.evClientCertificateValidation and SmppClient.evServerCertificateValidation;
- fixed: TimeoutException in automatic EnquiryLink when client disconnects
- fixed: missed SMSC Delivery Receipt message type in EmsClass when creating Delivery Receipt with SMS builder.

Version 1.1.24.1 16.10.2016
- changed: output build date of the library

Version 1.1.24.0 09.10.2016
- changed: renamed property SheduleDeliveryTime to ScheduledDeliveryTime
- fixed: worker thread stops when library cannot serialize PDU. Now this PDU will be rejected and error message will be written in log.

Version 1.1.23.2 06.10.2016
- fixed: sometimes LicenseException has been thrown for purchased Developer License.
- fixed: SmppServer doesn't receive client certificate

Version 1.1.23.1 29.09.2016
- changed: parse Receipt for messages that contain SME Delivery Acknowledgment.

Version 1.1.23.0 21.09.2016
- added: allow to set WorkerThreads for receive queue when client is connected to SmppServer.

Version 1.1.22.7 30.07.2016
- improved: send smpp response with status ESME_RSYSERR when unable to parse user data of smpp request.

Version 1.1.22.6 29.06.2016
- improved: added ability to send delivery receipt in MessagePayload parameter.

Version 1.1.22.5 25.06.2016
- fixed: NullRefenceExeption in MessageComposer when try to call GetFullMessage method for sms that didn't receive all parts

Version 1.1.22.4 24.06.2016
- fixed: Delivery Receipt serialization in UCS2 data coding.

Version 1.1.22.3 21.06.2016
- fixed: handle failed EnquireLinkResp

Version 1.1.22.2 06.06.2016
- fixed: NullReferenceException in client certificates.

Version 1.1.22.1 23.05.2016
- fixed: ObjectDisposedException by disconnection of SmppServerClient with enabled EnquireLink

Version 1.1.22.0 13.05.2016
- changed: Responsibility to define Encoding for data conding has been moved to EncodingMapper class
- added: ability to define message reference number in concatenated messages. New methods in the SMS builders: ConcatenationInUDH(byte referenceNumber), ConcatenationInSAR(byte referenceNumber)
- added: ability to display License status with static property LicenseInfo.Status
- fixed: avoid SMPPCLIENT_RCVTIMEOUT when application sends concatenated SubmitSm PDUs with the same sequence number in one SMPP session.
- fixed: Exception when timer sends EnquireLink for disconnecting SmppServerClient.

Version 1.1.21.5 15.11.2015
- fixed: NullReferenceException in MessageComposer

Version 1.1.21.4 15.11.2015
- changed: send RST over TCP connection when remote side times out or unbind
- fixed: NullReferenceException in MessageComposer

Version 1.1.21.3 19.10.2015
- changed: handle Error Code: 10054 Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host.
- improved: SmppClientDemo is able to send SubmitMulti when destination address field contains a list of addresses separated by comma.
- improved: SmppServerDemo now shows how to use MessageComposer on the server side.
- fixed: MessageComposer issue with short sms.

Version 1.1.21.2 25.08.2015
- fixed: serialization issue when ISerializable interface is used

Version 1.1.21.1 15.07.2015
- fixed: SendSpeedLimit multi-threading issue

Version 1.1.21.0 21.06.2015
- changed: MessageComposer depends on SmppClientBase
- fixed: Prepend 'filler' bits to align the user data to septets after UDH 

Version 1.1.20.4 26.05.2015
- added: allow to set LocalEndPoint for SmppClient.

Version 1.1.20.3 22.05.2015
- fixed: Concatenation property is null when ReferenceNumber equals to 0

Version 1.1.20.2 11.05.2015
- improved: smpp socket error handling

Version 1.1.20.1 28.04.2015
- improved: send EnquiryLink request in priority queue 

Version 1.1.20.0 27.04.2015
- fixed: delayed EnquiryLink when client disconnects
- improved: connect disconnect thread-safety
- improved: BatchMonitor 
- improved: Send and receive queue. Response PDU has now priority over Request PDU.  
- changed: throw SmppConnectionException when disconnecting client before connection process is finished, or during connect of the same already connected client 

Version 1.1.19.2 20.04.2015
 - fixed: license expired issue

Version 1.1.19.0 12.04.2015
 - changed: SmppServer.Start method works as old StartAsync. StartAsync method was removed.
 - changed: Rewrite smpp socket. Add thread-safety for Connect and Disconnect methods

Version 1.1.18.4 03.04.2015
 - added: information when expires trial version

Version 1.1.18.3 19.03.2015
 - fixed: EnquiryLink timer issue when sending Bind several times after connection

Version 1.1.18.2 01.03.2015
 - improved: error handling and log output

Version 1.1.18.1 26.02.2015
 - changed: return Fail command status when event handler for incoming PDU request is not attached

Version 1.1.18.0 19.02.2015
 - added: method AddUserDataHeader to SMS builder interfaces.
 - fixed: Binary messages concatenation

Version 1.1.17.3 16.02.2015
 - fixed: DeliverSm serialization

Version 1.1.17.2 26.01.2015
 - fixed: incorrect enumerators output in log under mono
 
Version 1.1.17.1 14.01.2015
 - fixed: wrong CommandStatus output in log

Version 1.1.17.0 04.01.2015
 - added: SmppClientBase.AddressEncoding that defines default encoding for source and destination addresses in PDUs
 - added: SmppClientBase.Tag that can contain data associated with the client

Version 1.1.16.0 07.10.2014
 - added: SmppClientBase.Queue property that returns internal queue state for sent and received PDUs.
 - changed: Process EnquiryLink request and response independently of internal queue.

Version 1.1.15.7 22.08.2014
 - added: logging for disconnection reasons

Version 1.1.15.6 13.08.2014
 - changed: Source and Destination Addresses are encoded with DataCodings.Default.
 - fixed: GSMEncoding GetChars had an issue when buffer index was greater than 0

Version 1.1.15.5 03.07.2014
 - fixed: SMS builder should create one PDU when text is empty 

Version 1.1.15.4 03.06.2014
 - changed: SmppClientBase.RaiseEventsInMainThread default value is false

Version 1.1.15.3 29.05.2014
 - added: client.WorkerThreads property sets a number of worker threads that process received messages.

Version 1.1.15.2 23.04.2014
 - fixed: bug in Connect method.

Version 1.1.15.1 21.04.2014
 - added: ConnectAsync method.
 - changed: SSL protocol can be now enabled with client.EnabledSslProtocols = SslProtocols.Default;
 - fixed: Library doesn't pack message text in encoding GSMPackedEncoding to octests

Version 1.1.14.4 30.03.2014
 - improve Delivery Receipt parser

Version 1.1.14.3 18.02.2014
 - fixed app crash in obfuscated version (Developer and Evaluation)

Version 1.1.14.2 16.02.2014
 - added support of MessageId field in DeliverSmResp

Version 1.1.14.1   15.02.2014
 - improved send queue 
 - added ConsoleLogger
 - fixed memory leak in timeout manager

Version 1.1.14.0   12.02.2014
 - Send Response PDU with priority
 - implement response timeout for async method. 'complete' events are raised when response has not been received in time
 - improve GSM Encoding performance

Version 1.1.12.4 28.11.2013
- remove validation for SystemID in Bind class

Version 1.1.12.3 23.10.2013
- remove validation for source and destination addresses

Version 1.1.12.2 29.09.2013
- fix issues related to Mono 3.2.3 framework.

Version 1.1.12.1 22.08.2013
- Feature. SMS Builder can now send long text in the TLV parameter "message_payload" as one segment

Version 1.1.12.0 18.08.2013
- Feature. MessageComposer now has two new events evFullMessageReceived and evFullMessageTimedout.

Version 1.1.11.2 14.08.2013
- fixed bug with sequence number in EnquireLink

Version 1.1.11.1 21.07.2013
- fixed memory leak in SmppClient

Version 1.1.11 18.07.2013
- Added ability to connect to SmppServer with SSL Client Certificate.

Version 1.1.10 15.07.2013
- Two events evDeliverSmComplete and evDataSmComplete have been removed. Since this version you can send answer PDU to sender. F.i.
  You can send SubmitSm within evDeliverSm event on client side or you can send DeliverSm within evClientSubmitSm event on the server side.
  Response PDU will be sent before any other PDU within event handler method. It means that you can change response status only before sending first Answer PDU.

Version 1.1.9.1 09.06.2013
- added ability to convert user data headers to byte array

Version 1.1.9.0 12.05.2013
- added support of network_error_code TLV parameter in delivery receipt

Version 1.1.8.1 07.05.2013
- fixed issue with message length when send concatenated parts in message_payload field. Maximum length should be 153 characters.

Version 1.1.8.0 27.04.2013
- added Spanish shift table

Version 1.1.7.0 05.03.2013
- Removed global sequence generator. Since this version each SmppClient uses its own sequence generator.
  When PDU created sequense_number is not assigned. When you send PDU, library gets next sequence number from client.SequenceGenerator.NextSequenceNumber().
  You can assign number before sending with same function. It helps to store sequence number for further processing.
- removed password limit in Bind

Version 1.1.6.2 22.11.2012
- removed unused property SmppClient.ServiceType

Version 1.1.6.1 22.11.2012
- added DataCodings 0xF6, 0xF7

Version 1.1.6.0 01.11.2012
- added Tag property that can contain any data to associate with the client.

Version 1.1.5.4 19.10.2012
- improved MessageText property in SubmitSm, SubmitMulti, DeliverSm, DataSm
- add creating Delivery receipt with SMS builder

Version 1.1.5.2 26.09.2012
- added National Language Single Shift support, only for Turkish language
- improved MessageComposer for SubmitMulti and SubmitSm

Version 1.1.4.0 07.09.2012
- solved blocking issue in sending queue when started bulk async submit
- sequence number generator can be assigned for each client

Version 1.1.3.2 30.08.2012
- fixed threading issue in MessageComposer

Version 1.1.3.1 22.08.2012
- process PDUs with vendor specific CommandStatus

Version 1.1.3.0 19.08.2012
- Improved MessageComposer supports multithreading and text in MessagePayload

Version 1.1.2.2 15.08.2012
- Create delivery receipt when server returns DeliverSm without MessageText

Version 1.1.2.1 14.08.2012
- Bypass sending queue for EnquiryLinkResp 

Version 1.1.2.0  23.07.2012
- avoid error message when process incomplete PDU
- fix SubmitMultiResp serializer

Version 1.1.1.1  04.07.2012
- fix bug in EnquiryLink

Version 1.1.1.0   25.06.2012
- added propery SendSpeedLimit that works also for async methods
- added event evDeliverComplete which raised when receive DeliverSmResp from client

Version 1.1.0.0   27.02.2012
- added SMS Builder classes which help to create SMS message parts for further sending
- removed PrepareSubmit, SubmitText methods. Use SMS builder with methods Submit, SubmitAsync, SubmitMulti, SubmitMultiAsync on client side, 
  and Deliver or DeliverAsync on server side.
- now it is possible to determine concatenated messages using Concatenation property 
  in classes SubmitSm, SubmitMulti, DeliverSm, DataSm
- added helper class for compose concatenation messages
- added helper class for tracking batch async message sending.

Version 1.0.12.9   28.05.2012
- store Bind parameters in SmppServerClient

Version 1.0.12.8   24.05.2012
- don't generate MessageId for SubmitSmResp in SmppClient

Version 1.0.12.7   21.05.2012
- added some methods in TLVCollection for different types of value

Version 1.0.12.6   10.05.2012
- fix adding TLV parameter as string

Version 1.0.12.5   02.04.2012
- added workaround to parse delivery receipt with empty values

Version 1.0.12.4   29.03.2012
- fixed concatenation detection for DeliverSm when message in UserData and segment informaion in SAR paramenters.

Version 1.0.12.3   30.01.2012
- fixed Queue is empty exception

Version 1.0.12.2   26.12.2011
- fixed mistake of property name SheduleDeliveryTime
- added method DeliverAsync

Version 1.0.12.1   15.12.2011
- added method Client.GetEncoding 
- improved Delivery Receipt parsing 
- fixed clone method in DeliverSm 

Version 1.0.12.0   14.12.2011
- BindResp SystemId can be modified with method ChangeSystemId
- fixed empty MessageId in QuerySmResp created by QuerySm
- in DeliverSm class added properties ProtocolId, PriorityFlag, RegisteredDelivery
- added ability to clone DeliverSm object

Version 1.0.11.4    06.12.2011
- added setters for some properties in QuerySmResp

Version 1.0.11.3    24.10.2011
- fixed missing question mark in GSMEncoding

Version 1.0.11.2    18.08.2011
- fixed delivery reciept to string

Version 1.0.11.1    15.08.2011
- some improvements in delivery reciept and TLV collection for supporting HLR requests

Version 1.0.11.0    27.07.2011
- changed indexed properties in TLVCollection class. 

Version 1.0.10.2    28.06.2011
- fixed sending EnquireLink from SmppServer

Version 1.0.10.1    24.05.2011
- fixed Mono related issues

Version 1.0.10.0    16.05.2011
- improved Delivery Receipt parsing
- improved received data parsing

Version 1.0.9.1    22.04.2011
- improved error handling

Version 1.0.9.0    11.04.2011
- added Outbind support
- updated DataSm, some properies have setters
- fixed GSM Packed encoding
- fixed bugs in VB.NET version of Demo applications

Version 1.0.8.0    14.03.2011
- added GSM Packed encoding
- fixed DeiliverSm Receipt parsing

Version 1.0.7.5    08.02.2011
- improved connection methods, Dns.GetHostEntry not used when IP address is specified

Version 1.0.7.3    07.02.2011
- fixed bug with connection after disconnect

Version 1.0.7.1    03.02.2011
- fixed BeginWrite problem in SSL connection
- added property RaiseEventsInMainThread for enable/disable running events in Main UI Thread
- added event evServerStarted
- fixed bug with Open client status after BindAsync

Version 1.0.6    24.01.2011
- added workaround for parsing date with 4 digits year in delivery receipt

Version 1.0.5    17.01.2011
- added asynchronous binding

Version 1.0.4    19.12.2010
- added EsmClass property for DeliverSm, DataSmclasses classes. Now you can set directly required value.
- DeliverSm.Receipt property can be used in SMPP server to send delivery receipt
- fixed bug with parsing delivery receipt

Version 1.0.3.1  23.11.2010
- fixed bug in EnquireLink

Version 1.0.3   30.10.2010
- added deliverSm.Receipt property which represents parsed delivery receipt text.
- added new submit mode ShortMessageWithSAR which allows to send user data in the short_message field and concatenation options in TLV paramaters
- added more OptionalTags

Version 1.0.2   26.10.2010
- fixed bug with sar_segment_seqnum

Version 1.0.1   19.09.2010
- Raising SmppException when client is trying to send message to disconnected remote side.

Version 1.0.0   30.07.2010
- First version of completele rewritten SMPP library based on ALT.SMS.SmppClient.


