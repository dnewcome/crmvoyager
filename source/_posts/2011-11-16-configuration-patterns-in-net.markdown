---
comments: true
date: 2011-11-16 18:55:47
layout: post
slug: configuration-patterns-in-net
title: Configuration patterns in .NET
wordpress_id: 568
---

I have been meaning to write a post on configuration patterns in the .NET framework. Just about any app we write has some configuration data associated with it, and it sometimes gets ignored until the app is in production or at the very least until very late in the development cycle.

Microsoft provides a lot of options out of the box with the .NET framework, and even more through the [Configuration Management Application Block](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=22640). Most configuration concerns are the same whether it is a Windows app or a [Web app](http://msdn.microsoft.com/en-us/library/ms178683.aspx), but since there are a lot of different options, how do we pick what is best for our application?

Before we get into any .NET-specific items, here are some general guidelines that I have used in the past:



## Don't rely on implicit configuration - allow the app to be explicitly configured programmatically



In the case of a library, we'll want to be able to override configuration as needed for testing and special use cases. For example, I have a library for accessing CRM that has a configuration section. In some cases I want to test different configurations against different CRM servers in my tests, so I need a way to override the configuration file. Yes, you can do this in a convoluted way using the .NET framework, but it is much better to bake this into the library design. Another scenario is doing data export/import between multiple servers. I have a few tools that use the library I mentioned to take data from one CRM installation and move it to another one. If the library only supported one configuration section we'd have to build this functionality into the library, which is a concern that shouldn't be part of the underlying, but of the application.

However, a very nice feature of a library is to have a default configuration, as well as a default configuration location. This means that if we provide a config specifically it will override everything else, but if not, we can make it easy on ourselves by providing some sensible defaults or by using the app.config file if it exists. This kind of fallthrough can be a little tricky, so it is worth thinking about up-front so that things go smoothly and predictably as development progresses. Configuration behavior is one of those things that is tough to change once it gets into production.



## Use separate classes to hold configuration data



This should go without saying, but I'll say it anyway - don't put configuration variables into any other classes. Keep these concerns separate. If a class needs the configuration data, use composition to make the configuration object a private member. Another thing to watch out for is not using a configuration data in their own objects at all. I've seen a lot of project where configuration data was scattered all over the place. Don't do this!

I'm going to follow up with some code examples soon so stay tuned.
