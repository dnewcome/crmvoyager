---
comments: true
date: 2010-11-30 06:32:49
layout: post
slug: sharepoint-web-part-error-when-switching-deployment-types
title: SharePoint Web Part Error When Switching Deployment Types
wordpress_id: 313
---

I just had to deal with this error once again after having redeployed some SharePoint 2010 Web Part code as a farm solution after having been installed as a sandboxed solution.

The error text is:

[sourcecode]
Web Part Error: The request could not be completed because the specified solution was not found.

Show Error Details 
Hide Error Details 

[SPUserCodeExecutionPipelineFailedException: The request could not be completed because the specified solution was not found.]
  at Microsoft.SharePoint.UserCode.SPUserCodeExecutionManager.VerifyExecutionAllowed(Guid siteCollectionId, SPUserCodeExecutionContext executionContext) 
  at Microsoft.SharePoint.UserCode.SPUserCodeExecutionManager.Execute(Type userCodeWrapperType, SPSite site, SPUserCodeExecutionContext executionContext) 
  at Microsoft.SharePoint.UserCode.SPUserCodeWebPartRemoteExecutionHelper.<>c__DisplayClassa.b__9() 
  at Microsoft.SharePoint.Utilities.SecurityContext.RunAsProcess(CodeToRunElevated secureCode) 
  at Microsoft.SharePoint.UserCode.SPUserCodeWebPartRemoteExecutionHelper.ExecuteRequestInSandBox(HttpContext context, SPWeb web, SPWebPartManager manager, SPUserCodeWebPart userCodeWebPart)  
[/sourcecode]

This typically happens when either a web part has been added to a SharePoint page already and then the code is redeployed in another location or the Web Part gallery has not updated after the code was redeployed.

In the first case, all that we need to do is delete the Web Part from the page and re-add it from the gallery. In the latter case, we'll have to go into the web part gallery and delete all of the web parts and then re-deploy the solution.

Either way it is not a big deal, but it helps to know where to look and what is going on.
