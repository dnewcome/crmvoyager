---
comments: true
date: 2011-10-20 16:27:49
layout: post
slug: running-microsoft-crm-4-0-apps-when-2011-is-installed
title: Running Microsoft CRM 4.0 apps when 2011 is installed
wordpress_id: 534
---

Hey guys, back after a bit of a break in blogging. I've been super busy on a lot of projects.

Here is a little tip that I found out about when I tried to consolidate my development environment a bit. I'm now running a CRM 2011 server on my dev machine. Unfortunately I also still need to support clients that are on CRM 4.0.

I didn't think that this should be a problem since I have the 4.0 server on another box. However it seems that there is a conflict when the .NET runtime tries to locate the 4.0 SDK assembly. Since CRM 2011 supports backward compatibility by exposing support for the old SDK, it supplies its own version of Microsoft.Crm.Sdk.dll. The difference is that it is strongly signed with an updated version number of 5.0.0.0.

Here is the error that we get if we try to run a project compiled against the 4.0 SDK on a machine that has CRM 2011 installed (even though we are using the service endpoint of the CRM 4.0 server):

[sourcecode]
Error: Could not load file or assembly 'Microsoft.Crm.Sdk, Version=5.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. The located assembly's manifest definition does not match the assembly reference. (Exception from HRESULT: 0x80131040)		
[/sourcecode]

We can force the runtime to load our own supplied version of Microsoft.Crm.Sdk by overriding the publisher policy. This is done in App.config (or Web.config for a web application) using the following code:

[sourcecode language="xml"]
<configuration>
...
 <runtime>
    <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
      <dependentAssembly>
        <assemblyIdentity name="Microsoft.Crm.Sdk" publicKeyToken="31bf3856ad364e35" culture="neutral" />
        <publisherPolicy apply="no" />
      </dependentAssembly>
    </assemblyBinding>
  </runtime>
</configuration>
[/sourcecode]

That's all there is to it. Now Fusion binding should find the correct assembly instead of trying to load the CRM 2011 out of the GAC.
