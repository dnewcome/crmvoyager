---
comments: true
date: 2010-07-07 03:28:24
layout: post
slug: enabling-design-mode-in-asp-net-webparts
title: Enabling design mode in ASP.NET WebParts
wordpress_id: 96
---

WebPart pages support lots of advanced functionality right out of the box, but there are some complexities in getting everything set up correctly. In a previous post, I pointed out an issue that I had with WebParts personalization support. Another thing that I got tripped up on recently was getting design mode working. 

Design mode is a page viewing mode in which the WebParts on the page can be moved around via drag-and-drop using the mouse. This support is baked in, you just need to know how to enable it. Intuitively it seems like there should be an enum somewhere with the different display modes that you can use to change the mode. It doesn't quite work this way, and the documentation is a little bit confusing on exactly how things are supposed to work.

[The documentation](http://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.webparts.webpartmanager.designdisplaymode.aspx) gives an example in which the supported DisplayModes are queried from the WebPartManager and their names are added to a drop-down field on the page. Simply stated, all of the possible display modes are stored as static fields of the WebPartManager class rather than as an enum. The display modes supported at runtime for a given WebPartManager instance are found using the SupportedDisplayModes property.

[sourcecode language="csharp"]
foreach( WebPartDisplayMode mode in WebPartManager1.SupportedDisplayModes ) {
     // .....
}
[/sourcecode]

Setting the display mode for the page is done like this:
[sourcecode language="csharp"]
WebPartManager1.DisplayMode = WebPartManager.DesignDisplayMode;
[/sourcecode]

Note that if we try to set a display mode that is not supported by the WebPartManager, a runtime error will result. Also, drag-and-drop doesn't work in all browsers. It didn't work in Google Chrome for me, but it worked fine (go figure) in IE8.

Here is what it looks like in action:
[![](http://crmvoyager.files.wordpress.com/2010/07/webparts.png)](http://crmvoyager.files.wordpress.com/2010/07/webparts.png)
