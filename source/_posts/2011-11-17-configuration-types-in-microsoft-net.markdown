---
comments: true
date: 2011-11-17 01:40:58
layout: post
slug: configuration-types-in-microsoft-net
title: Configuration types in Microsoft .NET
wordpress_id: 579
---

Here is a quick post to outline the different ways that .NET custom config files can be written. The MSDN documentation does a bad job generally at giving examples of what the actual XML markup looks like in their examples. They go through all of the different types involved without showing what the desired markup looks like.

Briefly, by default, custom configuration uses XML attributes, unlike the AppSettings section, which uses a special configuration case of a key/value dictionary. I think that this is confusing and is not well treated in MSDN.

If we want a custom appSettings-like config like the following:

```

<myAppSettings>
      <add key="NewKey0" value="Monday, March 30, 
           2009 1:36:33 PM" />
      <add key="NewKey1" value="Monday, March 30, 
           2009 1:36:40 PM" />
  </myAppSettings>

```


We need to use a [DictionarySectionHandler](http://msdn.microsoft.com/en-us/library/system.configuration.dictionarysectionhandler.aspx) instead of the usual ConfigurationSection.

Using a ConfigurationSection, our XML markup would look like this:

```

<myAppSettings
     NewKey0="Monday, March 30, 2009 1:36:33 PM"
     NewKey1="Monday, March 30, 2009 1:36:40 PM"
/>

```


It is possible to create elements as children with some additional coding using ConfigurationElement.

There is a long CodeProject article on this stuff that uses C++ [here](http://www.codeproject.com/KB/mcpp/WorkingWithConfigFiles.aspx) that I found useful and there is an extensive treatment on configuration [here](http://www.codeproject.com/KB/dotnet/mysteriesofconfiguration.aspx), but I've abridged things greatly here for my own reference. Hopefully this helps you too.
