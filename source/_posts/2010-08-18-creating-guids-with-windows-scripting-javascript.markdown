---
comments: true
date: 2010-08-18 21:44:17
layout: post
slug: creating-guids-with-windows-scripting-javascript
title: Creating GUIDs with Javascript Windows scripting host
wordpress_id: 140
---

I need to do some scripting from time to time, and I usually use Javascript to do everything. I needed to generate GUIDs for some CRM data that I wanted to create programmatically. I found [this article](http://blogs.technet.com/b/heyscriptingguy/archive/2005/02/21/how-can-i-create-a-guid-using-a-script.aspx) from the Microsoft Scripting Guy.

He gave the example in VBScript, so here is the equivalent in JScript:
[sourcecode language="javascript"]
function guidGen() {
    var typeLib = new ActiveXObject( "Scriptlet.TypeLib" );
    return typeLib.Guid;
}
[/sourcecode]
