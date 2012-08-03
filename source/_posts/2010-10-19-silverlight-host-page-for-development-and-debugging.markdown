---
comments: true
date: 2010-10-19 18:50:37
layout: post
slug: silverlight-host-page-for-development-and-debugging
title: Silverlight host page for development and debugging
wordpress_id: 234
---

When you first get into Silverlight development using Visual Studio, you probably created a new project using the Visual Studio project wizard for Silverlight applications. During this process you are asked if you want to create a default host page for the Silverlight application. 
[![](http://crmvoyager.files.wordpress.com/2010/10/silverlight-project.png)](http://crmvoyager.files.wordpress.com/2010/10/silverlight-project.png)

If we let Visual Studio create a new web site to host the Silverlight app as shown in the previous screenshot, it is equivalent to selecting the following option later on in an existing web site. 
[![](http://crmvoyager.files.wordpress.com/2010/10/add-silverlight.png)](http://crmvoyager.files.wordpress.com/2010/10/add-silverlight.png)

Adding a Silverlight project to a web site will cause that project to be built and the .xap archive will be published to a ClientBin/ folder under the web site.

If we elect not to create a separate web site for the Silverlight application a test html page called TestPage.html is generated in the bin folder of the Silverlight project, and the web browser will be launched, referencing the file on the local filesystem rather than using a web server.

The consequences of the local file access are that web service calls will not work unless a ClientAccessPolicy file is set up on the service. See my previous posts on this for details.
