---
comments: true
date: 2010-05-02 19:32:27
layout: post
slug: working-with-default-filters-in-ms-crm-report-files
title: Working with default filters in MS CRM report files
wordpress_id: 6
---

As you are probably aware, Microsoft CRM uses Sql Server Reporting services under the hood to run reports against CRM data. However, to allow more flexibility than would ordinarily be provided by SRSS alone Microsoft has several tricks up its sleeve when it comes to running the reports under CRM. To allow reports to run in different CRM contexts, dataset queries can be written in a way that CRM can rewrite the query to report contextually on selected rows or active view filters. That behavior is well covered elsewhere on the web so I probably won't cover it here.

What I want to talk about here are default filters, which are added to the report when the .rdl file is uploaded to CRM. This filter persists as extended data added to the report file when downloaded from CRM.

By default, the data is filtered to show only the last 30 days' worth of records. Downloading a report from CRM and comparing it with the originally uploaded .rdl file, we can see that the following chunk gets added to the .rdl file:

[sourcecode language="xml"]

<CustomProperties>
  <CustomProperty>
    <Name>Custom</Name>
    <Value>&lt;MSCRM xmlns="mscrm"&gt;&amp;lt;ReportFilter&amp;gt;&amp;
lt;ReportEntity paramname=
"P1"&amp;gt;&amp;lt;fetch version="1.0" output-format="xml-platform" mapping="logical" distinct="false"&amp;gt;&amp;lt;entity name="contract"&amp;gt;&amp;lt;all-attributes /&amp;gt;&amp;lt;filter type="and"&amp;gt;&amp;lt;condition attribute="modifiedon" 
operator="last-x-days" value="30" /&amp;gt;&amp;lt;/filter&amp;gt;&amp;lt;/entity
&amp;gt;&amp;lt;/
fetch&amp;gt;&amp;lt;/
ReportEntity&amp;gt;&amp;lt;ReportEntity paramname="CRM_Filteredcontract"
&amp;gt;&amp;
lt;fetch version="1.0" output-format="xml-platform" mapping="logical" distinct="false"&amp;gt;
&amp;lt;entity name="contract"&amp;gt;&amp;lt;all-attributes /&amp;gt;&amp;lt;filter type="and"&amp;gt;&amp;lt;
condition attribute="modifiedon" operator="last-x-days" value="30" /&amp;gt;&amp;lt;/filter&amp;gt;&amp;lt;/entity&amp;gt;&amp;lt;/
fetch&amp;gt;&amp;lt;/
ReportEntity&amp;gt;&amp;lt;/ReportFilter&amp;gt;&lt;/MSCRM&gt;</Value>
  </CustomProperty>
</CustomProperties>

[/sourcecode]

Yuck. Unfortunately, since the report filter is an embedded xml document, it must be escaped in order for CRM to import it correctly. It would be nice if we could format this so that it is easy to edit. At first blush it seems the a simple CDATA section should do the trick. However, the data is actually escaped twice, which requires nested CDATA sections, which are not allowed in the XML standard. What can we do? Fortunately we can embed the CDATA sections so that when the data is unescaped the first time, the second CDATA section remains intact.

The first thing to do is to recover the plain XML markup from the escaped text. I used a tool called [XMLStarlet](http://xmlstar.sourceforge.net/), but anything that can unescape XML entity references will do the trick.

[sourcecode language="bash" light="true"]

$ cat crm.xml | xmlstarlet unesc | xmlstarlet unesc

[/sourcecode]

Gives us something like the following:

[sourcecode language="xml"]
<CustomProperties>
  <CustomProperty>
    <Name>Custom</Name>
    <Value>
      <MSCRM xmlns="mscrm">
        <ReportFilter>
          <ReportEntity paramname="P1">
            <fetch version="1.0" output-format="xml-platform" mapping="logical" distinct="false">
              <entity name="contract">
                <all-attributes/>
                <filter type="and">
                  <condition attribute="modifiedon" operator="last-x-days" value="30"/>
                </filter>
              </entity>
            </fetch>
          </ReportEntity>
          <ReportEntity paramname="CRM_Filteredcontract">
            <fetch version="1.0" output-format="xml-platform" mapping="logical" distinct="false">
              <entity name="contract">
                <all-attributes/>
                <filter type="and">
                  <condition attribute="modifiedon" operator="last-x-days" value="30"/>
                </filter>
              </entity>
            </fetch>
          </ReportEntity>
        </ReportFilter>
      </MSCRM>
    </Value>
  </CustomProperty>
</CustomProperties>
[/sourcecode]

In order to work correctly when uploading the default filter, we escape it with CDATA tags like the following:

[sourcecode language="xml"]
<CustomProperties>
  <CustomProperty>
    <Name>Custom</Name>
    <Value><![CDATA[
      <MSCRM xmlns="mscrm"><![CDATA[
        <ReportFilter>
          <ReportEntity paramname="P1">
            <fetch version="1.0" output-format="xml-platform" mapping="logical" distinct="false">
              <entity name="contract">
                <all-attributes/>
                <filter type="and">
                  <condition attribute="modifiedon" operator="last-x-days" value="30"/>
                </filter>
              </entity>
            </fetch>
          </ReportEntity>
          <ReportEntity paramname="CRM_Filteredcontract">
            <fetch version="1.0" output-format="xml-platform" mapping="logical" distinct="false">
              <entity name="contract">
                <all-attributes/>
                <filter type="and">
                  <condition attribute="modifiedon" operator="last-x-days" value="30"/>
                </filter>
              </entity>
            </fetch>
          </ReportEntity>
        </ReportFilter>]]]]><![CDATA[>
      </MSCRM>]]>
    </Value>
  </CustomProperty>
</CustomProperties>
[/sourcecode]

Now we are free to edit the filter easily and when the .rdl file is uploaded, the changes to the filter will take effect.


