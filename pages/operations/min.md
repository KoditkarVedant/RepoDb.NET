---
layout: navpage
sidebar: operations
title: "Min"
permalink: /operation/min
tags: [repodb, tutorial, min, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Min

This method is used to computes the min value of the target field.

#### Code Snippets

Below is a sample code that gets the minimum value of column `Value` from the `[dbo].[Sales]` table for a specific customer since yesterday.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var customerExpenses = connection.Min<Sales>(e => e.Value,
		e => e.CustomerId == 10045 && e.DateInsertedUtc >= DateTime.UtcNow.Date.AddDays(-1));
}
```

#### Targetting a Table

You can also target a specific table by passing the literal table and field name like below.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var customerExpenses = connection.Min("[dbo].[Sales]", Field.From("Value"), new { State = "Michigan" });
}
```

Or, use the [QueryGroup](/class/querygroup) or [QueryField](/class/queryfield) if you are to enhance the `WHERE` expressions.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var where = new []
	{
		new QueryField("CustomerId", 10045),
		new QueryField("DateInsertedUtc", Operation.GreaterThanOrEqual, DateTime.UtcNow.Date.AddDays(-1))
	}
	var customerExpenses = connection.Min("[dbo].[Sales]", Field.From("Value"), where: where);
}
```

#### Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var customerExpenses = connection.Min<Sales>(e => e.Value,
		e => e.CustomerId == 10045 && e.DateInsertedUtc >= DateTime.UtcNow.Date.AddDays(-1),
		hints: "WITH (NOLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var customerExpenses = connection.Min<Sales>(e => e.CustomerId == 10045 && e.DateInsertedUtc >= DateTime.UtcNow.Date.AddDays(-1),
		hints: SqlServerTableHints.NoLock);
}
```
