# Previous major releases

This article shows a *high-level* overview of past major releases.
For *detailed_info* on previous releases, see the [Release notes](https://github.com/Rhetos/Rhetos/blob/master/ChangeLog.md).

## Rhetos 5: Migration to .NET 5/6

* Migrated from .NET Framework 4.7.2:
  * Rhetos 5.0 supports .NET 5 applications.
  * Rhetos 5.1 supports .NET 5 and .NET 6.
* Rhetos framework can be used in any type of C# project,
  from simple class libraries to ASP.NET Core web applications.
* Rhetos apps can run on Windows, Linux and macOS.
* Some features are no longer managed by Rhetos framework.
  Instead, a standard .NET toolset is used for
  [user authentication](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/?view=aspnetcore-5.0),
  web API ([Swagger](https://docs.microsoft.com/en-us/aspnet/core/tutorials/getting-started-with-swashbuckle?view=aspnetcore-5.0&tabs=visual-studio), e.g.),
  custom web controllers and host logging.

## Rhetos 4: Better IDE experience and build performance

* Rhetos DSL IntelliSense for Visual Studio.
* Rhetos framework is simply added to an application as a NuGet package.
* Seamless development and build workflow in Visual Studio.
  Custom C# code is compiled together with DSL scripts, as a part of build in Visual Studio.
* Significantly reduced build time.

## Rhetos 3

* Upgrade from .NET Framework 4.5.1 to .NET Framework 4.7.2.
* Build and runtime performances improvements.

## Rhetos 2

* ServerDom assembly split to multiple files.
* Build and runtime performances improvements.
* New concepts: UniqueReference, ApplyFilterOnClientRead, DefaultValue, SkipRecomputeOnDeploy, Hardcoded, ...
* DeployPackages DatabaseOnly for easier deployment on test and production environments.

## Rhetos 1

* Migrations from NHibernate to Entity Framework 6.1.3.
* New concepts: AllowSave, InvalidData custom messages and metadata, SamePropertyValue, ...
* Support for Azure SQL Database.
* Localization of end-user messages.
* Separated simple and queryable class for each data structure.
* Rhetos NuGet package available for plugin package development.
* Repository members are available in all C# code snippets.

## Rhetos 0.9

* Core framework features: DSL parser, code generators, database update, SOAP and REST API.
* Many business patterns implemented as DSL concepts in CommonConcepts package.
* Basic security and row permissions.
* Official plugin packages: authentication methods, web APIs, AD integration, OData, ...
