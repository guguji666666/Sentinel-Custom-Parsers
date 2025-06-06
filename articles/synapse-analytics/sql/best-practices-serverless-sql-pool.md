---
title: Best practices for serverless SQL pool
description: Recommendations and best practices for working with serverless SQL pools in Azure Synapse Analytics.
author: filippopovic
ms.author: fipopovi

ms.date: 11/08/2024
ms.service: azure-synapse-analytics
ms.subservice: sql
ms.topic: best-practice
---

# Best practices for serverless SQL pool in Azure Synapse Analytics

In this article, you'll find a collection of best practices for using serverless SQL pool. Serverless SQL pool is a resource in Azure Synapse Analytics. If you're working with a dedicated SQL pool, see [Best practices for dedicated SQL pools](best-practices-dedicated-sql-pool.md) for specific guidance.

Serverless SQL pool allows you to query files in your Azure Storage accounts. It doesn't have local storage or ingestion capabilities. All files that the queries target are external to serverless SQL pool. Everything related to reading files from storage might affect query performance.

Some generic guidelines are:

- Make sure your client applications are collocated with serverless SQL pool.
  - If you're using client applications outside Azure, make sure you're using serverless SQL pool in a region that's close to your client computer. Client application examples include Power BI Desktop, SQL Server Management Studio, and Azure Data Studio.
