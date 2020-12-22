---
title: " SAP配置RFC连接-SM59 "
date: 2020-07-26
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - utils

---

[原文链接](https://www.guru99.com/how-to-configure-and-test-rfc.html)

## SAP配置RFC连接-SM59

SAP配置的RFC Connection都保存在表**RFCDES**中

### Step1:Procedure to steup an RFC connection

使用**Tcode:SM59**配置RFC connection

在SM59中，可以浏览已经创建的RFC连接。

![SM59](/images/SAPUtils/SAP_RFC_CONNECTION_1.png)

**创建新的RFC Destination**

![SM59](/images/SAPUtils/SAP_RFC_CONNECTION_2.png)

- **RFC Destination** – Name of Destination (could be Target System ID or anything relevant)
- **Connection Type** – here we choose one of the types (as explained previously) of RFC connections as per requirements.
- **Description** – This is a short informative description, probably to explain the purpose of connection.

**Technical Settings Tab**

![SM59](/images/SAPUtils/SAP_RFC_CONNECTION_3.png)

- **Target Host**– Here we provide the complete hostname or IP address of the target system.
- **System Number –** This is the system number of the target SAP system.
- Click Save

**Logon and Security  Tab**

![SM59](/images/SAPUtils/SAP_RFC_CONNECTION_4.png)

- **Language** – As per the target system's language

- **Client** – In SAP we never logon to a system, there has to be a particular client always, therefore we need to specify client number here for correct execution.

- **User ID and Password** – preferably not to be your own login ID, there should be some generic ID so that the connection should not be affected by constantly changing end-user IDs or passwords. *Mostly, a user of type 'System' or 'Communication' is used here.* Please note that this is the User ID for the target system and not the source system where we are creating this connection.

**Note**: By default, a connection is defined as aRFC. To define a connection as tRFC or qRFC go to Menu Bar -> Destination aRFC options / tRFC options ; provide inputs as per requirements. To define qRFC, use the special options tab.

### Step 2: Trusted RFC connection

There is an option to make the RFC connection as **'Trusted'.** Once selected, the calling (trusted) system doesn't require a password to connect with target (trusting) system.

![SM59](/images/SAPUtils/SAP_RFC_CONNECTION_5.png)

Following are the advantages for using trusted channels:

- Cross-system Single-Sign-On facility
- Password does not need to be sent across the network
- Timeout mechanism for the logon data prevents misuse.
- Prevents the mishandling of logon data because of the time-out mechanism.
- User-specific logon details of the calling/trusted system is checked.

The RFC users must have the required authorizations in the trusting system (authorization object **S_RFCACL**).Trusted connections are mostly used to connect SAP Solution Manager Systems with other SAP systems (satellites)

### Step 3: Testing the RFC Connection

After the RFCs are created (or sometimes in the case of already existing RFCs) we need to test, whether the connection is established successfully or not.

![SM59](/images/SAPUtils/SAP_RFC_CONNECTION_6.png)

  As shown above we go to SM59 to choose the RFC connection to be tested and then we expand drop down menu - "**Utilities->Test->…**". We have three options:

**Connection test ->** This attempts to make a connection with the remote system and hence validates IP address / Hostname and other connection details. If both systems are not able to connect, it throws an error. On success, it displays the table with response times. This test is just to check if the calling system can reach the remote system.  

  **Authorization Test ->** It is used to validate the User ID and Password (provided under 'logon and security' tab for the target system) and also the authorizations that are provided. If a test is successful, then the same screen will appear as shown above for the connection test.

**Unicode Test ->** It is to check if the Target system is a Unicode or not.  

**Remote Logon –>**This is also a kind of connection test, in which a new session of the target system is opened, and we need to specify a login ID and Password (if not already mentioned under 'Logon and Security' tab). If the user is of type 'Dialog' then a dialog session is created. To justify the successful connection test, output will be the response times for the communication packets, else error message will appear.

### Step 4: What went wrong?

If somehow the RFC connection is not established successfully, we can check the logs (to analyze the issue) at OS level in the 'WORK' director. There we can find the log files with the naming convention as "*dev_rfc<sequence no.>*" and the error description can be read from such files.

