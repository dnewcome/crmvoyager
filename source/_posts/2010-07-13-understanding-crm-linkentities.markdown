---
comments: true
date: 2010-07-13 03:37:22
layout: post
slug: understanding-crm-linkentities
title: Understanding CRM LinkEntities
wordpress_id: 112
---

Anyone that has spent some time developing solutions for Microsoft CRM should be familiar at least with grabbing an entity instance or collection of instances from CRM using CrmService.Retrieve() or CrmSevice.RetrieveMultiple(). It is conceptually straightforward to pass CRM an ID and get the corresponding instance back. While a little more advanced, [using FilterExpressions](http://crmvoyager.wordpress.com/2010/07/07/querying-crm-with-conditions-and-filters/) is still pretty easy to visualize, since we are dealing with properties of a single entity. Things start to get a little more complex if we'd like to involve more than a single entity in our selection criteria however.

In CRM, in order to perform a query that takes more than a single entity into consideration we need to use a LinkEntity. Notice that I didn't say that we'd like to perform a query that returns more than a single entity type, since CRM QueryExpression queries are limited to returning a single type of entity. We can get around this limitation by using the FetchXml interface, but the data is returned as Xml rather than BusinessEntity instances, so we would have to pull out what we need by hand.

One of the things that makes LinkEntities confusing initially is the idea that we are traversing the relationship in a particular direction -- that is, there is a 'from' entity and a 'to' entity. In terms of a typical SQL join, we are dealing with sets, so we can express the relation from either end. That is, we could write a query that returns a parent row along with the related detail rows using either of the following SQL statements:

``` sql

select detail.* from parent 
inner join detail
on detail.parentid = parent.id
where parent.id = 123

select detail.* from detail
inner join parent 
on detail.parentid = parent.id
where parent.id = 123

```


However, in CRM, we must start the relation with the entity that we wish to return as the result. So, if we'd like to return the detail entities for a particular parent entity, we'd need to traverse the relationship from detail to parent rather than parent to detail. 

Let's try to write a LinkEntity expression using the example given above. We know that the corresponding SQL statement would be of the second form in the code listing. The LinkEntity is constructed using the entity name and field on each side of the SQL 'on' clause. The only thing to watch out for is the order -- that is, which entity is used as the 'from' side and which is the 'to' side. Since we are going from detail to parent given that we'd like to return the detail entities as the result, we'd use SQL of the form:

``` sql

detail.parentid = parent.id

```


which corresponds to the following LinkEntity construction:

``` csharp

LinkEntity linkEntity = new LinkEntity();			
linkEntity.LinkFromEntityName = "detail";
linkEntity.LinkFromAttributeName = "parentid";
linkEntity.LinkToEntityName = "parent";
linkEntity.LinkToAttributeName = "id";
linkEntity.JoinOperator = JoinOperator.Inner;

```


That seemed pretty straightforward. There is one more thing that we must do in order to construct a useful query -- add the condition that selects which parent instance we are interested in. I've covered FilterConditions in a [previous post](http://crmvoyager.wordpress.com/2010/07/07/querying-crm-with-conditions-and-filters/), and we are going to construct a FilterCondition in just the same way. Here is the code:

``` csharp

FilterExpression filter = new FilterExpression();
filter.FilterOperator = LogicalOperator.And;

ConditionExpression condition = new ConditionExpression();
condition.AttributeName = "id";
condition.Operator = ConditionOperator.Equal;
condition.Values = new int[] { 123 };

filter.Conditions = new ConditionExpression[] { condition };

```


Now that we have the filter, the only tricky part is where to put it. Since FilterExpressions don't specify the entity to which they apply, the placement of the expression is critical. Since we want to apply this filter to the parent entity, and the parent entity is the 'from' entity of the LinkCriteria, we'll need to attach it to the LinkCriteria of the LinkEntity rather than the FilterCriteria of the QueryExpression like this:

``` csharp

linkEntity.LinkCriteria = filter;

```


The full query code looks like this, including the creating of the QueryExpression:

``` csharp

LinkEntity linkEntity = new LinkEntity();			
linkEntity.LinkFromEntityName = "detail";
linkEntity.LinkFromAttributeName = "parentid";
linkEntity.LinkToEntityName = "parent";
linkEntity.LinkToAttributeName = "id";
linkEntity.JoinOperator = JoinOperator.Inner;

FilterExpression filter = new FilterExpression();
filter.FilterOperator = LogicalOperator.And;

ConditionExpression condition = new ConditionExpression();
condition.AttributeName = "id";
condition.Operator = ConditionOperator.Equal;
condition.Values = new int[] { 123 };

filter.Conditions = new ConditionExpression[] { condition };

linkEntity.LinkCriteria = filter;

QueryExpression query = new QueryExpression( in_fromEntity );
query.LinkEntities.Add( linkEntity );
query.ColumnSet = new AllColumns();

```


Hopefully this post clears up some of the confusing aspects of using LinkEntities. I have spent a long time trying to track down mysterious bugs related to getting the entity order wrong or attaching the FilterCriteria in the wrong place. The documentation doesn't explain this well in my opinion, so hopefully this saves you some grief.
