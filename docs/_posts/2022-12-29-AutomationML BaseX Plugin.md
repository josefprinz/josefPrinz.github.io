---
layout: post
title:  "AutomationML BaseX Plugin"
author: Josef Prinz
hero_height: is-fullwidth
image: /img/basex.png
date:   2022-12-29 13:51:00 +0100
categories: AutomationML
---

## Next Development Project
AutomationML documents should be able to be archived in a BaseX database and be directly visualizable and editable via a web interface in the AutomationML Editor. An editor plugin is to be developed for this purpose.

## AutomationML
[AutomationML (AML)](https://www.AutomationML.org) is a comprehensive XML based object-oriented data modeling language. It allows the modelling, storage and exchange of engineering models covering a multitude of relevant aspects of engineering. AML provides a comprehensive set of flexible mechanisms and innovations to model today’s engineering aspects as well as future engineering aspects to come. Its language characteristics allow to model existing or new domain models.

## AutomationML Editor
[AutomationML editor](https://github.com/AutomationML/AutomationMLEditor#readme) is a great tool to visualize, create and edit AutomationML files but has the limitation that only locally stored files of limited size can be processed efficiently.

## BaseX
[BaseX](https://basex.org/) is a robust, high-performance XML database engine and a highly compliant XQuery 3.1 processor with full support of the W3C Update and Full Text extensions. It serves as excellent framework for building complex data-intensive web applications. BaseX offers a RESTful API for accessing database resources via URLs. REST (**RE**presentational **S**tate **T**ransfer) facilitates a simple and fast access to databases through HTTP. The HTTP methods GET, PUT, DELETE, and POST can be used to interact with the database. 

## Aml.Editor.PlugIn.BaseX
It is, I think, a great idea if AutomationML documents could be archived in a BaseX database and be directly visualizable and editable via a web interface in the AutomationML Editor.

So I will try to develop a **BaseX Editor PlugIn** and report about the progress here. 

First I will install BaseX and try to store an AutomationML document in the database. The next step will be to create a simple editor plugin that makes document content from the database accessible to the AutomationML editor via a Rest-API. For the PlugIn I'll use [ASP .Net Core](https://learn.microsoft.com/en-us/aspnet/core/introduction-to-aspnet-core}view=aspnetcore-7.0). The API will be published as a free new [Aml.Engine](https://www.nuget.org/packages/Aml.Engine) package at [NuGet.org](https://www.nuget.org). The API project will be an Open Source project at GitHub. 

To start with PlugIn development you first need the PlugIn development resources, which you can get from GitHub using this command.

`git clone https://github.com/AutomationML/AMLEditorPlugin.git`

For the latest version of the editor the resources provided in the *PlugInDevelopment_V6*-folder are required.

​	