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
BaseX is a light-weight, high-performance and scalable XML Database and an **XQuery 3.1** Processor
with full support for the W3C Update and Full-Text extensions. A BaseX XML-Database can contain multiple XML-Files. 
XQuery is the functional programming language you can use to read, update, or search
your databases. XQuery functions use XPath expressions to select nodes within the documents of the database. 

## AutomationML Editor BaseX Interaction
The idea is that a database connection is established via the AMLBaseX plugin. At the beginning an empty AutomationML document is loaded in the editor. 

## Source Repository
You can find the source code in the [Aml.Editor.Plugin.BaseX GitHub Repo][Aml.Editor.Plugin.BaseX GitHub Repo].


[Aml.Editor.Plugin.BaseX GitHub Repo]: https://github.com/josefprinz/Aml.Editor.Plugin.BaseX	
[AutomationML Engine]: https://www.nuget.org/packages/Aml.Engine
