---
comments: true
date: 2010-05-19 21:26:48
layout: post
slug: dependency-properties-rant
title: Dependency properties rant
wordpress_id: 33
---

- Update:
I ran into these issues when testing the activity classes outside of a workflow runtime context. It turns out that there is more to this than I thought. Once running inside of CRM we don't have the name collision issue. However, I wasn't able to put common properties in a superclass, so apparently trying to test workflows outside of CRM only works in certain cases.

When I first looked at Windows Workflow Foundation and WPF back when .net 3.0 hit RTM I thought that dependency properties were confusing. Or at the very least over-complex. Probably both. As I looked further into WPF and saw the XAML magic that they enabled I guess I eased up a little bit. I thought I understood them pretty fully until today when I was writing a suite of workflow activities for MS CRM.

My development work was moving along swimmingly, and the test cases of my first activity were passing with flying colors. As I started in on testing the rest of the activities I ran into some issues.

I got the following runtime error following the successful run of the tests on the first activity and before the second:
[sourcecode]
Unhandled Exception: System.TypeInitializationException: The type initializer for 'Altai.Workflow.ContractActivity' threw an exception. ---> System.InvalidOperationException: DependencyProperty 'FtpPassword' could not be registered.  A property with the same name already exists for type 'Altai.Workflow.VendorActivity'.
   at System.Workflow.ComponentModel.DependencyProperty.ValidateAndRegister(String name, Type propertyType, Type ownerType, PropertyMetadata defaultMetadata, Type validatorType, Boolean isRegistered)
[/sourcecode]




Since the dependency property boilerplate code is so verbose, I used codegen to spit out all of the properties that I needed. I figured I made a mistake when running my codegen tool and specified the same type name for the properties on both of the activity classes. I double checked everything and it turned out that everything looked fine. On a hunch I changed the names given to the properties so that they were unique and the error was replaced by a new error:


[sourcecode]
Unhandled Exception: System.TypeInitializationException: The type initializer for 'Altai.Workflow.VendorActivity' threw an exception. ---> System.ArgumentException: Type 'Altai.Workflow.VendorActivity' does not define a static dependency property with name 'PVAFtpPasswordProperty'.
[/sourcecode]




So it looks like the properties are looked up by the **string name alone. **This surprised me since we give the .net type when we register the property. Really I don't know what Microsoft was thinking with this one. Not only are dependency properties globally registered using a static registry, they don't use the registered type to disambiguate potential conflicts in field names.




So to restate my displeasure here, not only are we referencing the registered properties using a string and ignoring the type info provided, **we are doing so lazily at runtime when the type initializers for the class are running. **This is one of those cases where type safety is just an illusion, and really only serves to trip us up while simultaneously providing a false sense of security.
