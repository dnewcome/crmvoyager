---
comments: true
date: 2011-10-24 18:19:50
layout: post
slug: list-of-type-differences-between-crm4-and-2011
title: List of type differences between CRM4 and 2011
wordpress_id: 538
---

I'm posting this list since it seems to be difficult for me to find quickly in the SDK documentation. It is an incredibly handy list of type mappings that are useful for converting CRM4 code to 2011 or for (in my case) writing abstraction libraries that need to convert between the two different CRM versions.

Without further ado, the [list is here](http://msdn.microsoft.com/en-us/library/gg328507.aspx).

I'm including it inline here for quick reference:





<table style='border:1px solid black'>

	  <tr>

		<th>

		  <strong>AttributeTypeCode</strong>

		</th>

		<th>

								

									Microsoft Dynamics CRM 2011 type

							</th>

		<th>
Microsoft Dynamics CRM 4.0 type </th>

	  </tr>

	  <tr>

		<td>

		  <p>

			<a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.attributetypecode.aspx">AttributeTypeCode</a>.<a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.attributetypecode.aspx">BigInt</a></p>

		</td>

		<td>

		  <p>

			<strong>long</strong>

		  </p>

		</td>

		<td>

		  <p>N/A</p>

		</td>

	  </tr>

	  <tr>

		<td>

		  <p>

			<strong>AttributeTypeCode</strong>.<a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.attributetypecode.aspx">Boolean</a></p>

		</td>

		<td>

		  <p>

			<strong>bool</strong>

		  </p>

		</td>

		<td>

		  <p>

			<a href="http://msdn.microsoft.com/en-us/library/bb928926.aspx">CrmBoolean</a>

		  </p>

		</td>

	  </tr>

	  <tr>

		<td>

		  <p>

			<a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.attributemetadata.attributetype.aspx">AttributeType</a>.<a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.attributetypecode.aspx">CalendarRules</a></p>

		</td>

		<td>

		  <p>

			

		  </p>

		  <p>

			<a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.entitycollection.aspx">EntityCollection</a> or <strong>CalendarRules</strong>[]</p>

		</td>

		<td>

		  <p>

			<a href="http://msdn.microsoft.com/en-us/library/bb956153.aspx">DynamicEntity</a>[] or <a href="http://msdn.microsoft.com/en-us/library/bb928506.aspx">calendarrule</a>[]</p>

		</td>

	  </tr>

	  <tr>

		<td>

		  <p>

			<strong>AttributeType</strong>.<a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.attributetypecode.aspx">Customer</a></p>

		</td>

		<td>

		  <p>

			<a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.entityreference.aspx">EntityReference</a>

		  </p>

		</td>

		<td>

		  <p>

			<a href="http://msdn.microsoft.com/en-us/library/bb955969.aspx">Customer</a>

		  </p>

		</td>

	  </tr>

	  <tr>

		<td>

		  <p>

			<strong>AttributeType</strong>.<a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.attributetypecode.aspx">DateTime</a></p>

		</td>

		<td>

		  <p>

			<strong>System.DateTime</strong>

		  </p>

		</td>

		<td>

		  <p>

			<a href="http://msdn.microsoft.com/en-us/library/bb928935.aspx">CrmDateTime</a>

		  </p>

		</td>

	  </tr>

	  <tr>

		<td>

		  <p>

			<strong>AttributeType</strong>.<a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.attributetypecode.aspx">Decimal</a></p>

		</td>

		<td>

		  <p>

			<strong>decimal</strong>

		  </p>

		</td>

		<td>

		  <p>

			<a href="http://msdn.microsoft.com/en-us/library/bb928945.aspx">CrmDecimal</a>

		  </p>

		</td>

	  </tr>

	  <tr>

		<td>

		  <p>

			<strong>AttributeType</strong>.<a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.attributetypecode.aspx">Double</a></p>

		</td>

		<td>

		  <p>

			<strong>double</strong>

		  </p>

		</td>

		<td>

		  <p>

			<a href="http://msdn.microsoft.com/en-us/library/bb928954.aspx">CrmFloat</a>

		  </p>

		</td>

	  </tr>

	  <tr>

		<td>

		  <p>

			<strong>AttributeType</strong>.<a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.attributetypecode.aspx">EntityName</a></p>

		</td>

		<td>

		  <p>

			<strong>string</strong>

		  </p>

		</td>

		<td>

		  <p>Attributes with <strong>ObjectTypeCode</strong> in <strong>DisplayMask</strong></p>

		</td>

	  </tr>

	  <tr>

		<td>

		  <p>

			<strong>AttributeType</strong>.<a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.attributetypecode.aspx">Integer</a></p>

		</td>

		<td>

		  <p>

			<strong>int</strong>

		  </p>

		</td>

		<td>

		  <p>

			<a href="http://msdn.microsoft.com/en-us/library/bb928972.aspx">CrmNumber</a>

		  </p>

		</td>

	  </tr>

	  <tr>

		<td>

		  <p>

			<strong>AttributeType</strong>.<a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.attributetypecode.aspx">Lookup</a></p>

		</td>

		<td>

		  <p>

			<strong>EntityReference</strong>

		  </p>

		</td>

		<td>

		  <p>

			<a href="http://msdn.microsoft.com/en-us/library/bb957132.aspx">Lookup</a>

		  </p>

		</td>

	  </tr>

	  <tr>

		<td>

		  <p>

			<strong>AttributeType</strong>.<a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.attributetypecode.aspx">ManagedProperty</a></p>

		</td>

		<td>

		  <p>

			<a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.booleanmanagedproperty.aspx">BooleanManagedProperty</a>

		  </p>

		</td>

		<td>

		  <p>N/A</p>

		</td>

	  </tr>

	  <tr>

		<td>

		  <p>

			<strong>AttributeType</strong>.<a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.attributetypecode.aspx">Memo</a></p>

		</td>

		<td>

		  <p>

			<strong>string</strong>

		  </p>

		</td>

		<td>

		  <p>

			<strong>System.String</strong>

		  </p>

		</td>

	  </tr>

	  <tr>

		<td>

		  <p>

			<strong>AttributeType</strong>.<a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.attributetypecode.aspx">Money</a></p>

		</td>

		<td>

		  <p> <a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.money.aspx">Money</a></p>

		</td>

		<td>

		  <p>

			<a href="http://msdn.microsoft.com/en-us/library/bb928963.aspx">CrmMoney</a>

		  </p>

		</td>

	  </tr>

	  <tr>

		<td>

		  <p>

			<strong>AttributeType</strong>.<a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.attributetypecode.aspx">Owner</a></p>

		</td>

		<td>

		  <p>

			<strong>EntityReference</strong>

		  </p>

		</td>

		<td>

		  <p>

			<a href="http://msdn.microsoft.com/en-us/library/bb670715.aspx">Owner</a>

		  </p>

		</td>

	  </tr>

	  <tr>

		<td>

		  <p>

			<strong>AttributeType</strong>.<a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.attributetypecode.aspx">PartyList</a></p>

		</td>

		<td>

		  <p> <strong>EntityCollection</strong> or <strong>ActivityParty</strong>[]</p>

		</td>

		<td>

		  <p>

			<a href="http://msdn.microsoft.com/en-us/library/bb955715.aspx">activityparty[]</a> or <a href="http://msdn.microsoft.com/en-us/library/bb956153.aspx">DynamicEntity</a> []</p>

		</td>

	  </tr>

	  <tr>

		<td>

		  <p>

			<strong>AttributeType</strong>.<a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.attributetypecode.aspx">Picklist</a></p>

		</td>

		<td>

		  <p>

			<a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.optionsetvalue.aspx">OptionSetValue</a>

		  </p>

		</td>

		<td>

		  <p>

			<a href="http://msdn.microsoft.com/en-us/library/bb929048.aspx">Picklist</a>

		  </p>

		</td>

	  </tr>

	  <tr>

		<td>

		  <p>

			<strong>AttributeType</strong>. <a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.attributetypecode.aspx">Uniqueidentifier</a> (Formerly <strong>PrimaryKey</strong>)</p>

		</td>

		<td>

		  <p>

			<strong>System.Guid</strong>

		  </p>

		</td>

		<td>

		  <p>

			<a href="http://msdn.microsoft.com/en-us/library/bb956919.aspx">Key</a>

		  </p>

		</td>

	  </tr>

	  <tr>

		<td>

		  <p>

			<strong>AttributeType</strong>.<a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.attributetypecode.aspx">String</a></p>

		</td>

		<td>

		  <p>

			<strong>string</strong>

		  </p>

		</td>

		<td>

		  <p>

			<strong>System.String</strong>

		  </p>

		</td>

	  </tr>

	  <tr>

		<td>

		  <p>

			<strong>AttributeType</strong>.<a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.attributetypecode.aspx">State</a></p>

		</td>

		<td>

		  <p>

			<strong>OptionSetValue</strong> or enumeration generated for the entity state</p>

		</td>

		<td>

		  <p>

			<em>EntityName</em>StateInfo</p>

		</td>

	  </tr>

	  <tr>

		<td>

		  <p>

			<strong>AttributeType</strong>.<a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.attributetypecode.aspx">Status</a></p>

		</td>

		<td>

		  <p>

			<strong>OptionSetValue</strong>

		  </p>

		</td>

		<td>

		  <p>

			<a href="http://msdn.microsoft.com/en-us/library/bb958198.aspx">Status</a>

		  </p>

		</td>

	  </tr>

	  <tr>

		<td>

		  <p>

			<strong>AttributeType</strong>.<strong>Uniqueidentifier</strong></p>

		</td>

		<td>

		  <p>

			<strong>System.Guid</strong>

		  </p>

		</td>

		<td>

		  <p>

			<a href="http://msdn.microsoft.com/en-us/library/bb890012.aspx">UniqueIdentifier</a>

		  </p>

		</td>

	  </tr>

	  <tr>

		<td>

		  <p>

			<strong>AttributeType</strong>.<a href="http://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.attributetypecode.aspx">Virtual</a></p>

		</td>

		<td>

		  <p>Not used in records.</p>

		</td>

		<td>

		  <p>Not used in records.</p>

		</td>

	  </tr>

</table>
		
	  
	
