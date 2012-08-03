---
comments: true
date: 2010-10-06 00:09:50
layout: post
slug: building-sharepoint-projects-with-cruisecontrol
title: Building SharePoint projects with CruiseControl
wordpress_id: 178
---

**Short version**: 

See [this MSDN article](http://msdn.microsoft.com/en-us/library/ff622991.aspx).

**Longer version:**

I needed to get another developer's WebParts building under my [CruiseControl](http://confluence.public.thoughtworks.org/display/CCNET/Welcome+to+CruiseControl.NET) server today. After checking the code out on the build box I realized that I would need a few dependencies that were not available since SharePoint was not installed on the build machine. After grabbing the obvious references I needed to build the project and changing the project references around I got to a point where MSBuild was giving me the following error:

``` bash

C:\WINDOWS\Microsoft.NET\Framework\v4.0.30319\Microsoft.Common.targets(1835,9):
error MSB3091: Task failed because "resgen.exe" was not found, or the correct
Microsoft Windows SDK is not installed. 

```



Ok, so apparently I needed to install the Windows SDK on the build box. More specifically, I needed to grab the .NET SDK, which is now distributed as part of the Windows SDK as of the .NET 4.0 Framework. Thankfully the Web installer allows you to install just the .NET components, so I didn't have to download the entire ISO image just to grab the .NET SDK.

I ran msbuild again and got the following error:

``` bash

C:\Program Files\CruiseControl.NET\...\SharePoint.WebParts.csproj(153,3): er
ror MSB4019: The imported project "C:\Program Files\MSBuild\Microsoft\VisualStu
dio\v10.0\SharePointTools\Microsoft.VisualStudio.SharePoint.targets" was not fo
und. Confirm that the path in the <Import> declaration is correct, and that the
 file exists on disk.

```


I copied the targets over from another machine that had Visual Studio installed, and now I got another error:

``` bash

C:\Program Files\MSBuild\Microsoft\VisualStudio\v10.0\SharePointTools\Microso
ft.VisualStudio.SharePoint.targets(343,5): error : Could not load file or assem
bly 'Microsoft.VisualStudio.SharePoint.Designers.Models, Version=10.0.0.0, Cult
ure=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The s
ystem cannot find the file specified.

```


I searched around for Microsoft.VisualStudio.SharePoint.Designers.Models.dll but I didn't find it right away. I thought that it was time to start Googling around to see where this tunnel ends and turned up [this document](http://msdn.microsoft.com/en-us/library/ff622991.aspx) from Microsoft.

This outlines exactly what I was trying to figure out in the steps above. Unfortunately it looks like a lot of work so I have to bail and just install CruiseControl on one of the SharePoint machines for now. I'll be sure to update this if I give it another shot.

UPDATE: 
I did get CruiseControl to build a SharePoint project on a machine without SharePoint installed on it. Every detail that you need to make this work is in the MSDN article for getting TFS builds to work. The only difference is that you will kick off the msbuild process from CruiseControl instead of TFS. One drawback to building on a machine that does not have SharePoint is that you won't be able to run automated tests that involve calls to SharePoint functionality. If you are careful about your design though, a lot of functionality can be tested without SharePoint since most Web Part functionality is now actually part of ASP.NET.
