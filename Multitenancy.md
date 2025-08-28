# Multitenancy

There are various approaches to the multi-tenant architecture.
This article provides recommendations for configuring the application for one of the approaches:
using a *single* application for multiple tenants, with a *separate* database for each tenant.

## Databases per Tenant

In this approach to the multi-tenant architecture, the application switches the connection string on each web request,
according the user's tenant. Your code should address the following:

1. **Scoped Registration of Connection Strings**:
   Register the connection string in your dependency-injection container as a scoped service
   (resolved separately for each web request), rather than as a singleton (shared globally).
   The code example below shows how to register the connection string in a multi-tenant Rhetos application.
2. **Tenant-Aware Caching**:
   Review any caching mechanisms (e.g., static variables, singletons, MemoryCache) to ensure they
   do not share data across tenants. For example, you might maintain a separate cache instance
   per tenant ID or per connection string.

Rhetos itself does **not** track the list of tenants, identify tenants or menage their databases -
these responsibilities fall to your custom application.
Rhetos does allow you to inject a custom connection string provider at various stages of the
application's lifecycle. The example below demonstrates an Autofac module (`MultiTenantAutofacModule`)
that resolves the connection string at runtime for each tenant:

1. Before running the **rhetos dbupdate** command, your deployment script can set the tenant ID
   or connection string in an environment variable, then run rhetos dbupdate separately for each
   tenant to update its schema.
   The application uses the configured connection string (see `GetConnectionStringForDbUpdate`) during dbupdate.
   In this example, the script uses the `Rhetos__DbUpdate__Tenant` variable, but your implementation may be different.
2. At **runtime**, the connection string is resolved for each incoming request (see `GetConnectionStringForUser`).
   Registering it with InstancePerMatchingLifetimeScope and a custom scope name ensures it's only used by Rhetos within that request's scope.
3. If you are using Rhetos with Entity Framework 6 instead of EF Core, there is a need for the
   global application **initialization** with a separate connection string (see `GetEf6InitializationConnectionString`).
   This connection string can point to any tenant's databases, or to a common master database
   that shares the same engine and version as the tenants' databases.

```cs
using Autofac;
using Rhetos;
using Rhetos.MsSqlEf6.CommonConcepts;
using Rhetos.Utilities;
using System;
using System.ComponentModel.Composition;

[Export(typeof(Module))]
public class MultiTenantAutofacModule : Module
{
    protected override void Load(ContainerBuilder builder)
    {
        ExecutionStage stage = builder.GetRhetosExecutionStage();

        if (stage.IsDatabaseUpdate || stage.IsApplicationInitialization)
            builder.Register(GetConnectionStringForDbUpdate).As<ConnectionString>().SingleInstance();
        else
        {
            builder.Register(GetConnectionStringForUser).As<ConnectionString>().InstancePerMatchingLifetimeScope(UnitOfWorkScope.ScopeName);
            builder.Register(GetEf6InitializationConnectionString).SingleInstance();
        }
    }

    const string dbUpdateTenantConfigurationKey = "Rhetos:DbUpdate:Tenant";
    const string runtimeInitializationConnectionStringConfigurationKey = "MultitenantCommonInitializationConnectionString";

    /// <summary>
    /// On deployment, admin can specify in configuration (e.g. in a local environment variable) the tenant name, or the database connection string.
    /// </summary>
    private static ConnectionString GetConnectionStringForDbUpdate(IComponentContext context)
    {
        // 1. If the dbupdate is called for a specific tenant, update the tenant's database.
        // For example, DbUpdate can be configured by setting the environment variable in command line before running rhetos dbupdate: SET Rhetos__DbUpdate__Tenant=...
        var configuration = context.Resolve<IConfiguration>();
        var tenant = configuration.GetValue<string>(dbUpdateTenantConfigurationKey);
        if (!string.IsNullOrEmpty(tenant))
            return GetTenantsConnectionString(tenant);

        // 2. Otherwise, use the default database, if provided.
        var sqlUtility = context.Resolve<ISqlUtility>();
        var rhetosConnectionString = new ConnectionString(configuration, sqlUtility);
        if (!string.IsNullOrEmpty(rhetosConnectionString))
            return rhetosConnectionString;

        throw new ArgumentException($"The database connection string is not specified. Please set the configuration option '{dbUpdateTenantConfigurationKey}' or '{ConnectionString.ConnectionStringConfigurationKey}'.");
    }

    /// <summary>
    /// In runtime, from user context we can get the tenant's connection string.
    /// </summary>
    private static ConnectionString GetConnectionStringForUser(IComponentContext context)
    {
        var user = context.Resolve<IUserInfo>();
        // TODO: Implement a custom tenant identification mechanism, for example a middleware that resolves the tenant by subdomain, HTTP header, JWT claim or cookie.
        var tenant = $"TENANT_FOR_{user.UserName}";
        return GetTenantsConnectionString(tenant);
    }

    private static ConnectionString GetTenantsConnectionString(string tenant)
    {
        // TODO: Implement a custom connection string storage, and retrieve the tenant's connection string here.
        return new ConnectionString($"CONNECTION_STRING_FOR_{tenant}");
    }

    private Ef6InitializationConnectionString GetEf6InitializationConnectionString(IComponentContext context)
    {
        var configuration = context.Resolve<IConfiguration>();
        var sqlUtility = context.Resolve<ISqlUtility>();
        var rhetosConnectionString = new ConnectionString(ConnectionString.CreateConnectionString(configuration, sqlUtility, runtimeInitializationConnectionStringConfigurationKey));
        if (string.IsNullOrEmpty(rhetosConnectionString))
            throw new ArgumentException($"The database connection string is not specified. Please set the configuration option '{runtimeInitializationConnectionStringConfigurationKey}'.");
        return new Ef6InitializationConnectionString(rhetosConnectionString);
    }
}
```
