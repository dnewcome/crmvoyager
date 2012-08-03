---
comments: true
date: 2011-11-17 01:27:28
layout: post
slug: programmatically-creating-crm-4-entity-metadata
title: Programmatically creating CRM 4 entity metadata
wordpress_id: 576
---

I have built a few tools to make life easier for myself. One of the tools that has proven itself invaluable is a metadata generator that I use to create a list of entities in a newly-created CRM bizorg.

Although the API supports most things that we'd want to do, there are a few tricky things that aren't that obvious from the documentation. I'm going to go into this in more detail in a future post, but there are a few key takeaways that I want to make sure I remember, so I'm posting them here now.

1) Creating an entity doesn't create any attributes other than the primary attribute.
Even if we give CRM a fully-populated entity metadata graph, only the primary attribute will be created on the CreateEntityRequest call.

2) An entity must have a primary attribute specified.
Creating the entity will fail if one string attribute is not provided and the primary attribute field is set to this entity. Both of these conditions must be met.

3) Lookup attributes cannot be created via the normal attribute metadata service call.
Entity references are created via a completely separate call.

4) Creating lookup attributes will fail if the referenced entity does not exist.

5) Entity names must adhere to the CRM naming prefix convention.
Entity names and field names must have a prefix and an underscore in order to be considered valid schema names.

6) Cryptic "Generic SQL" errors are often caused by missing labels
I'm still compiling a checklist of things that cause this frustrating error. Many times a call will fail with a SQL error, and it is very difficult to figure out what went wrong since there is no detail given.

7) Adding entities and fields don't require that we publish, but adding new picklist options does require a separate publish action before the items can be used.

8) Boolean fields must be specially treated and given labels for the true and false textual values.
If this is not done, the field will silently fail to be created.

That's a brief list for now. Expanding on all of these things will take a whole series of posts I'm sure. This stuff is way under-documented and is hard-won knowledge, so I'll be sure to give it a proper treatment here on the blog.
