---
layout: post
title:  "Interactions between AMLEditor and AMLBaseX"
author: Josef Prinz
hero_height: is-fullwidth
series: plugin_blog_series
image: /img/basex.png
date:   2023-01-01 16:00:00 +0100
categories: AutomationML
---

## XML Data Processing by AutomationML Editor
The AutomationML Editor uses the XML processor of the **[AutomationML Engine][AutomationML Engine]**. The engine always loads the entire AutomationML document, mapped to a CAEX domain model. This behavior must be maintained.

## XML Data Processing by BaseX
BaseX is a light-weight, high-performance and scalable XML Database and an **XQuery 3.1** Processor with full support for the W3C Update and Full-Text extensions. A BaseX XML-Database can contain multiple XML-Files.  XQuery is the functional programming language you can use to read, update, or search your databases. XQuery functions use XPath expressions to select nodes within the documents of the database. 

## AutomationML Editor AMLBaseX Interaction
The idea is that a database connection is established via the AMLBaseX plugin. At the beginning an empty AutomationML document is loaded in the editor. The main visualization for an AutomationML document in the AMLEditor is the tree view. The initialization of the tree view is possible using this XQuery by the plugin after opening the XML database.

```xml
for $node in CAEXFile/child::node()
return <element>{$node/name()}
<attributes>{$node/@*}</attributes>
</element>
```

The result is a list of the child element names and their attributes of a specific CAEX file.

```xml
<element>AdditionalInformation<attributes AutomationMLVersion="2.0"/></element>
<element>InterfaceClassLib<attributes Name="AutomationMLComponentBaseICL"/></element>
<element>InterfaceClassLib<attributes Name="AutomationMLFMIInterfaceClassLib"/></element>
<element>InterfaceClassLib<attributes Name="AutomationMLBPRInterfaceClassLib"/></element>
<element>InterfaceClassLib<attributes Name="AutomationMLLogicInterfaceClassLib"/></element>
<element>InterfaceClassLib<attributes Name="AutomationMLPLCopenXMLInterfaceClassLib"/></element>
<element>InterfaceClassLib<attributes Name="AutomationMLInterfaceClassLib"/></element>
<element>RoleClassLib<attributes Name="AutomationMLComponentBaseRCL"/></element>
<element>RoleClassLib<attributes Name="AutomationMLComponentStandardRCL"/></element>
<element>RoleClassLib<attributes Name="AutomationMLFMILogicRoleClassLib"/></element>
<element>RoleClassLib<attributes Name="AutomationMLBPRRoleClassLib"/></element>
<element>RoleClassLib<attributes Name="AutomationMLLogicRoleClassLib"/></element>
<element>RoleClassLib<attributes Name="AutomationMLBaseRoleClassLib"/></element>
```

The result contains all library elements and their attributes. This initializes the document in the editor. The information is sufficient to display the root elements of the library trees. The next tree levels are retrieved in a similar way by queries when the parent elements are expanded.

## Source Repository
You can find the source code in the [Aml.Editor.Plugin.BaseX GitHub Repo][Aml.Editor.Plugin.BaseX GitHub Repo].


[Aml.Editor.Plugin.BaseX GitHub Repo]: https://github.com/josefprinz/Aml.Editor.Plugin.BaseX
[AutomationML Engine]: https://www.nuget.org/packages/Aml.Engine
