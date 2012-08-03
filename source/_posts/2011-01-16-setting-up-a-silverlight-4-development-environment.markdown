---
comments: true
date: 2011-01-16 19:51:31
layout: post
slug: setting-up-a-silverlight-4-development-environment
title: Setting up a Silverlight 4 development environment
wordpress_id: 373
---

I had to run through a complete workstation setup recently including installation of Visual Studio and the Silverlight development tools so I wanted to write up a quick blog post to document the process.

I started off with a clean installation of Visual Studio 2010. This will install some project templates for Silverlight by default but unfortunately this will not be sufficient for development out of the box. At the minimum we will need to have the Silverlight developer runtime in addition to the project templates. Additionally, the templates included in the VS2010 installer are for an older 3.0 version of Silverlight. Not quite what we want.

To rectify all of this, we need to install the Silverlight 4 Tools for Visual studio, available from Microsoft [here](http://www.microsoft.com/downloads/en/details.aspx?FamilyID=b3deb194-ca86-4fb6-a716-b67c2604a139&displaylang=en).

The installer includes the following components. This can be used to check for previously installed software that may have come from previous attempts to get everything installed. 





  * Silverlight 4 developer runtime


  * Silverlight 4 SDK (software development kit)


  * Update for Visual Studio 2010 and Visual Web Developer Express 2010 (KB982218)


  * Silverlight 4 Tools for Visual Studio 2010


  * WCF RIA Services V1.0 for Silverlight 4


  * F# Runtime for Silverlight 4



[![](http://crmvoyager.files.wordpress.com/2011/01/sl4tools-install.png?w=300)](http://crmvoyager.files.wordpress.com/2011/01/sl4tools-install.png)
Although the developer runtime is listed among the components that this package should install, I've found that it must be installed separately as outlined below.

Grab the file Silverlight_Developer.exe from [here](http://go.microsoft.com/fwlink/?LinkId=146060) and install.
[![](http://crmvoyager.files.wordpress.com/2011/01/sl4-developer-install.png?w=300)](http://crmvoyager.files.wordpress.com/2011/01/sl4-developer-install.png)

Once both of these packages have been installed you should be able to open Visual Studio and create a new Silverlight project as shown in the screenshot below.

[![](http://crmvoyager.files.wordpress.com/2011/01/new-sl-project.png?w=300)](http://crmvoyager.files.wordpress.com/2011/01/new-sl-project.png)

If everything has been installed properly you should get prompted for a project options dialog for setting up the new Silverlight project.
[![](http://crmvoyager.files.wordpress.com/2011/01/new-sl4-app.png?w=300)](http://crmvoyager.files.wordpress.com/2011/01/new-sl4-app.png)

Hopefully this helps you get up and running with your Silverlight development environment.

