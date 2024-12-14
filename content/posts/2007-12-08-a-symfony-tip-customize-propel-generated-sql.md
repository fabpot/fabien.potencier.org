---
date: '2007-12-08'
title: 'A symfony tip: Customize Propel Generated SQL'
summary: |
  Learn how to customize the Propel generated SQL by executing some SQL statements after the `propel-insert-sql` task (`propel:insert-sql` in symfony 1.1).
params:
    author: Fabien Potencier
url: /a-symfony-tip-customize-propel-generated-sql.html
aliases:
    - /a-symfony-tip-customize-propel-generated-sql
    - /article/26/a-symfony-tip-customize-propel-generated-sql
---

The Propel schema is a great way to describe your database schema. You can declare basic stuff like columns,
foreign keys, and indexes. The schema also supports some more advanced features like `onDelete`:

```xml
<table name="comment">
  ...
  <column name="article_id" type="integer" required="true" />
  <foreign-key foreignTable="article" onDelete="cascade">
    <reference local="article_id" foreign="id"/>
  </foreign-key>
  ...
</table>

```

In YAML:

```yml
comment:
  ...
  article_id: { type: integer, foreignTable: article, foreignReference: id, onDelete: cascade }
  ...

```

But sometimes, you need a way to execute SQL statements after the schema has been created by Propel.
Let's see how to do this **automatically** every time you launch the `propel-insert-sql` task
(`propel:insert-sql` in symfony 1.1).

The `propel-build-sql` task executes SQL statements that drop and create tables in your database. These SQL statements have been generated previously by the `propel-build-sql` task (`propel:build-sql` in symfony 1.1).
The SQL files used by the task are stored under the `data/sql` directory.
Propel generates one file per schema. So, if you have installed some plugins like `sfGuardPlugin`,
you will have several files:

 * lib.model.schema.sql
 * plugins.sfGuardPlugin.lib.model.schema.sql

If you have a closer look, you will also find a `sqldb.map` file in this directory:

```ini
# Sqlfile -> Database map
lib.model.schema.sql=propel
plugins.sfGuardPlugin.lib.model.schema.sql=propel

```

`propel-insert-sql` task executes files referenced in `sqldb.map`.
So, if you want some SQL statements to be executed after Propel has created the tables,
you can just add a file at the bottom of this file:

```ini
# Sqlfile -> Database map
lib.model.schema.sql=propel
plugins.sfGuardPlugin.lib.model.schema.sql=propel
post-table-creation.sql=propel

```

In the `data/sql/post-table-creation.sql` file, you can put any SQL.
The second argument of each line (`propel` here), is the name of the database,
as defined in your `databases.yml`:

```yml
all:
  propel: # name of the Propel database
    class:  sfPropelDatabase
    param:
      dsn:  mysql://root:@localhost/aidedecamp

```
