---
comments: true
date: 2010-11-16 06:43:06
layout: post
slug: creating-configurable-web-part-properties
title: Creating Configurable Web Part Properties
wordpress_id: 253
---

I've been creating tons of ASP.NET Web Parts for use in various clients' SharePoint portals recently, and I've come to a point where I need to handle some configuration in some of the parts.

For those of you familiar with SharePoint, you'll recall that each SharePoint site has its own web.config file under the site root of the particular site that you are working with. The configuration that I'm going to detail here could be done by adding an element to the web.config site or even via a web service call or other custom configuration mechanism. However, while looking around for solutions I realized that there is a powerful and flexible configuration system built in to the ASP.NET Web Parts mechanism in the form of [IWebEditable](http://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.webparts.iwebeditable.aspx). Using this mechanism we will be able to allow our configuration options to be set by users during the page editing process right on the SharePoint site. Unfortunately, the [code samples](http://msdn.microsoft.com/en-us/library/dd584174(office.11).aspx) from Microsoft are pretty verbose and don't explain the basics in a very straightforward manner. We don't have to implement IWebEditable ourselves, as WebPart already implements it for us, we just have to know how to mark our properties so that they can be changed by the configuration mechanism.



### The Scenario:



Let's say I have a [Web Part that displays a Silverlight application](http://crmvoyager.wordpress.com/2010/10/12/embedding-silverlight-into-a-webpart/). Silverlight apps are deployed using [.xap archives](http://crmvoyager.wordpress.com/2010/10/15/whats-in-a-silverlight-xap/), which are simply .zip files with an included manifest telling Silverlight which resources are included and where any WCF service endpoints are located. In my Web Part I'd like to be able to specify the location of the Silverlight application so that I don't have to hard-code the path.



### The Solution:



Fortunately the solution is pretty simple. We just need to add a .NET property to the Web Part class and adorn it with the right attributes. In the following code snippet I have defined a string property called SilverlightUrl that I'll use for storing my Silverlight .xap file path:

``` csharp

[WebBrowsable( true ), Category( "Silverlight" ),
WebDisplayName( "Silverlight URL" ),
WebDescription( "web url to Silverlight XAP package" ),
Personalizable( PersonalizationScope.User )]
public string SilverlightUrl { get; set; }

```


Note that there are five different attributes applied to this single property. The most important attribute is WebBrowsable. This makes the field visible to an editor part. I'll cover editor parts in the next section, but just know that most of the attributes are for controlling how the field shows up in the property editor part. Category,WebDisplayNameandWebDescription describe how the field is grouped, named, and described in the editor part, respectively. The Personalizable attribute is important in controlling the conditions under which the editor part is shown at all. In our example I have set it to User scope in order to get the editor to show up without toggling the [personalization scope](http://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.webparts.personalizationscope.aspx). I'll cover personalization scope in another post, but just know that if we aren't in the right scope the editor will not be shown when we put the page into edit mode. This is something that tripped me up for a while when trying to get WebBrowsale properties working initially.



### Testing:



Just publishing our code to SharePoint as-is will be enough for us to configure our Web Part as desired. However, I always develop and debug my code in a simple test page outside of SharePoint, so I'll cover setting things up to test EditDisplayMode also.

In addition to the typical WebPartManager and WebPartZone on the page we will need an EditorZone. The particular editor that we are interested in is the [PropertyGridEditorPart](http://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.webparts.propertygrideditorpart.aspx). The ASP.NET markup would look something like this:

``` xml

<asp:EditorZone ID="EditorZone1" runat="server" >
      <ZoneTemplate>
        <asp:PropertyGridEditorPart Title="Edit Custom Properties" ID="PropertyGridEditorPart1" runat="server"/>
      </ZoneTemplate>
    </asp:EditorZone>

```


In order to toggle edit mode, we'll want to put an ASP.NET button on the page and wire it up to something like this:

``` csharp

protected void Enable_Edit( object sender, EventArgs e ) {
	WebPartManager1.DisplayMode = WebPartManager.EditDisplayMode;
}

```


By default, ASP.NET will set the Web Part page up in PersonalizationScope.User mode, meaning that we are editing per-user configuration rather than configuration that all site users will see. If we set the Personalizable attribute on our property to PersonalizationScope.Shared we would not see the editor part at all.

If everything is working correctly, when EditDisplayMode is toggled, the PropertyGridEditorPart will show up on the page like this:[![](http://crmvoyager.files.wordpress.com/2010/11/propertygrideditor.jpg)](http://crmvoyager.files.wordpress.com/2010/11/propertygrideditor.jpg)

There are some more related areas to explore when dealing with Web Part user configuration but I'll have to follow up on these topics in a future post.
