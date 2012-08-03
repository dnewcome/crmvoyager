---
comments: true
date: 2010-07-06 18:28:30
layout: post
slug: getting-started-with-asp-net-web-parts
title: Getting started with ASP.NET Web Parts
wordpress_id: 69
---

If you are developing Microsoft CRM solutions for clients like I am, you have probably gotten requests for Microsoft SharePoint integration. SharePoint provides a framework for user customization and authentication as well as providing users the ability to create dynamic content without any programming. However, this power comes at the price of slightly more complex development paradigms than what we are used to with plain ASP.NET. Fortunately, once we understand the basic building block, the WebPart, the rest should begin to fall into place.

I'm going to be targeting SharePoint 2010, but the concepts should be similar for previous versions of SharePoint. It is worth noting that Microsoft recommends development of ASP.NET WebParts instead of SharePoint WebParts. There are some differences in capability, but for our purposes here it won't matter. Also, to do the latter type of WebPart, we would need to have SharePoint installed locally on the development machine, which is a big drawback. ASP.NET WebParts can be developed as long as the .NET framework is installed (and if it isn't you probably wouldn't be reading this blog).

Ok, for starters, although it is possible to use a plain UserControl as a WebPart, we'll be creating a proper server control that implements System.Web.UI.WebControls.WebParts.WebPart.

To get started, all we need is an empty ASP.NET web site. Once we have created a web site in Visual Studio, creating the web part is as simple as inheriting from WebPart and overriding a single method. Here is the shell of the class:

``` csharp

	public class MyWebPart : System.Web.UI.WebControls.WebParts.WebPart
	{
		protected override void CreateChildControls() {}
	}

```


So, as you may have guessed, we need to implement code that generates the content for the WebPart just like we would when writing a server control. Probably the simplest thing we could do here is to insert a Label control, making our code look like this:

``` csharp

	public class MyWebPart : System.Web.UI.WebControls.WebParts.WebPart
	{
		protected override void CreateChildControls() {
			Label label = new Label();
			label.Text = "Test";
			Controls.Add( label );
			ChildControlsCreated = true;
                }
	}

```


The code should look familiar if you have ever written an ASP.NET server control. What we have done so far is exactly what we would have done to create a server control. However, in order to use the WebPart in a page, we need some extra machinery: namely the WebPartManager and the WebPartZone. The WebPartManager coordinates all of the WebParts and WebPartZones on the page, so we only need one of them. WebPartZones can be thought of as the container for a WebPart.

The markup for a basic WebPart page looks something like this:

``` html

	<asp:WebPartManager ID="WebPartManager1" runat="server" >
	</asp:WebPartManager>
	<asp:WebPartZone ID="WebPartZone1" runat="server">
		<zonetemplate>
          <webparts:MyWebPart
            runat="server"   
            id="mywebpart" 
            title="Simple Label WebPart" />
        </zonetemplate>
	</asp:WebPartZone>

```


Note that the WebPart gets added to the page in much the same way as a normal ASP.NET custom control. We need to use something like this to register the tag name:

``` html

<%@ register tagprefix="webparts" Namespace="AppCode" %>

```


With personalization enabled, the rendered control looks like this:
[![](http://crmvoyager.files.wordpress.com/2010/07/webpart.png)](http://crmvoyager.files.wordpress.com/2010/07/webpart.png)

There is one final thing that I'll leave you with. After playing around with the WebParts for a while, I accidentally deleted the WebPart from the page and I couldn't get it to display again. This is due to the way that [personalization](http://msdn.microsoft.com/en-us/library/z36h8be9.aspx) works. I didn't set up a personalization provider, but somehow it was still being persisted. Personalization can be disabled on the WebPartManager by setting Personalization-Enabled to false, either in code or in the markup. Alternatively, if you'd like to keep personalization enabled, the state can be reset by calling ResetPersonalizationState() on the WebPartManager. I added a button to the page so that I could clear the state easily at runtime.

``` csharp

protected void Reset_Personalization( object sender, EventArgs e ) {
		WebPartManager1.Personalization.ResetPersonalizationState();
	}

```


I'll talk a little bit more about developing WebParts in a later post.
