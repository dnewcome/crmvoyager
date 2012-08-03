---
comments: true
date: 2011-11-17 01:49:41
layout: post
slug: loading-configuration-from-an-alternative-location-in-microsoft-net
title: Loading configuration from an alternative location in Microsoft .NET
wordpress_id: 574
---

I'm continuing my series of posts on configuration patterns in .NET. One of the things I had to do recently was to retrofit some code that I was writing to use an alternate configuration file. That is, I needed the ability to specify file from the commandline to grab a config section rather than having the .NET framework use the default app.config file that resides in the application directory.

It turns out that this is easy to do. There is a method on the ConfigurationManager called [OpenMappedMachineConfiguration](http://msdn.microsoft.com/en-us/library/system.configuration.configurationmanager.openmappedmachineconfiguration.aspx) that will let you define another config file to use.

I have a little method for pulling in the configuration from a given file:

``` csharp

// MSCrmConfigurationSection is my custom config section type
MSCrmConfigurationSection GetConfig( string in_filename ) {
	ConfigurationFileMap fileMap = new ConfigurationFileMap( in_filename );
	Configuration configuration = System.Configuration.ConfigurationManager.OpenMappedMachineConfiguration( fileMap );
	MSCrmConfigurationSection config = ( MSCrmConfigurationSection )configuration.GetSection( "MSCrm" );
	return config;
}

```


A really nice feature that we get for free is that we can mix and match files at will, pulling configuration sections from different files. And, as a bonus, the app.config file still takes effect, so any assembly redirects or publisher policies that are defined there still work as usual. Pretty awesome.
