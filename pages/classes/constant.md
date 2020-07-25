---
layout: navpage
sidebar: classes
title: "Constant"
description: "A constant class that is being used by the library."
permalink: /class/constant
tags: [repodb, class, constant, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Constant

A class that holds the constant values for the library.

#### Values

- `DefaultBatchOperationSize` - the default value of the batch operation size. It is targetting the bulk operations (i.e.: [BulkInsert](/operation/bulkinsert), [BulkDelete](/operation/bulkdelete), [BulkMerge](/operation/bulkinsert) and [BulkUpdate](/operation/bulkupdate)) default batch-size.
- `DefaultCacheItemExpirationInMinutes` - the default value of the cache expiration in minutes. It is targetting the default value of the [CacheItem.ExpirationInMinutes](/class/cacheitem) property.
- `MaxParametersCount` - stands as the maximum parameters of ADO.Net when executing a command.
