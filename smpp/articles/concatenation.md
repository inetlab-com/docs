Concatenation
==============

Different SMPP providers support different concatenation ways. There are 3 ways:

1) message text in the field **short_message** and concatenation parameters in **user data header**

SMS Builder classes uses this type of concatenation by default. Example how to create SubmitSm instances:
```cs
   var builder = SMS.ForSubmit()
   .From(_config.ShortCode, AddressTON.NetworkSpecific, AddressNPI.Unknown)
   .To(message.PhoneNumber)
   .Text(message.Text);

   
   _client.Submit(builder);
```

Example how to get concatenation parameters from PDU user data header:
```cs
    List<ConcatenatedShortMessages8bit> udh8 = data.UserDataPdu.Headers.Of<ConcatenatedShortMessages8bit>();
    if (udh8 != null && udh8.Count > 0)
    {
       refNumber = udh8[0].ReferenceNumber;
       total = udh8[0].Total;
       seqNum = udh8[0].SequenceNumber;
    }
```

Example how you can manually create SubmitSm instance that contains only one message part with concatenation parameters in user data header:

```cs
    ushort referenceNumber = 512;
    byte totalParts = 2;
    byte partNumber = 1;

    SubmitSm sm = new SubmitSm();
    sm.SourceAddr = "1111";
    sm.SourceAddrTon = 0;
    sm.SourceAddrNpi = 1;
    sm.DestAddr = "79171234567";
    sm.DestAddrTon = 0;
    sm.DestAddrNpi = 1;
    sm.DataCoding = DataCodings.Default;
    sm.RegisteredDelivery = 1;
    sm.UserDataPdu.ShortMessage = _client.GetMessageBytes("Message Text Part", sm.DataCoding);

    sm.UserDataPdu.Headers.Add(new ConcatenatedShortMessage16bit(referenceNumber, totalParts, partNumber));

```


2) message text in the field **short_message** and concatenation parameters in **SAR TLV parameters** (sar_msg_ref_num, sar_total_segments, sar_segment_seqnum, more_messages_to_send)

Example how to create SubmitSm instances with SMS Builder:

```cs
    var builder = SMS.ForSubmit()
    .From(_config.ShortCode, AddressTON.NetworkSpecific, AddressNPI.Unknown)
    .To(message.PhoneNumber)
    .Text(message.Text);

    builder.ConcatenationInSAR();

    _client.Submit(builder);
```


Example how to get concatenation parameters from TLV Parameters:

```cs
    ushort refNumber = 0;
    byte total = 0;
    byte seqNum = 0;

    byte[] tlvGlobalSeq = data.Optional[OptionalTags.SARReferenceNumber];
    if (tlvGlobalSeq != null)
    {
        refNumber = ByteArray.ToUShort(tlvGlobalSeq);
    }
    byte[] tlvPocketsNumber = data.Optional[OptionalTags.SARTotalSegments];
    if (tlvPocketsNumber != null)
    {
        total = tlvPocketsNumber[0];
    }
    byte[] tlvLocalSeq = data.Optional[OptionalTags.SARSequenceNumber];
    if (tlvLocalSeq != null)
    {
        seqNum = tlvLocalSeq[0];
    }
```

3) message text in the TLV parameter **message_payload** and concatenation parameters in **SAR TLV parameters**

Example how to create SubmitSm instances with SMS Builder:

```cs
    var builder = SMS.ForSubmit()
    .From(_config.ShortCode, AddressTON.NetworkSpecific, AddressNPI.Unknown)
    .To(message.PhoneNumber)
    .Text(message.Text);

    builder.MessageInPayload();

    _client.Submit(builder);
```


> Please ask your provider which way they support.



