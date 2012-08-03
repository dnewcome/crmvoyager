---
comments: true
date: 2010-08-24 18:42:01
layout: post
slug: working-with-dynamicentity-properties
title: Working with DynamicEntity properties
wordpress_id: 157
---

After working on several client projects involving DynamicEntities, I have a few things to get written up from my notes. Since DynamicEntities, by definition, are not strongly-typed (in terms of CRM entities, of course they are concrete .NET types themselves) they require a bit of casting-and-cajoling in order to work with.

The first thing to note is that if a field is empty in CRM, DynamicEntity objects that are passed back from the CRM service won't have this field in the properties collection at all. This means that we have to check for the field before we even start. Accessing the collection via its string indexer will throw an exception if the property doesn't exist. In code:

``` csharp

if( dynamicEntity.Properties.Contains( "myprop" ) ) { 
     // throws if "myprop" doesn't exist in the collection
     myProp = de.Properties["myprop" ];
}

```


The second big thing is that, while we need an actual Property instance when adding properties to a DynamicEntity, they are not returned when read back from the collection. The DynamicEntity doesn't even keep a reference to the Property instance under the hood -- it basically throws it away. In code:

``` csharp

// adding
DynamicEntity de = new DynamicEntity();
de.Properties.Add( new CrmNumberProperty( "num", new CrmNumber( 42 ) ) );

// retrieving
CrmNumber num = ( CrmNumber )de.Properties[ "num" ];

```


I'll post a few more things later and hopefully some code to keep this kind of complexity in check.

Update:
I just wanted to add a little tidbit brought up by someone about iterating over a PropertyCollection. When performing an iteration over a collection of properties, there is an implicit conversion to Property that happens in the iteration statement. Take this sample code:

``` csharp

foreach( <strong>Property prop in in_entity.Properties</strong> ) {
				if( prop.Name.IndexOf( in_entity.Name + "id" ) != -1 ) {
					return ( (Key) ( <strong>( KeyProperty )</strong>prop ).Value).Value;
				}
			}


```


I've bolded the conversion to Property as well as the cast back to KeyProperty. We still start out with a Key object, but it apparently GetEnumerator() wraps things up into a Property object behind the scenes.

Here is the disassembled code for PropertyCollection.InternalGetEnumerator():

``` csharp

private IEnumerator<Property> InternalGetEnumerator()
{
    List<Property> list = new List<Property>();
    foreach (KeyValuePair<string, object> pair in this._nameToPropertyValue)
    {
        list.Add(PropertyFactory.Instance.CreateInstance(pair.Key, pair.Value));
    }
    return list.GetEnumerator();
}

```


Here we see that internally there is a PropertyFactory that wraps the raw value up into a Property.

Hopefully this sheds some light on this confusing behavior.
