---
comments: true
date: 2010-09-21 01:53:01
layout: post
slug: key-properties-in-crm
title: Key properties in CRM
wordpress_id: 170
---

I was debugging a piece of code just now when I realized that my problem was in an assumption that I had made about Key properties in a DynamicEntity.

I thought that a Key field was a unique key for the entity instance, which it is, sort of. There apparently can be more than one Key field in an entity instance, so my code that was supposed to return the ID of an entity instance wasn't working because it was finding a second Key field:

``` csharp

		public static Guid GetDynamicEntityId( DynamicEntity in_entity ) {
			foreach( object prop in in_entity.Properties ) {
				if( prop is KeyProperty ) {
					return ( ( Key )( ( KeyProperty )prop ).Value ).Value;
				}
			}
			return Guid.Empty;
		}

```


I'm at a loss now for a good way to return the ID of an entity instance. You could always assume that it is going to be the entity name + 'id', but I don't think that is 100% reliable. I could be wrong though. Since the IDs are GUIDs, I have no idea why it makes any sense to have more than one key (or a composite key). I'm still looking into this one.

Update:
I ended up with a hack that just looks at the entity name and tacks on 'id' to the end and searches for this string pattern in the attribute names. This feels pretty dirty, but I don't know what else to do.

Here is the code:

``` csharp

	public static Guid GetDynamicEntityId( DynamicEntity in_entity ) {
			foreach( Property prop in in_entity.Properties ) {
				if( prop.Name.IndexOf( in_entity.Name + "id" ) != -1 ) {
					return ( (Key) ( ( KeyProperty )prop ).Value).Value;
				}
			}
			return Guid.Empty;
		}

```

