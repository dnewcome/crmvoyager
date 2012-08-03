---
comments: true
date: 2011-01-14 21:31:04
layout: post
slug: deploying-referenced-assemblies-in-crm-2011-plugins-and-workflows
title: Deploying referenced assemblies in CRM 2011 plugins and workflows
wordpress_id: 366
---

If you've ever developed a more complex plugin or custom workflow for Microsoft CRM that relies on external libraries you know that it can be tricky to get everything working correctly. The problem is that when the dll is registered using the Plugin Registration Tool included with the CRM SDK, only the metadata for the assembly containing the plugin is actually considered by the tool. This can cause issues at runtime even if the plugin appears to have been registered correctly.

Briefly, there are three ways to deal with referenced code:



#### 1 - Install the assemblies in the Global Assembly Cache.



Installing the assemblies into the assembly cache will allow you to take full advantage of the GACs versioning system, preventing conflicting versions of the same assembly if multiple versions are needed. The downside is that installation of the plugin will require a separate step potentially involving gacutil.exe, which may not be desirable for some deployments. If possible though, the GAC method is the preferred installation method.



#### 2 - Install the assemblies in %Program Files%\Microsoft Dynamics CRM\Server\bin\assembly



As a stopgap method that avoids the GAC but still allows you to deploy referenced assemblies to the CRM server, CRM 2011 will look in the assembly folder under its own installation path while probing for needed assemblies. The upside of this method is that it is easy to deploy dlls by simply copying them to the appropriate folder. The downside is that if many plugins use different versions of the same assembly there could be version conflicts that are difficult to track down.



#### 3 - Include all code in one assembly.



The least desirable option in my opinion is to cram all of the code into the same assembly. If you have access to all of the source code involved you could implement a solution like [this](http://blogs.inetium.com/blogs/azimmer/archive/2010/03/27/another-options-for-referencing-external-assemblies-from-plugins.aspx) where all of the source files are linked from external locations into the same Visual Studio project. If you don't have the source code, there is a tool from Microsoft called ILMerge that can take multiple assemblies and combine them into a single output assembly. This kind of thing should be taken as a last resort only if for some reason it is not feasible to install the assemblies in one of the first two ways I outlined above.

Hopefully this gives you some insight on how assembly references work in CRM 2011 and lets you hit the ground running.
