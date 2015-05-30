---
title: "Supported Data Types"
parent: "Data Types"
---
Drill reads from and writes to data sources having a wide variety of types. Drill uses data types at the RPC level that are not supported for query input, often implicitly casting data. Drill supports the following SQL data types for query input:

| SQL Data Type                                     | Description                                                                                                          | Example                                                                        |
|---------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------|
| BIGINT                                            | 8-byte signed integer in the range -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807                           | 9223372036854775807                                                            |
| BINARY                                            | Variable-length byte string                                                                                          | B@e6d9eb7                                                                      |
| BOOLEAN                                           | True or false                                                                                                        | true                                                                           |
| DATE                                              | Years, months, and days in YYYY-MM-DD format since 4713 BC                                                           | 2015-12-30                                                                     |
| DECIMAL(p,s), or DEC(p,s), NUMERIC(p,s)*          | 38-digit precision number, precision is p, and scale is s                                                            | DECIMAL(6,2) is 1234.56,  4 digits before and 2 digits after the decimal point |
| FLOAT                                             | 4-byte floating point number                                                                                         | 0.456                                                                          |
| DOUBLE, DOUBLE PRECISION                          | 8-byte floating point number, precision-scalable                                                                     | 0.456                                                                          |
| INTEGER or INT                                    | 4-byte signed integer in the range -2,147,483,648 to 2,147,483,647                                                   | 2147483646                                                                     |
| INTERVALDAY                                       | A period of time in days, hours, minutes, and seconds only                                                           | '1 10:20:30.123'                                                               |
| INTERVALYEAR                                      | A period of time in years and months only                                                                            | '1-2' year to month                                                            |
| SMALLINT**                                        | 2-byte signed integer in the range -32,768 to 32,767                                                                 | 32000                                                                          |
| TIME                                              | 24-hour based time before or after January 1, 2001 in hours, minutes, seconds format: HH:mm:ss                       | 22:55:55.23                                                                    |
| TIMESTAMP                                         | JDBC timestamp in year, month, date hour, minute, second, and optional milliseconds format: yyyy-MM-dd HH:mm:ss.SSS  | 2015-12-30 22:55:55.23                                                         |
| CHARACTER VARYING, CHARACTER, CHAR,*** or VARCHAR | UTF8-encoded variable-length string. The default limit is 1 character. The maximum character limit is 2,147,483,647. | CHAR(30) casts data to a 30-character string maximum.                          |


\* In this release, Drill disables the DECIMAL data type (an alpha feature), including casting to DECIMAL and reading DECIMAL types from Parquet and Hive. The NUMERIC data type is an alias for the DECIMAL data type.  
\*\* Not currently supported.  
\*\*\* Currently, Drill supports only variable-length strings.  

## Enabling the DECIMAL Type

To enable the DECIMAL type, set the `planner.enable_decimal_data_type` option to `true`. The DECIMAL type is released as an alpha feature and not recommended for production use.

     ALTER SYSTEM SET `planner.enable_decimal_data_type` = true;

    +-------+--------------------------------------------+
    |  ok   |                  summary                   |
    +-------+--------------------------------------------+
    | true  | planner.enable_decimal_data_type updated.  |
    +-------+--------------------------------------------+
    1 row selected (0.08 seconds)

## Composite Types

Drill supports the following composite types:

* Map
* Array

A map is a set of name/value pairs. A value in a map can be a scalar type, such as string or int, or a complex type, such as an array or another map. An array is a repeated list of values. A value in an array can be a scalar type, such as string or int, or an array can be a complex type, such as a map or another array.

Drill uses map and array data types internally for reading complex and nested data structures from data sources. In this release of Drill, you cannot reference a composite type by name in a query, but Drill supports array values coming from data sources. For example, you can use the index syntax to query data and get the value of an array element:  

`a[1]`  

You can refer to the value for a key in a map using dot notation:

`t.m.k`

