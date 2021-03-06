# Migrating from WCF to ASP.NET Core

Rhetos v5 is migrated to .NET 5, and it no longer supports WCF.
New Rhetos applications should use APS.NET Core instead.

When migrating existing Rhetos application to APS.NET Core, the configuration in web.config should also be migrated.
How the migration is done depends on the web server which is used.

## IIS

IIS still uses the web.config file for server specific configuration.

The web.config file is not added automatically when creating an empty ASP.NET Core application so you need to add it manually.

The `system.serviceModel` section in web.config is WCF specific so it is not used anymore.

The `system.web` section is ASP.NET specific and it is not used anymore
but some of its configuration can be set under the `system.webserver` section which is IIS specific.

1. The `system.web:httpRuntime maxUrlLength` value can be set under the `system.webServer: security:requestFiltering requestLimits maxUrl` attribute.
2. The `system.web:httpRuntime maxQueryStringLength` value can be set under the `system.webServer:security:requestFiltering:requestLimits maxQueryString` attribute.
3. The `system.web:httpRuntime maxRequestLength` value can be set under the `system.webServer:security:requestFiltering:requestLimits maxAllowedContentLength` attribute.

## Kestrel

For Kestrel you can follow the instructions on https://docs.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-5.0.

## File upload

To configure the limit of each multipart body, in the startup class you need to call.

```cs
services.Configure<FormOptions>(options =>
{
    options.MultipartBodyLengthLimit = multipartBodyLengthValue;
});
```

This is required, for example, when using the **Rhetos.LightMDS** package if you want to increase the file upload size limit.
