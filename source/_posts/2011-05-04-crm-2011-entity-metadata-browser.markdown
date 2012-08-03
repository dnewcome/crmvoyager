---
comments: true
date: 2011-05-04 19:49:34
layout: post
slug: crm-2011-entity-metadata-browser
title: Crm 2011 Entity Metadata Browser
wordpress_id: 507
---

Any of you out there that have been working with Microsoft CRM for a while will remember that since CRM3.0 there has been a handy metadata browser built into the CRM server for quickly looking up things like field names and entity relationships. In CRM3.0 you just browsed to http://yourcrmserver/sdk/list.aspx and in 4.0 it was the same only we had to specify the organization name like http://yourcrmserver/orgname/sdk/list.aspx since 4.0 was a fully multi-tenant CRM solution.

I was really disappointed to see that the metadata browser was omitted from CRM 2011. However, Microsoft came through recently by including an [all-new version of the metadata browser](http://blogs.msdn.com/b/crm/archive/2011/03/09/metadata-browser-for-microsoft-dynamics-crm-2011.aspx) in the latest CRM 2011 SDK.

The new browser is distributed as a managed CRM solution and can be installed and uninstalled just like any other CRM 2011 customization package. This is a really cool demonstration of what is possible with CRM solutions.

To install, grab the latest SDK from [here](http://www.microsoft.com/downloads/en/details.aspx?FamilyID=420f0f05-c226-4194-b7e1-f23ceaa83b69) and extract. Open up your CRM installation and browse to the customizations as shown in the following screenshot:

[![](http://crmvoyager.files.wordpress.com/2011/05/import-solution.png)](http://crmvoyager.files.wordpress.com/2011/05/import-solution.png)

The file that we want to import is located in the sdk under:

```

sdk\tools\metadatabrowser\metadatabrowser_1_0_0_1_managed.zip

```


[![](http://crmvoyager.files.wordpress.com/2011/05/select-file.png)](http://crmvoyager.files.wordpress.com/2011/05/select-file.png)

Once the import is complete you can navigate to the browser by going to settings/customizations/metadata browser as shown below:

[![](http://crmvoyager.files.wordpress.com/2011/05/browser.png)](http://crmvoyager.files.wordpress.com/2011/05/browser.png)

Enjoy not having to poke around in the customizations menu and drill into the individual entities just to figure out what you named that field.
