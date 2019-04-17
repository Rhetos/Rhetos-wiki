# Rhetos – Introduction to use

These concepts are used as a workaround for features that cannot be implemented with the available high-level concepts in Rhetos.

> A good rule of thumb is to *avoid* these low-level concepts if you are implementing a standard business pattern.
> Seeing low-level concepts in DSL scripts is a sign that we are not looking at a standard feature, but **a very specific uncommon feature**.
> For example, if the purpose if the feature is "data validation", please use the InvalidData concept instead.
>
> Breaking down the requirements to a set of **standard business features** is a good way to make your software more maintainable.
> Also consider developing a new concept if you need to implement a standard business pattern, but there is no existing concept available.

The SQL script that creates a database object can be written in-line
(inside the .rhe script) or written in a separate file (see SqlProcedure example below).

## Rhetos
Table of Contents:

1. [Rhetos](https://github.com/Rhetos/Rhetos/wiki/SqlObject-concept)
2. [Preface](#preface)
3. [Concepts (keywords)](#concepts-(keywords))
4. [Permissions](#permissions)
5. [Application funcionality testing](#sqlview)
6. [SqlIndex](#sqlindex)
7. [SqlDefault](#sqldefault)
8. [SqlObject](#sqlobject)
9. [Dependencies between database objects](#dependencies-between-database-objects)
10. [See also](#see-also)

## Preface

Rhetos is framework that makes application development easier and faster, ie. parts that repeat in every application, like creating objektnog modela sustava (entiteti, validacije), databases, system security (autentification, authorization), connecting model object to database, generating REST API, implementation of often used patterns/forms. 
Rhetos uses declarative programming that decreases need for using of imperative code.

System architecture:

![Rhetos- System Architecture](https://github.com/stjepanantolovic/Rhetos-wiki/blob/master/images/Rhetos%20-System%20Architecture.png)
 

User access client's web application, that is being developed with MVC in Visual Studio, using Internet browser. Client application does not communicate with database base directly, but thru Rhetos servis. Rhetos generates system business logic and database. Business logic layer (web servis) is exposed thru API so it can be reached directly with outside applications. Rhetos serialize everything to JSON, and is being run on IIS. Web servis contains .dll files that can be referenced directly (not just thru Rhetos), but this is being done during development only, not in production. Rhetos/bin/Generated folder contains generated .dll files that contain business logic: RestService.dll containing code of all service and exposes business logic to API, and ServerDom files: 

1.	ServerDom.Model – contains entity classes
2.	ServerDom.Repositories – conatin business logic and maps objects to database
3.	ServerDom.Orm – Defines EntityFrameworkContext ( EntityFrameworkOnModelCreating and EntityFrameworkContextMembers)


## Concepts (keywords)

List of concepts and their basic definition is on page:[List of DSL conceps in Rhetos OmegaCommonConcepts package](http://wiki/Popis-DSL-koncepata-u-Rhetos-OmegaCommonConcepts-paketu.ashx)

Some of the concepts and their implementation are explained below in more details.

Model example:


```C
Module OrderingSystem
{

	Entity Bill
	{
        ShortString Number{Autocode; DefaultValue 'item => “Rac+++“'; DenyUserEdit;}
		Decimal AmountWithoutVAT{Required;}
		Decimal AmountWithVAT{Required;}
		Logging{AllProperties;}
		
		ItemFilter AmountWithVATCantBeeLessThenAmountWithoutVAT 'item => item.AmountWithVAT!=null && item.AmountWithVAT.Value <= item.AmountWithoutVAT.Value';
		InvalidData AmountWithVATCantBeeLessThenAmountWithoutVAT 'Amount With Vat Can't Be Less Then Amount Without VAT.';	
	} 
}

```
Entity – concept that, during deployment, generates C# classes, database tables, defines EntityFramework mapping between the calssess and the tables, generates methods for reading and writing of classes etc (Creation and deployment of Rhetos project is available on wiki page http://wiki/Izrada-prvog-Rhetos-projekta.ashx ).
 
Logging – complex concept that creates triggers that track all changes in database (What has being changed, deleted, when and by who is being saved in common.log). Generally all entitities/tables have logging property. It needs to be defined which properties are beig logged in database. Logging of specific properties can be made using Log attribute, while  AllProperties; means that all properties are being logged in database.
 
Unique – attribute that ensures that data in database is unique. It generates unique indeks in database. 
Required – this attribute ensures that dana in database exists(NOT NULL). Required unites two subattribute: UserRequired (user must send this data to server while saving in database) and SystemRequired (system ensures that dana exists after saving in database)

Autocode – this attribute makes autoincerement of passwords and implies Unique and SystemRequired attributes. Saving data in specific format can be done using keyword DefaultValue. DefaultValue 'item => “abc+“'; means that dana value will begin with abc , and + means that number will be incremented (abd1, abc2, abc3 itd..). If using +++, three digit numbers will be saved. DenyUserEdit; means that data can be changed only by system, not by user.

Detail – attribute for implementation of Master-Detail model that includes Required and CascadeDelete. Atribute Detail is being set in „Detail“ entity on reference to „Master“. For example, Order (Master) contains items (Detail):


```C
Module OrderingSystem
{
	Entity Request
	{
		ShortString Number{Required;}
		ShortString Status{Required;}
		LongString Remark;
	}
	
	Entity Item
	{
		ShortString Name{Required;}
		ShortString UniteOfMeaure{Required;}
		Integer Amount{Required;}
		Reference RequestId OrderingSystem.Request{Detail;}
	}
}

```

ItemFilter – performs data validation after writing data to database, but inside transction, so that InvalidData concept can return validation message in case of error. ItemFilter and InvalidData are always in pair and have same name. In example above, AmountWithVATCantBeeLessThenAmountWithoutVAT, verifies if bill amount with VAT is less then amount without VAT. I fit is less it does not save data to database and returns message: Amount With Vat  Can't Be Less Then Amunt Without Vat.

Extends – this concept extends entities with additionally calculated dana or data added to application by user. Aditional data that user writes are neccessary if we want to save spefic case of certain entity. For example, in system for IT equipment we have entity Article that has Name and Price. Each Article must have name and price, but hard disc and proccessor do not have common properties – Hard disc needs to have information about it's capacity available, and proccesor needs to have working frequency. Iti s recommended to use this model using inheritance concept, not using Extends concept. If using Extends concept, model would be realized as follows:

```C
Module ITEquipment
{
	Entity Article
	{
		ShortString Password{Required;}
		ShortString Name{Required;}
		Decimal Price{Required;}	
	}

	EntityArticleDisc
	{
		Extends ITEquipment.Article;
	Integer KapacitetGB;
		Reference ITEquipment.TypeOfDisc;
	}

	EntityArticleCPU
	{
		Extends ITEquipment.Article;
	Decimal Frequency;
		Reference ITEquipment.Socket;
	}
}

```
Additionally calculated dana are being added thru SqlQueryable  concept SqlQueryable which creates View in database with data defined in SQL query and adds entity extension in object model. For example, if we want to show full name of some City in State:

```C
Module Citizen
{

	Entity City
	{
		ShortString Name{Required;}
		Integer PostalNumber{Required;}
		References StateID Citizen.State{Required;}	
	}

	Entity State
	{
		ShortString Name{Required;}	
	}

	SqlQuaryable CityFullName 
	“
	SELECT FullName = City.Name + “, “+ State.Name 
	FROM Citizen.City JOIN Citizen.State ON City.StateID = State.ID
	“
	{
		Extends Citizen.City;
		ShortString FullName;
	}
}


```
It is recommended not to write SQL code directly in .rhe, but to reference it from external file. CityFullName can be written like:
```C
SqlQuaryable CityFullName<sql\CityFullName.sql>
    {
        Extends Citizen.City;
        ShortString FullName;
    }

```
CityFullName.sql is located in  sql directory and it contains code:

```SQL
SELECT FullName = City.Name + “, “+ State.Name 
FROM Citizen.City JOIN Citizen.State ON City.StateID = State.ID

```

Exstension of entitety City can be used in code as follows:
```C
Citizen st;
String FullName = st.Extension_CityFullName.FullName;
```

Rhetos supports macro concepts that use other concepts in order to create new type of dana.
Example of macro concepts are Email, ShortString, SSN etc. Inside themselves, These concepts contain validations that we would usually write all over again. Ie. ShortString have length limitation at 256 , SSN needs to have format format "AAA-GG-SSSS".

Poco classes – the simplest classes that conatain only properties, without attributes, methods, referneces etc. For example:
```C
Entity Bill
    {
        ShortString Number;
        Decimal AmountWithoutVAT;
        Decimal AmountWithVAT;
    }

```




## Permissions
In order to have REST approach in our project, it is neccessary to assign permissions to it. Rhetos basic properties are contained in Common package. Every database will contain following tables generated by Rhetos:
1.	Common.Principal – Users are being saved here (ie. OS\mjackson)
2.	Common .Claim – List of all permissions (by each entity) in our system – New, Read, Edit, Remove (CRUD operations)
3.	Common .PrincipalPermission – Connects Principal i Claim, ie. here is possible to assign permissions directly to Users (principal), but this is usually not being done.
4.	Common.Role – Roles that can be assigned to Users
5.	Common .RolePermission – Connects Role and Claim, ie. permisions (Claims) can be assigned to Roles here. 
6.	Common.PrincipalHasRole –Roles are being assigned to Users here.
7.	Common.RoleInheritsRole – Possible subroles assing
If we are logged in to computer as Admin user, in Rhetos/web.config file we can set BuiltinAdminOverride = True, and that gives us permission to data: 

```C
<appSettings>
  	  <add key="BuiltinAdminOverride" value="True" />
</appSettings>
```
If BuiltinAdminOverride=False, assigning permissions to roles can be done as follows:
1.	Create role Admin in table Common.Role
2.	Run script in SQL Management Studio that assigns all permissions (Claims) to Admin role:
```SQL
DELETE FROM Common.RolePermission
INSERT INTO Common.RolePermission(ID, ClaimID, RoleID, IsAuthorized)
SELECT
NEWID(),
c.ID, 
'76bfdb74-8e80-42f3-a173-997df8b3d148',
1
FROM Common.Claim c
```
*	*'76bfdb74-8e80-42f3-a173-997df8b3d148' is example of ID of created Admin role 



## Application  funcionality testing
Testing of application funcionality and saving data to database can be done with:
1.	Unit (functional) tests. Instructions for Unit tests will be availbale dodane afterwards.
2.	Postman for testing REST calls. Short instructions about Postman are availbale at [Postman – User Instructions](http://wiki/Postman-Upute-za-koristenje.ashx)
3.	LinqPad. Short instructions about LinqPad are availbale at [LinqPad – User Instructions](https://github.com/Rhetos/Rhetos-wiki/blob/master/Using-the-Domain-Object-Model.md)

