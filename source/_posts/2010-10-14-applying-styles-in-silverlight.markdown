---
comments: true
date: 2010-10-14 05:10:11
layout: post
slug: applying-styles-in-silverlight
title: Applying styles in Silverlight
wordpress_id: 212
---

Microsoft made a big deal about the separation of design and application structure in Silverlight with the introduction of Expression Blend and Expression Studio. Now I'm at a point where I want to style my Silverlight applications, so I started digging into Silverlight styles.

I was horrified initially when I started coming across code samples like this where we have to explicitly add the style rule that we want to apply to each individual control:
``` xml

<Button x:Name="button1" Content="First Button" Style="{StaticResource ButtonStyle}"/>

```


I didn't want to have to go through my application and add all of these Style attributes. I was expecting something like CSS where I could apply styles based on the existing document structure.

Fortunately in Silverlight 4 there is a styling method referred to as implicit styling, where we can avoid having to set the style individually on each element. Most of the examples that I found initially were for earlier versions of Silverlight.

This is what an expliclit style definition would look like when placed in a resources section:
``` xml

<Style x:Key="ButtonStyling" TargetType="Button">
    <Setter Property="Background" Value="Yellow"/>
</Style>

```


Here is what the same style looks like when using implicit styling
``` xml

<Style TargetType="Button">
    <Setter Property="Background" Value="Yellow"/>
</Style>

```


The only difference is that we don't specify a key. According to [this blog post](http://www.silverlightshow.net/items/Implicit-Styles-in-Silverlight-4.aspx), the key is actually set implicitly by Silverlight, much the same as an anonymous type actually has a name internally to the compiler.

In case it wasn't obvious, creating a control that receives an implicit style just means that we omit the Style attribute entirely:
``` xml

<Button x:Name="button1" Content="First Button"/>

```


We can override the implicit style with an explicit style by specifying the Style, however:

``` xml

<Button x:Name="button1" Content="First Button" Style="{StaticResource ButtonStyle}"/>

```


At this point I should point out that Silverlight styles are implemented using a Silverlight mechanism called [ResourceDictionaries](http://msdn.microsoft.com/en-us/library/system.windows.resourcedictionary(VS.95).aspx). Many examples out on the net define dictionary items using x:Name, but in Silverlight 4 we should be using x:Key. Name still works but is considered to be a legacy attribute.

ResourceDictionary implements DependencyProperty, and so we can define members of the Application or UserControl's dictionaries using dot notation like this:

``` xml

<Application.Resources>
  <Style TargetType="Button" x:Key="GelButton" >
   ...
   </Style>
<Application.Resources>

```


ResourceDictionaries [can be merged](http://weblogs.asp.net/fredriknormen/archive/2009/03/31/silverlight-3-0-split-style-and-template-into-different-files-and-merge-resources.aspx), so we can define styles in several places and merge them into a single dictionary. Unfortunately this is necessary for applying several style definitions to the same control, unlike in CSS where we are free to define as many cascading sections as we wish. Silverlight 4 supplies a mechanism for explicitly cascading styles using the BasedOn attribute.

The following example from the [Silverlight Show](http://www.silverlightshow.net/items/Implicit-Styles-in-Silverlight-4.aspx) blog shows how BasedOn works:

``` xml

<Style x:Key="BasedStyle" TargetType="Button">
    <Setter Property="FontSize" Value="16"/>
</Style>
 
<Style TargetType="Button" BasedOn="{StaticResource BasedStyle}">
    <Setter Property="Background" Value="Red"/>
    <Setter Property="Foreground" Value="Black"/>
</Style>

```


    
    


I'm going to get back to styling my app now. Till next time, style away.
