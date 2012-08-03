---
comments: true
date: 2010-11-25 02:17:34
layout: post
slug: enabling-user-sandboxed-code-in-sharepoint-2010
title: Enabling user (sandboxed) code in SharePoint 2010
wordpress_id: 298
---

With SharePoint 2010 Microsoft has added the ability to run code in a more protected and limited environment than the fully-trusted farm deployment level. This allows individual site owners to install third-party code without risking the stability and integrity of the entire SharePoint server farm or even an individual server.

As you'd expect, there are certain things you won't be able to do from the sandbox, such as access the filesystem or use .ascx user controls in your web parts. However, the added control over the code can make this tradeoff worth it if you can deal with the limitations.

I'm going to show a few points of configuration for the user code service in this post. The user code service is the mechanism by which SharePoint runs the sandboxed code. In normal non-sandbox code execution, the third-party code runs in-process with the rest of SharePoint in the ASP.NET w3wp.exe process. As you probably know, this is the familiar ASP.NET worker process that forms the actual IIS application pool that runs our .NET code within IIS. In order to isolate the sandbox from this main SharePoint process, Microsoft has another process that is hosted outside of IIS completely. In order to do this, there is a Windows service called the SharePoint 2010 User Code Host. The short name of the service is SPUserCodeV4 and the process name is SPUCHostService.exe. 

_Windows services console_
[![](http://crmvoyager.files.wordpress.com/2010/11/uc-service.png)](http://crmvoyager.files.wordpress.com/2010/11/uc-service.png)


This process also spawns two other processes, SPUCWorkerProcess.exe and SPUCWorkerProcessProxy.exe. We can attach a debugger to SPUCWorkerProcess.exe in order to debug user code. 

_Process Explorer showing the process hierarchy of SPUCHostService_
[![](http://crmvoyager.files.wordpress.com/2010/11/processes.png)](http://crmvoyager.files.wordpress.com/2010/11/processes.png)

Ok, now that we know a bit about how the code is run on the server at the OS level, we need to look at one more thing. Under the SharePoint central administration site we can go to the server and look at all of the services that are running on the machine.

_SharePoint service administration console_
[![](http://crmvoyager.files.wordpress.com/2010/11/central-admin-services.png)](http://crmvoyager.files.wordpress.com/2010/11/central-admin-services.png)

Although we can start and stop the service directly in Windows, the SharePoint management console will not always stay in sync with whether the service is actually running. It is better to manage the services through the central administration console, as stopping the service there will also set its startup type to "Disabled" so that the service does not start back up on a server reboot.

Hopefully this gives you a bit more background on the sandbox or user code service in SharePoint 2010.

