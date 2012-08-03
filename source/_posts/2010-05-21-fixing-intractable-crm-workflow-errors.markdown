---
comments: true
date: 2010-05-21 15:01:18
layout: post
slug: fixing-intractable-crm-workflow-errors
title: Fixing intractable CRM Workflow errors
wordpress_id: 44
---

I was having issues adding a custom workflow activity to one of my workflows today, and everything seemed right but I still was seeing an error in the CRM trace output when adding the activity to a new workflow.

[sourcecode]
[2010-05-21 10:49:35.3] Process: w3wp |Organization:11a5abbd-36aa-de11-8729-00155d00050c |Thread:   25 |Category: Application |User: 00000000-0000-0000-0000-000000000000 |Level: Error | ErrorInformation.LogError
&gt;MSCRM Error Report:
--------------------------------------------------------------------------------------------------------
Error: Field 'Altai.Workflow.ContractActivity.FtpPasswordProperty' not found.
[/sourcecode]

I checked and double checked and that field is certainly there. I used Reflector to check the code in the actual assembly.

Finally, I renamed the assembly and reimported and the error was resolved. Apparently there is some caching going on behind the scenes that was the root of my issue.
