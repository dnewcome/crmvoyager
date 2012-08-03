---
comments: true
date: 2011-02-25 11:30:06
layout: post
slug: how-to-list-process-ids-for-iis7-worker-processes
title: How to list process IDs for IIS7 worker processes
wordpress_id: 448
---

In Microsoft IIS7, all management commands have been consolidated under the appcmd.exe commandline tool. In the past there was a collection of VBscripts that allowed you to script access to IIS.

One of the things that I need to do that can't be done in the GUI is to list which application pools are running under which w3wp.exe worker process instances. Using the Windows task manager will show you the process IDs but won't show you which application pools are being served by each. The inetmgr tool will show you the application pools but won't show you the process information.

Under IIS6 there was a script called iisapp.vbs that would show you the running w3wp processes, their PIDs and the application pool that they were serving. Under IIS7 this functionality has been merged into appcmd.exe.

Appcmd was not in the path for me, so I had to add it using:

[sourcecode]
c:\> set path=%path%;C:\Windows\System32\inetsrv
[/sourcecode]

Then we can run
[sourcecode]
C:\Program Files (x86)\ICW>appcmd list wp
[/sourcecode]

to get:

[sourcecode]
WP "5260" (applicationPool:CRMAppPool)
WP "5736" (applicationPool:SharePoint - 80)
WP "4460" (applicationPool:SecurityTokenServiceApplicationPool)
WP "4700" (applicationPool:2d129968e2144980a7614f288ac778fd)
WP "5684" (applicationPool:Sitecore6.0AppPool)
[/sourcecode]

Short and sweet.
