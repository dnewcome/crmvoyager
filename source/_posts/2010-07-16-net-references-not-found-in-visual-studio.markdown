---
comments: true
date: 2010-07-16 19:31:27
layout: post
slug: net-references-not-found-in-visual-studio
title: .NET references not found in Visual Studio
wordpress_id: 121
---

I just had this happen to me a second time, and I didn't recall what I did the first time so I'm blogging it now. The issue is that you add a reference to some library in Visual Studio and it just won't pick up the reference. There are a lot of things that can cause this, but the one that gets me is the platform target setting of the project. If you are building for a specific CPU the project will try to resolve references to any assemblies using the same CPU target. Most of the time we are building purely managed assemblies, so the setting is 'Any CPU'. Sometimes new projects get created by Visual studio with x86 as the target platform (not sure why this is) so none of your references work until you recall to change the setting back to 'Any CPU'. If you don't know what I'm talking about, take a look at the following screenshots:
[gallery order="DESC"]
