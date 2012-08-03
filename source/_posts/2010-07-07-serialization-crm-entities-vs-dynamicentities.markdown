---
comments: true
date: 2010-07-07 20:46:46
layout: post
slug: serialization-crm-entities-vs-dynamicentities
title: 'Serialization: CRM entities vs DynamicEntities'
wordpress_id: 104
---

I did a quick experiment to see what the serialization formats look like for a DynamicEntity and a static entity type as found in the Microsoft.Crm.SdkTypeProxy namespace. The results were predictable by looking at the structure of each type of entity, but I thought that I'd post my results here if for no other reason than to provide you with some canned CRM data that you can use for testing, which was the entire point of this exercise for me. I also wanted to know if CRM entities could be 'round-tripped' -- that is, serialized to xml and then deserialized back to entity instances. The answer is yes, they certainly can. So we can grab some data from a live CRM installation and use it in offline tests. 

The following code illustrates the concept of using .NET serialization to round-trip a .NET type (a CRM entity in our specific case):
[sourcecode language="csharp"]
	public static Object RoundTrip( Type in_type, Object in_instance ) {
			System.Xml.Serialization.XmlSerializer xser = new System.Xml.Serialization.XmlSerializer( in_type );
			
			MemoryStream ms = new MemoryStream();
			xser.Serialize( ms, in_instance );
			ms.Seek( 0, SeekOrigin.Begin );

			Object retval = xser.Deserialize( ms );
			ms.Close();
			return retval;
		}
[/sourcecode]

When we save the intermediate results to disk, we get a file like this for a static product type:
[sourcecode language="xml"]
<?xml version="1.0"?>
<product xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
  <createdby name="First name Last name" dsc="0" xmlns="http://schemas.microsoft.com/crm/2007/WebServices">3bbf2574-c324-df11-b753-0800277bde92</createdby>
  <createdon date="6/22/2010" time="3:06 PM" xmlns="http://schemas.microsoft.com/crm/2007/WebServices">2010-06-22T15:06:20-04:00</createdon>
  <defaultuomid name="Primary Unit" dsc="0" xmlns="http://schemas.microsoft.com/crm/2007/WebServices">36d207f3-3d9e-4e29-abf7-de19896e78dc</defaultuomid>
  <defaultuomscheduleid name="Default Unit" dsc="0" xmlns="http://schemas.microsoft.com/crm/2007/WebServices">999cdc43-8207-42f5-b15b-f4afdaee3f0f</defaultuomscheduleid>
  <exchangerate formattedvalue="1.0000000000" xmlns="http://schemas.microsoft.com/crm/2007/WebServices">1</exchangerate>
  <iskit name="No" xmlns="http://schemas.microsoft.com/crm/2007/WebServices">false</iskit>
  <isstockitem name="No" xmlns="http://schemas.microsoft.com/crm/2007/WebServices">false</isstockitem>
  <modifiedby name="First name Last name" dsc="0" xmlns="http://schemas.microsoft.com/crm/2007/WebServices">3bbf2574-c324-df11-b753-0800277bde92</modifiedby>
  <modifiedon date="6/22/2010" time="5:14 PM" xmlns="http://schemas.microsoft.com/crm/2007/WebServices">2010-06-22T17:14:49-04:00</modifiedon>
  <name xmlns="http://schemas.microsoft.com/crm/2007/WebServices">Test Product</name>
  <organizationid name="danco" dsc="0" xmlns="http://schemas.microsoft.com/crm/2007/WebServices">c1c701e4-f69c-4363-8dba-41aa5d5754ac</organizationid>
  <pricelevelid name="My Price List" dsc="0" xmlns="http://schemas.microsoft.com/crm/2007/WebServices">4098399d-317e-df11-a711-0800277bde92</pricelevelid>
  <productid xmlns="http://schemas.microsoft.com/crm/2007/WebServices">40b0ae3d-317e-df11-a711-0800277bde92</productid>
  <productnumber xmlns="http://schemas.microsoft.com/crm/2007/WebServices">PROD1</productnumber>
  <producttypecode name="Sales Inventory" xmlns="http://schemas.microsoft.com/crm/2007/WebServices">1</producttypecode>
  <quantitydecimal formattedvalue="2" xmlns="http://schemas.microsoft.com/crm/2007/WebServices">2</quantitydecimal>
  <statecode formattedvalue="Active" xmlns="http://schemas.microsoft.com/crm/2007/WebServices">Active</statecode>
  <statuscode name="Active" xmlns="http://schemas.microsoft.com/crm/2007/WebServices">1</statuscode>
  <transactioncurrencyid name="US Dollar" dsc="0" xmlns="http://schemas.microsoft.com/crm/2007/WebServices">447adec9-c324-df11-b753-0800277bde92</transactioncurrencyid>
