---
comments: true
date: 2011-04-22 16:13:33
layout: post
slug: storing-a-guid-in-an-asp-net-checkbox-control
title: Storing a Guid in an ASP.NET CheckBox control
wordpress_id: 495
---

I recently had to build a simple UI for listing out a set of Microsoft CRM entities to choose from. The natural way of presenting this to the user was to give them a list of items with some associated checkboxes for selection. I wanted to associate a CRM entity ID with each checkbox so that I could update the entity in CRM when the user submitted the form.

There are a number of different ways that this problem can be approached. One thing that I've done in the past is to use hidden form fields to store the data for postback. Another would be to use the entity Guid as the control ID. Yet another way would be to store a dictionary in ASP.NET session state, keyed by the control ID.

However, I wanted to create a more elegant solution to this problem by attaching a Guid field directly to the CheckBox control so that we'd have easy access to it during page postback.

My first thought was to create an ASP.NET server control that extends Microsoft's own CheckBox control. This is easy to do, as I show in the following code:

``` csharp

public class ExtendedCheckbox : CheckBox
{
	public Guid ItemID { get; set; }
	public ExtendedCheckbox() {}
}

```


All we did was add a single property to the basic CheckBox control. This code will let us use the ExtendedCheckbox control on our pages and allow us to set a property called ItemID. For example:

``` html

<djn:ExtendedCheckbox runat="server" ItemID="<%=myID%>"/>

```


However in order to be useful to us, the value of ItemID must persist when the page is posted back. When the user hits the 'submit' button on our page, we presumably want to do something useful with this ID like make a CRM request to modify the entity with the checkbox information. All of the built-in properties of the CheckBox use the ASP.NET ViewState mechanism to accomplish this. Unfortunately our custom property doesn't benefit from the base class's ViewState implicitly, so we have to make a change to our property as follows:

``` csharp

public Guid ItemID {
	get {
		if( ViewState["ItemID"] != null )
			return ( Guid )ViewState["ItemID"];
		return default( Guid );
	}
	set { ViewState["ItemID"] = value; }
}

```

 
So you can see that we used the base class's own ViewState collection to store our own data for the custom property. Now when we do a postback, the value of ItemID will be available to us.

To use this new control, we have to register it in the page where we are going to use it. In my case, the code for the new control is in the App_Code folder, so my @register directive looks like this:

``` html

<%@ Register TagPrefix="djn" namespace="Djn.Controls" Assembly="__code" %>

```


Note that we can reference the assembly generated from App_Code by using __code as the assembly reference.

We can now use the control just like a built in control, albeit with a new custom field. Accessing the ItemID later on in code is as simple as referencing the checkbox by name:

``` csharp

Guid id = myCustomCheckbox.ItemID

```


Hopefully this gives you some ideas when doing custom ASP.NET work for your clients.
