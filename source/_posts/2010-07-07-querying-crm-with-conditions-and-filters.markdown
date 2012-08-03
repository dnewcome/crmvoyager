---
comments: true
date: 2010-07-07 03:03:13
layout: post
slug: querying-crm-with-conditions-and-filters
title: Querying CRM with conditions and filters
wordpress_id: 86
---

On more than one occasion I've had to assemble a complex query for retrieving CRM entities. Unfortunately, the QueryExpression API can be pretty confusing. I've actually written several query APIs for other projects myself, and this one still trips me up sometimes. 

The basis for my confusion, and probably the confusion of many others, is that the naming is slightly confusing and it is not obvious at first blush why there is a need for both FilterExpression and ConditionExpression.

To clear up the naming convention, there are two types that are used in the construction of the selection condition: FilterExpression and ConditionExpression. FilterExpression is used to express boolean conjunctions and disjunctions (and and or), and ConditionExpression is used to represent (in)equality, and set membership. There is an inconsistency in naming of the field that holds the filters at the top level however, there being a QueryExpression.Criteria property rather than QueryExpression.Filters.

One way to look at a complex query is that the criteria consist of a tree of FilterExpressions with ConditionExpressions as the terminals. That is, FilterExpression has a collection of other FilterExpressions, whereas ConditionExpressions do not contain any further expressions.

As an example, if we wanted to express the clause:
[sourcecode]
( A = 1 and ( B = 2 or C = 3 ) ) 
[/sourcecode]
we would need two FilterExpressions and three ConditionExpressions. It is easy to see how to construct the ConditionExpressions - we simply create three instances using ConditionOperator.Equal as the operator. For example:

[sourcecode language="csharp"]
ConditionExpression conditionA = new ConditionExpression();
conditionA.AttributeName = "A";
conditionA.Operator = ConditionOperator.Equal;
conditionA.Values = new int[] { 1 }; 
[/sourcecode]

Notice that the value is placed in an array of integers. ConditionExpression.Values is a collection since we support the possibility of testing for set membership, thus necessitating the use of a collection for the possible values.

Next we create two filters, one each for the 'and' and 'or' expressions:
[sourcecode language="csharp"]
FilterExpression andFilter = new FilterExpression();
andFilter.FilterOperator = LogicalOperator.And;

FilterExpression orFilter = new FilterExpression();
childFilter.FilterOperator = LogicalOperator.Or;

// Now establish the filter hierarchy
andFilter.Filters = new FilterExpression[] { orFilter };
[/sourcecode]

Once the filters are set up, we can apply the conditionals:
[sourcecode language="csharp"]
andFilter.Conditions = new ConditionExpression[] { conditionA };
orFilter.Conditions = new ConditionExpression[] { conditionB, conditionC };
[/sourcecode]

I have organized the code slightly differently than most of the code samples available from Microsoft. I think that looking at the filters as a tree structure makes things clearer. Notice that a ConditionExpression is given the same consideration that a FilterExpression is given when evaluating the entire expression -- the boolean operator is applied to conditionA and the result of  the evaluation of orFilter.

Somehow, I think that this API could have been simpler. I might explore wrapping this API in something a little less confusing, perhaps at the expense of supporting less frequently used operations. Hopefully this gives you a slightly different perspective on constructing Criteria expressions in CRM. I'll cover (the also confusing) LinkEntity in an upcoming post.

