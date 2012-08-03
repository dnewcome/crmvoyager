---
comments: true
date: 2010-12-07 06:03:08
layout: post
slug: cross-domain-silverlight-4-application-xap-hosting
title: Cross-Domain Silverlight 4 Application (.xap) Hosting
wordpress_id: 347
---

One aspect of Silverlight application deployment recently caused me a significant amount of pain -- the security rules governing where the Silverlight browser plug-in will and will not load code from and under what circumstances.

There is a lot of talk about making cross-domain service calls from Silverlight, but not a lot of coverage given to hosting the application packages (.xap files) themselves. In my case, I was loading the .xap file in a SharePoint Web Part. The .xap file was hosted on a separate IIS web site on the same server but running on a different port. This had always worked for me in the past so I thought nothing of hosting the code on a separate site.

However, later on after adding some new features to the Silverlight application I was no longer able to deploy my code to SharePoint successfully. I tried a small "hello world" Silverlight application and everything worked fine. I also tried moving my application under the SharePoint site and got it to work.

So the question I had was, what was different about my application that was preventing it from working in a cross-domain situation? Fortunately I found out about [EnableHtmlAccess](http://msdn.microsoft.com/en-us/library/cc838264(VS.95).aspx) via this [blog post](http://timheuer.com/blog/archive/2010/06/10/troubleshooting-debugging-silverlight-cross-domain-xap-hosting.aspx) by Tim Heuer. 

It turns out that the Silverlight browser plug-in will happily load code from any domain as long as it doesn't try to access the host page in any way, including reading any Javascript variables on the page. The change that I made to my application was a call to read some configuration value from the Web Part that hosted the Silverlight application, thus triggering the security restriction. Here is what the code looked like:

``` csharp

public static ScriptObject GetJsVariable( string in_name ) { 
	return ( ScriptObject )HtmlPage.Window.GetProperty( in_name );
}

```


To solve this problem, we can either just host the code on the same domain, or we can make a small change to the way that the Silverlight plugin is embedded in the page. I don't want to have to maintain a static source file in the SharePoint hive, so I added the following code to my Web Part:

``` html

<object data='data:application/x-silverlight-2,' type='application/x-silverlight-2' width='100%' height='100%'>
				<param name='source' value='ClientBin/MyApplication.xap'/>
				<param name='onError' value='onSilverlightError' />
				<param name='background' value='white' />
				<param name='minRuntimeVersion' value='4.0.50401.0' />
				<param name='autoUpgrade' value='true' />
				<param name='enableHtmlAccess' value='true' />
				<a href='http://go.microsoft.com/fwlink/?LinkID=149156&v=4.0.50401.0' style='text-decoration:none'>
 					<img src='http://go.microsoft.com/fwlink/?LinkId=161376' alt='Get Microsoft Silverlight' style='border-style:none'/>
				</a>
			</object>

```


Note the addition of
``` html

<param name='enableHtmlAccess' value='true' />

```


Once I had this parameter set everything started working again. One thing that I'd like to be able to do is to display an error message instead of just displaying an empty Silverlight plugin. The SharePoint Silverlight content control that ships with SharePoint 2010 does this, but I'm not sure what how it does it. I'll look into it at some point when I get time, but in the meantime if anyone knows I'd love to hear from you.
