---
comments: true
date: 2011-04-20 14:23:02
layout: post
slug: creating-custom-error-pages-in-asp-net
title: Creating custom error pages in ASP.NET
wordpress_id: 492
---

How many times have you been in a client demo when your application throws an ugly stack trace up on the screen? Double points if it is up on the projector at about eight feet wide.

Now, I don't like to go overboard with excessive exception handling just to avoid the appearance of errors, but there is one thing that we can do easily that will still allow us to avoid swallowing exceptions all over and it is as easy as putting a few lines into your web.config.

``` xml

<system.web>
    <customErrors mode="On" defaultRedirect="Error.aspx"/>
    ...
</system.web>

```


This will redirect the browser to Error.aspx on any unhandled exception in the application. We can still do some error logging by looking at the results of:

``` csharp

Server.GetLastError()

```


[Here](http://www.codeproject.com/KB/aspnet/Error_Page_for_aspnst.aspx) are some more details about getting error details in the custom error page.
