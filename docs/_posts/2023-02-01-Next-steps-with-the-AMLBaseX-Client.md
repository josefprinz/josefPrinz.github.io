---
layout: post
title:  "Next steps with the AMLBaseX Client"
author: Josef Prinz
hero_height: is-fullwidth
series: plugin_blog_series
image: /img/bigfile.png
date:   2023-02-01 18:00:00 +0100
categories: AutomationML
---

## AMLBaseX Rest client
I have spent quite a bit of time trying to figure out how to send an XQuery expression via http post request to the rest service of the BaseX database without errors. I had the problem that the XQuery expression could be processed without errors in the BaseX GUI application, but the same expression when submitted via HTTP request from the C# program was always incorrect. For the implementation I followed the [RESTPost.Java](https://github.com/BaseXdb/basex/blob/9/basex-examples/src/main/java/org/basex/examples/rest/RESTPost.java) example which presents the HTTP POST method as java code. In this example the content type for the post request is defined as *"application/query+xml"*.  This corresponds to the following statement when using the .NET *HttpClient* in c#: 

```c#
var stringContent = new StringContent(request, Encoding.UTF8, "application/query+xml");
```

In Paul Swennenhuis' Tutorial: [BaseX for Dummies](https://www.swennenhuis.nl/basexfordummies/BaseX_for_dummies.pdf) this example XQuery using the "Facts"-database is defined:

```xquery
for $country in collection("Facts")//country[matches(@name,'^P','i')
and @population>10000000]
where number($country/@infant_mortality) le 50
let $province := count($country/province)
order by $country/@name
return 
<country><name>{$country/@name}</name>
<provinces>{$province}</provinces>
</country>
```

The inserted XML elements, `<country>, <name> and <provinces>` ensure that the result can be read by an XML parser. To post this query using REST the query must be embedded in an XML format:

```xml
<query xmlns="http://basex.org/rest">
  <text>the query text</text>
</query>
```

However, the sent content could not be processed by the REST service in this composition. The solution to the problem was to transform the actual query string in the request to an xml-inserted string by replacing the whitespace characters and transform all special characters by creating an [XText](https://learn.microsoft.com/en-us/dotnet/api/system.xml.linq.xtext?view=net-7.0) class-instance.

```c#
query = Regex.Replace(query, @"\t\r\n?|\n", " ");
var xmlText = new XText(query);
string request = "<query>" +
               $"<text>{xmlText}</text>" +
                "</query>";
```

By using this text transformation it was possible to send this request to partially query the attributes and special elements of the CAEXFile root element of an AutomationML document.

```xquery
let $fileNode:=doc('AutomationML/BigFile.aml')/CAEXFile
return <CAEXFile>
     {$fileNode/@*} 
     {$fileNode/Description}
     {$fileNode/Version}
     {$fileNode/Revision}
     {$fileNode/Copyright}
     {$fileNode/SourceObjectInformation}
     {$fileNode/AdditionalInformation}
     {$fileNode/SuperiorStandardVersion}
     {$fileNode/SourceDocumentInformation}  
     {$fileNode/ExternalReference}
</CAEXFile>
```

The partial definition of the query allows to process very large AutomationML files in small tailored bites. The size of the *BigFile.aml* in this example is 1.6 GB. The result of this query can be read directly into a [CAEXDocument](https://github.com/AutomationML/AMLEngine2.1/blob/master/Documentation/Aml.Engine.CAEX/CAEXDocument/README.md) by using the [AutomationML Engine][AutomationML Engine], which would not be possible with this big file as a whole.

```C#
// the resource specifies the AutomationML document in the database
// the CAEXFileHeaderTemplate defines the partial CAEXFile query
var query = XQueryCAEXTemplates.CAEXFileHeaderTemplate (database, resource);

// this async method is the HTTP post REST client method
var caexFile = await RunXQueryAsync(database, query);

if (!string.IsNullOrEmpty(caexFile))
{ 
    // the result string is converted to a CAEXDocument
    var document = await CAEXDocument.LoadFromStringAsync(caexFile);
    
    ....
}
```



## BaseX Size Restrictions

As I mentioned in my first post to this series, AutomationML documents and collections exported from technical systems can be far too large to visualize or edit in their raw form in the AutomationML editor. This prompted me to realize this BaseX database plug-in. In this context, the question arises what size restrictions exist for BaseX databases and how fast queries to large AutomationML documents are executed. In terms of size constraints, this [source](https://docs.basex.org/wiki/Statistics) is informative. 

| Instances  | FileSize                 | #Files                 | DbSize     | #Nodes                   | #Attr      | #ENames          | #ANames          | #URIs         | Height     |
| ---------- | ------------------------ | ---------------------- | ---------- | ------------------------ | ---------- | ---------------- | ---------------- | ------------- | :--------- |
| **Limits** | **512 GiB** (2^39 Bytes) | **536'870'912** (2^29) | *no limit* | **2'147'483'648** (2^31) | *no limit* | **32768** (2^15) | **32768** (2^15) | **256** (2^8) | *no limit* |

This is the meaning of the attributes:

- *FileSize* is the original size of the input documents
- *#Files* indicates the number of stored XML documents
- *DbSize* is the size of the resulting database (excluding the [value index structures](https://docs.basex.org/wiki/Indexes#Value_Indexes))
- *#Nodes* represents the number of XML nodes (elements, attributes, texts, etc.) stored in the database
- *#Attr* indicates the maximum number of attributes stored for a single element
- *#ENames* and #ANames reflect the number of distinct element and attribute names
- *#URIs* represent the number of distinct namespace URIs
- *Height* indicates the maximum level depth of the stored nodes

If a database limit is reached, you can distribute documents  across multiple database instances and access all of them with a single  XQuery expression.

## AutomationML Editor Integration

As mentioned in [part 2](https://josefprinz.github.io/automationml/2023/01/01/Interactions-between-AMLEditor-and-AMLBaseX-Kopie/) of this series, an AutomationML document opened via a BaseX database can be loaded into the AutomationML Editor with only a part of its entire content. This takes advantage of the fact that the libraries of the document are visualized in the editor in a tree view (from the root to the leaves). Only when expanding a level of the tree, the elements on this level are read from the database and inserted into the document. I was able to display the test file with a size of about 2GB in the editor without any problems. As a complete document, the editor needs about 5 minutes to load the document. After loading, any interaction in the editor is very slow. With the partially loaded document, on the other hand, there are no problems.

In addition to hierarchical navigation, the editor also uses relations to access elements of the document in other hierarchies that may not yet be loaded. Since the editor uses the methods of the AutomationML Engine for this and the AutomationML Engine can only access elements of the loaded document, these elements would currently not be found. The solution to this problem is to integrate a special service interface into the AutomationML Engine that can be used by the Engine to retrieve elements from the database if needed. This feature is complicated to realize, I will cover some aspects of this in a later blog post. The next part in this series will cover updates of documents with XQuery.

## Source Repositories

You can find the source code for the AMLBaseX plugin in the [Aml.Editor.Plugin.BaseX GitHub Repo][Aml.Editor.Plugin.BaseX GitHub Repo].

The source code for the AMLBaseX service is published in the [Aml.Engine.BaseX GitHup Repo][Aml.Engine.BaseX GitHup Repo].


[Aml.Editor.Plugin.BaseX GitHub Repo]: https://github.com/josefprinz/Aml.Editor.Plugin.BaseX
[Aml.Engine.BaseX GitHup Repo]: https://github.com/josefprinz/Aml.Engine.BaseX
[AutomationML Engine]: https://www.nuget.org/packages/Aml.Engine
