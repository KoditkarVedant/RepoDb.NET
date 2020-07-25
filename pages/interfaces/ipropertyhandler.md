---
layout: navpage
sidebar: interfaces
title: "IPropertyHandler"
permalink: /interface/ipropertyhandler
tags: [repodb, class, ipropertyhandler, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# IPropertyHandler

This interface is used to mark a class to be a property handler object. This interface has `TInput` and `TResult` generic types in which being mapped into `Get` and `Set` methods.

#### Generic Types

- `TInput` - refers to the type of the database column. The input type for the getter; the output type for the setter.
- `TOutput` - refers to the type of the data entity property. The input type for the setter; the output type for the getter.

#### Methods

- `Get` - the method that is being invoked when the outbound transformation is triggered (i.e.: [Query](/operation/query), [QueryAll](/operation/queryall) and [BatchQuery](/operation/batchquery)).
- `Set` - the method that is being invoked when the inbound transformation is triggered (i.e.: [Insert](/operation/insert), [Update](/operation/update), [Merge](/operation/merge) and etc).

> Both methods accept the [ClassProperty](/class/classproperty) to give more context on the current method of the property handler.

#### Use-Cases

This is very useful when you would like to handle the following scenarios.

- Converting a JSON column into a class object.
- Handling the correct `DateTime` objects `Kind`.
- Overriding the monetary columns conversion into a specific .NET type.
- Querying a child records of the parent rows.
- Updating a record as a reaction to the transformation.
- Can be used as trigger.
- Manually override the default handler for the Enumerations.

> The use-cases can be unlimitted depends on your situation. In addition to this note, by implementing the property handler and mapping it to the property will ignore the automatic conversion of [TypeMapper](/mapper/typemapper#automatic) and enumerations.

#### How to Implement?

You have to manually create a class that implements this interface.

```csharp
public class AddressPropertyHandler : IPropertyHandler<string, Address>
{
        public Address Get(string input, ClassProperty property)
        {
                // Handle the transformation from the DB towards the Class
        }

        public string Set(Address input, ClassProperty property)
        {
                // Handle the transformation from the Class towards the DB
        }
}
```

#### Property Level Handling

You can handle the property transformation on a property level. Imagine that you have a table named `[dbo].[Person]` in which the column `Address` is of type `NVARCHAR(MAX)` and let us say you have the following classes. 

```csharp
public class Address
{
        public int HouseNo { get; set; }
        public string Country { get; set; }
        public string State { get; set; }
        public string Street { get; set; }
        public string Region { get; set; }
        public int ZipCode { get; set; }
}
public class Person
{
        public int Id { get; set; }
        public string Name { get; set; }
        [PropertyHandler(typeof(AddressPropertyHandler))]
        public Address Address { get; set; }
}
```

And you implemented the handler like this.

```csharp
public class AddressPropertyHandler : IPropertyHandler<string, Address>
{
        public Address Get(string input, ClassProperty property)
        {
                return JsonConvert.DeserializeObject<Address>(input);
        }

        public string Set(Address input, ClassProperty property)
        {
                return JsonConvert.SerializeObject(input);
        }
}
```

> You have to use the [PropertyHandler](/attribute/propertyhandler) to map the custom property handler class you had created. Also, please take a note that the property `Address` of class `Person` is of type `Address` (not `String`).

When you call any of the fetch ([Query](/operation/query), [QueryAll](/operation/queryall) and [BatchQuery](/operation/batchquery)) or push ([Insert](/operation/insert), [Update](/operation/update), [Merge](/operation/merge)) operations, the methods `Get` and `Set` of the property handler will be invoked immediately.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var person = connection.Query<Person>(10045);
	Console.WriteLine($"Name: {person.Name}, Address: {person.Address.Street}, {person.Address.Region}, {person.Address.Country} ({person.Address.ZipCode})")
}
```

#### Type Level Handling

On the other hand, you can also handle the property transformation on a type level. It is useful on a situation if you would like to handle a specific database type transformation into a .NET CLR type (i.e.: converting the `DateTime` object `Kind` to `Utc`).

To enable this, you have to use the [PropertyHandlerMapper](/mapper/propertyhandlermapper) class for the mappings.

Let us say, the scenario is to convert all the `DateTime.Kind` properties to Utc in all read operations.

```csharp
public class DateTimeKindToUtcPropertyHandler : IPropertyHandler<DateTime?, DateTime?>
{
    public DateTime? Get(DateTime? input, ClassProperty property)
    {
        // Reading from DB, setting the class
        return input.HasValue ? DateTime.SpecifyKind(input.Value, DateTimeKind.Utc) : null;
    }

    public DateTime? Set(DateTime? input, ClassProperty property)
    {
        // Reading from class, setting back to DB
        return input.HasValue ? DateTime.SpecifyKind(input.Value, DateTimeKind.Unspecified) : null;
    }
}
```

Then, simply call the Add method of the [PropertyHandlerMapper](/mapper/propertyhandlermapper) class to add a mapping directly to `DateTime` type.

```csharp
PropertyHandlerMapper.Add(typeof(DateTime), new DateTimeKindToUtcPropertyHandler(), true);
```

