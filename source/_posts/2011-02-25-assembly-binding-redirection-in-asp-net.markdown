---
comments: true
date: 2011-02-25 12:07:05
layout: post
slug: assembly-binding-redirection-in-asp-net
title: Assembly binding redirection in ASP.NET
wordpress_id: 454
---

When dealing with signed strongly-named assemblies in .NET, we've all come across the dreaded

[sourcecode]
BadImageFormatException "An attempt was made to load a program with an incorrect format"
[/sourcecode]

Or

[sourcecode]
System.IO.FileLoadException: Could not load file or assembly 'Utility, Version=1.1.0.2, Culture=neutral, PublicKeyToken=37dd581281d764f7' or one of its dependencies. The located assembly's manifest definition does not match the assembly reference. (Exception from HRESULT: 0x80131040)
[/sourcecode]

In the first case, we need to find the right dll for the platform that we are on, i.e. 64bit vs 32bit. In my case the first error led to the second, since once I found the right dll, of course the version number was newer, and I could not find the older version to download.

Of course in .NET desktop apps, we could add an assembly binding redirect to get the right .dll loaded up provided that we have it. In a Web application, it is just as easy, you just have to add a runtime section directly under the main configuration tag.

For example, I wanted to drop a different version of SQLite into my SiteCore installation, so I grabbed the [System.Data.SQLite binaries](http://sourceforge.net/projects/sqlite-dotnet2/files/SQLite%20for%20ADO.NET%202.0/) and dropped the one that I wanted into SiteCore's \bin directory. Then I tried to load up the site and looked at the error message to see which version it was looking for. Once we know what it is looking for, we can write a binding redirection in the web.config file like this:

[sourcecode language="xml"]
 <runtime>
    <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
      <dependentAssembly>
        <assemblyIdentity name="System.Data.SQLite"
                          publicKeyToken="db937bc2d44ff139"
                          culture="neutral" />
        <bindingRedirect oldVersion="1.0.48.0"
                         newVersion="1.0.66.0"/>
      </dependentAssembly>
    </assemblyBinding>
  </runtime>
[/sourcecode]

Here is the [documentation for bindingRedirect](http://msdn.microsoft.com/en-us/library/eftw1fys.aspx) from MSDN. It doesn't explicitly cover redirects in Web applications, and there is a lot of confusing information out on the web about this. Hopefully this post clears some of it up.
