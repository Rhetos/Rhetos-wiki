Rhetos DSL concept is the basis of the Rhetos DSL platform.
It consists of the declaration of the DSL keyword and the implementation behind it.
The CommonConcepts Rhetos package already implements a number of DSL concepts which can be used to build your application.

However, if you need some specific business rule or repetitive mechanism,
the best way to implement it is to write your own Rhetos DSL concept.

The new concept can be implemented as a part of your application,
or as a stand-alone Rhetos plugin that can be shared between multiple projects.

Table of contents:

1. [What is Rhetos DSL concept](#what-is-rhetos-dsl-concept)
2. [How it works](#how-it-works)
3. [How to write a macro concept](#how-to-write-a-macro-concept)
4. [How to write a concept with code generator](#how-to-write-a-concept-with-code-generator)
5. [Debugging custom Rhetos concepts at build-time](#debugging-custom-rhetos-concepts-at-build-time)
6. [How to deploy created concept](#how-to-deploy-created-concept)
7. [Advanced topics](#advanced-topics)
   1. [Dependency between code generators](#dependency-between-code-generators)
8. [See also](#see-also)

## What is Rhetos DSL concept

As stated before, Rhetos DSL concept is the basis of the Rhetos DSL platform.
From an application developer's perspective, a Rhetos DSL concept is a DSL keyword which
assumes some syntax and functionality, and can be used to declare some data structure,
business rule, etc.

From a platform developer's perspective Rhetos DSL concept is a structured way
to develop some functionality and bring it to the application developer.
This new functionality can then be used any number of times only by stating
a specific keyword in the application's DSL script.

For example, let's say that we need to ensure all the phone numbers in our
application must be stated in a structured way.
We can do that by adding a regex validation to all the properties which contain
a phone number. Something like this:

```c
Module Bookstore
{
    Entity Employee
    {
        ShortString PrimaryPhoneNumber { RegexMatch "[+]*[(]{0,1}[0-9]{1,4}[)]{0,1}[-\s\./0-9]*" "Invalid phone number format."; }
        ShortString SecondaryPhoneNumber { RegexMatch "[+]*[(]{0,1}[0-9]{1,4}[)]{0,1}[-\s\./0-9]*" "Invalid phone number format."; }
        ShortString FaxNumber { RegexMatch "[+]*[(]{0,1}[0-9]{1,4}[)]{0,1}[-\s\./0-9]*" "Invalid phone number format."; }
        ShortString MobileNumber { RegexMatch "[+]*[(]{0,1}[0-9]{1,4}[)]{0,1}[-\s\./0-9]*" "Invalid phone number format."; }
    }
}
```

The DSL way of implementing the same feature is to write a new concept and use that concept,
instead of repeatedly copying the same code.
After writing such a concept (let's say we call it PhoneNumber),
we can then use it in our DSL script like this:

```c
Module Bookstore
{
    Entity Employee
    {
        PhoneNumber PrimaryPhoneNumber;
        PhoneNumber SecondaryPhoneNumber;
        PhoneNumber FaxNumber;
        PhoneNumber MobileNumber;
    }
}
```

Implementing functionality this way is not only more elegant and human-readable,
but also easier to test and change in the future, since the implementation is centralized.

## How it works

From a Rhetos DSL platform perspective, a concept is a code generator which inserts
a specific part of code to a specific place in already generated code.
One concept depends on the other, and that's how your code is structured from your DSL scripts.
Bearing that in mind, we have the following types of DSL concepts:

* concept with code generator - concept that defines a DSL keyword and has a code generator which implements functionality
* macro concept - a simple concept which does not generate code directly, instead it generates a couple of other concepts to implement some functionality
* mixed concept - combination of two types mentioned above

Choosing which type of concept to implement depends on the functionality that we want to achieve.

## How to write a macro concept

What every concept needs is the definition of its DSL syntax.
This is done by implementing IConceptInfo interface and exposing it via MEF (using Export attribute).
IConceptInfo interface is defined in Rhetos.Dsl.Interfaces assembly which is part of Rhetos core.
You also need to define a DSL keyword for your concept. This is done by adding the ConceptKeyword attribute
(defined in the same assembly).
Naming convention for the concept definition class is \<ConceptKeyword>Info (e.g. PhoneNumberInfo).
You can define a concept definition class from scratch or inherit an existing one, depending on what your requirements are.
Let's try to write the PhoneNumber concept from previous example.
Phone numbers are stored in string properties so we will build our new concept around ShortString concept
(defined in Rhetos.Dsl.DefaultConcepts assembly).

Example:

```csharp
using System.ComponentModel.Composition;
using Rhetos.Dsl;
using Rhetos.Dsl.DefaultConcepts;

namespace MyFirstConcept
{
    [Export(typeof(IConceptInfo))]
    [ConceptKeyword("PhoneNumber")]
    public class PhoneNumberInfo : ShortStringPropertyInfo
    {
    }
}
```

This code alone does exactly the same as ShortString concept.
Now, we have to provide some additional functionality,
and that is to add regex validation of the phone number.
This is done by implementing IConceptMacro interface and exposing it via MEF (using Export attribute):

```csharp
using System.Collections.Generic;
using System.ComponentModel.Composition;
using Rhetos.Dsl;
using Rhetos.Dsl.DefaultConcepts;

namespace MyFirstConcept
{
    [Export(typeof(IConceptMacro))]
    public class PhoneNumberMacro : IConceptMacro<PhoneNumberInfo>
    {
        public IEnumerable<IConceptInfo> CreateNewConcepts(PhoneNumberInfo conceptInfo, IDslModel existingConcepts)
        {
            var newConcepts = new List<IConceptInfo>();

            if (conceptInfo.DataStructure is IWritableOrmDataStructure) // Activate validation only on writable data, for example on Entity.
                newConcepts.Add(new RegExMatchInfo // Effect is the same as adding "RegExMatch" validation on this property in DSL script.
                {
                    Property = conceptInfo,
                    RegularExpression = @"[+]*[(]{0,1}[0-9]{1,4}[)]{0,1}[-\s\./0-9]*",
                    ErrorMessage = "Invalid phone number format."
                });

            return newConcepts;
        }
    }
}
```

With those two classes you have just created your first macro concept.

## How to write a concept with code generator

Defining the IConceptInfo implementation for the concept is the same as for the macro concept.
Now we have to define a code generator in which we will implement the desired functionality.
This is done by implementing IConceptCodeGenerator interface (defined in Rhetos.Compiler.Interfaces assembly).

Let's say we have to implement additional functionality for an Entity with a Deactivatable statement:
When the Delete function is called, the given record **needs to be deactivated** instead of deleted.

Note that the existing Deactivatable concept simply adds a `bool Active` property, without any automation.
See the article [Implementing simple business rules](Implementing-simple-business-rules)
for more info on the Deactivatable concept.

In order to create a new concept, it is required to add two NuGet packages: `Rhetos` and `Rhetos.CommonConcepts`.
If the `Export` attribute is not recognized, add an assembly reference to `System.ComponentModel.Composition`.

First, we will implement `ConceptInfo` for this new concept:

```csharp
using System.ComponentModel.Composition;
using Rhetos.Dsl;
using Rhetos.Dsl.DefaultConcepts;

namespace Htz.RhetosConcepts
{
    [Export(typeof(IConceptInfo))]
    [ConceptKeyword("DeactivateOnDelete")]
    public class DeactivateOnDeleteInfo : IConceptInfo
    {
        [ConceptKey]
        public DeactivatableInfo Deactivatable { get; set; }
    }
}
```

Now we must implement the `IConceptCodeGenerator` interface and add the desired functionality:

```csharp
using System;
using System.ComponentModel.Composition;
using Rhetos.Compiler;
using Rhetos.Dom.DefaultConcepts;
using Rhetos.Dsl;
using Rhetos.Extensibility;

namespace Htz.RhetosConcepts
{
    [Export(typeof(IConceptCodeGenerator))]
    [ExportMetadata(MefProvider.Implements, typeof(DeactivateOnDeleteInfo))]
    public class DeactivateOnDeleteCodeGenerator : IConceptCodeGenerator
    {
        public void GenerateCode(IConceptInfo conceptInfo, ICodeBuilder codeBuilder)
        {
            var info = (DeactivateOnDeleteInfo)conceptInfo;

            var code = String.Format(
            @"var deactivated = deleted.ToList();

            foreach(var item in deleted)
                item.Active = false;

            updated = updated.Concat(deleted).ToArray();
            updatedNew = updatedNew.Concat(deleted).ToArray();

            deleted = new Common.Queryable.{0}_{1}[]{{}};
            deletedIds = new {0}.{1}[]{{}};
            ",
                info.Deactivatable.Entity.Module.Name,
                info.Deactivatable.Entity.Name);

            codeBuilder.InsertCode(code, WritableOrmDataStructureCodeGenerator.OldDataLoadedTag, info.Deactivatable.Entity);
        }
    }
}
```

As you can see, this is where it gets a bit tricky.
In order to write a code generator you have to know exactly where to place your code
and the context in which your code will be run.
There is no other way of finding that out but to browse already generated code
(e.g. ServerDom.Repositories.cs, or other "*Repositories.cs" files that are generated within the project folder),
and Rhetos core source code itself.
The best way to do this is to find similar functionality in
[CommonConcepts](https://github.com/Rhetos/Rhetos/tree/master/src/Rhetos.CommonConcepts) and work from there.
After a while you will get a hang of it and navigate through code generators and generated code relatively easily.

Now that we have created a new concept **DeactivateOnDelete**, we can use it in a DSL script, for example:

```c
Entity Book
{
    ShortString Code { Unique; Required; }
    ShortString Title { Required; }
    Integer NumberOfPages;

    Deactivatable { DeactivateOnDelete };
}
```

## Debugging custom Rhetos concepts at build-time

To debug a macro concept or a code generator, you can add a **breakpoint** in the implementation of
`IConceptMacro` or `IConceptCodeGenerator` and attach a debugger to the `rhetos build` command.

How to attach the debugger:

1. When building a Rhetos app, check the beginning of the build output for the line `Rhetos Build: Started in C:\Users\<user>\.nuget\packages\rhetos.msbuild\<version>\tools\`.
   This is the location of the Rhetos CLI.
2. Open a terminal in the directory with the Rhetos app project (`.csproj` file that has a direct dependency on the `Rhetos.MsBuild` NuGet package).
3. Run the Rhetos CLI build command with pause to allow attaching the debugger: `C:\Users\<user>\.nuget\packages\rhetos.msbuild\<version>\tools\rhetos.exe --start-paused build .`
   * On some older versions of Rhetos, instead of `--start-paused` use `[debug]`.
4. In Visual Studio, with a breakpoint in the code generator or macro implementation, select Debug => Attach to Process... => select rhetos.exe in the list => Attach.
5. Back in terminal, press any key to continue the build. The Visual Studio debugger will automatically stop at the breakpoint.

## How to deploy created concept

Rhetos concepts are usually developed in a stand-alone C# library that references the `Rhetos` and `Rhetos.CommonConcepts` NuGet packages.

* If you are developing custom concepts specific to your application,
  you can directly reference the project with concepts from your application project.
  For example, see the solution for [Bookstore](https://github.com/Rhetos/Bookstore) demo application:
  it contains the main application Bookstore.Service that references the Bookstore.RhetosExtensions project with
  custom DSL concepts.
* If you are developing a reusable library, [create a NuGet package](Creating-a-Rhetos-package)
  and add your library to it, then reference it from a Rhetos application.

## Advanced topics

### Dependency between code generators

A code generator should use tags (for example `InsertCode(..., concept, tag)` or `tag.Evaluate(concept)`)
*only* for the concepts that it references directly or indirectly.
For example, a code generator for EntityInfo concept should only use tags for EntityInfo,
DataStructureInfo (base class) and for ModuleInfo in which the entity is placed,
because the EntityInfo class has a property that references ModuleInfo.

* If this rule is not followed, then Rhetos may not be aware of the dependencies
  between the code generators.
  Deployment might fail with error `Generated script does not contain tag ...`
  if the code generator that *uses* some tag is executed
  before the code generator that *adds* this tag.
* You may test if your code generators have consistent dependencies,
  by checking if deployment runs successfully with different internal ordering of concepts
  (available *since Rhetos v4.0*):
  * Run the deployment once with [Build configuration](Configuration-management#build-configuration)
    setting `{ "Rhetos": { "Build": { "InitialConceptsSort": "Key" } } }`,
    then again with `{ "Rhetos": { "Build": { "InitialConceptsSort": "KeyDescending" } } }`.

## See also

* Read [Developing new DSL concepts](Rhetos-coding-standard#developing-new-dsl-concepts)
  in the Rhetos coding standard, for **naming convention** and **design principles**
  when developing new concepts.
