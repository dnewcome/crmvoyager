---
comments: true
date: 2010-10-15 01:29:50
layout: post
slug: wspbuilder-and-sharepoint-deployment-layout-where-does-this-stuff-end-up
title: WSPBuilder and SharePoint deployment layout - where does this stuff end up?
wordpress_id: 219
---

I had the dubious pleasure of trying to figure out how to get some Web Parts that I developed deployed to a SharePoint site in order to demo for a client. I had the Web Parts working in a test host outside of SharePoint so I figured it wouldn't be a big deal to get them pushed to a real SharePoint installation. Well, you might guess it took me a little longer to work this situation out, since I'm taking the time to write about it here.



## First attempt -- manual deployment



The first shot I took was to drop the binaries out to the SharePoint server as outlined [here](http://technet.microsoft.com/en-us/library/cc261736.aspx). It should be possible to drop the assemblies under the bin/ folder of the SharePoint site, allow the assembly as safe code in web.config, and pull it into the Web Part catalog using the following admin page:

[sourcecode language="xml"]
http://<MyServer>/_layouts/newdwp.aspx 
[/sourcecode]

Individual SharePoint sites are set up under the IIS Inetpub/ folder structure by default, so this is roughly the path we are looking at:

[sourcecode language="xml"]
C:\inetpub\wwwroot\wss\VirtualDirectories\80
[/sourcecode]

Needless to say I couldn't get this to work. The problem was that nothing showed up in the admin page. The assembly was being loaded since I was getting errors if I messed up the web.config file, but alas, no luck getting the parts to display.



## Second attempt -- using WSPBuilder



Having failed at a quick and dirty approach, I moved my Web Parts to proper WSPBuilder Web Part projects. This is what you get if you create a new Visual Web Part project in Visual Studio. I also tried to create a WSPBuilder deployment project, but this didn't do what I wanted. There seemed to be a place where you could put your Web Part code, but it didn't look like what I wanted. Someone feel free to correct me on this.

One small note when creating a WSPBuilder project is that we want to give it the specific SharePoint site we want to work with, as this is the worker process that Visual Studio will attach to. So if the site is on a different port, you'll want to specify the full URL like this assuming that the site is on port 5559:

[sourcecode language="xml"]
http://myhost:5559/
[/sourcecode]

After un-mangling the default names and namespaces and moving my existing code into the new project layout, I hit F5 and crossed my fingers. The Web Parts get installed under a catalog section called "Custom". I was almost there at this point, but for some reason WSPBuilder was not adding the correct namespaces to the trusted assemblies list in web.config. I figured this out by having the site open in Windows Explorer and noticing that WSPBuilder was making backup copies of web.config every time I deployed. When you stop debugging it restores the previous web.config, removing any changes to the trusted assemblies configuration.

I manually added the assembly to web.confg like this:

[sourcecode language="xml"]
      <SafeControl Assembly="EventWebParts, Version=1.0.0.0, Culture=neutral, PublicKeyToken=6e5bb8b2abdf55b5" Namespace="EventWebParts" TypeName="*" Safe="True" SafeAgainstScript="False" />
[/sourcecode]

So now that I had my code running I poked around to figure out where things were ending up. SharePoint puts things generally in one of three places:

1) The GAC
2) IIS root (inetpub)
3) SharePoint "hive" C:\Program Files\Common Files\Microsoft Shared\Web Server Extensions\14\TEMPLATE\FEATURES\

By default, WSPBuilder seems to install the assemblies in the GAC. There is a configuration option clearly visible on the project for this setting. The .webpart xml configuration goes into the hive, and changes to web.config happen under the IIS root.

Hopefully this helps you to figure out where the heck all of this stuff ends up when deploying Web Parts to SharePoint. It takes a while to get used to.

