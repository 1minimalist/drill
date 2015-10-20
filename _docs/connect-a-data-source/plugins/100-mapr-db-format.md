---
title: "MapR-DB Format"
parent: "Connect a Data Source"
---
The MapR-DB format is not included in the Apache Drill release. Drill includes a `maprdb` format for MapR-DB that is defined within the
default `dfs` storage plugin configuration when you install Drill from the `mapr-drill` package on a MapR node. The `maprdb` format improves the
estimated number of rows that Drill uses to plan a query. It also enables you
to query tables like you would query files in a file system because MapR-DB
and MapR-FS share the same namespace. 

You can query tables stored across multiple directories. You do not need to
create a table mapping to a directory before you query a table in the
directory. You can select from any table in any directory the same way you
would select from files in MapR-FS, using the same syntax.

Instead of including the name of a file, you include the table name in the
query. The userid running the query must have read permission to access the MapR table.  

**Example**

    SELECT * FROM mfs.`/users/max/mytable`;

The following image shows a portion of the `dfs` configuration with the `maprdb`
format:

![drill query flow]({{ site.baseurl }}/docs/img/18.png)

