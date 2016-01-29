##Entity Framework Bulk Operations & Utilities##

## Download

Full Version | NuGet | NuGet Install
------------ | :-------------: | :-------------:
Z.EntityFramework.Plus.EF7 | <a href="https://www.nuget.org/packages/Z.EntityFramework.Plus.EF7/" target="_blank" alt="download nuget"><img src="https://img.shields.io/nuget/v/Z.EntityFramework.Plus.EF7.svg?style=flat-square" /></a> <a href="https://www.nuget.org/packages/Z.EntityFramework.Plus.EF7/" target="_blank" alt="download nuget"><img src="https://img.shields.io/nuget/dt/Z.EntityFramework.Plus.EF7.svg?style=flat-square" /></a> | ```PM> Install-Package Z.EntityFramework.Plus.EF7```
Z.EntityFramework.Plus.EF6 | <a href="https://www.nuget.org/packages/Z.EntityFramework.Plus.EF6/" target="_blank" alt="download nuget"><img src="https://img.shields.io/nuget/v/Z.EntityFramework.Plus.EF6.svg?style=flat-square" /></a> <a href="https://www.nuget.org/packages/Z.EntityFramework.Plus.EF6/" target="_blank" alt="download nuget"><img src="https://img.shields.io/nuget/dt/Z.EntityFramework.Plus.EF6.svg?style=flat-square" /></a> | ```PM> Install-Package Z.EntityFramework.Plus.EF6```
Z.EntityFramework.Plus.EF5 | <a href="https://www.nuget.org/packages/Z.EntityFramework.Plus.EF5/" target="_blank" alt="download nuget"><img src="https://img.shields.io/nuget/v/Z.EntityFramework.Plus.EF5.svg?style=flat-square" /></a> <a href="https://www.nuget.org/packages/Z.EntityFramework.Plus.EF5/" target="_blank" alt="download nuget"><img src="https://img.shields.io/nuget/dt/Z.EntityFramework.Plus.EF5.svg?style=flat-square" /></a> | ```PM> Install-Package Z.EntityFramework.Plus.EF5```

<a href="https://github.com/zzzprojects/EntityFramework-Plus/wiki/Downloads">More download options (Full and Standalone Version)</a>