- Make sure the storage and serverless SQL pool are in the same region. Storage examples include Azure Data Lake Storage and Azure Cosmos DB.
- Try to [optimize storage layout](#prepare-files-for-querying) by using partitioning and keeping your files in the range between 100 MB and 10 GB.
- If you're returning a large number of results, make sure you're using SQL Server Management Studio or Azure Data Studio and not Azure Synapse Studio. Azure Synapse Studio is a web tool that isn't designed for large result sets.
- If you're filtering results by string column, try to use a `BIN2_UTF8` collation. For more information on changing collations, see [Collation types supported for Synapse SQL](reference-collation-types.md).
- Consider caching the results on the client side by using Power BI import mode or Azure Analysis Services, and periodically refresh them. Serverless SQL pools can't provide an interactive experience in Power BI Direct Query mode if you're using complex queries or processing a large amount of data.
- Maximum concurrency isn't limited and depends on the query complexity and amount of data scanned. One serverless SQL pool can concurrently handle 1,000 active sessions that are executing lightweight queries. The numbers will drop if the queries are more complex or scan a larger amount of data, so in that case consider decreasing concurrency and execute queries over a longer period of time if possible.

## Client applications and network connections

Make sure your client application is connected to the closest possible Azure Synapse workspace with the optimal connection.
- Colocate a client application with the Azure Synapse workspace. If you're using applications such as Power BI or Azure Analysis Service, make sure they're in the same region where you placed your Azure Synapse workspace. If needed, create the separate workspaces that are paired with your client applications. Placing a client application and the Azure Synapse workspace in different regions could cause bigger latency and slower streaming of results.
- If you're reading data from your on-premises application, make sure the Azure Synapse workspace is in the region that's close to your location.
- Make sure you don't have network bandwidth issues while reading a large amount of data.
- Don't use Azure Synapse Studio to return a large amount of data. Azure Synapse Studio is a web tool that uses the HTTPS protocol to transfer data. Use Azure Data Studio or SQL Server Management Studio to read a large amount of data.

## Storage and content layout

Here are best practices for storage and content layout in serverless SQL pool.

### Colocate your storage and serverless SQL pool

To minimize latency, colocate your Azure Storage account or Azure Cosmos DB analytic storage and your serverless SQL pool endpoint. Storage accounts and endpoints provisioned during workspace creation are located in the same region.

For optimal performance, if you access other storage accounts with serverless SQL pool, make sure they're in the same region. If they aren't in the same region, there will be increased latency for the data's network transfer between the remote region and the endpoint's region.

### Colocate your Azure Cosmos DB analytical storage and serverless SQL pool

Make sure your Azure Cosmos DB analytical storage is placed in the same region as an Azure Synapse workspace. Cross-region queries might cause huge latencies. Use the region property in the connection string to explicitly specify the region where the analytical store is placed (see [Query Azure Cosmos DB by using serverless SQL pool](query-cosmos-db-analytical-store.md#overview)): `account=<database account name>;database=<database name>;region=<region name>'`

### Azure Storage throttling

Multiple applications and services might access your storage account. Storage throttling occurs when the combined IOPS or throughput generated by applications, services, and serverless SQL pool workloads exceeds the limits of the storage account. As a result, you'll experience a significant negative effect on query performance.

When throttling is detected, serverless SQL pool has built-in handling to resolve it. Serverless SQL pool makes requests to storage at a slower pace until throttling is resolved.

> [!TIP]  
> For optimal query execution, don't stress the storage account with other workloads during query execution.

### Prepare files for querying

If possible, you can prepare files for better performance:

- Convert large CSV and JSON files to Parquet. Parquet is a columnar format. Because it's compressed, its file sizes are smaller than CSV or JSON files that contain the same data. Serverless SQL pool skips the columns and rows that aren't needed in a query if you're reading Parquet files. Serverless SQL pool needs less time and fewer storage requests to read it.
- If a query targets a single large file, you'll benefit from splitting it into multiple smaller files.
- Try to keep your CSV file size between 100 MB and 10 GB.
- It's better to have equally sized files for a single OPENROWSET path or an external table LOCATION.
- Partition your data by storing partitions to different folders or file names. See [Use filename and filepath functions to target specific partitions](#use-filename-and-filepath-functions-to-target-specific-partitions).

## CSV optimizations

Here are best practices for using CSV files in serverless SQL pool.

### Use PARSER_VERSION 2.0 to query CSV files

You can use a performance-optimized parser when you query CSV files. For details, see [PARSER_VERSION](develop-openrowset.md).

### Manually create statistics for CSV files

Serverless SQL pool relies on statistics to generate optimal query execution plans. Statistics are automatically created for columns using sampling and in most cases sampling percentage will be less than 100%. This flow is the same for every file format. Have in mind that when reading CSV with parser version 1.0 sampling isn't supported and automatic creation of statistics won't happen with sampling percentage less than 100%. For small tables with estimated low cardinality (number of rows) automatic statistics creation will be triggered with sampling percentage of 100%. That means that fullscan is triggered and automatic statistics are created even for CSV with parser version 1.0. In case statistics aren't automatically created, create statistics manually for columns that you use in queries, particularly those used in DISTINCT, JOIN, WHERE, ORDER BY, and GROUP BY. Check [statistics in serverless SQL pool](develop-tables-statistics.md#statistics-in-serverless-sql-pool) for details.

## Delta Lake optimizations

Here are best practices for using Delta Lake files in serverless SQL pool.

### Optimize checkpoints

Query performance of Delta Lake format is influenced by the number of JSON files in the _delta_log directory. To ensure optimal performance, avoid accumulating too many JSON files. Ideally, the log should contain only the latest Parquet checkpoint file with no additional JSON files. However, this setup may not be optimal for write-heavy workloads.

A balanced approach is to maintain around 10 JSON files between checkpoints, which typically offers good performance for both readers and writers. Be cautious of configurations that delay checkpoint creation, as they can lead to excessive JSON file accumulation and degrade query performance.

Set the following table property to ensure a checkpoint is created after every 10 JSON log files:

```sql
ALTER TABLE tableName SET TBLPROPERTIES ('delta.checkpointInterval' = '10')
```

## Data types

Here are best practices for using data types in serverless SQL pool.

### Use appropriate data types

The data types you use in your query affect performance and concurrency. You can get better performance if you follow these guidelines:

- Use the smallest data size that can accommodate the largest possible value.
  - If the maximum character value length is 30 characters, use a character data type of length 30.
  - If all character column values are of a fixed size, use **char** or **nchar**. Otherwise, use **varchar** or **nvarchar**.
  - If the maximum integer column value is 500, use **smallint** because it's the smallest data type that can accommodate this value. For more information, see [integer data type ranges](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=azure-sqldw-latest&preserve-view=true).
- If possible, use **varchar** and **char** instead of **nvarchar** and **nchar**.
  - Use the **varchar** type with some UTF8 collation if you're reading data from Parquet, Azure Cosmos DB, Delta Lake, or CSV with UTF-8 encoding.
  - Use the **varchar** type without UTF8 collation if you're reading data from CSV non-Unicode files (for example, ASCII).
  - Use the **nvarchar** type if you're reading data from a CSV UTF-16 file.
- Use integer-based data types if possible. SORT, JOIN, and GROUP BY operations complete faster on integers than on character data.
- If you're using schema inference, [check inferred data types](#check-inferred-data-types) and override them explicitly with the smaller types if possible.

### Check inferred data types

[Schema inference](query-parquet-files.md#automatic-schema-inference) helps you quickly write queries and explore data without knowing file schemas. The cost of this convenience is that inferred data types might be larger than the actual data types. This discrepancy happens when there isn't enough information in the source files to make sure the appropriate data type is used. For example, Parquet files don't contain metadata about maximum character column length. So serverless SQL pool infers it as varchar(8000).

Have in mind that the situation can be different in case of the shareable managed and external Spark tables exposed in the SQL engine as external tables. Spark tables provide different data types than the Synapse SQL engines. Mapping between Spark table data types and SQL types can be found [here](../metadata/table.md#share-spark-tables). 

You can use the system stored procedure [sp_describe_first_results_set](/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15&preserve-view=true) to check the resulting data types of your query.

The following example shows how you can optimize inferred data types. This procedure is used to show the inferred data types:

```sql
EXEC sp_describe_first_result_set N'
    SELECT
        vendor_id, pickup_datetime, passenger_count
    FROM  
        OPENROWSET(
            BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*'',
            FORMAT=''PARQUET''
        ) AS nyc';
```

Here's the result set:

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------|
|0|1|vendor_id|varchar(8000)|8000|
|0|2|pickup_datetime|datetime2(7)|8|
|0|3|passenger_count|int|4|

After you know the inferred data types for the query, you can specify appropriate data types:

```sql
SELECT
    vendorID, tpepPickupDateTime, passengerCount
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=2018/puMonth=*/*.snappy.parquet',
        FORMAT='PARQUET'
    )  
    WITH (
        vendorID varchar(4), -- we used length of 4 instead of the inferred 8000
        tpepPickupDateTime datetime2,
        passengerCount int
    ) AS nyc;
```

## Filter optimization

Here are best practices for using queries in serverless SQL pool.

### Push wildcards to lower levels in the path

You can use wildcards in your path to [query multiple files and folders](query-data-storage.md#query-multiple-files-or-folders). Serverless SQL pool lists files in your storage account, starting from the first asterisk (*), by using the storage API. It eliminates files that don't match the specified path. Reducing the initial list of files can improve performance if there are many files that match the specified path up to the first wildcard.

### Use filename and filepath functions to target specific partitions

Data is often organized in partitions. You can instruct serverless SQL pool to query particular folders and files. Doing so reduces the number of files and the amount of data the query needs to read and process. An added bonus is that you'll achieve better performance.

For more information, read about the [filename](query-data-storage.md#filename-function) and [filepath](query-data-storage.md#filepath-function) functions and see the examples for [querying specific files](query-specific-files.md).

> [!TIP]  
> Always cast the results of the filepath and filename functions to appropriate data types. If you use character data types, be sure to use the appropriate length.

Functions used for partition elimination, filepath, and filename, aren't currently supported for external tables, other than those created automatically for each table created in Apache Spark for Azure Synapse Analytics.

If your stored data isn't partitioned, consider partitioning it. That way you can use these functions to optimize queries that target those files. When you [query partitioned Apache Spark for Azure Synapse tables](develop-storage-files-spark-tables.md) from serverless SQL pool, the query automatically targets only the necessary files.

### Use proper collation to utilize predicate pushdown for character columns

Data in a Parquet file is organized in row groups. Serverless SQL pool skips row groups based on the specified predicate in the WHERE clause, which reduces IO. The result is increased query performance.

Predicate pushdown for character columns in Parquet files is supported for Latin1_General_100_BIN2_UTF8 collation only. You can specify collation for a particular column by using a WITH clause. If you don't specify this collation by using a WITH clause, the database collation is used.

## Optimize repeating queries

Here are best practices for using CETAS in serverless SQL pool.

### Use CETAS to enhance query performance and joins

[CETAS](develop-tables-cetas.md) is one of the most important features available in serverless SQL pool. CETAS is a parallel operation that creates external table metadata and exports the SELECT query results to a set of files in your storage account.

You can use CETAS to materialize frequently used parts of queries, like joined reference tables, to a new set of files. You can then join to this single external table instead of repeating common joins in multiple queries.

As CETAS generates Parquet files, statistics are automatically created when the first query targets this external table. The result is improved performance for subsequent queries targeting table generated with CETAS.

## Query Azure data

Serverless SQL pools enable you to query data in Azure Storage or Azure Cosmos DB by using [external tables and the OPENROWSET function](develop-storage-files-overview.md). Make sure that you have proper [permission set up](develop-storage-files-overview.md#permissions) on your storage.

### Query CSV data

Learn how to [query a single CSV file](query-single-csv-file.md) or [folders and multiple CSV files](query-folders-multiple-csv-files.md). You can also [query partitioned files](query-specific-files.md)

### Query Parquet data

Learn how to [query Parquet files](query-parquet-files.md) with [nested types](query-parquet-nested-types.md). You can also [query partitioned files](query-specific-files.md).

### Query Delta Lake

Learn how to [query Delta Lake files](query-delta-lake-format.md) with [nested types](query-parquet-nested-types.md).

### Query Azure Cosmos DB data

Learn how to [query Azure Cosmos DB analytical store](query-cosmos-db-analytical-store.md). You can use an [online generator](https://htmlpreview.github.io/?https://github.com/Azure-Samples/Synapse/blob/main/SQL/tools/cosmosdb/generate-openrowset.html) to generate the WITH clause based on a sample Azure Cosmos DB document. You can [create views](create-use-views.md#cosmosdb-view) on top of Azure Cosmos DB containers.

### Query JSON data

Learn how to [query JSON files](query-json-files.md). You can also [query partitioned files](query-specific-files.md).

### Create views, tables, and other database objects

Learn how to create and use [views](create-use-views.md) and [external tables](create-use-external-tables.md) or set up [row-level security](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/how-to-implement-row-level-security-in-serverless-sql-pools/ba-p/2354759).
If you have [partitioned files](query-specific-files.md), make sure you use [partitioned views](create-use-views.md#partitioned-views).

### Copy and transform data (CETAS)

Learn how to [store query results to storage](create-external-table-as-select.md) by using the CETAS command.

## Next steps

- Review the [troubleshooting serverless SQL pools](resources-self-help-sql-on-demand.md) article for solutions to common problems.  
- If you're working with a dedicated SQL pool rather than serverless SQL pool, see [Best practices for dedicated SQL pools](best-practices-dedicated-sql-pool.md) for specific guidance.
- [Azure Synapse Analytics frequently asked questions](../overview-faq.yml)
- [Grant permissions to workspace managed identity](../security/how-to-grant-workspace-managed-identity-permissions.md)
