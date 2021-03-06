---
layout: navpage
sidebar: operations
title: "InsertAll"
permalink: /operation/insertall
tags: [repodb, tutorial, insertall, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# InsertAll

This method is used to inserts the multiple data entity objects (as new rows) in the table.

#### Use Case

If you are inserting multiple rows in the database, do not ever "iterate and insert it in atomic way". This method solves that problem by creating a multi-packed SQL statements and pass it all in one-go.

The performance of this not comparable to the atomic way of insertion. It is more performant and efficient.

You can adjust the size of the batch on how much rows you want to process per batch. This scenario applies depends on your situation (i.e.: *No of Columns*, *Network Latency*, etc).

The execution is ACID as the transaction object will be created if not given.

> Be aware that if you are managing the size of your batch, it may collide on the number of maximum allowable parameters of ADO.NET. The max parameters are `2100`.

#### Code Snippets

Let us you have a method that returns a list of `Person` models.

```csharp
private IEnumerable<Person> GetPeople()
{
	var people = new List<Person>();
	people.Add(new Person
	{
		Name = "John Doe",
		Address = "New York",
		DateOfBirth = DateTime.Parse("2020-01-01"),
		IsActive = true,
		DateInsertedUtc = DateTime.UtcNow
	});
	people.Add(new Person
	{
		...
	});
	people.Add(new Person
	{
		...
	});
	return people;
}
```

Below is a sample code to insert a list of `Person` into the `[dbo].[Person]` table.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var people = GetPeople();
	var insertedRows = connection.InsertAll(people);
}
```

#### Targetting a Table

You can also target a specific table by passing the literal table and dynamic object like below.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var people = GetPeople();
	var insertedRows = connection.InsertAll("[dbo].[Person]",
		entities: people);
}
```

##### Inserting Specific Columns

You can also target a specific columns to be inserted by passing the list of fields to be included in the `fields` argument.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var people = GetPeople();
	var insertedRows = connection.InsertAll("[dbo].[Person]",
		entities: people,
		fields: Field.From("Id", "Name", "DateInsertedUtc"));
}
```

> Even your model has so many properties, only the `Id`, `Name` and `DateInsertedUtc` will be inserted.

#### Batch Size

You can adjust the size of your batch by simply passing the value at the `batchSize` argument. By default, the value is `10` (found at `Constant.DefaultBatchOperationSize`).

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var people = GetPeople();
	var insertedRows = connection.InsertAll(people,
		batchSize: 30);
}
```

#### Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var insertedRows = connection.InsertAll<Person>(person,
		hints: "WITH (TABLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var insertedRows = connection.InsertAll<Person>(person,
		hints: SqlServerTableHints.TabLock);
}
```
