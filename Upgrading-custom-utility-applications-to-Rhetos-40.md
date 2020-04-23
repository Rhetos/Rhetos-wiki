# Upgrading custom utility applications to Rhetos 4.0

Rhetos has migrated to new configuration and DI container initialization design.
Changes are not fully backward-compatible, so some of the existing code may stop working.

Mostly, these changes will affect any kind of **tools/executables** which are custom entry points
to running Rhetos. Code that initializes Rhetos container or sets up options will be affected.
Expected errors are:

* `System.MissingMethodException: Method not found: 'Void Rhetos.Utilities.Paths.InitializeRhetosServerRootPath(System.String)'`
* `System.MissingMethodException: Method not found: 'Void Rhetos.Extensibility.Plugins.SetInitializationLogging(Rhetos.Logging.ILogProvider)'`

Here are described some common scenarios of **upgrade to Rhetos 4.0**,
for different types of custom utility applications:

Contents:

1. [Custom code that utilizes Rhetos static utilities, but DOES NOT construct Rhetos container](#custom-code-that-utilizes-rhetos-static-utilities-but-does-not-construct-rhetos-container)
2. [Custom code that constructs and uses Rhetos DI container to interact with Rhetos application](#custom-code-that-constructs-and-uses-rhetos-di-container-to-interact-with-rhetos-application)
3. [Custom code that uses RhetosTestContainer to encapsulate container creation and usage](#custom-code-that-uses-rhetostestcontainer-to-encapsulate-container-creation-and-usage)

## Custom code that utilizes Rhetos static utilities, but DOES NOT construct Rhetos container

Often there will be some form of initialization (at least for Rhetos app root path) in form of:

```cs
Paths.InitializeRhetosServerRootPath(Path.Combine(AppDomain.CurrentDomain.BaseDirectory, @"..\.."));
```

This needs to be replaced with proper creation of configuration object.
Configuration is then used to initialize all legacy static classes.

```cs
var host = Host.Find(AppDomain.CurrentDomain.BaseDirectory, new ConsoleLogProvider());
var configuration = host.RhetosRuntime.BuildConfiguration(new ConsoleLogProvider(), host.ConfigurationFolder, null);
LegacyUtilities.Initialize(configuration);
```

Notes:

* The `Host.Find` method locates the Rhetos application in specified folder or any parent folder.
  The example above uses `AppDomain.CurrentDomain.BaseDirectory`, assuming that
  the utility application is located **within the Rhetos application**.
  If that is not the case, specify the path to Rhetos application.
* The `configuration` includes configuration settings of main Rhetos application
  (typically from *Web.config*) extended with settings from this custom utility application
  (from *CustomUtility.exe.config*, for example).
* Add `registerCustomComponents` parameter to `BuildConfiguration` method to add or override
  configuration settings in code.

## Custom code that constructs and uses Rhetos DI container to interact with Rhetos application

This scenario will commonly use some form of default Rhetos DI container, for example:

```cs
Paths.InitializeRhetosServerRootPath(Path.Combine(AppDomain.CurrentDomain.BaseDirectory, @"..\.."));

Plugins.SetInitializationLogging(new ConsoleLogProvider());
ConsoleLogger.MinLevel = EventType.Info;

var builder = new ContainerBuilder();
    builder.RegisterModule(new DefaultAutofacConfiguration(deploymentTime: false, deployDatabaseOnly: false));

builder.RegisterType<ProcessUserInfo>().As<IUserInfo>();
builder.RegisterType<ConsoleLogProvider>().As<ILogProvider>();

var container = builder.Build();
```

This should be replaced with the following code:

```cs
var container = Host.CreateRhetosContainer(registerCustomComponents: builder => { builder.RegisterType<ProcessUserInfo>().As<IUserInfo>(); });
```

Notes:

* Add parameters `addCustomConfiguration` or `registerCustomComponents` to `CreateRhetosContainer`
  method to add or override configuration setting of customize DI components registration.
* `ProcessUserInfo` is used in the example above to provide that account the executes
  this utility application as a Rhetos application user.
  Depending of business features of the application, this account might need to be added
  to `Common.Principal` table.
* In this scenario, note that we are not explicitly initializing legacy static utilities
  via `LegacyUtilities.Initialize()`. This is implicitly called in `CreateRhetosContainer`
  to simplify the migration.

## Custom code that uses RhetosTestContainer to encapsulate container creation and usage

`RhetosTestContainer` class has been modified internally to comply with new design and using it
is backward-compatible.