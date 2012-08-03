---
comments: true
date: 2010-10-15 06:07:00
layout: post
slug: whats-in-a-silverlight-xap
title: What's in a Silverlight .XAP
wordpress_id: 225
---

Microsoft Silverlight applications are distributed in archive files with a .xap file extension. This model mirrors the deployment strategy of Flash .swf files and several other binary web application types. However, these files aren't as opaque as they seem on the surface. On further inspection we find that .xap files are simple zip archives with a different file extension.

Using an archive viewer ([7zip ](http://www.7-zip.org/)is my tool of choice here -- seriously, I'll wait while you go grab it) we can open the .xap up and examine its contents.

[![](http://crmvoyager.files.wordpress.com/2010/10/xap.png)](http://crmvoyager.files.wordpress.com/2010/10/xap.png)

We can see that along with the assemblies required to run the Silverlight code, we have two configuration files in the archive -- AppManifest.xaml and ServiceReferences.ClientConfig. These two files provide the assembly references and service references for the application, respectively. Following are samples of these two files:

AppManifest.xaml:
``` xml

<Deployment xmlns="http://schemas.microsoft.com/client/2007/deployment" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" EntryPointAssembly="SilverlightWizard" EntryPointType="SilverlightWizard.App" RuntimeVersion="4.0.50401.0">
  <Deployment.Parts>
    <AssemblyPart x:Name="SilverlightWizard" Source="SilverlightWizard.dll" />
    <AssemblyPart x:Name="SilverlightClassLibrary1" Source="SilverlightClassLibrary1.dll" />
    <AssemblyPart x:Name="System.ComponentModel.DataAnnotations" Source="System.ComponentModel.DataAnnotations.dll" />
    <AssemblyPart x:Name="System.ServiceModel.DomainServices.Client" Source="System.ServiceModel.DomainServices.Client.dll" />
    <AssemblyPart x:Name="System.ServiceModel.DomainServices.Client.Web" Source="System.ServiceModel.DomainServices.Client.Web.dll" />
    <AssemblyPart x:Name="System.ServiceModel.Web.Extensions" Source="System.ServiceModel.Web.Extensions.dll" />
    <AssemblyPart x:Name="System.Windows.Controls.Data" Source="System.Windows.Controls.Data.dll" />
    <AssemblyPart x:Name="System.Windows.Controls.Data.Input" Source="System.Windows.Controls.Data.Input.dll" />
    <AssemblyPart x:Name="System.Windows.Controls" Source="System.Windows.Controls.dll" />
    <AssemblyPart x:Name="System.Windows.Data" Source="System.Windows.Data.dll" />
  </Deployment.Parts>
</Deployment>

```


ServiceReferences.ClientConfig
``` xml

<configuration>
    <system.serviceModel>
        <bindings>
            <basicHttpBinding>
                <binding name="EventsSoap" maxBufferSize="2147483647" maxReceivedMessageSize="2147483647">
                    <security mode="None" />
                </binding>
            </basicHttpBinding>
        </bindings>
        <client>
            <endpoint address="http://localhost:4589/Events.asmx" binding="basicHttpBinding"
                bindingConfiguration="EventsSoap" contract="ServiceReference1.EventsSoap"
                name="EventsSoap" />
        </client>
    </system.serviceModel>
</configuration>

```


Normally we wouldn't need to touch these files, but in the case of a moved webservice location, knowing that the endpoint is defined here can be a lifesaver. We can use 7zip to edit the client config in-place to change the endpoint url of a service that the Silverlight application references.

Hopefully this shows you that you shouldn't take .xap files at face value -- there is more there than meets the eye.
