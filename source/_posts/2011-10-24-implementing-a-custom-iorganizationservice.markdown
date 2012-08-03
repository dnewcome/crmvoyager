---
comments: true
date: 2011-10-24 21:21:42
layout: post
slug: implementing-a-custom-iorganizationservice
title: Implementing a custom IOrganizationService
wordpress_id: 542
---

This is a topic that I've wanted to talk about for a while. Some time ago I implemented a quick dummy CRM service for testing out code without having to run a full CRM server. The initial version I wrote was for CRM4, and unfortunately by some oversight, MS did not actually implement ICrmService in their actual server API. This created an awkward situation where I had to wrap the "real" CrmService in my own shim that implemented the actual interface.

Fortunately CRM 2011 gets this right, so we no longer have to worry about that. In fact, MS has shown us how to implement our own IOrganizationService right in the sample code. Just look under samplecode\cs\client\soaplogger\soaplogger in the SDK. In the example, the SOAP requests are logged from the intermediate IOrganizationService implementation. However I have found using custom services to provide dummy data very useful for testing.

Eventually I fleshed out the mock CRM service and released it as [FakeCRM](https://github.com/dnewcome/FakeCRM).

I'll be blogging more about this topic in the future, stay tuned!

