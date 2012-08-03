---
comments: true
date: 2010-10-15 01:01:48
layout: post
slug: observations-on-observablecollection
title: Observations on ObservableCollection
wordpress_id: 215
---

In a [previous post](http://crmvoyager.wordpress.com/2010/10/13/data-binding-in-silverlight/) I extolled the virtues of the new .NET ObservableCollection and its wonderful databinding goodness. Well I have experienced the flip side of the coin today, having gotten the proverbial horns after taunting the bull.

Ok enough bull. What happened was that I had a conflict between two different definitions of the same ObservableCollection type. Yes, it is defined in both System.Windows.dll and in System.dll. The difference is that System.Windows.dll is a Silverlight assembly and defines some types that are intended for use in the more limited Silverlight environment. So what happens is that I want to define an interface in my service that returns an ObservableCollection for me to consume in my Silverlight app. The service is a full-fledged .NET 4.0 app that references System.dll, and the Silverlight app references System.Windows.dll. We can reference Silverlight assemblies in regular .NET projects but we can't reference regular .NET assemblies in Silverlight projects. 

Keeping the previous assertions in mind, my first thought was to just reference the Silverlight assembly, System.Windows.dll in my Service app and use the ObservableCollection type defined there. In order to do this, we have to disambiguate the assembly references by using an [alias](http://www.codeproject.com/KB/cs/aliases.aspx). In the property page of the assembly reference for System.Windows.dll, I changed the Alias "global" to "silverlight" and declared an extern at the top of my file like this:

``` csharp

extern alias silverlight;

```


Once I had this set up, I could reference the Silverlight version of ObservableCollection thusly:


``` csharp

silverlight::System.Collections.ObjectModel.ObservableCollection collection;

```


This seemed to work at first, but I ended up getting errors when I ran the project. I'm still not sure why this didn't work out, maybe I'll look into it some more. If anyone has any ideas let me know in the comments.
