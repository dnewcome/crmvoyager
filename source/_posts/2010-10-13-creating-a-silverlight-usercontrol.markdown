---
comments: true
date: 2010-10-13 01:52:10
layout: post
slug: creating-a-silverlight-usercontrol
title: Referencing a Silverlight UserControl
wordpress_id: 198
---

Funny how a lot of details are glossed over in the copious blog coverage that surrounds Silverlight. Creating UserControls is supposed to be dead easy, and it is relatively easy, but the method by which we register the control on the page eluded me for far too long, so here I aim to make the process just a little less painful for those who come after me.

Creating the UserControl is very easy. Given that you probably already have at least one UserControl in your Silverlight application -- the "main" page is a user control -- you can simply copy this and use it as the base of your new user control.

You'll notice that some of the Microsoft-supplied controls use an XML prefix such as "sdk:" for their user controls. For example, putting a TabControl into the .xaml file results in some markup that looks like so:

``` xml

<sdk:TabControl Height="Auto" Width="Auto" Name="tabControl1">
    ...
</sdk:TabControl>

```


We must do the same when instantiating our UserControls,  so any markup we add to the XAML file must be declared as part of a namespace. The [documentation ](http://msdn.microsoft.com/en-us/library/cc189061(VS.95).aspx)was difficult to find until I figured out what to search for -- Microsoft has a particular namespace root for referencing assemblies and types in XAML files. This namespace root is "clr-namespace". In order to add a namespace prefix that references our new UserControl types, we need to declare the namespace prefix using a literal "clr-namespace" in the URI. For example, if we have a type called MyUserControl that is in the .NET namespace MyProject, our namespace ends up being "clr-namespace:MyProject". In a XAML file we just need to add this XML namespace prefix somewhere in the file so that it is in scope where we need it. Practically speaking such prefixes are nearly always declared in the root element of the document, so we'll do the same. Here is what my code ended up looking like:

``` xml

<UserControl x:Class="MyProject.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
     ....
    xmlns:myprefix="clr-namespace:MyProject"/>

```


I have omitted several of the default namespace prefixes for clarity. To instantiate a UserControl type defined in the "MyProject" namespace, we need only to write an XML element with the name of our type, prefixed by the XML namespace prefix that we just defined. Here is a sample:

``` xml

<myprefix:MyUserControl Height="Auto" Width="Auto"></myprefix:MyUserControl>

```


According to the more general [documentation ](http://msdn.microsoft.com/en-us/library/ms747086.aspx)for WPF, of which Silverlight is a derivative, the assembly name may be specified in the URI format as follows:

``` xml

xmlns:custom="clr-namespace:SDKSample;assembly=SDKSampleLibrary"

```


Hopefully this sheds some light on a detail seemingly glossed over in many Silverlight articles and tutorials.
