---
comments: true
date: 2011-05-17 00:24:55
layout: post
slug: managing-crm-2011-from-powershell
title: Managing CRM 2011 from PowerShell
wordpress_id: 518
---

Starting with Windows Server 2008, Microsoft has stepped up its commitment to scriptable server management with the inclusion of PowerShell by default in the operating system. PowerShell enables the ease of scripting like batch files with the power of a scripting language like perl. Anyone that has done any extensive batch scripting will know what a pain it can be to do simple things like string handling.

Microsoft has doubled down on PowerShell in both CRM and SharePoint. Both are able to be managed using PowerShell snapins. What is a snapin? Snapins are what Microsoft calls script modules that can be loaded into PowerShell. The management features of CRM 2011 are supplied via a snapin.

The snapin will be installed on the CRM server by default, so to start powershell and load the snapin, we open up a console window and do the following:

[sourcecode]
C:\> powershell
Windows PowerShell
Copyright (C) 2009 Microsoft Corporation. All rights reserved.

PS C:\Users\Administrator> add-pssnapin Microsoft.CRM.Powershell

[/sourcecode]

Now we have access to all of the cmdlets (this is PowerShell terminology for a command tool).

For example, we can get information on the default CRM organization:

[sourcecode]

PS C:\Users\Administrator> get-crmorganization


BaseCurrencyCode      : USD
BaseCurrencyName      : US Dollar
BaseCurrencyPrecision : 2
BaseCurrencySymbol    : $
BaseLanguageCode      : 1033
DatabaseName          : crmtest_MSCRM
FriendlyName          : CRM Test
Id                    : 04721a7a-e0f5-45dc-a4b7-57e02088a13a
SqlCollation          : Latin1_General_CI_AI
SqlServerName         : WIN-0VODKV30814\SQL2008R2STD
SqmIsEnabled          : False
SrsUrl                : http://win-0vodkv30814/reportserver_sql2008r2std
State                 : Enabled
UniqueName            : crmtest
Version               : 5.0.9688.34
ExtensionData         : System.Runtime.Serialization.ExtensionDataObject

[/sourcecode]

Here is a list of all of the [available commands](http://msdn.microsoft.com/en-us/library/gg328563.aspx).
