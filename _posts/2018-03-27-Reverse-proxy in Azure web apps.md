---
layout: post
title: Reverse-proxy in Azure web apps.
---

In this post i will be demonstrating you how to configure reverse-proxy in Azure App Service (Managed service in Azure for hosting web and mobile workload).

Lets see what actually reverse-proxy term refers to:

Lets take a scenario wherein i hosted two websites in azure app service.So usually i get by default the domain as:
* First application with name app1 will have access url as: app1.azurewebsites.net
* Second application with name app2 will have access url as: app2.azurewebsites.net

Lets create the third application with no content to act as reverse proxy.
* Create an azure web app (blank web app) with access url as rpapp.azurewebsites.net
* Access the kudu console of the reverse proxy app, [https://rpapp.scm.azurewebsites.net](https://rpapp.scm.azurewebsites.net)
* Browse the debug console menu and select CMD or Powershell option.
* Create new file by selecting + option.
* Rename the file as applicationHost.xdt. The file should be saved in Site folder.
* Place below content in applicationHost.xdt file

``` xml

<?xml version="1.0"?>
 <configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
 <system.webServer>
 <proxy xdt:Transform="InsertIfMissing" enabled="true" preserveHostHeader="false" reverseRewriteHostInResponseHeaders="false" />
 </system.webServer>
 </configuration>

```
* Create web.config file in wwwroot folder and place the routing rules as

``` xml
<configuration>
<system.webServer>
<rewrite>
<rules>
<rule name="demapi2" stopProcessing="true">
<match url="^demapi2/?(.*)" />
<action type="Rewrite" url="http://app1.azurewebsites.net/{R:1}" />
</rule>
<rule name="demapi3" stopProcessing="true">
<match url="^demapi3/?(.*)" />
<action type="Rewrite" url="http://app2.azurewebsites.net/{R:1}" />
</rule>
</rules>
</rewrite>
</system.webServer>
</configuration>

```
* Once applicationHost.xdt and web.config file is saved. Browse to [http://rpapp.azurewebsites.net/demapi2](http://rpapp.azurewebsites.net/demapi2) to load content from [http://app1.azurewebsites.net/] (http://app1.azurewebsites.net/) without changing the url. Similarly you can test for [http://app2.azurewebsites.net/] (http://app2.azurewebsites.net/)

References: [http://ruslany.net/2014/05/using-azure-web-site-as-a-reverse-proxy/](http://ruslany.net/2014/05/using-azure-web-site-as-a-reverse-proxy/)

   Happy Coding !

