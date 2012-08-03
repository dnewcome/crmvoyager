---
comments: true
date: 2011-10-25 12:35:00
layout: post
slug: building-multiple-visual-studio-project-configurations-with-msbuild
title: Building multiple Visual Studio project configurations with msbuild
wordpress_id: 551
---

I've been cleaning up a lot of projects lately, and one of the things that I really needed to get sorted out was build scripts for everything so that I had a repeatable way of obtaining correct binary builds of all of my dependencies. Typically I would use msbuild or NAnt to define how the code should be built, but I already had Visual Studio project files for the projects in question and I wanted them to be resilient to having the project files messed with in Visual studio.

Since Visual Studio project files are just custom msbuild definitions supplied by Microsoft, we can build them with msbuild on the commandline. I didn't want to do any custom tweaking of the files themselves, since I've had issues with losing my changes when someone decided to mess with the project file. What I really want out of the project file is the list of the files that need to be built. In the future I'm thinking about devising a way to create a wrapper msbuild project that looks at the parts of the Visual Studio file for the source file list and dependencies and defines its own build targets, but I'm too busy to try to get that working for now.

I've had all sorts of issues with trying to tweak msbuild files. Microsoft really came up with a powerful build system, but unfortunately it is hamstrung when trying to work with Visual studio. Some things I wanted to do in the past included getting a version number from the Assemblyinfo.cs file. This turned into a fiasco because of a silly limitation in the scope of property variables within the build targets.

After all of this fiddling I have come to the conclusion that, at least for now, the best way to get msbuild to build a Visual Studio project in a particular way is to manhandle the property settings on the commandline when calling msbuild.

Here is a sample batch file that I use to call msbuild. I want to set the .NET framework version and project configuration (debug/release) along with assertion of some conditional compilation flags used to generate different targeted versions of the code (CRM 4/CRM 2011):

[sourcecode]
set msbuild=C:\Windows\Microsoft.NET\Framework64\v4.0.30319\MSBuild.exe
set PointVersion=1.0.0
set outpathprop=/property:OutputPath=..\release\%PointVersion%\3.5

%msbuild% ^
    /property:Configuration=Debug ^
    /property:TargetFrameworkVersion=v3.5 ^
    %outpathprop%\CRM4\Debug ^
    /p:DefineConstants="CRM4" ^
    CrmQuery\CrmQuery.csproj
[/sourcecode]

You can see that I set the output path to include the release version number and the framework version. I could parameterize this even further, but that's an exercise I'll leave to the reader or until I refine this technique next time around. Notice that I define a constant called 'CRM4' which is responsible for triggering some #ifdefs in the code that include CRM4-specific code. The beauty of this script is that now I can just add a section for each configuration that I need to build, run it once and I've got all the debug and release configurations of both CRM4 and 2011 versions of my project.

The file layout that I've chosen is:

[sourcecode]
\release\<version>\<crmversion>\<configuration\
[/sourcecode]

Effectively, this build script controls the versioning of the build. I got tired of trying to do this by reading Assemblyinfo.cs, but we'll have to deal with this if we want to build a signed assembly.

The advantage of having a consistent folder structure is that we can now put the built artifacts into an assembly cache and use a dependency management script to copy them to a local lib\ folder under the project directory. We can manage multiple versions of the same assembly and not get completely confused as to which version we are referencing in the project. Let me know about your quick-and-dirty build techniques in the comments!
