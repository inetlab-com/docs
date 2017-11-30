Mapping DataCodings to .NET Encoding
====================================



For each @Inetlab.SMPP.SmppClient instance you can define which @System.Text.Encoding will be used for specified @Inetlab.SMPP.Common.DataCodings.

    //Set GSM Packed Encoding for data_coding Latin1 (0x3)
    client.MapEncoding(DataCodings.Latin1, new Inetlab.SMPP.Encodings.GSMPackedEncoding());


By default @Inetlab.SMPP.SmppClient has following @Inetlab.SMPP.Common.DataCodings to @System.Text.Encoding mappings:

    client.MapEncoding(DataCodings.Default, new Inetlab.SMPP.Encodings.GSMEncoding());
    client.MapEncoding(DataCodings.UCS2, System.Text.Encoding.BigEndianUnicode);
    client.MapEncoding(DataCodings.UnicodeFlashSMS, System.Text.Encoding.BigEndianUnicode);
      
Before change mapping settings please clarify what encoding is expected for @Inetlab.SMPP.Common.DataCodings value with SMSC technician.


National Language tables
------------------------

These tables allow using of different character sets in SMS messages. You choose the language by adding User Data Header. 
There is ability to replace standard GSM 7 bit default alphabet table for whole text (*Locking shift table*) or only extension table (*Single shift table*). 
Code bellow shows ways how you can specify desired character set:

    Client.Submit(SMS.ForSubmit()
                     .Text(text).From(sourceAddress).To(phone)
                     .NationalLanguageSingleShift(NationalLanguage.Turkish)
                     );
                     
    Client.Submit(SMS.ForSubmit()
                     .Text(text).From(sourceAddress).To(phone)
                     .NationalLanguageLockingShift(NationalLanguage.Spanish)
                     );

or 

    submitSm.UserDataPdu.Headers.Add(new NationalLanguageSingleShift(NationalLanguage.Spanish));
    
    submitSm.UserDataPdu.Headers.Add(new NationalLanguageLockingShift(NationalLanguage.Turkish));


Library is also able to detect national language User Data Header in received PDU and show text with correct character set within property @Inetlab.SMPP.PDU.SubmitSm.MessageText.


  * [GSM 03.38](http://en.wikipedia.org/wiki/GSM_03.38)
  * GSM 03.38 packed
  * any ASCII encoding in your language.
  * [National language shift tables](http://en.wikipedia.org/wiki/GSM_03.38#National_language_shift_tables)
  * [Data Coding Scheme](https://en.wikipedia.org/wiki/Data_Coding_Scheme)



