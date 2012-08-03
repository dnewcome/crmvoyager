---
comments: true
date: 2011-10-25 00:02:40
layout: post
slug: digging-into-microsoft-crm-4-dynamicentity-properties
title: Digging into Microsoft CRM 4 DynamicEntity properties
wordpress_id: 544
---

I've been meaning to write this post for a while. Even though CRM 4 is on the way out now, I still find myself working with clients that have legacy CRM 4 systems in production. Whether it is porting code or fixing bugs, CRM 4 is far from gone.

For those of you that don't know, the DynamicEntity type gives us a way to write code against an arbitrary CRM4 installation without having to generate a custom service proxy. We don't get the advantages of having all of the field names specified (early binding) but we don't have to recompile for different client installations in order to get access to new fields. 

However, despite of (or perhaps due to) the flexibility that DynamicEntities afford, there are some idiosyncratic things about how data property values are set and retrieved.

The hallmark of CRM 4 DyanmicEntities is the use of property classes for the data fields of the entity. These classes are all of the form XXXProperty, where XXX is something like String, CrmMoney, etc. CRM 2011 has largely done away with these special-purpose field wrappers, but in CRM 4 we are stuck with them. This wouldn't be so bad, but there are some inconsistencies in their use in the Microsoft CRM API. I'll go over a few cases that have bitten me in the past here.



### 1. Iteration



[sourcecode language="csharp"]
foreach( Property prop in m_entity.Properties ) {
    /// do something
}
[/sourcecode]

When we use a foreach construct, the IEnumerator implementation returns a Property type. Why is this inconsistent? Well it isn't from an API standpoint, but it was very confusing to me when I took a look at how the data was actually stored at runtime. Hint: there are no Property objects to be found in the backing store. They are created dynamically in the IEnumerator implementation. Take a look at the disassembly of IGetEnumerator():

[sourcecode language="csharp"]
private IEnumerator<Property> InternalGetEnumerator()
{
    List<Property> list = new List<Property>();
    foreach (KeyValuePair<string, object> pair in this._nameToPropertyValue)
    {
        list.Add(PropertyFactory.Instance.CreateInstance(pair.Key, pair.Value));
    }
    return list.GetEnumerator();
}
[/sourcecode]

We can see that the Property types are actually being created and returned on-the-fly from the internal data store _nameToPropertyValue.



### 2. Assignment



We can do assignment in one of two ways. In each case the value that we assign will be slightly different.

[sourcecode language="csharp"]
DyanmicEntity entity = new DynamicEntity();
entity.Properties.Add( new StringProperty( key, value ) );
[/sourcecode]

or

[sourcecode language="csharp"]
entity[ key ] = value;
[/sourcecode]

This example also applies to retrieval of the property value, that is, if we want to get the plain string without the StringProperty wrapper we would write:

[sourcecode language="csharp"]
string val = entity[ key ];
[/sourcecode]

I've noticed that when we make an assignment using a Property, the SDK library actually throws it away and only stores the inner value! This makes the property simply an ephemeral container that is effectively used only for conveying the field name. Check out disassembly for PropertyCollection.Add():

[sourcecode language="csharp"]
public void Add(Property property)
{
    this._nameToPropertyValue[property.Name] = property.GetValue();
}
[/sourcecode]
 
So when iterating and using Add() we are dealing with Property types. When using [] indexer notation we are dealing with the underlying value data type. I find this to be quite inconsistent and tedious, but as long as it is kept in mind, things work out ok.

Hopefully this clears up some confusion about the finer points of DynamicEntities for you!