## Features
Use [.NET Entity Framework Extensions](http://www.zzzprojects.com/products/dotnet-development/entity-framework-extensions/) for BulkSaveChanges and Bulk Operations

- BulkSaveChanges _(under development)_
- Bulk Operations
    - Bulk Insert _(under development)_
    - Bulk Update _(under development)_
    - Bulk Delete _(under development)_
    - Bulk Merge _(under development)_
- Query
    - Query Batch Operations _(under development)_
    - [Query Cache](https://github.com/zzzprojects/EntityFramework-Plus/wiki/EF-Query-Cache-%7C-Entity-Framework-Second-Level-Caching)
    - [Query Deferred](https://github.com/zzzprojects/EntityFramework-Plus/wiki/EF-Query-Deferred-%7C-Entity-Framework-deferring-immediate-LINQ-query-execution)
    - [Query Filter](https://github.com/zzzprojects/EntityFramework-Plus/wiki/EF-Query-Filter-%7C-Entity-Framework-Dynamic-Instance-and-Global-Filters)
    - [Query Future](https://github.com/zzzprojects/EntityFramework-Plus/wiki/EF-Query-Future-%7C-Entity-Framework-Combine-and-Execute-Multiple-SQL-Command)
    - [Query IncludeFilter](https://github.com/zzzprojects/EntityFramework-Plus/wiki/EF-Query-IncludeFilter-%7C-Entity-Framework-Include-Related-Entities-using-Where-Filter) 
    - [Query IncludeOptimized](https://github.com/zzzprojects/EntityFramework-Plus/wiki/EF-Query-IncludeOptimized-%7C-Entity-Framework-Filter-Child-Collections-and-Optimize-Include)
- [Audit](https://github.com/zzzprojects/EntityFramework-Plus/wiki/Audit) (_Alpha_)  
- Save Change Security _(under development)_
- Utilities
   - DbContext.MapReader&lt;TEntity&gt;(reader)

## Query Cache
**Query cache is the second level cache for Entity Framework.**

The result of the query is returned from the cache. If the query is not cached yet, the query is materialized and cached before being returned.

You can specify cache policy and cache tag to control CacheItem expiration.

**Support:**

_Cache Policy_

```csharp
// The query is cached using default QueryCacheManager options
var countries = ctx.Countries.Where(x => x.IsActive).FromCache();

// (EF5 | EF6) The query is cached for 2 hours
var states = ctx.States.Where(x => x.IsActive).FromCache(DateTime.Now.AddHours(2));

// (EF7) The query is cached for 2 hours without any activity
var options = new MemoryCacheEntryOptions() { SlidingExpiration = TimeSpan.FromHours(2)};
var states = ctx.States.Where(x => x.IsActive).FromCache(options);
```

_Cache Tags_

```csharp
var states = db.States.Where(x => x.IsActive).FromCache("countries", "states");
var stateCount = db.States.Where(x => x.IsActive).DeferredCount().FromCache("countries", "states");

// Expire all cache entry using the "countries" tag
QueryCacheManager.ExpireTag("countries");
```

**[Learn more](https://github.com/zzzprojects/EntityFramework-Plus/wiki/EF-Query-Cache-%7C-Entity-Framework-Second-Level-Caching)**

## Query Deferred
**Defer the execution of a query which is normally executed to allow some features like Query Cache and Query Future.**

```csharp
// Oops! The query is already executed, we cannot use Query Cache or Query Future features
var count = ctx.Customers.Count();

// Query Cache
ctx.Customers.DeferredCount().FromCache();

// Query Future
ctx.Customers.DeferredCount().FutureValue();
```
> All LINQ extensions are supported: Count, First, FirstOrDefault, Sum, etc. 

**[Learn more](https://github.com/zzzprojects/EntityFramework-Plus/wiki/EF-Query-Deferred-%7C-Entity-Framework-deferring-immediate-LINQ-query-execution)**

## Query Filter
**Filter query with predicate at global, instance or query level.**

**Support:**

_Global Filter_
```csharp
// CREATE global filter
QueryFilterManager.Filter<Customer>(x => x.Where(c => c.IsActive));

var ctx = new EntityContext();

// TIP: Add this line in EntitiesContext constructor instead
QueryFilterManager.InitilizeGlobalFilter(ctx);

// SELECT * FROM Customer WHERE IsActive = true
var customer = ctx.Customers.ToList();
```

_Instance Filter_
```csharp
var ctx = new EntityContext();

// CREATE filter
ctx.Filter<Customer>(x => x.Where(c => c.IsActive));

// SELECT * FROM Customer WHERE IsActive = true
var customer = ctx.Customers.ToList();
```

_Query Filter_
```csharp
var ctx = new EntityContext();

// CREATE filter disabled
ctx.Filter<Customer>(CustomEnum.EnumValue, x => x.Where(c => c.IsActive), false);

// SELECT * FROM Customer WHERE IsActive = true
var customer = ctx.Customers.Filter(CustomEnum.EnumValue).ToList();
```

**[Learn more](https://github.com/zzzprojects/EntityFramework-Plus/wiki/EF-Query-Filter-%7C-Entity-Framework-Dynamic-Instance-and-Global-Filters)**

## Query Future
**Query Future allow to reduce database roundtrip by batching multiple queries in the same sql command.**

All future query are stored in a pending list. When the first future query require a database roundtrip, all query are resolved in the same sql command instead of making a database roundtrip for every sql command.

**Support:**

_Future_

```csharp
// GET the states & countries list
var futureCountries = db.Countries.Where(x => x.IsActive).Future();
var futureStates = db.States.Where(x => x.IsActive).Future();

// TRIGGER all pending queries (futureCountries & futureStates)
var countries = futureCountries.ToList();
```

_FutureValue_

```csharp
// GET the first active customer and the number of avtive customers
var futureFirstCustomer = db.Customers.Where(x => x.IsActive).DeferredFirstOrDefault().FutureValue();
var futureCustomerCount = db.Customers.Where(x => x.IsActive).DeferredCount().FutureValue();

// TRIGGER all pending queries (futureFirstCustomer & futureCustomerCount)
Customer firstCustomer = futureFirstCustomer.Value;
```

**[Learn more](https://github.com/zzzprojects/EntityFramework-Plus/wiki/EF-Query-Future-%7C-Entity-Framework-Combine-and-Execute-Multiple-SQL-Command)**

## Query IncludeFilter
Entity Framework already support eager loading however the major drawback is you cannot control what will be included. There is no way to load only active item or load only the first 10 comments.

**EF+ Query Include** make it easy:
```csharp
var ctx = new EntityContext();

// Load only active comments
var posts = ctx.Post.IncludeFilter(x => x.Comments.Where(x => x.IsActive));
```

**[Learn more](https://github.com/zzzprojects/EntityFramework-Plus/wiki/EF-Query-IncludeFilter-%7C-Entity-Framework-Include-Related-Entities-using-Where-Filter)**

## Query IncludeOptimized
Improve SQL generate by Include and filter child collections at the same times!

```csharp
var ctx = new EntityContext();

// Load only active comments using an optimized include
var posts = ctx.Post.IncludeOptimized(x => x.Comments.Where(x => x.IsActive));
```

**[Learn more](https://github.com/zzzprojects/EntityFramework-Plus/wiki/EF-Query-IncludeFilter-%7C-Entity-Framework-Include-Related-Entities-using-Where-Filter)**

## Audit
Entity Framework allow to save changes but doesn’t log what change has been made in the Change Tracker. Audit allow to capture every changes made on entities and relations saved to your underling database.

**Support:**
 - Identity
 - All kind of entity/inheritance (Complex Type, TPC, TPH, TPT)
 - All kind of relations (Many to Many, One to Many, One to One, etc.)
 - Audit AutoSaving

```csharp
var ctx = new EntityContext();

// ... ctx changes ...
var audit = new Audit();
ctx.SaveChanges(audit);

// You have now access to all captured informations
var entries = audit.Entries;
foreach(var entry in entries)
{
    foreach(var property in entry.Properties)
    {
    }
}
```

Want to save audit entries automatically after every SaveChanges(audit) call?
```csharp
// ADD AuditEntry && AuditEntryProperty to your context or use your own entity
AuditManager.DefaultConfiguration.AutoSaveAction = (context, audit) =>
    (context as EntityContext).AuditEntries.AddRange(audit.Entries);
```

**[Learn more](https://github.com/zzzprojects/EntityFramework-Plus/wiki/Audit)**

## FREE vs PRO
Every month, a new monthly trial of the PRO Version is available to let you evaluate all its features without limitations.

Features | FREE Version | [PRO Version](http://entityframework-plus.net/#pro)
------------ | :-------------: | :-------------:
Audit | Yes | Yes
Query Batch | Yes | Yes
Query Cache | Yes | Yes
Query Deferred | Yes | Yes
Query Filter | Yes | Yes
Query Future | Yes | Yes
Query IncludeFilter | Yes | Yes
Query IncludeOptimized | Yes | Yes
Commercial License | Yes | Yes
Royalty-Free | Yes | Yes
Support & Upgrades (1 year) | **No** | Yes
Learn more about the **[PRO Version](http://entityframework-plus.net/#pro)**

(Compatible with license from [.NET Entity Framework Extensions](http://www.zzzprojects.com/products/dotnet-development/entity-framework-extensions/))

## Contributing
_You received support from us and/or the FREE version helped you?_

Contribute to keep us developing FREE features and to thank us for our support.

We'll never require donations, but we appreciate them greatly

<a href="http://www.zzzprojects.com/contribute/" target="_blank"><img src="http://www.zzzprojects.com/images/paypal-contribute.png" alt="Contribute" height="64"></a>

A **huge thanks** for your extra support.


## More Projects

**Entity Framework**
- [Entity Framework Extensions](http://www.zzzprojects.com/products/dotnet-development/entity-framework-extensions/)
- [Entity Framework Plus](https://github.com/zzzprojects/EntityFramework-Plus)

**Bulk Operations**
- [NET Entity Framework Extensions](http://www.zzzprojects.com/products/dotnet-development/entity-framework-extensions/)
- [NET Bulk Operations](http://www.zzzprojects.com/products/dotnet-development/bulk-operations/)

**Expression Evaluator**
- [Eval SQL.NET](https://github.com/zzzprojects/Eval-SQL.NET)
- [Eval Expression.NET](https://github.com/zzzprojects/Eval-Expression.NET)

**Others**
- [Extension Methods Library](https://github.com/zzzprojects/Z.ExtensionMethods/)
- [LINQ Async](https://github.com/zzzprojects/Linq-AsyncExtensions)

**Need more info?** info@zzzprojects.com

Contact our outstanding customer support for any request. We usually answer within the next business day, hour, or minutes!
