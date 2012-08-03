---
comments: true
date: 2010-08-19 14:39:18
layout: post
slug: creating-versioned-output-folders-with-visual-studio
title: Creating versioned output folders with Visual Studio (msbuild)
wordpress_id: 148
---

I've been messing with several ways of doing binary code releases recently with mixed success. Ideally I want to be able to build a solution with msbuild and have the output moved into folders for debug and release, with the assembly version and the target framework having separate folder locations for each. In other words, the bin\ folder would look like this:

[sourcecode language="csharp"]
bin\
  1.0.0.0\
    v3.5\
      Debug\
      Release\
[/sourcecode]

The main requirement is that the modifications that I need to make must be compatible with Visual Studio. Other developers have to be able to use the project files normally and they have to do something sensible by default.

The other part of the problem is that I want the version number to be read out of the AssemblyInfo.cs file so that the folder name and the actual assembly version are always in sync. Unfortunately my attempts at reading AssemblyInfo.cs haven't worked out. Due to the peculiar behavior of msbuild when building Visual Studio project files, I'm not able to dynamically change the value of a property based on the results of a custom build task that reads the version number out of the file. There is enough there for another post on this, but that will have to come another time.

My new approach is to just feed the version number manually to a batch file that will call msbuild with the appropriate property value set.

Let's look at the changes that we need to make to the .csproj file. First we look at the OutputPath property and change it to use the target framework version and a custom property that we will define called PointVersion. The property definition looks like this:

[sourcecode language="xml"]
 <OutputPath>bin\$(PointVersion)\$(TargetFrameworkVersion)\Debug\</OutputPath>
[/sourcecode]

I'm going to do the same change for the release configuration. The next thing is to define PointVersion and give it a default value. The default value is going to be SNAPSHOT, since if we aren't doing a release, we want to keep overwriting the same output rather than bumping the version number all the time or mistakenly updating the code of already-released versions. I want to avoid having binaries floating around that have the same version number but potentially different code. Here is the property definition:

[sourcecode language="xml"]
<PointVersion>SNAPSHOT</PointVersion>
[/sourcecode]

Note that this is declared under the main section of the .csproj file. Now that this is set up, building the project from Visual Studio will give us a folder structure like this:

[sourcecode language="csharp"]
bin\
  SNAPSHOT\
    v3.5\
      Debug\
      Release\
[/sourcecode]

We're getting pretty close. Now we want a script called publish.bat that we can use to build all configurations that we want with one action. The very simplest thing we can do is to put several parameterized calls to msbuild into a batch file like this:

[sourcecode language="shell"]
msbuild /property:Configuration=Debug /property:PointVersion=%1
msbuild /property:Configuration=Release /property:PointVersion=%1
[/sourcecode]

which we can call with the version number as the first parameter like this:

[sourcecode language="shell"]
c:\> publish.cmd 1.0.9.0
[/sourcecode]

This is not completely ideal, as I'd rather be able to bump the version in the AssemblyInfo.cs file. The next step is to grab the regular expression from the msbuild task that I found and run it in the batch file probably. I wish things would have worked out within msbuild, but it seems that this kind of thing is endemic to build systems in general.
