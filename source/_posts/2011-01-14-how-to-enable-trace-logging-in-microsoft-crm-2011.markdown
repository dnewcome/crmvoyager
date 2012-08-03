---
comments: true
date: 2011-01-14 22:59:16
layout: post
slug: how-to-enable-trace-logging-in-microsoft-crm-2011
title: How to enable trace logging in Microsoft CRM 2011
wordpress_id: 369
---

Hello again CRM fans. I'm tearing through a bunch of client code upgrades from the old CRM to the new 2011 stuff. In the process, I'm taking notes on what is different and what is the same. Fortunately enabling tracing is just the same as it was in CRM 4.0, but since there don't seem to be any articles out there that validate this, I'm posting it here. 

In case you didn't know already, there are a few registry keys that control the behavior of the CRM trace log file. Here is the [MSDN article](http://support.microsoft.com/kb/907490) that describes the different registry keys involved.

If you are in a hurry, there are only three keys that you really need: one to specify the file name, and two flags to actually enable tracing. 

[sourcecode]
TraceDirectory
TraceEnabled
TraceRefresh
[/sourcecode]

Here is a screenshot to help you out:
[![](http://crmvoyager.files.wordpress.com/2011/01/registry.png?w=300)](http://crmvoyager.files.wordpress.com/2011/01/registry.png)

