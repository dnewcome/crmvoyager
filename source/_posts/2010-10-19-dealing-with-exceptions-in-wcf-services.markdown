---
comments: true
date: 2010-10-19 17:15:44
layout: post
slug: dealing-with-exceptions-in-wcf-services
title: Dealing with exceptions in WCF services
wordpress_id: 230
---

**Overview**

Windows Communication Foundation Web services handle exceptions differently than regular .NET code. So in much the same that we re-throw CRM exceptions, we have to handle .NET exceptions at the top-level of our service methods in order to avoid the dreaded generic "NotFound" errors that we get when a WCF service fails for some reason.

For code that calls WCF services we can construct a FaultException to wrap the general exception that is thrown like this:

[sourcecode language="csharp"]
		try {
				
                            // perform the action that could throw an exception
			}
			catch( Exception e ) {
				throw new FaultException(
					new FaultReason( e.Message ),
					new FaultCode( e.GetType().ToString() )
				);
			}
[/sourcecode]

However in Silverlight this doesn't seem to work as expected. I still get a "Not Found" exception. Back to the drawing board.
