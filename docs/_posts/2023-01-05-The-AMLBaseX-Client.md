---
layout: post
title:  "The AMLBaseX Client"
author: Josef Prinz
hero_height: is-fullwidth
series: plugin_blog_series
image: /img/connect.png
date:   2023-01-05 10:00:00 +0100
categories: AutomationML
---

## Client Server Architecture
For running the AMLBaseX plugin as a BaseX client application, the basexhttp.bat start script is used which starts the BaseX database server and the HTTP server for REST as a background process. The REST interface uses basic authorization (that's why we had to sign in when we asked the browser to display http://localhost:8080/rest/) and without it the request will return an “unauthorized” error. Once the connection is established, http requests can be sent to the server via the REST interface. Results are returned as XML fragments. 

```c#
public async Task<IEnumerable<DatabaseInfo>> Connect(string address, string userName, string password)
{
	// define the credentials for the BaseX server
	var handler = new HttpClientHandler
	{
		Credentials = new NetworkCredential(userName, password)
	};
	// create the HttpClient with server address (local address is http://localhost:8080/rest/)
	_client = new HttpClient(handler)
	{
		BaseAddress = new Uri(address),
	};
	// get the result as XML 
	_client.DefaultRequestHeaders.Accept.Add(
		new MediaTypeWithQualityHeaderValue("application/xml"));

	_error = null;
	// check, if the server is running by getting the list of databases
	try
	{
		using var response = await _client.GetAsync($"{_client.BaseAddress}");
		if (response.IsSuccessStatusCode)
		{
			var result = await response.Content.ReadAsStringAsync();
			// parse the XML result as an XDocument
			var content = XDocument.Parse(result);
			var databases = content.Descendants(Name("database"));
			return databases.Select(d => new DatabaseInfo(d));
		}
		else
		{
			_error = response.StatusCode.ToString();
		}
	}
	catch (Exception ex)
	{
		_error = ex.Message;
	}
	return Enumerable.Empty<DatabaseInfo>();
}
```

The final goal is to have a working BaseX application so that we can create, manage and query AutomationML XML databases, from anywhere on this planet. Therefore it is needed, to deploy the BaseX Database and server. I will cover this topic in a later blog post.

## AMLBaseX Client
I decided to implement the AMLBaseX client as an **AMLService**. This gives me the possibility to integrate the service later directly into the core functions of the AutomationML engine. The engine provides the InterfaceClass **IAMLService** for services. Methods to register a new service and to unregister are required.

```c#
public class AMLDatabaseService : IAMLService
{
    public static AMLDatabaseService Register()
    {
        var service = new AMLDatabaseService();
        ServiceLocator.Register(service);
        return service;
    }
    
    public static void UnRegister()
    {
        var service = ServiceLocator.GetService<AMLDatabaseService>();
        if (service != null)
        {
            ServiceLocator.UnRegister<AMLDatabaseService>();
        }
    }
}
```

The BaseX server can manage multiple databases with multiple XML resources (documents). The AMLBaseX service can initially only query the contents of a single database and a single document. The database and document reference must be included in the XQuery expressions. The result of this query is a list of attributes of the root element CAEXFile of the addressed AutomationML document *AssetAdministrationShellLib.aml* in the *AutomationML* database.

```xquery
{doc('AutomationML/AssetAdministrationShellLib.aml')/CAEXFile/@*}
```

The source code for the AMLBaseX service is available in the [Aml.Engine.BaseX GitHup Repo][Aml.Engine.BaseX GitHup Repo].


## AMLBaseX Plugin

Once a connection to the BaseX server is established, the plugin lists all available document resources of the selected database.

<img src="/img/documentList.png" alt="Document List" style="zoom:50%;" />

## Source Repositories
You can find the source code for the AMLBaseX plugin in the [Aml.Editor.Plugin.BaseX GitHub Repo][Aml.Editor.Plugin.BaseX GitHub Repo].

The source code for the AMLBaseX service is published in the [Aml.Engine.BaseX GitHup Repo][Aml.Engine.BaseX GitHup Repo].


[Aml.Editor.Plugin.BaseX GitHub Repo]: https://github.com/josefprinz/Aml.Editor.Plugin.BaseX
[Aml.Engine.BaseX GitHup Repo]: https://github.com/josefprinz/Aml.Engine.BaseX
[AutomationML Engine]: https://www.nuget.org/packages/Aml.Engine