The section [“Query Complex Data”]({{ site.baseurl }}/docs/querying-complex-data-introduction) shows how to use composite types to access nested arrays. ["Handling Different Data Types"]({{ site.baseurl }}/docs/handling-different-data-types/#handling-json-and-parquet-data) includes examples of JSON maps and arrays. Drill provides functions for handling array and map types:

* ["KVGEN"]({{site.baseurl}}/docs/kvgen/)
* ["FLATTEN"]({{site.baseurl}}/docs/flatten/)

## ANY Type
The ANY type is a key technological advance in Drill that enables it to address late typing problems. Drill uses the ANY type internally and you might see references to ANY in the output of the DESCRIBE or other commands. You cannot cast a value to the ANY type in this release.

Using the ANY type, the parser postpones the problem of resolving the type of some value until the query is actually running.  At that point, Drill has an empirical schema available for each record batch to use for final code
generation and optimization.  If the empirical schema changes due to
changes in the data processing, Drill regenerates the code as necessary.

## Casting and Converting Data Types

In Drill, you cast or convert data to the required type for moving data from one data source to another or to make the data readable.
You do not assign a data type to every column name in a CREATE TABLE statement to define the table as you do in database software. Instead, you use the CREATE TABLE AS SELECT (CTAS) statement with one or more of the following functions to define the table:

* [CAST]({{ site.baseurl }}/docs/data-type-conversion#cast)    
* [CONVERT TO/FROM]({{ site.baseurl }}/docs/data-type-conversion#convert_to-and-convert_from)   
  Use the [CONVERT TO AND CONVERT FROM data types]({{ site.baseurl }}/docs/supported-data-types/#convert_to-and-convert_from-data-types)  
* Other [data conversion functions]({{ site.baseurl }}/docs/data-type-conversion#other-data-type-conversions)   

In some cases, Drill converts schema-less data to correctly-typed data implicitly. In this case, you do not need to cast the data. The file format of the data and the nature of your query determines the requirement for casting or converting. Differences in casting depend on the data source. The following list describes how Drill treats data types from various data sources:

* HBase  
  Does not implicitly cast input to SQL types. Convert data to appropriate types as shown in ["Querying HBase."]({{ site.baseurl }}/docs/querying-hbase/)
* Hive  
  Implicitly casts Hive types to SQL types as shown in the Hive [type mapping example]({{ site.baseurl }}/docs/hive-to-drill-data-type-mapping#type-mapping-example)
* JSON  
  Implicitly casts JSON data to its [corresponding SQL types]({{ site.baseurl }}/docs/json-data-model#data-type-mapping) or to VARCHAR if Drill is in all text mode. 
* MapR-DB  
  Implicitly casts MapR-DB data to SQL types when you use [the maprdb format]({{ site.baseurl }}/docs/mapr-db-format) for reading MapR-DB data. The dfs storage plugin defines the format when you install Drill from the mapr-drill package on a MapR node.
* Parquet  
  Implicitly casts Parquet data to the SQL types shown in [SQL Data Types to Parquet]({{ site.baseurl }}/docs/parquet-format#sql-data-types-to-parquet). 
* Text: CSV, TSV, and other text  
  Implicitly casts all textual data to VARCHAR.

## Implicit Casting Precedence of Data Types

The following list includes data types Drill uses in descending order of precedence. Casting precedence shown in the following table applies to the implicit casting that Drill performs. For example, Drill might implicitly cast data when a query includes a function or filter on mismatched data types:

    SELECT myBigInt FROM mytable WHERE myBigInt = 2.5;

As shown in the table, Drill can cast a NULL value, which has the lowest precedence, to any other type; you can cast a SMALLINT (not supported in this release) value to INT. Drill might deviate from these precedence rules for performance reasons. Under certain circumstances, such as queries involving SUBSTR and CONCAT functions, Drill reverses the order of precedence and allows a cast to VARCHAR from a type of higher precedence than VARCHAR, such as BIGINT.

### Casting Precedence

| Precedence | Data Type              | Precedence | Data Type     |
|------------|------------------------|------------|---------------|
| 1          | INTERVALYEAR (highest) | 11         | INT           |
| 2          | INTERVLADAY            | 12         | UINT2         |
| 3          | TIMESTAMP              | 13         | SMALLINT*     |
| 4          | DATE                   | 14         | UINT1         |
| 5          | TIME                   | 15         | VAR16CHAR     |
| 6          | DOUBLE                 | 16         | FIXED16CHAR   |
| 7          | DECIMAL                | 17         | VARCHAR       |
| 8          | UINT8                  | 18         | CHAR          |
| 9          | BIGINT                 | 19         | VARBINARY     |
| 10         | UINT4                  | 20         | FIXEDBINARY   |
|            |                        | 21         | NULL (lowest) |

\* Not supported in this release.

## Explicit Casting

In a textual file, such as CSV, Drill interprets every field as a VARCHAR, as previously mentioned. To handle textual data, you can use the following functions to cast and convert compatible data types:

* [CAST]({{ site.baseurl }}/docs/data-type-conversion#cast)  
  Casts data from one data type to another.
* [CONVERT_TO and CONVERT_FROM]({{ site.baseurl }}/docs/data-type-conversion#convert_to-and-convert_from)  
  Converts data, including binary data, from one data type to another.
* [TO_CHAR]({{ site.baseurl }}/docs/data-type-conversion/#to_char)  
  Converts a TIMESTAMP, INTERVALDAY/INTERVALYEAR, INTEGER, DOUBLE, or DECIMAL to a string.
* [TO_DATE]({{ site.baseurl }}/docs/data-type-conversion/#to_date)  
  Converts a string to DATE.
* [TO_NUMBER]({{ site.baseurl }}/docs/data-type-conversion/#to_number)  
  Converts a string to a DECIMAL.
* [TO_TIMESTAMP]({{ site.baseurl }}/docs/data-type-conversion/#to_timestamp)  
  Converts a string to TIMESTAMP.

If the SELECT statement includes a WHERE clause that compares a column of an unknown data type, cast both the value of the column and the comparison value in the WHERE clause.

## Explicit Type Casting Maps

The following tables show data types that Drill can cast to/from other data types. Not all types are available for explicit casting in the current release.

### Numerical and Character Data Types

| To            | SMALLINT | INT | BIGINT | DECIMAL | FLOAT | CHAR | FIXEDBINARY | VARCHAR | VARBINARY |
|---------------|----------|-----|--------|---------|-------|------|-------------|---------|-----------|
| **From:**     |          |     |        |         |       |      |             |         |           |
| SMALLINT*     | yes      | yes | yes    | yes     | yes   | yes  | yes         | yes     | yes       |
| INT           | yes      | yes | yes    | yes     | yes   | yes  | yes         | yes     | yes       |
| BIGINT        | yes      | yes | yes    | yes     | yes   | yes  | yes         | yes     | yes       |
| DECIMAL       | yes      | yes | yes    | yes     | yes   | yes  | yes         | yes     | yes       |
| DOUBLE        | yes      | yes | yes    | yes     | yes   | yes  | no          | yes     | no        |
| FLOAT         | yes      | yes | yes    | yes     | yes   | yes  | no          | yes     | no        |
| CHAR          | yes      | yes | yes    | yes     | yes   | no   | yes         | yes     | yes       |
| FIXEDBINARY** | yes      | yes | yes    | yes     | yes   | no   | no          | yes     | yes       |
| VARCHAR***    | yes      | yes | yes    | yes     | yes   | yes  | yes         | no      | yes       |
| VARBINARY**   | yes      | yes | yes    | yes     | yes   | no   | yes         | yes     | no        |


\* Not supported in this release.   
\*\* Used to cast binary UTF-8 data coming to/from sources such as MapR-DB/HBase.   
\*\*\* You cannot convert a character string having a decimal point to an INT or BIGINT.   

{% include startnote.html %}The CAST function does not support all representations of FIXEDBINARY and VARBINARY. Only the UTF-8 format is supported. {% include endnote.html %}

If your FIXEDBINARY or VARBINARY data is in a format other than UTF-8, such as big endian, use the CONVERT_TO/FROM functions instead of CAST.

### Date and Time Data Types

| To:          | DATE | TIME | TIMESTAMP | INTERVALDAY | INTERVALYEAR | INTERVALDAY |
|--------------|------|------|-----------|-------------|--------------|-------------|
| From:        |      |      |           |             |              |             |
| CHAR         | Yes  | Yes  | Yes       | Yes         | Yes          | Yes         |
| FIXEDBINARY* | No   | No   | No        | No          | No           | No          |
| VARCHAR      | Yes  | Yes  | Yes       | Yes         | Yes          | Yes         |
| VARBINARY*   | No   | No   | Yes       | No          | No           | No          |
| DATE         | No   | No   | Yes       | No          | No           | No          |
| TIME         | No   | Yes  | Yes       | No          | No           | No          |
| TIMESTAMP    | Yes  | Yes  | Yes       | No          | No           | No          |
| INTERVALYEAR | Yes  | No   | Yes       | Yes         | No           | Yes         |
| INTERVALDAY  | Yes  | No   | Yes       | Yes         | Yes          | No          |

\* Used to cast binary UTF-8 data coming to/from sources such as MapR-DB/HBase. The CAST function does not support all representations of FIXEDBINARY and VARBINARY. Only the UTF-8 format is supported. 

## CONVERT_TO and CONVERT_FROM

CONVERT_TO converts data to binary from the input type. CONVERT_FROM converts data from binary to the input type. For example, the following CONVERT_TO function converts an integer in big endian format to VARBINARY:

    CONVERT_TO(mycolumn, 'INT_BE')

CONVERT_FROM and CONVERT_TO methods transform a known binary representation/encoding to a Drill internal format. 

We recommend storing HBase/MapR-DB data in a binary representation rather than
a string representation. Use the \*\_BE types to store integer data types in an HBase or Mapr-DB table.  INT is a 4-byte little endian signed integer. INT_BE is a 4-byte big endian signed integer. The comparison order of \*\_BE encoded bytes is the same as the integer value itself if the bytes are unsigned or positive. Using a *_BE type facilitates scan range pruning and filter pushdown into HBase scan. 

\*\_HADOOPV in the data type name denotes the variable length integer as defined by Hadoop libraries. Use a \*\_HADOOPV type if user data is encoded in this format by a Hadoop tool outside MapR.

The following table lists the data types for use with the CONVERT_TO
and CONVERT_FROM functions:

**Type**| **Input Type**| **Output Type**  
---|---|---  
JSON | bytes | varchar
BOOLEAN_BYTE| bytes(1)| BOOLEAN  
TINYINT_BE| bytes(1)| TINYINT  
TINYINT| bytes(1)| TINYINT  
SMALLINT_BE| bytes(2)| SMALLINT  
SMALLINT| bytes(2)| SMALLINT  
INT_BE| bytes(4)| INT  
INT| bytes(4)| INT  
BIGINT_BE| bytes(8)| BIGINT  
BIGINT| bytes(8)| BIGINT  
FLOAT| bytes(4)| FLOAT (float4)  
DOUBLE| bytes(8)| DOUBLE (float8)  
INT_HADOOPV| bytes(1-9)| INT  
BIGINT_HADOOPV| BYTES(1-9)| BIGINT  
DATE_EPOCH_BE| bytes(8)| DATE  
DATE_EPOCH| bytes(8)| DATE  
TIME_EPOCH_BE| bytes(8)| TIME  
TIME_EPOCH| bytes(8)| TIME  
UTF8| bytes| VARCHAR  
UTF16| bytes| VAR16CHAR  
UINT8| bytes(8)| UINT8  

If you are unsure that the size of the source and destination INT or BIGINT you are converting is the same, use CAST to convert these data types to/from binary.


