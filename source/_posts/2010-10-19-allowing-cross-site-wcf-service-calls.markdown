---
comments: true
date: 2010-10-19 18:33:21
layout: post
slug: allowing-cross-site-wcf-service-calls
title: Allowing cross-site WCF service calls
wordpress_id: 232
---

One thing that I often forget during Silverlight development is that unless the Silverlight application and any services that it calls are hosted by the same server, the calls will fail due to security policy by default. This means that in Visual Studio you will have to use IIS for all sites and services unless everything is hosted under the same instance of WebDev.Webserver (the built-in development server in .NET).

In order to run things under IIS in Vista we have to run Visual Studio as an administrator, which is a pain and seems unnecessary. Fortunately Microsoft supplies us with a security policy escape hatch for WCF services in the form of a ClientAccessPolicy.xml file. 

Here is a policy file that I use frequently that allows access from any client, but is also insecure, so you shouldn't deploy something like this beyond your local development workstation:

``` xml

<?xml version="1.0" encoding="utf-8"?>
<access-policy>
  <cross-domain-access>
    <policy>
      <allow-from http-request-headers="*">
        <domain uri="*"/>
      </allow-from>
      <grant-to>
        <resource path="/" include-subpaths="true"/>
      </grant-to>
    </policy>
  </cross-domain-access>
</access-policy>

```


Place the file in the root of the domain, and Silverlight should be able to call it from a different server hostname or port. Note that this file needs to be at the _domain_ root rather than the root of the WCF service in order to be found by the Silverlight plugin.
