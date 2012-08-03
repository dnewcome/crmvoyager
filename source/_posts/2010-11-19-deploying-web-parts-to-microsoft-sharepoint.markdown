---
comments: true
date: 2010-11-19 09:35:53
layout: post
slug: deploying-web-parts-to-microsoft-sharepoint
title: Deploying Web Parts to Microsoft SharePoint 2010
wordpress_id: 265
---

_This is the first of several SharePoint-related articles that I have in the works, all dealing with code deployment and feature configuration._



### Background



Microsoft SharePoint allows developers to build custom UI pieces called Web Parts that plug into a standard SharePoint page at design time. These Web Parts can be extremely powerful, but unfortunately deployment scenarios can be confusing since Web Parts are just one small part of the SharePoint extensibility story and the documentation can be difficult to decipher as a result. In this post I'm going to go through the most straightforward deployment scenario, perhaps mentioning other possibilities in passing, to be covered in later articles.

Although there are other ways to get code deployed to SharePoint, the main mechanism by which custom or third-party code is introduced to a running SharePoint environment is via the [SharePoint solution](http://msdn.microsoft.com/en-us/library/bb862721(office.12).aspx) package. Solutions typically have a .wsp file extension, and are actually archives in the Microsoft .cab file format. Solution packages contain an XML manifest file, .NET assemblies and other resources.

One other important thing to note regarding the layout of a SharePoint solution is that a solution may contain one or more features. Features are units of functionality that may be enabled and disabled from within SharePoint. The relationship between features and solutions allows quite a bit of flexibility when setting up the capabilities of individual site collections, but again poses many opportunities for error and confusion.

The last thing to note before we get started with an example is that as of SharePoint 2010 solutions may be deployed in a sandbox environment in addition to being deployed at the farm level. It is possible to deploy the same solution at many levels, but unpredictable behavior may result if the features from both deployments of the solution are available in the same site.



### Creating the Package



We are going to build a .wsp package using Visual Studio 2010 and deploy it at the farm level of a fresh installation of Windows Sharepoint Foundation 2010. Assuming you already have a Web Part project in your Visual Studio solution, all we need to do is check a few properties on the project and build the package. Take a look at the following screenshot of the project properties for the Web Part project. The most important things here are the Sandboxed Solution and Assembly Deployment Target. We will need to create a non-sandboxed package in order to accommodate visual web parts -- that is, web parts whose layout is determined by an ascx user control. Visual Studio has a special project type for visual Web Parts and it will generate an error if we try to build a sandboxed package that contains visual web parts. The second issue is that for a farm-level deployment we must use the global assembly cache, hence the Assembly Deployment Target setting.

_Visual Studio project properties pane_
[![](http://crmvoyager.files.wordpress.com/2010/11/visual-studio-properties.png)](http://crmvoyager.files.wordpress.com/2010/11/visual-studio-properties.png)

Once these settings are checked and we are sure that our web parts are included in the feature and the feature is included in the package, we can build the .wsp file. The build is triggered via the project context menu. Open the menu and select Package in order to build the .wsp, which will appear in the /bin folder of the Web Part project.

_Creating the .wsp Package in Visual Studio_
[![](http://crmvoyager.files.wordpress.com/2010/11/visual-studio-package.png)](http://crmvoyager.files.wordpress.com/2010/11/visual-studio-package.png)



### Deploying the Code



Now that we have the .wsp file, we need to pull the package into SharePoint. SharePoint provides a scriptable tool called stsadm as well as a PowerShell cmdlet, since the PowerShell interface is what Microsoft will be moving forward with in the future, I'll stick with using the cmdlet. We'll need to have access to the SharePoint Central Administration site in order to complete the deployment. Open the SharePoint Management Shell from the Start menu and type the following:

``` bash

Add-SPSolution -LiteralPath c:\path\to\solution.wsp

```


Notice that the **full** path to the package is necessary.

Once the solution is added, we should browse to the System Settings area and click on Manage Farm Solutions as seen here:

_Taking a Look at the Installed Package_
 [![](http://crmvoyager.files.wordpress.com/2010/11/central-admin.png)](http://crmvoyager.files.wordpress.com/2010/11/central-admin.png)

This lets us see the added package and deploy it to any site collections that we wish to use the features on. Since we are deploying the .wsp files from the farm level, we "push" the solution down to the individual site collections, making the features visible to the sites. Once the feature is enabled the web parts should be visible in the web part gallery.

There are several other paths that we could have taken to get our code onto the SharePoint server, but this will suffice for the time being. Hopefully this helps out others in their journey to deploy code successfully to SharePoint.
