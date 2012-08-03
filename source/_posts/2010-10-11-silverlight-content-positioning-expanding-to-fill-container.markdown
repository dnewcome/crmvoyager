---
comments: true
date: 2010-10-11 21:33:24
layout: post
slug: silverlight-content-positioning-expanding-to-fill-container
title: Silverlight content positioning - expanding to fill container
wordpress_id: 192
---

I just started writing some new Silverlight controls for a client project and I was struggling with trying to get a tab control to span the full width of its container. I was reading everywhere that setting Width="Auto" was the way to accomplish this in Silverlight4, rather than the old way of doing it, which was setting Width="*". However no matter what I did, I couldn't get my control to span its container.

Here was the code:

[sourcecode language="xml"]
  <Grid x:Name="LayoutRoot" Background="White" Width="Auto" Height="Auto">
        <sdk:TabControl Height="Auto" Width="Auto" HorizontalAlignment="Left" Name="tabControl1" VerticalAlignment="Top" SelectionChanged="tabControl1_SelectionChanged">
            <sdk:TabItem Header="Details" Name="tabItem1" Width="Auto" Height="Auto">
                    <Canvas Width="Auto" Height="Auto">
                        <Button Height="12" Margin="0,0,82,-6" Width="124" Content="Back"></Button>
                        <Button Height="12" Margin="0,0,82,-6" Width="124" Content="Next"></Button>
                    </Canvas>
           </sdk:TabItem>
      </sdk:TabControl>
  </Grid>
[/sourcecode]

The Grid element was filling its container just fine using Width="Auto", but its child TabControl was still not expanding. Finally I started removing attributes on the TabControl to get to the bare basics of what could work. After I removed the alignment properties I started getting somewhere. Here is the new code for the TabControl:

[sourcecode language="xml"]
 <sdk:TabControl <strong>Height="Auto" Width="Auto" Name="tabControl1" SelectionChanged="tabControl1_SelectionChanged">
[/sourcecode]

This makes sense if you think about the alignment directives as CSS floats. There is no equivalent in CSS for the VerticalAlignment property, but it looks like the HorizontalAlignment is roughly analogous to a float. In HTML/CSS, floating a block level element causes it to contract in size to the dimensions of its contents. This is roughly what I was seeing here. Removing the float let the element expand to its container.

Hopefully the hour I spent messing with this helps somebody else out.
