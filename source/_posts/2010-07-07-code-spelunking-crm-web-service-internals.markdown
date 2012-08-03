---
comments: true
date: 2010-07-07 00:03:23
layout: post
slug: code-spelunking-crm-web-service-internals
title: Code spelunking - CRM web service internals
wordpress_id: 80
---

I was poking around in some of the CRM libraries in the course of writing a mock CRM web service for testing purposes and I found a few interesting things that I thought would be useful to share. If you have never used the wonderful [.NET Reflector](http://www.red-gate.com/products/reflector/) you really owe it to yourself to grab it and poke around in your favorite .NET libraries to see how they work under the hood.

Anyway, on with the analysis. What I wanted to find out is how CRM knew what kind of entity was being passed in to it via the web service. If the entity happened to be a DynamicEntity, things are straightforward as there is a name field. BusinessEntity, however, has no such field. In fact, it seems to serve only as placeholder type, contributing no fields of its own. This was kind of surprising to me, as I would have thought that since every entity has a name, and every instance has an ID that we'd have some common fields here. Otherwise on the server, Microsoft would have to use reflection to figure out what we were passing.

The definition of Microsoft.Crm.Sdk.BusinessEntity looks like this:
[sourcecode language="csharp"]
[Serializable, XmlType(Namespace="http://schemas.microsoft.com/crm/2006/WebServices"), XmlInclude(typeof(DynamicEntity))]
public abstract class BusinessEntity 
{
    protected BusinessEntity();
}
[/sourcecode]

Notice that there is no superclass, and it does not implement any fields -- only a protected default constructor. I went to the CRM server and grabbed the .NET assemblies for further inspection. I was looking for the implementation of the 'Create()' web service method. It took a little guesswork, but I found the method Microsoft.Crm.Sdk.Crm2007.CrmService.Create(), which looks like the following:

[sourcecode language="csharp"]
[SoapHeader("CallerOriginToken"), CrmSoapExtension(typeof(CrmAuthenticationSoapExtension)), SoapHeader("UnknownHeaders"), SoapHeader("AuthenticationToken"), WebMethod(Description="Creates an instance of an entity."), SoapHeader("CorrelationToken")]
public Guid Create(BusinessEntity entity)
{
    Exceptions.ThrowIfNull(entity, "entity");
    base.CheckUnknownHeaders();
    CrmServiceInternal internal2 = new CrmServiceInternal();
    return internal2.Create("http://schemas.microsoft.com/crm/2007/WebServices", entity, this._correlationToken, base.CallerOriginToken, SecurityLibrary.GetCallerAndBusinessGuids(), base.CurrentCallerId);
} 
[/sourcecode]

We can see that the dirty work gets done by the Create() method on an internal class called CrmServiceInternal. Let's take a look at this method to get the details on the BusinessEntity type that we are passing around:

[sourcecode language="csharp"]
internal Guid Create(string namespaceName, BusinessEntityBase entity, CorrelationToken correlationToken, CallerOriginToken originToken, UserAuth userAuth, Guid callerId)
{
    int primaryObjectTypeCode = this.VerifyAndRetrieveEntityForCategoryMessage("Create", "Create", namespaceName, entity.LogicalName);
    PropertyBag fields = new PropertyBag();
    fields["OptionalParameters"] = new OptionalParameter[0];
    fields["Target"] = entity.InternalEntity;
    return (Guid) ExternalMessageDispatcher.Instance.Execute("Create", primaryObjectTypeCode, 0, fields, correlationToken, originToken, userAuth, callerId)["id"];
}
[/sourcecode]

So here we use a field called LogicalName, which wasn't on BusinessEntity when we looked at it before. Looking at the argument list, it takes a single argument of type BusinessEntity, which is what we were expecting, but on navigating to the type, I realized that it was a different type altogether than what we had in the client-side library. This version of BusinessEntity derives from a BusinessEntityBase type, as follows:

[sourcecode type="csharp"]
public class BusinessEntity : BusinessEntityBase
{
    public BusinessEntity();
    public BusinessEntity(DynamicEntity entity, bool skipOuterNode);
}
[/sourcecode]

And this base type has a field for the type of the entity:
[sourcecode language="csharp"]
public abstract class BusinessEntityBase : IXmlSerializable
{
    public DynamicEntity InternalEntity { get; set; }
    public string LogicalName { get; }
    public string PlatformName { get; }
}
[/sourcecode]

So, by virtue of serialization, the types are effectively converted from a type in one hierarchy to one in another unrelated hierarchy. This was a great exercise in understanding what was going on under the hood, but unfortunately didn't help me out much when creating my mock service. I might try to use the internal CRM classes for this to see where it gets me, but since I'm aiming to mock what the client sees, this doesn't seem likely to help.
 
As a parting note, I'll add that the server side implementation of the web service does not actually implement ICrmService that is presented to the client via the SDK.

