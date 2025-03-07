---
title: "SQL Server, SQL Errors object"
description: "Learn about the SQLServer:SQL Errors object, which provides counters to monitor SQL Errors in SQL Server."
author: MikeRayMSFT
ms.author: mikeray
ms.date: 12/04/2023
ms.service: sql
ms.subservice: performance
ms.topic: reference
helpviewer_keywords:
  - "SQL Errors object"
  - "SQLServer:SQL Errors"
---
# SQL Server, SQL Errors object
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  The **SQLServer:SQL Errors** object provides counters to monitor errors in Microsoft [!INCLUDE [ssNoVersion](../../includes/ssnoversion-md.md)].  
  
 This table describes the [!INCLUDE [ssNoVersion](../../includes/ssnoversion-md.md)] **SQL Errors** counters.  
  
|SQL Server SQL Errors counters|Description|  
|------------------------------------|-----------------|  
|**Errors/sec**|Number of errors/sec.|  
  
 Each counter in the object contains the following instances:  
  
|Item|Definition|  
|----------|----------------|  
|**_Total**|Information for all errors.|  
|**DB Offline Errors**|Tracks severe errors that cause [!INCLUDE [ssNoVersion](../../includes/ssnoversion-md.md)] to take the current database offline.|  
|**Info Errors**|Information related to error messages that provide information to users but do not cause errors.|  
|**Kill Connection Errors**|Tracks severe errors that cause [!INCLUDE [ssNoVersion](../../includes/ssnoversion-md.md)] to kill the current connection.|  
|**User Errors**|Information about user errors.|  
  
  
## Example

You begin to explore the query performance counters in this object using this T-SQL query on the [sys.dm_os_performance_counters](../system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql.md) dynamic management view:

```sql
SELECT * FROM sys.dm_os_performance_counters
WHERE object_name LIKE '%SQL Errors%';
```  

## Related content

- [Monitor Resource Usage (Performance Monitor)](monitor-resource-usage-system-monitor.md)
