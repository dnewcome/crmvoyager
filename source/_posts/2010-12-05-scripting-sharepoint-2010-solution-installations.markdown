---
comments: true
date: 2010-12-05 04:56:06
layout: post
slug: scripting-sharepoint-2010-solution-installations
title: Scripting SharePoint 2010 Solution Installations
wordpress_id: 324
---

I was just working up some installation instructions for a client's SharePoint solutions when I realized that I should probably be creating some scripts to reduce the number of manual steps required to get the solution installed and working.

The thing that really pushed the issue for me was the initial addition of the SharePoint solution to the farm solution store. As you may know, the Web interface for managing farm solutions only allows the removal of existing solutions, [not the addition of new ones.](http://msdn.microsoft.com/en-us/library/aa544500.aspx) So as a first step I thought I'd share what I figured out initially about scripting the SharePoint Management Shell.



### SharePoint Management Shell Overview


Before we go any further, we need to understand what the SharePoint Management Shell is all about. In versions of SharePoint prior to 2010 Microsoft included a command line tool called stsadm.exe for scripting operations against the server. Although it is included in 2010, it has been deprecated in favor of the [PowerShell](http://en.wikipedia.org/wiki/Windows_PowerShell)-based management shell.

In order to figure out what is going on when we run the management shell, take a look at the shortcut properties to find out which script is being run. Here is the path that I found:

```
C:\Program Files\Common Files\Microsoft Shared\Web Server Extensions\14\CONFIG\POWERSHELL\Registration\SharePoint.ps1
```


Taking a look at the contents of the script we see something like the following:

```
$ver = $host | select version
if ($ver.Version.Major -gt 1)  {$Host.Runspace.ThreadOptions = "ReuseThread"}
Add-PsSnapin Microsoft.SharePoint.PowerShell
Set-location $home
```


Ok now we are getting somewhere. In order to write a PowerShell script that imports the SharePoint management cmdlets we can call Add-PsSnapin and gain access to the tools that we need. I later found this to be documented on MSDN [here](http://msdn.microsoft.com/en-us/library/ee537913.aspx).



### Creating a SharePoint Solution Upload Script


The simple script that I came up with to get the client through the hoop of getting the solution imported into SharePoint looks like this:

```
C:\Windows\System32\WindowsPowerShell\v1.0\PowerShell.exe  ^
	-Command  "& { "^
		"Add-PsSnapin Microsoft.SharePoint.PowerShell; "^
		"Add-SPSolution -LiteralPath %cd%\ClientSolution.wsp "^
	"}"
```


Note that this is a batch file which invokes the PowerShell Windows executable. We could probably just write a PowerShell script and hope that the client's machine is set up correctly so that he can run the .ps1 file directly, but I didn't want to count on it. The downside is that the version of PowerShell is hard coded into the path. These are all refinements for a later date though.

The command syntax for PowerShell literal command scripts looks like  where  is any number of semicolon-delimited PowerShell commands. The carets are just Windows batch file line break characters so that we can format the script for readability.

One last thing about the SharePoint Add-SPSolution cmdlet. I covered this in a [previous post](http://crmvoyager.wordpress.com/2010/11/19/deploying-web-parts-to-microsoft-sharepoint/), but it bears repeating here -- we must use the full path to the .wsp file in the -LiteralPath argument. In the script above I used %cd% to pass the current directory in, but we could use other tricks to allow this to be independent of how we call the script. I'll save that for a later post on Windows batch files.

Hopefully this gives you a jump on automating some SharePoint tasks. There are a lot of ways to improve this, so I'll be sure to post more as I go.
