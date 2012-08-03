---
comments: true
date: 2010-08-18 22:03:50
layout: post
slug: including-multiple-script-files-with-windows-scripting
title: Including multiple script files with Windows scripting
wordpress_id: 145
---

I'm using a Javascript library in order to do some data manipulation, and I wondered if there was a way to include the source JS file in my script. There is nothing like an include directive in WSH but it does support a mechanism for managing script files that is very similar to how it would work in a Web browser. To do this we create a file with the .wsf extension:

[sourcecode language="xml"]
<job>
  <script language="JScript" src="library.js"/>
  <script language="JScript" src="main.js"/>
    <script language="JScript">
       WScript.Echo( doFunction() );
    </script>
</job>
[/sourcecode]

Note that we can include files as well as inline code. The wsf script can now be invoked on the commandline with cscript as you would any vbs or js script file.
