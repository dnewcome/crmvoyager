---
comments: true
date: 2011-04-26 00:01:45
layout: post
slug: starting-sql-server-2008-in-single-user-mode
title: Starting SQL Server 2008 in single-user mode
wordpress_id: 501
---

I had to go through the rain dance of [adding the Administrator account back](http://msdn.microsoft.com/en-us/library/dd207004.aspx) to SQL Server after there were no admin accounts that could log in. This can happen if the SQL server box has been removed from a domain and SQL Server was installed using domain accounts. If mixed mode authentication is disabled and you can't log in as the sa account, you'll have to start SQL up in single-user mode in order to connect to it.

Ordinarily you would give the -m flag to the sqlserver.exe executable and be on your merry way. However, this time around I ran into a perplexing issue. Take a look at the following screenshot to see the welcome that I received after running:

[sourcecode language="bash"]
C:\Program Files\Microsoft SQL Server\MSSQL10_50.SQL2008R2STD\MSSQL\Binn>sqlservr.exe -m
[/sourcecode]

[![](http://crmvoyager.files.wordpress.com/2011/04/sqlsserver.png)](http://crmvoyager.files.wordpress.com/2011/04/sqlsserver.png)

Quite informative. I took a look in the services administrative tool to see what the service commandline was and discovered that there is an [-s flag](http://msdn.microsoft.com/en-us/library/ms190737.aspx) that starts a named instance. I also have SQL Server Express running on this same machine, so there was a conflict.

I used the following command to get it started:
[sourcecode language="bash"]
C:\Program Files\Microsoft SQL Server\MSSQL10_50.SQL2008R2STD\MSSQL\Binn>sqlservr.exe  -sSQL2008R2STD -m
[/sourcecode]

And you should be greeted by this:

[sourcecode]
2011-04-26 00:50:37.40 Server      Microsoft SQL Server 2008 R2 (RTM) - 10.50.16
00.1 (X64)
        Apr  2 2010 15:48:46
        Copyright (c) Microsoft Corporation
        Standard Edition (64-bit) on Windows NT 6.1 <X64> (Build 7600: )

2011-04-26 00:50:37.41 Server      (c) Microsoft Corporation.
2011-04-26 00:50:37.41 Server      All rights reserved.
2011-04-26 00:50:37.41 Server      Server process ID is 4064.
2011-04-26 00:50:37.41 Server      System Manufacturer: 'innotek GmbH', System M
odel: 'VirtualBox'.
2011-04-26 00:50:37.41 Server      Authentication mode is WINDOWS-ONLY.
[/sourcecode]

