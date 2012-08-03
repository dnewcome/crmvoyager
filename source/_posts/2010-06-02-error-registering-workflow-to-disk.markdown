---
comments: true
date: 2010-06-02 16:06:45
layout: post
slug: error-registering-workflow-to-disk
title: Error registering workflow to disk
wordpress_id: 50
---

The other day I was working on a CRM workflow activity that I had registered to the database with the plugin registration tool. Later on, I wanted to register the same assembly to disk for easy debugging, but I was confronted with the following exception when trying to register the assembly:

``` bash

Unhandled Exception: System.Web.Services.Protocols.SoapException: Server was unable to process request.
Detail: <detail><error>
  <code>0x80044191</code>
  <description>Assembly can not be loaded from C:\Program Files\Microsoft Dynamics CRM\server\bin\assembly\MyWorkflow.dll.</description>

```


I thought that maybe I had to run the registration tool as administrator since the path was under `program files', but this did not help.

It isn't obvious from the docs, but they mention that you are responsible for copying the assemblies to the target folder under C:\Program Files\Microsoft Dynamics CRM\server\bin\assembly. However, it doesn't work unless you copy the assembly beforehand, and point the tool to that location when registering the assembly. You can't register the assembly from another location and then copy the binary afterward.

This limitation is mentioned in the documentation for plugin registration [here](http://msdn.microsoft.com/en-us/library/cc151098.aspx).
