---
layout: navpage
sidebar: operations
title: "Insert"
permalink: /operation/insert
tags: [repodb, tutorial, insert, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Insert

This method is used to inserts a data entity object (as a new row) in the table.

#### Code Snippets

Below is a sample code to insert a row into the `[dbo].[Person]` table.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var id = connection.Insert<Person>(new Person
	{
		Name = "John Doe",
		Address = "New York",
		DateOfBirth = DateTime.Parse("2020-01-01"),
		IsActive = true,
		DateInsertedUtc = DateTime.UtcNow
	});
}
```

> The result is always the value of primary key. If the primary key is identity, then the newly generated identity value will be returned.

#### Targetting a Table

You can also target a specific table by passing the literal table and dynamic object like below.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var person = new
	{
		Name = "John Doe",
		Address = "New York",
		DateOfBirth = DateTime.Parse("2020-01-01"),
		IsActive = true,
		DateInsertedUtc = DateTime.UtcNow
	};
	var id = connection.Insert("[dbo].[Person]",
		entity: person);
}
```

#### Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var id = connection.Insert<Person>(person,
		hints: "WITH (TABLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var id = connection.Insert<Person>(person,
		hints: SqlServerTableHints.TabLock);
}
```
