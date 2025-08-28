# Migrating a Rhetos app from EF6 to EF Core

EF Core is enabled in Rhetos since version 6.0.0.

*Disclaimer*:
It is recommended to keep EF6 in old Rhetos apps for backward compatibility, since there are
many subtle changes in the way that LINQ queries are converter to SQL queries,
which may lead to changes in behavior of the application.

* Migrate from EF6 to EF Core: In the existing projects that reference the NuGet package `Rhetos.CommonConcepts`, add the NuGet package `Rhetos.MsSql` instead of `Rhetos.MsSqlEf6`.
* Lazy loading is disabled by default on EF Core.
  If migrating an existing app from EF6 to EF Core, enable lazy loading for backward compatibility.
  For new apps, it is recommended to avoid lazy loading.
  See [Beware of lazy loading](https://learn.microsoft.com/en-us/ef/core/performance/efficient-querying#beware-of-lazy-loading) in EF Core documentation.
  * To enable lazy loading when migrating a Rhetos app, add the NuGet package "Microsoft.EntityFrameworkCore.Proxies",
    and add after `AddRhetosHost()` the following code: `.SetRhetosDbContextOptions(optionsBuilder => optionsBuilder.UseLazyLoadingProxies())`.
    Note that if SetRhetosDbContextOptions is called multiple times, only the last configuration will be used.
* Breaking changes in libraries API:
  * System.Data.SqlClient library is replaced with newer Microsoft.Data.SqlClient.
    * In C# code, replace namespace `System.Data.SqlClient` with `Microsoft.Data.SqlClient`.
    * Microsoft.Data.SqlClient handles the **database connection string** differently:
      `Encrypt` defaults to `true` and the driver will always validate the server certificate based on `TrustServerCertificate`.
      * In a local development environment, if you use encryption with a self-signed certificate on the server,
        you can specify `TrustServerCertificate=true` in the connection string.
        If you need to turn off encryption, you can specify `Encrypt=false` instead.
  * Most EF6 types were in namespace `System.Data.Entity`, while EF Core uses namespace `Microsoft.EntityFrameworkCore`.
  * Removed Guid comparison extension methods GuidIsGreaterThan, GuidIsGreaterThanOrEqual, GuidIsLessThan, GuidIsLessThanOrEqual. They are supported by standard operators `>` and `<`.
  * Removed the 'FullTextSearch' extension method for Entity Framework LINQ queries. Use `EF.Functions.Contains` instead.
  * The EF6 method `EntityFrameworkContext.Database.SqlQuery` method is different in EF Core:
    * When querying classes that are registered in EF DbContext (for example an Entity or an SqlQueryable), simply use the EF Core methods instead of EF 6,
      for example `EntityFrameworkContext.Bookstore_Book.FromSqlRaw(sql, parameters).AsNoTracking().ToSimple()`.
    * For classes that are not registered in EF DbContext (for example DataStructure or Computed),
      use Rhetos ISqlExecuter ([example](https://github.com/Rhetos/Rhetos/blob/4ce994c993accead793d5664185e7f7521dd0dc9/test/CommonConcepts.TestApp/DslScripts/SqlWorkarounds.cs#L13)),
      or Dapper to load data from a custom SQL query.
* Breaking changes in behavior:
  * Converting boolean to string in EF LINQ query will result with strings `0` and `1` instead of `false` and `true`.
    This can affect LINQ queries such as `query.Select(book => book.Title + " " + book.Active)`,
    where bool Active is converted to a string in the generated database SQL query.
  * In LINQ queries and in generic filters (FilterCriteria), operations `equals` and `notequals`
    with a variable parameter containing null value will return different results then EF6.
    * For example `string n = null; books.Where(b => b.Title == n)` will return all books with title null (`WHERE b.Title IS NULL`). In EF6 this query generated the SQL `WHERE b.Title = @param` which never returns records.
    * Both EF6 and EF Core behave the same with literal null values, such as `books.Where(b => b.Title == null)` returning books with title null.
  * In EF6 LINQ query, when reading data from a database view with `.Query().ToList()`,
    if the view returns multiple records with the same ID value, EF would return only one record and ignore others.
    In EF Core LINQ query on Rhetos, by default all records are returned, even if there are duplicate ID values.
    * If needed, this can be configured by adding after `AddRhetosHost()` the following code:
      `.SetRhetosDbContextOptions(optionsBuilder => optionsBuilder.UseQueryTrackingBehavior(QueryTrackingBehavior.NoTrackingWithIdentityResolution))`.
      Note that if SetRhetosDbContextOptions is called multiple times, only the last configuration will be used.
  * In EF6 LINQ query, `.ToString()` method returns the expected SQL query. In EF Core use `.ToQueryString()` instead.
  * EF Core LINQ query with `Contains` method generates an optimized SQL code that is not supported on SQL Server 2014 and older version.
    * For older SQL Servers, configure the EF options with `.SetRhetosDbContextOptions` as described in
      [Contains in LINQ queries may stop working on older SQL Server versions](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-8.0/breaking-changes#contains-in-linq-queries-may-stop-working-on-older-sql-server-versions)
  * In EF6, when saving a decimal number with more then 10 digits to database, the excess digits are simply *cut off*.
    In EF Core, the number is *rounded* to 10 decimal places.
  * In dynamically-constructed queries, the `Contains` method does not support Expression.Constant argument.
    Use PropertyExpression or FieldExpression instead,
    see [Dynamically-constructed queries](https://learn.microsoft.com/en-us/ef/core/performance/advanced-performance-topics#dynamically-constructed-queries).
  * The `Cast` method on LINQ query may result with ArgumentException.
    For example on `repository.Common.Role.Query().Select(r => r.ID).Cast<Guid?>().ToList()`,
    instead of `Cast<Guid?>()`, convert the value directly in select with `Select(item => (Guid?)item.ID)`,
    or with additional `.Select(id => (Guid?)id)`.
