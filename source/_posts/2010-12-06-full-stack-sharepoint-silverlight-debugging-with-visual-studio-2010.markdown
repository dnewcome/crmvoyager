---
comments: true
date: 2010-12-06 22:33:29
layout: post
slug: full-stack-sharepoint-silverlight-debugging-with-visual-studio-2010
title: Full-Stack SharePoint + Silverlight Debugging with Visual Studio 2010
wordpress_id: 339
---

So you've gotten your Silverlight-based SharePoint Web Parts working in a test harness but when it comes time to get the whole stack out there under the target SharePoint site things aren't working. Of course it helps to have good logging in place, but sometimes we just want to get the debugger attached to see what is going on.

Creating a SharePoint project in Visual Studio sets things up to attach the debugger to the SharePoint site automatically, so stepping through ASP.NET Web Part code is easy enough right off the bat. Likewise, creating a Silverlight application prompts you to enable Silverlight debugging on the host site.

The question I had recently is, what happens when you want to attach the debugger to a Silverlight project while it is actually running inside of SharePoint and not the generated test site in Visual Studio?

Fortunately the answer is very simple. Assuming the SharePoint Web Part project is already set up to deploy to the target SharePoint site, we only need to make one more configuration change to the SharePoint project to turn on Silverlight debugging. The screen shot below shows it all:

_Visual Studio 2010 SharePoint Project Properties Pane_
[![](http://crmvoyager.files.wordpress.com/2010/12/silverlight-sharepoint-debug.jpg)](http://crmvoyager.files.wordpress.com/2010/12/silverlight-sharepoint-debug.jpg)

All we need to do is go under the SharePoint tab of the Web Part project and specify Silverlight debugging instead of Javascript debugging. Once this is set, running the project will attach the debugger to SharePoint, letting you set breakpoints in the Web Part code itself and also set breakpoints in any Silverlight code that gets loaded up.
