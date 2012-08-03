---
comments: true
date: 2011-02-18 14:32:54
layout: post
slug: accessing-metadata-in-microsoft-crm-2011
title: Accessing metadata in Microsoft CRM 2011
wordpress_id: 441
---

I have been porting a lot of code over from CRM4 to the new CRM 2011 recently. I was investigating how to get some code converted that uses the MetadataService in CRM4 but I wasn't finding out much about it on the web.

If you are looking for the metadata service in 2011 you can stop now, because there isn't one. The new way of doing things is just to make your metadata requests to the main Web service.

The message classes are named the same, so the code will be mostly compatible once you set things up to use the 2011 Web service.

The namespaces you'll need in most cases will be:

[sourcecode langauge="csharp"]
// for service proxy type, IOrganizationService/OrganizationServiceProxy
using Microsoft.Xrm.Sdk.Client;

// needed for 'RetrieveXXXRequest' types
using Microsoft.Xrm.Sdk.Messages;

// needed for actual metadata descriptor types
using Microsoft.Xrm.Sdk.Metadata;
[/sourcecode]

For example, one of the things that I use metadata for is to get the text values that are in a CRM picklist. The following code snippet will query CRM 2011 metadata for the gendercode field on the contact entity and print out the values.

[sourcecode language="csharp"]
IOrganizationService serviceProxy = new OrganizationServiceProxy(
	Config.OrganizationUri,
	null,
	(System.Net.NetworkCredential)System.Net.CredentialCache.DefaultCredentials,
	null
);

RetrieveAttributeRequest request = new RetrieveAttributeRequest();
request.EntityLogicalName = "contact";
request.LogicalName = "gendercode";

RetrieveAttributeResponse response = ( RetrieveAttributeResponse )serviceProxy.Execute( request );
PicklistAttributeMetadata picklist = ( PicklistAttributeMetadata )response.AttributeMetadata;

foreach( OptionMetadata option in picklist.OptionSet.Options ) {
	Console.WriteLine( option.Label.UserLocalizedLabel.Label );
}

[/sourcecode]


