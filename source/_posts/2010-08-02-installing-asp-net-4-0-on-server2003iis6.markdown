---
comments: true
date: 2010-08-02 16:35:20
layout: post
slug: installing-asp-net-4-0-on-server2003iis6
title: Installing ASP.NET 4.0 on Server2003/IIS6
wordpress_id: 131
---

I was deploying an application for a client today when I realized that they didn't have the 4.0 framework installed on the target machine. I've done this plenty of times, but apparently I forget this one configuration step each time. For the benefit of myself and hopefully others, here is a description of the problem and the solution.

Problem: we need to install a 4.0 application to a server that is running IIS6 and doesn't have the 4.0 framework yet installed.

Initially, hitting the page gives us the following error:

Line 19: 	/> Line 20: 	<system.web> Line 21: 		<compilation debug="true" targetFramework="4.0"/> Line 22: 	</system.web> Line 23: </configuration>

Ok, great, so all we have to do is install .NET 4.0. We'll fire off the web installer from [here](http://www.microsoft.com/downloads/details.aspx?familyid=9cfb2d51-5ff4-4491-b0e5-b386f32c0992&displaylang=en)

Once we have things installed, we can select 4.0 as the framework for our site in the IIS6 configuration page for the site:

[![](http://crmvoyager.files.wordpress.com/2010/08/set-site-4-0.png)](http://crmvoyager.files.wordpress.com/2010/08/set-site-4-0.png)

So far, so good. Restart IIS and browse to the page, and:


# The page cannot be found


The page you are looking for might have been  removed, had its name changed, or is temporarily unavailable.



* * *

Please try the following:



	
  * Make sure that the Web site address displayed in the address bar of your  browser is spelled and formatted correctly.

	
  * If you reached this page by clicking a link, contact the Web site  administrator to alert them that the link is incorrectly formatted.

	
  * Click the [Back](http://localhost:9982/bacb/Default.aspx) button to try  another link.




## HTTP Error 404 - File or directory not found.
Internet Information  Services (IIS)


Hmm.. a 404 error? Double check that .aspx files are registered to the new aspnet ISAPI filter:

[![](http://crmvoyager.files.wordpress.com/2010/08/aspx-registration.png)](http://crmvoyager.files.wordpress.com/2010/08/aspx-registration.png)

Yes, they are registered. What else could cause a 404 error for our aspx pages? Other file types work: xml, html, etc.

This is the part that I always forget:

[![](http://crmvoyager.files.wordpress.com/2010/08/service-extension-4.png)](http://crmvoyager.files.wordpress.com/2010/08/service-extension-4.png)

By default, new extensions are disabled. We have to enable the ISAPI extension that processes 4.0 framework pages.

Hopefully this helps someone out. It will help me the next time.
