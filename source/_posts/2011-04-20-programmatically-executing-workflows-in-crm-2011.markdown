---
comments: true
date: 2011-04-20 08:52:47
layout: post
slug: programmatically-executing-workflows-in-crm-2011
title: Programmatically executing workflows in CRM 2011
wordpress_id: 490
---

I came across some code recently that gave me pause, so I thought I'd explore it a little bit here.

We've recently converted a lot of code from CRM 4.0 to CRM 2011, and one of those pieces of code involved kicking off a Windows workflow. The following code snippet is right out of the CRM 2011 SDK:

``` csharp


using (_serviceProxy = new OrganizationServiceProxy(serverConfig.OrganizationUri,
    serverConfig.HomeRealmUri,
    serverConfig.Credentials,
    serverConfig.DeviceCredentials))
{
     // Create an ExecuteWorkflow request.
     ExecuteWorkflowRequest request = new ExecuteWorkflowRequest() {
         WorkflowId = _workflowId,
         EntityId = _leadId
     };
     Console.Write("Created ExecuteWorkflow request, ");

     // Execute the workflow.
     ExecuteWorkflowResponse response =
        (ExecuteWorkflowResponse)_serviceProxy.Execute(request);


```


I have paraphrased this code to illustrate my example more clearly. This is an example of a very typical workflow execution scenario. The thing that gave me pause was that I couldn't resolve the type ExecuteWorkflowRequest or ExecuteWorkflowResponse.

Looking more closely at the SDK example, I noticed the following at the top of the file:

``` csharp

// This namespace is found in Microsoft.Crm.Sdk.Proxy.dll assembly
// found in the SDK\bin folder.
using Microsoft.Crm.Sdk.Messages;

```


So even though most of the other types are in the Microsoft.Xrm namespace, for some reason these types were left behind in Microsoft.Crm.

Hopefully this saves you some time.

