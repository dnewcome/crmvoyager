---
comments: true
date: 2010-12-08 20:58:30
layout: post
slug: migrating-sdk-code-from-crm4-to-crm5-2011
title: Migrating SDK Code from CRM4 to CRM5 (2011)
wordpress_id: 354
---

Now that the CRM5 beta is [generally available](http://www.microsoft.com/downloads/en/details.aspx?FamilyID=0c7dcc45-9d41-4e2e-8126-895517b4274c) (and many of us were privy to the preview code) it's time to start dusting off those data access libraries and shaking them up to 2011 spec. If you've written any custom application code using the Microsoft.Crm.Sdk library, you'll want to tune in here. Fortunately, I didn't run into too many issues but there were a few gotchas that I'll go into here in this post.

I was upgrading my open-source CRM query library, [CrmQuery](https://github.com/dnewcome/crmQuery) (which has saved us a ton of time over in our shop - I highly recommend that you check it out), so what I cover here is going to be limited to what I ran into during that migration. I'll follow up with more posts when I start finding out more.



## Namespace and Library Differences



First off, you'll need to reference the Microsoft.Xrm.Sdk.dll library in your project. This replaces the CRM4 library Microsoft.Crm.Sdk.dll. Most of the types are the same name. Anywhere you'd use the namespaces:

[sourcecode language="csharp"]
using Microsoft.Crm.Sdk;
using Microsoft.Crm.Sdk.Query;
[/sourcecode]

Becomes:

[sourcecode language="csharp"]
using Microsoft.Xrm.Sdk;
using Microsoft.Xrm.Sdk.Query;
[/sourcecode]

This was a bit confusing to me since I was familiar with the interim 4.0 SDK release that introduced what Microsoft referred to as "Xrm" which was basically a set of extensions and a new code generator to allow building proxies for the CRM services that could be queried using Linq. However in the 5.0 SDK, Microsoft has used the Xrm naming convention throughout the entire SDK, replacing most of the old Crm namespaces.



## Type Differences



Certain base types have been removed, one of which being the column set base type which was used for anything that specified a list of columns to return or deal with in CRM. The use of the following type:

[sourcecode language="csharp"]
ColumnSetBase
[/sourcecode]

Becomes simply:

[sourcecode language="csharp"]
ColumnSet
[/sourcecode]

As a consequence, there is no Allcolumns type to represent all of the fields of an entity. Instead we create an instance of ColumnSet using a specialized constructor to indicate that we intend to specify all columns:


[sourcecode language="csharp"]
ColumnSet cols = new ColumnSet( true );
[/sourcecode]



## Collection Types


Members that formerly used the ArrayList type are now implemented using generic lists in the form of DataCollection For example:

[sourcecode language="csharp"]
LinkEntity.LinkEntities
[/sourcecode]

Is no longer an ArrayList. Likewise, ConditionExpression.Values is now of the type:

[sourcecode language="csharp"]
[DataMemberAttribute]
public DataCollection<Object> Values { get; }
[/sourcecode]

Notice also that there is no setter, so code that creates a collection and set this property will no longer work.

That's it for now. If you liked this articled be sure to follow me on [Twitter](http://twitter.com/dnewcome) and if you're interested in what we're doing with CRM and other Microsoft technologies, be sure to follow us on Twitter [here](http://twitter.com/AltaiSystems).
