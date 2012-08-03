---
comments: true
date: 2010-09-09 23:12:29
layout: post
slug: crm-api-quirks
title: CRM API quirks
wordpress_id: 164
---

I come across oddities and oversights pretty frequently in the CRM SDK/API. The latest one is that there is no way to write a generic method to pull a property value out of a CRM Property type. Let me show you what I'd like to write:

[sourcecode language="csharp"]
public static TResult GetPropertyValue<T, TResult>( T in_property ) where T : Property
{
      try {
        return ( ( T )in_property ).Value;
     }
     catch{
        return default( TResult );
    }
}
[/sourcecode]

This doesn't work since Property doesn't define a value parameter. It is up to the individual derived classes to define this. I can understand maybe they didn't want to constrain all Property types to have a value, but it would be nice to have IDataProperty or some kind of interface that defines the Value parameter. To put this into perspective, the SDK code that handles this simple function is nearly 100 lines of code, and relies on runtime type identification (reflection) in order to determine how to cast the value properly.

[sourcecode language="csharp"]
       private object GetPropertyValue(object prop)
        {
            Type propertyType = prop.GetType();
            if( propertyType == typeof( StringProperty ) ) {
                return ( (StringProperty )prop ).Value;
            }
            else if (propertyType == typeof(CrmDateTimeProperty)) {
               ...
           }
       ...
     }
[/sourcecode]
