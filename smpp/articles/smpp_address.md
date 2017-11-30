SMPP Address
============

SMPP Address (SME Address) is comprised of 3 parameters: **Address**, **TON**, **NPI**.

**Address** is text field that represents originator and/or recipient of the message.

**TON** defines Type of Number 

|Name              | Value
|------------------|---
|Unknown           |0
|International     |1
|National          |2
|Network Specific  |3
|Subscriber Number |4
|Alphanumeric      |5
|Abbreviated       |6

**NPI** defines Numeric Plan Indicator

|Name               | Value
|-------------------|-------
|Unknown            |0
|ISDN (E163/E164)   |1
|Data (X.121)       |3
|Telex (F.69)       |4
|Land Mobile (E.212)|6
|National           |8
|Private            |9
|ERMES              |10
|Internet (IP)      |14
|WAP Client Id      |18

Most used SME address examples
------------------------------

**Mobile phone number:**

  address: +79171234567, TON: 1, NPI: 1

phone number must be provided in format `<country code><area code><subscriber number>`
   
**Short number:**

  address: 55555, TON: 3, NPI: 0

**Alphanumeric string:**

  address: MyService, TON: 5, NPI: 0
