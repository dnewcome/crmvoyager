---
comments: true
date: 2010-10-13 03:22:59
layout: post
slug: data-binding-in-silverlight
title: Data binding in Silverlight
wordpress_id: 206
---

One of the most compelling features of Microsoft Silverlight is the ability to display data in a rich and dynamic grid view. This grid view shares many features of its big siblings over in ASP.NET, but there are some differences to watch out for. I'm going to focus on one difference that affected me in a recent project in order to focus this post and keep things short.

**There is no DataSet in Silverlight**

What, you ask, how can we do advanced data binding without DataSets? I asked the same thing, but the answer lies in the .NET Framework 4's [ObservableCollection](http://msdn.microsoft.com/en-us/library/ms668604.aspx). System.Collections.ObjectModel.ObservableCollection provides a notification interface in the form of events that are raised when items are added or removed from the list. This allows the Silverlight GridView UI to stay updated when the underlying data changes.

In order to create a bindable data structure, I created a POCO (plain old C# object) that had the fields I wished to display like so:

[sourcecode language="csharp"]
	public class Product
	{
		public string name { get; set; }
		public string productid { get; set; }
		public string price { get; set; }
               ...
      }
[/sourcecode]

Once we have a class to represent the list items, we instantiate the generic list using the new type:

[sourcecode language="csharp"]
ObservableCollection<Product> itemsource = new ObservableCollection<Product>();
[/sourcecode]

The ObservableCollection supports all list operations, so building the list works just like any other IList:
[sourcecode language="csharp"]
itemsource.Add( new Product() );
[/sourcecode]

Finally we can bind the new collection to the grid as follows:
[sourcecode language="csharp"]
dataGrid.ItemsSource = itemsource;
[/sourcecode]

This is just the tip of the iceberg with databinding in Silverlight. In a forthcoming post I'm going to show how Silverlight supports binding to nested collections.