</product>
[/sourcecode]

The DynamicEntity looks like this:
[sourcecode language="csharp"]
<?xml version="1.0"?>
<DynamicEntity xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Name="product">
  <Properties xmlns="http://schemas.microsoft.com/crm/2006/WebServices">
    <Property xsi:type="StateProperty" Name="statecode">
      <Value>Active</Value>
    </Property>
    <Property xsi:type="KeyProperty" Name="productid">
      <Value>40b0ae3d-317e-df11-a711-0800277bde92</Value>
    </Property>
    <Property xsi:type="CrmBooleanProperty" Name="iskit">
      <Value name="No">false</Value>
    </Property>
    <Property xsi:type="CrmDateTimeProperty" Name="createdon">
      <Value date="6/22/2010" time="3:06 PM">2010-06-22T15:06:20-04:00</Value>
    </Property>
    <Property xsi:type="CrmDateTimeProperty" Name="modifiedon">
      <Value date="6/22/2010" time="5:14 PM">2010-06-22T17:14:49-04:00</Value>
    </Property>
    <Property xsi:type="LookupProperty" Name="defaultuomscheduleid">
      <Value name="Default Unit" type="uomschedule" dsc="0">999cdc43-8207-42f5-b15b-f4afdaee3f0f</Value>
    </Property>
    <Property xsi:type="PicklistProperty" Name="producttypecode">
      <Value name="Sales Inventory">1</Value>
    </Property>
    <Property xsi:type="StringProperty" Name="name">
      <Value>Test Product</Value>
    </Property>
    <Property xsi:type="CrmBooleanProperty" Name="isstockitem">
      <Value name="No">false</Value>
    </Property>
    <Property xsi:type="StatusProperty" Name="statuscode">
      <Value name="Active">1</Value>
    </Property>
    <Property xsi:type="LookupProperty" Name="transactioncurrencyid">
      <Value name="US Dollar" type="transactioncurrency" dsc="0">447adec9-c324-df11-b753-0800277bde92</Value>
    </Property>
    <Property xsi:type="CrmDecimalProperty" Name="exchangerate">
      <Value formattedvalue="1.0000000000">1.0000000000</Value>
    </Property>
    <Property xsi:type="LookupProperty" Name="pricelevelid">
      <Value name="My Price List" type="pricelevel" dsc="0">4098399d-317e-df11-a711-0800277bde92</Value>
    </Property>
    <Property xsi:type="LookupProperty" Name="modifiedby">
      <Value name="First name Last name" type="systemuser" dsc="0">3bbf2574-c324-df11-b753-0800277bde92</Value>
    </Property>
    <Property xsi:type="StringProperty" Name="productnumber">
      <Value>PROD1</Value>
    </Property>
    <Property xsi:type="LookupProperty" Name="defaultuomid">
      <Value name="Primary Unit" type="uom" dsc="0">36d207f3-3d9e-4e29-abf7-de19896e78dc</Value>
    </Property>
    <Property xsi:type="CrmNumberProperty" Name="quantitydecimal">
      <Value formattedvalue="2">2</Value>
    </Property>
    <Property xsi:type="LookupProperty" Name="createdby">
      <Value name="First name Last name" type="systemuser" dsc="0">3bbf2574-c324-df11-b753-0800277bde92</Value>
    </Property>
    <Property xsi:type="LookupProperty" Name="organizationid">
      <Value name="danco" type="organization" dsc="0">c1c701e4-f69c-4363-8dba-41aa5d5754ac</Value>
    </Property>
  </Properties>
</DynamicEntity>
[/sourcecode]

I'll be following up with how I'm using this in my mock CRM implementation that I'm going to use for testing.
