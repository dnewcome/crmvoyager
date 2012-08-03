---
comments: true
date: 2010-10-12 20:14:15
layout: post
slug: embedding-silverlight-into-a-webpart
title: Embedding Silverlight into a WebPart
wordpress_id: 194
---

A current project required me to drop a [Silverlight](http://www.silverlight.net/) control into a [WebPart ](http://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.webparts.webpart.aspx)for inclusion in a Microsoft [SharePoint ](http://sharepoint.microsoft.com/en-us/Pages/default.aspx)site. In the past, I think that the Silverlight component could be created programmatically like any other ASP.NET control, but if I understand correctly, this support has been dropped from the latest version of Silverlight (4.0 as of this writing). 

As you may know, WebParts do not natively support the use of the ASP.NET templating engine in order to assist with rendering its contents. That is, we can't create a corresponding markup file as we do with .aspx or .ascx components. We can work around this by using [LoadControl()](http://msdn.microsoft.com/en-us/library/system.web.ui.page.loadcontrol.aspx) in order to render a user control (.ascx file) within the body of the WebPart's CreateChildControls() method, but I wanted to see if I could avoid having to create a separate user control just for embedding my Silverlight component.

I found two currently supported approaches to adding Silverlight content to a page in version 4: using an object tag or [client-side using Javascript](http://msdn.microsoft.com/en-us/library/cc265155(VS.95).aspx). Since I wasn't doing much else with Javascript on the client, I wanted to stick to a server-side solution. My attempts at programmatically creating the Silverlight control as [outlilned here](http://blogs.msdn.com/b/andreww/archive/2009/03/12/silverlight-web-part-in-sharepoint.aspx) failed, so what I ended up doing was to embed the object tag into an [HtmlGenericControl ](http://msdn.microsoft.com/en-us/library/system.web.ui.htmlcontrols.htmlgenericcontrol.aspx)directly in the WebPart. Here is the code:

[sourcecode language="csharp"]
	public class SilverlightPart : WebPart
	{
		protected override void CreateChildControls() {
			this.Title = "Embedded Silverlight";
			HtmlGenericControl control = new HtmlGenericControl( "div" );
			control.InnerHtml = @"<object data='data:application/x-silverlight-2,' type='application/x-silverlight-2' width='100%' height='300px'>
				<param name='source' value='ClientBin/SilverlightWizard.xap'/>
				<param name='onError' value='onSilverlightError' />
				<param name='background' value='white' />
				<param name='minRuntimeVersion' value='4.0.50401.0' />
				<param name='autoUpgrade' value='true' />
				<a href='http://go.microsoft.com/fwlink/?LinkID=149156&v=4.0.50401.0' style='text-decoration:none'>
 					<img src='http://go.microsoft.com/fwlink/?LinkId=161376' alt='Get Microsoft Silverlight' style='border-style:none'/>
				</a>
			</object>";
			Controls.Add( control );
			ChildControlsCreated = true;
		}
	}
[/sourcecode]

One small thing to note is that we need to specify a height for the Silverlight control when it appears in a WebPart zone. Setting width to 100% is fine, but setting the height must be done using a specific height due to the layout logic used by the zones.

