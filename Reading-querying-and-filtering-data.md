# Reading, querying and filtering data

Contents:

1. [Data sources](#data-sources)
2. [Loading, querying and filtering methods with parameters](#loading-querying-and-filtering-methods-with-parameters)
3. [Direct implementation in C#, without code snippets is DSL scripts](#direct-implementation-in-c-without-code-snippets-is-dsl-scripts)

## Data sources

Basic components in Rhetos apps are called data structures.
They act as readable or writable data sources.
For example:

- `Entity` - the data source is a **table** in database, writable
- `SqlQueryable` - the data source is an **SQL query**
- `Browse` - the data source is a simplified **LINQ query**
- `Computed` - the data source is a custom **C# code**

For each readable data structure, Rhetos will generate:

1. a **POCO class** - Simple class with properties that describe the data structure, for example Bookstore.Book.
2. a **repository class** - It contains methods for reading data, writing data and other business rules (validations, computations and other data processing), for example Book_Repository.
3. a navigation class - This is a derivation of the POCO class, with additional references to other related entities. This class is used with Entity Framework queries. The class is named Bookstore_Book to allow all modules in a single namespace to avoid ORM issues.

**All repository classes implement the same interfaces**, in order to unify the different types of data sources. This helps developer to modify the implementation without affecting the usage. For example, when replacing a LINQ query with an SQL query, the other code that uses it remains the same, and the **web API** for reading the data source is unchanged.

Examples:

```c
Entity Book // Generats a table in database.
{
  ShortString Title;
  Integer NumberOfPages;
}

SqlQueryable BookSales // Generates an SQL view in database.
  "SELECT book.ID, Description = book.Title + ' ' + author.Name, book.Price
  FROM Bookstore.Book book
  LEFT JOIN Bookstore.Person author ON author.ID = book.AuthorID"
{
  ShortString Description;
  Money Price;
}

Computed BookPopularity // Generates a data source with the following custom C# code returning the data.
  'reposotory => {
    var books = repository.Bookstore.Book.Query().Select(b => new { b.Title, Score = 0 }).ToArray();
    foreach (var book in books)
      book.Score = book.Title.Length + book.Title.Count(letter => letter == '!');
    return books;
  }'
{
  ShortString Title;
  Integer Score;
}

Browse BookGrid Bookstore.Book // Generates a simple LINQ query from Bookstore.Book
{
  Take Title;
  Take Author.Name;
}
```

All of those data sources have the **same interface** in the generated C# code
(with methods Load(), Query() and Filter(...)),
and the same **web API** for reading the data.
All of them support the same **filtering**, **paging** and **sorting** API.

```
C#:
Book[] books = repository.Bookstore.Book.Load();
BookSales[] books = repository.Bookstore.BookSales.Load();
BookPopularity[] books = repository.Bookstore.BookPopularity.Load();
BookGrid[] books = repository.Bookstore.BookGrid.Load();

HTTP GET:
http://myapplication/Bookstore/Book/ => returns JSON object with all records
http://myapplication/Bookstore/BookSales/
http://myapplication/Bookstore/BookPopularity/
http://myapplication/Bookstore/BookGrid/
```

When implementing a new feature, the choice between different types of data sources depends on code maintainability:

- When a frontend needs to read the data for a grid or a dropdown control, from an entity and related entities (for example a grid with the book title and the book's author name), the simplest solution is the `Browse` concept.
- If there is a need for some data processing before returning the data (for example to generate the book description), the recommended way is to use an SQL view with the `SqlQueryable` concept.
  - The advantage of writing SQL view instead of C# implementation is that it can be used in wider range of cases. The application can easily apply **custom and generic filters** to the data source, without compromising performance. Developer does not need to implement **standard filters** on each data source, including paging and sorting. If the query grows more complex, requiring joins to multiple tables, it is often easier to optimize the performance directly in database.
- When implementing complex algorithms for data processing or to generate new data, a common approach is the implementation in C# code, with the `Computed` concept.
- When implementing data sources that read data from external sources, the `Computed` concept can also be used.

## Loading, querying and filtering methods with parameters

Each data source can have multiple methods of **reading data: loading, querying and filtering**, with or without provided parameters.

| DSL example | Generated repository method | Legacy concepts (before v4.1) |
| -- | -- | -- |
| Load LargeBooks | `IEnumerable<Book> Load(LargeBooks parameter)` | FilterBy |
| Query LargeBooks | `IQueryable<Bookstore_Book> Query(LargeBooks parameter)` | Query |
| Filter LargeBooks | `IEnumerable<Book> Filter(IEnumerable<>, LargeBooks parameter)` | none |
| QueryFilter LargeBooks | `IQueryable<Bookstore_Book> Filter(IQueryable<>, LargeBooks parameter)` | ComposableFilterBy |

Examples:

```c
Entity Book
{
  // The Load method returns an IEnumerable of books (usually a List or an Array).
  // Load is often used to provide row computed data, or the generate new data, when the result is not queryable.
  Load LargeBooks1 'parameter => { return new Book[] { new Book { Title = "A good book", NumberOfPages = parameter.MinimumPages + 1 } }; }';

  // The Query method returns an IQueryable of books.
  // Use it to implement a custom queryable data source, that can be efficiently combined with other custom and standard filters, including generic paging and sorting.
  Query LargeBooks2 'parameter => { return this.Query().Where(book => book.NumberOfPages >= parameter.MinimumPages; }';

  // For a given IEnumerable of books (items), returns a subset of the data.
  // The enumerable filter is used on data that is not queryable, when processing the data that is already loaded or generated in the application memory.
  Filter LargeBooks3 '(items, parameter) => { return items.Where(book => book.NumberOfPages >= parameter.MinimumPages; }';

  // For a given IQueryable of books (query), returns a subset of the data.
  // This is most common approach for data filtering. It can be efficiently combined with other custom and standard filters, including generic paging and sorting.
  QueryFilter LargeBooks4 '(query, parameter) => { return query.Where(book => book.NumberOfPages >= parameter.MinimumPages; }';
  
  // ItemFilter is a helper for generating simple QueryFilter.
  // Use it to simplify the code when the QueryFilter has a simple form: `return query.Where(...lambda..)`, and does not require any parameters.
  ItemFilter LargeBooks5 'book => book.NumberOfPages >= 500';
}

Parameter LargeBooks1 { Integer MinimumPages; }
Parameter LargeBooks2 { Integer MinimumPages; }
Parameter LargeBooks3 { Integer MinimumPages; }
Parameter LargeBooks4 { Integer MinimumPages; }
```

Note that those methods can be added to other types of data sources (`Browse`, `SqlQueryable`, `Computed`, ...), not only on the `Entity`.

The choice between different reading/filtering methods depends on performance considerations and code maintainability:

- Most common simple data processing methods in Rhetos app are `QueryFilter` and `ItemFilter` (when `QueryFilter` can be simplified). They are used for data validations and custom filters. The big advantage of query filters is that they are converted to SQL query by Entity Framework, so that the application does not need to load all the data from database and filter it in memory. `QueryFilter` can be combined with the other similar filters - this will result with execution of a single SQL query that applies all the filters together.
- When backend application need to generate some data with a complex algorithm implemented in C#, the common approach is a `Computed` data source with `Load` methods to handle parameters if needed.

The **C# interfaces** and **web API** for reading the data is **the same** for all of the methods above.
Examples:

```cs
C#:
var books = repository.Bookstore.Book.Load(new LargeBooks1 { MinimumPages = 500 });
var books = repository.Bookstore.Book.Load(new LargeBooks2 { MinimumPages = 500 });
var books = repository.Bookstore.Book.Load(new LargeBooks3 { MinimumPages = 500 });
var books = repository.Bookstore.Book.Load(new LargeBooks4 { MinimumPages = 500 });
var books = repository.Bookstore.Book.Load(new LargeBooks5()));

// `Query LargeBooks2` and `QueryFilter LargeBooks4` support queryable data sources
// what can be efficiently used with additional filters, that will be combined into a single SQL query to read the data.
var books = repository.Bookstore.Book.Query(new LargeBooks2 { MinimumPages = 500 })
  .Where(book => book.Author.Name.StartWith("R"))
  .OrderBy(book => book.Title)
  .Take(10).ToList();

HTTP GET:
http://myapplication/Bookstore/Book/?filters=[{"Filter":"LargeBooks1","Value":{"MinimumPages":500}}]`
http://myapplication/Bookstore/Book/?filters=[{"Filter":"LargeBooks2","Value":{"MinimumPages":500}}]&sort=Title&Top=20`
http://myapplication/Bookstore/Book/?filters=[{"Filter":"LargeBooks3","Value":{"MinimumPages":500}}]`
http://myapplication/Bookstore/Book/?filters=[{"Filter":"LargeBooks4","Value":{"MinimumPages":500}}]&sort=Title&Top=20`
http://myapplication/Bookstore/Book/?filters=[{"Filter":"LargeBooks5"}]`
```

## Direct implementation in C#, without code snippets is DSL scripts

**Load**, **Query**, **Filter** and **QueryFilter** methods can be implemented directly in C#,
without writing any C# code snippets in DSL scripts.

**Step 1.** Write the concept in DSL script without the code snippet, for example `QueryFilter OldBooks`
with the corresponding `Parameter OldBooks`. We will add the code later.

```c
Module Bookstore
{
    Entity Book // Generats a table in database.
    {
        ShortString Title;
        Integer NumberOfPages;

        QueryFilter OldBooks;
    }

    Parameter OldBooks;
}
```

**Step 2.** Build the project. It should result with the following error, which is expected:

CS8795 `Partial method 'Book_Repository.Filter(IQueryable<Bookstore_Book>, OldBooks)' must have an implementation part because it has accessibility modifiers.`

Rhetos has now generated the Filter method **prototype** in the partial class Book_Repository,
which we will implement.

**Step 3.** Create the C# file to implement the filter method. The recommended convention is to create
the .cs file next to the corresponding .rhe file.
For example, if the entity is implemented in `Book.rhe`, create `Book.cs` in the same source folder
(you can use Add -> Class helper in VS).

**Step 4.** Write the partial class for the entity's repository class.
It should look like the following code, with adjusted namespace and entity name (`Bookstore` and `Book` in this example).

```cs
namespace Bookstore.Repositories
{
    public partial class Book_Repository
    {
        partial
    }
}
```

When you press *space* and then *tab* after the `partial` keyword in the class,
the Visual Studio IntelliSense should offer to complete the method implementation,
including all input and output parameters:

```cs
namespace Bookstore.Repositories
{
    public partial class Book_Repository
    {
        public partial IQueryable<Bookstore_Book> Filter(IQueryable<Bookstore_Book> query, OldBooks parameter)
        {
            throw new System.NotImplementedException();
        }
    }
}
```

Now, you can remove the line with NotImplementedException and implemented the filter directly in C# class.

The `QueryFilter OldBooks` from DSL script will call this method directly.
