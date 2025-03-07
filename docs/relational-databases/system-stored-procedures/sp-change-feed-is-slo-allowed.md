---
title: "sys.sp_change_feed_is_slo_allowed (Transact-SQL)"
description: "The sys.sp_change_feed_is_slo_allowed system internal stored procedure checks for a supported service level objective."
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: imotiwala
ms.date: 09/24/2024
ms.service: fabric
ms.subservice: system-objects
ms.topic: "reference"
ms.custom:
  - ignite-2024
f1_keywords:
  - "sys.sp_change_feed_is_slo_allowed_TSQL"
  - "sys.sp_change_feed_is_slo_allowed"
  - "sp_change_feed_is_slo_allowed_TSQL"
  - "sp_change_feed_is_slo_allowed"
helpviewer_keywords:
  - "sp_change_feed_is_slo_allowed"
dev_langs:
  - "TSQL"
monikerRange: ">=sql-server-ver16 || =azuresqldb-current || =fabric || =azure-sqldw-latest"
---
# sys.sp_change_feed_is_slo_allowed (Transact-SQL)

[!INCLUDE [sqlserver2022-asdb-asa-fabricmirroredsqldb-fabricsqldb](../../includes/applies-to-version/sqlserver2022-asdb-asa-fabricmirroredsqldb-fabricsqldb.md)]

Checks for a supported service level objective.

> [!NOTE]
> This system stored procedure is used internally and isn't recommended for direct administrative use. Use Synapse Studio or the Fabric portal instead.

This system stored procedure is used for:

- The Azure Synapse Link feature for SQL Server instances and Azure SQL Database. For more information, see [Manage Azure Synapse Link for SQL Server and Azure SQL Database](../../sql-server/synapse-link/synapse-link-sql-server-change-feed-manage.md).
- The Fabric Mirrored Database feature for Azure SQL Database. For more information, see [Microsoft Fabric mirrored databases](/fabric/database/mirrored-database/overview).
- SQL database in Microsoft Fabric. For more information, see [SQL database in Microsoft Fabric](/fabric/database/sql/overview).

## Syntax

:::image type="icon" source="../../includes/media/topic-link-icon.svg" border="false"::: [Transact-SQL syntax conventions](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

```syntaxsql
sys.sp_change_feed_is_slo_allowed
```

## Permissions

A user with [CONTROL database permissions](../security/permissions-database-engine.md), **db_owner** database role membership, or **sysadmin** server role membership can execute this procedure.

## Related content

- [sys.sp_help_change_feed (Transact-SQL)](sp-help-change-feed.md)
- [sys.sp_help_change_feed_table (Transact-SQL)](sp-help-change-feed-table.md)
- [sys.sp_help_change_feed_table_groups (Transact-SQL)](sp-help-change-feed-table-groups.md)
- [sys.sp_help_change_feed_settings (Transact-SQL)](sp-help-change-feed-settings.md)
- [sys.sp_change_feed_configure_parameters (Transact-SQL)](sp-change-feed-configure-parameters.md)
- [sys.dm_change_feed_log_scan_sessions (Transact-SQL)](../system-dynamic-management-views/sys-dm-change-feed-log-scan-sessions.md)
- [sys.dm_change_feed_errors (Transact-SQL)](../system-dynamic-management-views/sys-dm-change-feed-errors.md)
