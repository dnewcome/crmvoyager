---
comments: true
date: 2011-04-13 11:59:42
layout: post
slug: handling-errors-with-the-crm-2011-web-services-api
title: Handling errors with the CRM 2011 Web services API
wordpress_id: 488
---

Early on in my experiences with CRM programming I learned to do whatever I can to get meaningful error responses from the server. Nothing is more difficult than having a Web service call fail and not being able to tell what happened by looking at the stack trace. 

Since the CRM API is delivered using .NET Web services, we are crossing some process and protocol boundaries. In particular, CRM adheres to the SOAP standard of returning errors as [SOAP faults](http://www.w3.org/TR/2000/NOTE-SOAP-20000508/#_Toc478383507).

In order to implement this feature with .NET exceptions, Microsoft has created a custom exception type - the SoapException. The meaningful data of the SOAP fault is contained in a field called SoapException.Detail.InnerXml. The issue here is that most of the time when the calling code is handling exceptions, we don't see this field since we are dealing with System.Exception types most of the time.

What I have been doing from the beginning is to rethrow the System.Web.Services.Protocols.SoapException as a System.Exception and include the Xml as the exception message. Consider the following error handler:

``` csharp

try {
	return DoCrmCall();
}
catch (System.Web.Services.Protocols.SoapException ex) {
	throw new Exception(ex.Detail.InnerXml, ex);
}

```


When we see the exception in the calling code (the .NET service proxy) we get a System.Exception where the message contains the Xml from the SOAP fault. There may be better ways of handling the SOAP messages, but this method has been working well for me, and I thought I'd put it up for you guys to comment on, since I know that error messages are a hot button topic in CRM and Web services in general.
