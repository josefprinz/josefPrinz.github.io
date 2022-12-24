---
title: AutomationML Editor Plugin 
subtitle: AutomationML Editor PlugIn Development 
product_code: 5
layout: product
hero_height: is-fullwidth
image: /img/plugin.png 
price: open source
features:
    - label: PlugIn Testing	in Sandbox
      icon: fa-location-arrow
    - label: Based on Managed Extensibility Framework (MEF) 
      icon: fa-location-arrow
    - label: Multiple PlugIn examples
      icon: fa-location-arrow	
    - label: Reactive PlugIn API
      icon: fa-location-arrow	
rating: 4
---

The **AutomationML Editor PlugIn Development** is an open source repository hosted at GitHub. The repository provides an implementation guide and sources which explain, how to develop PlugIns for the [AutomationML Editor](https://github.com/AutomationML/AutomationMLEditor/blob/main/README.md).
AutomationML provides a contract DLL for PlugIn Development, [Aml.Editor.Plugin.Contract](https://www.nuget.org/packages/Aml.Editor.Plugin.Contract).  The contract DLL contains interface classes, defining the API to access data, edited by AutomationML Editor and to call commands to execute editor actions. 
The API is based on the [Microsoft Extensibility Framework (MEF)](https://docs.microsoft.com/en-us/dotnet/framework/mef/). A new PlugIn class must implement one of the interface classes defined in the contract and export it for use in the AutomationML Editor. The PlugIn Manager of the AutomationML Editor allows the installation of the PlugIns that implement the contract correctly.
A complete API reference documentation is published in the [Wiki](https://github.com/AutomationML/AMLEditorPlugin/wiki). For PlugIn development additional resources are provided.

1. [Aml.Toolkit](https://www.nuget.org/packages/Aml.Toolkit), a package which provides the AML Tree control to visualize AutomationML hierarchies.
2. [Aml.Skins](https://www.nuget.org/packages/Aml.Skins), a package which provides the AML icon library and the colors used by the AutomationML editor for dark and light themes.
3. [Aml.Editor.API](https://www.nuget.org/packages/Aml.Editor.API/), a package providing an Application programming interface to the AutomationML editor.

Sample implementations are provided, like a simple COLLADA viewer, shown in the figure. The included Sandbox Project provides a User Interface which comes close to the original AutomationML editor. The Sandbox uses [Dirkster.AvalonDock](http://nuget.org/packages/Dirkster.AvalonDock) as a Docking Manager and the [MahApps.Metro](https://mahapps.com/) UI library.

[Aml.Editor.Plugin @NuGet](https://www.nuget.org/packages/Aml.Editor.PlugIn.Contract)

[Aml.Editor.Plugin @GitHub](https://github.com/AutomationML/AMLEditorPlugin)

[Aml.Editor.Plugin Wiki](https://github.com/AutomationML/AMLEditorPlugin/wiki)
