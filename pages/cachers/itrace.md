---
layout: page
title: "CommandTextCache (RepoDb)"
permalink: /interface/commandtextcache
tags: [repodb, class, commandtextcache, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## CommandTextCache

A cacher class for the generated command texts. It provides certain important internal methods for the library when it comes to SQL command text caching. As a result, the library is fast-enough when reusing the already generated SQL statements on any execution.

#### Methods

This class only expose a single method named `Flush()` when allows you as a developer to flush all the cached command texts.

#### Use-Cases

You should only call the `Flush()` method if you wish to regenerate the already-generated SQL statement, otherwise, please refrain of using this class.

> We recommend to not to use this class in any of your development.