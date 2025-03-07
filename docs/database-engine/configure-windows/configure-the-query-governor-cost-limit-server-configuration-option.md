---
title: "Server configuration: query governor cost limit"
description: Learn about the query governor cost limit option. See how to use it to limit execution of queries.
author: rwestMSFT
ms.author: randolphwest
ms.date: 10/18/2024
ms.service: sql
ms.subservice: configuration
ms.topic: conceptual
helpviewer_keywords:
  - "queries [SQL Server], time to execute"
  - "query governor cost limit option [SQL Server]"
  - "time [SQL Server], query run time"
---
# Server configuration: query governor cost limit

[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

This article describes how to configure the `query governor cost limit` server configuration option in [!INCLUDE [ssnoversion](../../includes/ssnoversion-md.md)] by using [!INCLUDE [ssManStudioFull](../../includes/ssmanstudiofull-md.md)] or [!INCLUDE [tsql](../../includes/tsql-md.md)]. The cost limit option specifies an upper limit on the estimated cost allowed for a given query to run. Query cost is an abstract figure determined by the query optimizer based on estimated execution requirements such as CPU time, memory, and disk I/O. It refers to the estimated elapsed time, in seconds, that would be required to complete a query on a specific hardware configuration. This abstract figure doesn't equate to the time required to complete a query on the running instance. It should be treated as a relative measure. The default value for this option is `0`, which sets the query governor to off. Setting the value to `0` allows all queries to run without any time limitation. If you specify a nonzero, nonnegative value, the query governor disallows execution of any query that has an estimated cost that exceeds that value.

## Recommendations

This option is an advanced option and should be changed only by an experienced database administrator or certified [!INCLUDE [ssNoVersion](../../includes/ssnoversion-md.md)] professional.

To change the value query governor cost limit on a per-connection basis, use the [SET QUERY_GOVERNOR_COST_LIMIT](../../t-sql/statements/set-query-governor-cost-limit-transact-sql.md) statement.

## Permissions

Execute permissions on `sp_configure` with no parameters or with only the first parameter are granted to all users by default. To execute `sp_configure` with both parameters to change a configuration option or to run the `RECONFIGURE` statement, a user must be granted the `ALTER SETTINGS` server-level permission. The `ALTER SETTINGS` permission is implicitly held by the **sysadmin** and **serveradmin** fixed server roles.

<a id="SSMSProcedure"></a>

## Use SQL Server Management Studio

1. In Object Explorer, right-click a server and select **Properties**.

1. Select the **Connections** page.

1. Select or clear the **Use query governor to prevent long-running queries** check box.

   If you select this check box, in the box below, enter a positive value, which the query governor uses to disallow execution of any query with an estimated cost exceeding that value.

<a id="TsqlProcedure"></a>

## Use Transact-SQL

1. Connect to the [!INCLUDE [ssDE](../../includes/ssde-md.md)].

1. From the Standard bar, select **New Query**.

1. Copy and paste the following example into the query window and select **Execute**. This example shows how to use [sp_configure](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md) to set the value of the `query governor cost limit` option to an estimated query cost upper limit of `120`.

   ```sql
   USE master;
   GO

   EXECUTE sp_configure 'show advanced options', 1;
   GO

   RECONFIGURE;
   GO

   EXECUTE sp_configure 'query governor cost limit', 120;
   GO

   RECONFIGURE;
   GO

   EXECUTE sp_configure 'show advanced options', 0;
   GO

   RECONFIGURE;
   GO
   ```

For more information, see [Server configuration options](server-configuration-options-sql-server.md).

<a id="FollowUp"></a>

## Follow up: After you configure the query governor cost limit option

The setting takes effect immediately without restarting the server.

## Related content

- [RECONFIGURE (Transact-SQL)](../../t-sql/language-elements/reconfigure-transact-sql.md)
- [SET QUERY_GOVERNOR_COST_LIMIT (Transact-SQL)](../../t-sql/statements/set-query-governor-cost-limit-transact-sql.md)
- [Server configuration options](server-configuration-options-sql-server.md)
- [sp_configure (Transact-SQL)](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md)
