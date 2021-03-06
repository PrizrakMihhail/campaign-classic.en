---
title: SFTP server usage
seo-title: SFTP server usage
description: SFTP server usage
seo-description: 
page-status-flag: never-activated
uuid: 5281058d-91bd-4f98-835d-1d46dc7b8b1f
contentOwner: sauviat
products: SG_CAMPAIGN/CLASSIC
audience: platform
content-type: reference
topic-tags: importing-and-exporting-data
discoiquuid: f449ccd5-3965-4ab8-b5a9-993f3260aba9
index: y
internal: n
snippet: y
---

# SFTP server usage{#sftp-server-usage}

## SFTP server best practices {#sftp-server-best-practices}

When managing files and data for ETL purposes, these files are stored on a hosted SFTP server provided by Adobe. This SFTP is designed to be a temporary storage space on which you can control retention and deletion of files.

When not correctly used or monitored, this space can quickly fill the physical space available on the server and lead to files being truncated on subsequent uploads. Once the space saturated, automatic purge may trigger and erase oldest files from the SFTP storage.

To avoid such problems, Adobe recommends following the best practices below.

>[!NOTE]
>
>If your instance is hosted on AWS, you can monitor your SFTP server storage with the Campaign Classic [Control Panel](https://docs.adobe.com/content/help/en/control-panel/using/sftp-management/sftp-storage-management.html).  
>
>To check if your instance is hosted on AWS, follow the steps detailed in [this section](https://docs.adobe.com/content/help/en/control-panel/using/faq.html#ims-org-id) .

* The server size capabilities vary according to your license. In any case, keep the minimum data possible, and keep data for only as long as required (15 days is the maximum time limit).
* Use key based authentication rather than password authentication, in order to avoid password expiration (passwords have a validity period of 90 days). Moreover, key based authentication lets you generate multiple keys, for example when managing multiple entities. On the contrary, password authentication requires that you share the password with all the entities you are managing.

  The supported key format is SSH-2 RSA 2048. Keys can be generated with tools like PyTTY (Windows), or ssh-keygen (Unix).You will have to provide the public key to Adobe Support team via a [support ticket](https://support.neolane.net) to have it uploaded on the Campaign server. 

* Use workflows to properly delete the data (manage the retention from workflows consuming the data).
* Use batching in SFTP uploads as well as in workflows.
* Handle errors/exceptions.
* Occasionally, log-in to SFTP to directly check what is lying there.
* Remember that SFTP disk management is primarily your responsibility.
* By default, all the folders you create are in Read/Write mode for your identifier only. When creating folders that need to be accessed by Campaign, make sure to configure them with Read/write rights for the whole group. Otherwise, workflows may not be able to create / delete files as they are run under a different identifier within the same group for security reasons.
* The public IPs from which you are trying to initiate the SFTP connection must be whitelisted on the Campaign instance. Whitelisting of IP addresses can be requested via a [support ticket](https://support.neolane.net).

>[!CAUTION]
>
>If you use your own SFTP server, make sure to follow the recommendations above as much as possible.

## SFTP server troubleshooting {#sftp-server-troubleshooting}

The section below lists the information to check and provide to the Adobe Support team via a [support ticket](https://support.neolane.net) when encountering connection issues with Adobe hosted SFTP servers.

1. Check that your instance is running. To do this, open your browser, then make a **[!UICONTROL GET]** call on the instance **[!UICONTROL /r/test]** endpoint:

   ```
   https://instanceUrl/r/test
   ```

   If the instance is running, you should get this type of response:

   ```
   <redir status='OK' date='YYYY-MM-DD HH:MM:SS' build='XXXX' instance='instanceName'
   sourceIP='AAA.BB.CCC.DD' host='instanceUrl' localHost='instanceName'/>
   ```

   In any case, provide the command response in the support ticket.

1. Check if the outbound port 22 is opened at the site from which you are trying to initiate the SFTP connection. To do this, use the following command:

   ```
   bash-3.2$ nc -vz <SFTP_URL> 22
   # Replace the SFTP_URL with actual SFTP instance URL
   # If the port 22 is opened you will see output similar to the below one
   # for e.g. the  output for the command on myCompany-stage-sftp.neolane.net after ssh-out, will give
   bash-3.2$ nc -vz myCompagny-stage-sftp.neolane.net 22
   myCompany-stage-sftp.neolane.net [AAA.BBB.CCC.D] 22 (ssh) open
   ```

   >[!NOTE]
   >
   >The Netcat tool lets you easily manage network connections on various operating systems (see [https://eternallybored.org/misc/netcat/](https://eternallybored.org/misc/netcat/)).

   If the port is not opened, make sure to open outbound connections at your side, then try again. If you still meet connection issues, share the output of the command with Adobe Support team.

1. Check that the public IP from which you are trying to initiate the SFTP connection is the one you provided to the Adobe Support for whitelisting.
1. If you are using a password-based authentication, your password may have expired (passwords have a 90 days validity period). We therefore strongly recommend using a key based authentication (see [SFTP server best practices](#sftp-server-best-practices)).
1. If you are using a key based authentication, check that the key you are using is the same that you provided to Adobe Support team for the instance configuration.
1. If you are using FileZilla or an equivalent FTP tool, provide the connection logs details in the support ticket.

