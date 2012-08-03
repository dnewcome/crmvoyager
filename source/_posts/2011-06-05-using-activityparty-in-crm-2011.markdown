---
comments: true
date: 2011-06-05 11:53:09
layout: post
slug: using-activityparty-in-crm-2011
title: Using ActivityParty in CRM 2011
wordpress_id: 521
---

I was writing some client code recently when I wanted to add a customer reference to a CRM campaignresponse entity instance. Using a simple EntityReference was giving me errors, so I looked to see what the data type was in CRM. Looking in the customisations screen, the type was 'Party List'. The CRM SDK docs say that we should be able to use ActivityParty[] or EntityCollection for the field value. 

The only info on ActivityParty that I found was [for CRM4](http://crmentropy.blogspot.com/2009/04/using-activityparty-in-partylist-for.html). Through trial and error I arrived at the following code:

``` csharp

Entity customer = new Entity("activityparty");
customer["partyid" ]= new EntityReference( "contact", in_contactID );
campaignResponse["customer"] = new EntityCollection( new List(){ customer } );

```


So what I had to do was create a list of entities that had an EntityReference field set that pointed to the contact. You cannot set the EntityReference directly on the campaignresponse, it is expecting an EntityCollection. Hopefully this saves you some time.
