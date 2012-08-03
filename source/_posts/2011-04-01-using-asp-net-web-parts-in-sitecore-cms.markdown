---
comments: true
date: 2011-04-01 17:22:21
layout: post
slug: using-asp-net-web-parts-in-sitecore-cms
title: Using ASP.NET Web Parts in Sitecore CMS
wordpress_id: 469
---

I'm going to share with you a technique for re-using simple Microsoft ASP.NET Web Parts in content management systems other than SharePoint. I'll be using [Sitecore ](http://www.sitecore.net/)for this article but the technique should apply to DotNetNuke and others. This is useful for keeping a consistent library of Web components that can share the same code base. Of course, the limitation here is that if we use any SharePoint-specific code we'll have to either write separate parts or try to abstract out the vendor-specific code. In my case, I had some code to retrieve the logged-in user. I won't show that code in this post for simplicity, but later I'll show you a technique that I've used to allow pluggable authentication code so the code is reusable except for the very small part that touches SharePoint directly.

In this example I'm going to use a very simple Web Part that I've created to easily test and illustrate the concept and nothing more. Here is the code:

``` csharp

using System;
using System.Data;
using System.Collections.Generic;
using System.ComponentModel;
using System.Web;
using System.Web.UI;
using System.Web.UI.HtmlControls;
using System.Web.UI.WebControls;
using System.Web.UI.WebControls.WebParts;

namespace WebParts
{
    [ToolboxItemAttribute(false)]
    public class IFramePart : WebPart
    {
        protected override void CreateChildControls()
        {
            this.Title = "IFrame";
				HtmlGenericControl iframe = new HtmlGenericControl( "iframe" );
				iframe.Attributes.Add( "src", "http://www.google.com");
            Controls.Add(iframe);
            ChildControlsCreated = true;
        }
    }
}

```


This Web Part contains an IFrame that points to Google. If we render this and see the Google home page we know it is working. Later on we can use [Web Part configurable properties](http://crmvoyager.wordpress.com/2010/11/16/creating-configurable-web-part-properties/) to allow this url to be set using the design mode of ASP.NET Web Parts.

In Sitecore, in order to make this code available in the CMS, we'll have to create an App_Code folder under the site root and place the file there. Sitecore is an ASP.NET Web site, and its behavior is no different than one that you'd create in Visual Studio. That is, there are special folders under the site like App_Code, App_Config and App_Data. Speaking of App_Data, some version of SQL Server (Express works) must be installed in order to take advantage of Web Part personalization features. That is, the state of the Web Part (hidden, collapsed) will be persisted to a database under App_Data automatically if we use personalization features. In my example we will so double-check that SQL Server is available and started, and that the App_Data folder is present under the Sitecore root.

I'm going to create the Web Part UI in Sitecore as a sublayout. This is basically another name for an .ascx UserControl in Sitecore terminology. The code for the UserControl looks like this:

``` html

<%@ Control Language="c#" AutoEventWireup="true" TargetSchema="http://schemas.microsoft.com/intellisense/ie5" %>
<%@ register tagprefix="webParts" Namespace="WebParts"%>

<div>
		<asp:WebPartManager ID="WebPartManager1" runat="server">
		</asp:WebPartManager>
	<table>
	<tr><td>
	<asp:WebPartZone ID="WebPartZone1" width="800px" runat="server">
		<zonetemplate>
			<webParts:IFramePart Height="200px"
				runat="server"   
				id="IFramePart1" 
				AllowZoneChange="true"/>         
		</zonetemplate>
		
	</asp:WebPartZone>
	
	</td>
	<td>
	<asp:EditorZone ID="EditorZone1" runat="server" >
      <ZoneTemplate>
        <asp:PropertyGridEditorPart Title="Edit Custom Properties" ID="PropertyGridEditorPart1" runat="server" />
		
		<asp:AppearanceEditorPart ID="AppearanceEditorPart1" 
          runat="server" />
        <asp:LayoutEditorPart ID="LayoutEditorPart1" 
          runat="server" />
		<asp:BehaviorEditorPart ID="BehaviorEditorPart1" runat="server" />
		
      </ZoneTemplate>
    </asp:EditorZone>
	</td></tr>
	</table>
	<asp:Button runat="server" onclick="Reset_Personalization" Text="Reset Personalization"/>
	<asp:Button ID="Button1" runat="server" onclick="Enable_Edit" Text="Enable Edit"/>
	<script runat="server">
	void Reset_Personalization( object sender, EventArgs e ) {
		WebPartManager1.Personalization.ResetPersonalizationState();
	}
	void Enable_Edit( object sender, EventArgs e ) {
		WebPartManager1.DisplayMode = WebPartManager.EditDisplayMode;
	}
	</script>
    </div>

```



In order to add this code to the site, we'll have to create a new sublayout in the Sitecore manager view and browse to the new file under the Sitecore site on the filesystem. The sublayouts should end up under SitecoreWebsite\WebSite\Layouts.

There is some extra code here that I should explain now. The primary advantage of having a Web Part, in addition to code reuse, is that there are built-in provisions for editing the part at design time once it is in the page. By adding an EditorZone in addition to the required WebPartZone and WebPartManager we are able to enable this default editor behavior in the page. I've added a button at the bottom of the UserControl that enables edit mode.

In order to use this in a Sitecore layout, I applied the included SampleLayout to a Sitecore data object and added my custom control. Editing won't work in the Sitecore designer, so go back to the data object and choose "preview". In preview, all of the functionality of the WebPart should be available.

Here is what it looks like when we are finished:

[![](http://crmvoyager.files.wordpress.com/2011/04/sc-webpart.jpg)](http://crmvoyager.files.wordpress.com/2011/04/sc-webpart.jpg)
