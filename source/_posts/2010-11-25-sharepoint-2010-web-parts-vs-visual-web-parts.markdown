---
comments: true
date: 2010-11-25 03:30:23
layout: post
slug: sharepoint-2010-web-parts-vs-visual-web-parts
title: 'SharePoint 2010 Web Parts vs. Visual Web Parts '
wordpress_id: 305
---

I have read a lot of SharePoint documentation and articles recently, and one confusing point that kept getting glossed over was whether there was a difference between what was called a "Visual" Web Part and a Web Part by any other name.

The answer is, there is a difference, but not in the .NET type. There is a difference in how the part is implemented and its project type in Visual Studio. The short answer is that a Web Part is "visual" if its implementation includes an ASP.NET user control, i.e. an .ascx file. Creating a project of type "Visual Web Part" from the Visual Studio project wizard creates a project that looks like this:

[![](http://crmvoyager.files.wordpress.com/2010/11/solution.png)](http://crmvoyager.files.wordpress.com/2010/11/solution.png)

Notice the automatically-created .ascx user control. Indeed, the code in the Web Part is the same as we would find on a normal part, but in the implementation the user control is instantiated programmatically like this:

``` csharp

    public class VisualWebPart1 : WebPart
    {
        private const string _ascxPath = 
			@"~/_CONTROLTEMPLATES/VisualWebPartProject2/VisualWebPart1/VisualWebPart1UserControl.ascx";

        protected override void CreateChildControls()
        {
            Control control = Page.LoadControl(_ascxPath);
            Controls.Add(control);
        }
    }

```


Trying to deploy this project as a sandboxed solution will fail, since user controls violate the constraints placed on code that runs in the sandbox. There is nothing intrinsically about the code that would prevent its installation as a sandboxed solution, but it will fail when the solution is activated.



### Changing a Visual Web Part to a regular Web Part



We can change the Web Part type by manually editing the .spdata file in the project. Visual Studio won't allow direct access to this file, so we'll have to browse to it on the filesystem and edit it in notepad.

``` xml

<?xml version="1.0" encoding="utf-8"?>
<ProjectItem Type="Microsoft.VisualStudio.SharePoint.VisualWebPart" DefaultFile="VisualWebPart1UserControl.ascx" SupportedTrustLevels="FullTrust" SupportedDeploymentScopes="Site" xmlns="http://schemas.microsoft.com/VisualStudio/2010/SharePointTools/SharePointProjectItemModel">
  <Files>
    <ProjectItemFile Source="Elements.xml" Target="VisualWebPart1\" Type="ElementManifest" />
    <ProjectItemFile Source="VisualWebPart1.webpart" Target="VisualWebPart1\" Type="ElementFile" />
    <ProjectItemFile Source="VisualWebPart1UserControl.ascx" Target="CONTROLTEMPLATES\VisualWebPartProject2\VisualWebPart1\" Type="TemplateFile" />
  </Files>
  <SafeControls>
    <SafeControl Name="SafeControlEntry1" Assembly="$SharePoint.Project.AssemblyFullName$" Namespace="VisualWebPartProject2.VisualWebPart1" TypeName="*" IsSafe="true" IsSafeAgainstScript="false" />
  </SafeControls>
</ProjectItem>

```


We are interested only in this line:

``` xml

<ProjectItem Type="Microsoft.VisualStudio.SharePoint.VisualWebPart" DefaultFile="VisualWebPart1UserControl.ascx" SupportedTrustLevels="FullTrust" 

```


We can change the Type from VisualWebPart to WebPart and SupportedTrustLevels from FullTrust to All.

The resulting code looks like this:


``` xml

<ProjectItem Type="Microsoft.VisualStudio.SharePoint.WebPart" DefaultFile="VisualWebPart1UserControl.ascx" SupportedTrustLevels="All" 

```


Once the change is made, reload the project in Visual Studio, and its type should be recognized as a normal web part. Be sure to remove the user control before attempting to deploy as a sandboxed solution!
