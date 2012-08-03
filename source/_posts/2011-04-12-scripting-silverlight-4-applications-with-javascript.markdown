---
comments: true
date: 2011-04-12 13:31:36
layout: post
slug: scripting-silverlight-4-applications-with-javascript
title: Scripting Silverlight 4 applications with Javascript
wordpress_id: 479
---

I was improving the design of some Silverlight code recently where the application was designed to be controlled externally from another Silverlight application. I'll cover how to do this in two parts. The first part is exposing some API to the browser via the [Html bridge](http://msdn.microsoft.com/en-us/library/cc645076(v=vs.95).aspx).

In order to make a type scriptable in Silverlight all we need to do is add an attribute to the type definition and register it with the Html page. My code looks something like this:

[sourcecode language="csharp"]
[ScriptableType]
	public partial class MainPage : UserControl
	{
			
		public MainPage() {
			InitializeComponent();
			HtmlPage.RegisterScriptableObject("MainPage", this);
			
	...
[/sourcecode]

This code will register the class as scriptable on the page. Here is the method that I wanted to expose to Javascript:

[sourcecode language="csharp"]
	[ScriptableMember]
		public void SetEventByID( string in_eventID ) {		
				ApplicationState.SetEventByID( in_eventID );
		}
[/sourcecode]

Notice that we have marked this member with a ScriptableMemberAttribute. Since the type is already marked as a ScriptableType, this is not necessary, I've just shown it as an alternative to marking the entire type as scriptable, thus exposing all of its immediate (non-inherited) members.

For more details on exposing Silverlight code to Javascript, I highly recommend that you read [this MSDN article](http://msdn.microsoft.com/en-us/library/cc645085(v=vs.95).aspx).

The calling code in Javascript looks like this:
[sourcecode langauge="javascript"]
document.getElementById('silverlight').Content.MainPage.SetEventByID( '097346E8-9917-DD11-96BD-0003FF464654' );
[/sourcecode]

Note that we get the Silverlight host object on the page by ID (which happens to be 'silverlight' in this example) and access the type by its name via the Content attribute exposed by Silverlight.

In the next installment I'll show how we can call out to a Javascript function in the page for the other half of our Silverlight-to-Silverlight communication scheme.
