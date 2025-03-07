---
title: SET CONTEXT_INFO (Transact-SQL)
description: SET CONTEXT_INFO (Transact-SQL)
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.date: 04/04/2024
ms.service: sql
ms.subservice: t-sql
ms.topic: reference
f1_keywords:
  - "SET_CONTEXT_INFO_TSQL"
  - "SET CONTEXT_INFO"
helpviewer_keywords:
  - "context information [SQL Server]"
  - "CONTEXT_INFO option"
  - "SET CONTEXT_INFO statement"
dev_langs:
  - "TSQL"
---

# SET CONTEXT_INFO (Transact-SQL)

[!INCLUDE [SQL Server Azure SQL Database Azure SQL Managed Instance](../../includes/applies-to-version/sql-asdb-asdbmi.md)]

Associates up to 128 bytes of binary information with the current session or connection. 
  
:::image type="icon" source="../../includes/media/topic-link-icon.svg" border="false"::: [Transact-SQL syntax conventions](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## Syntax
  
```syntaxsql
  
SET CONTEXT_INFO { binary_str | @binary_var }  
```  
  

## Arguments

#### *binary_str*  
 Is a **binary** constant, or a constant that is implicitly convertible to **binary**, to associate with the current session or connection.  
  
#### *@binary_var*
 Is a **varbinary** or **binary** variable holding a context value to associate with the current session or connection.  
  
## Remarks

 Like all [SET Statements](../../t-sql/statements/set-statements-transact-sql.md), SET CONTEXT_INFO affects the current session. The preferred way to retrieve the context information for the current session is to use the CONTEXT_INFO function. Session context information is also stored in the `context_info` columns in the following system views:  
  
-   `sys.dm_exec_requests`
-   `sys.dm_exec_sessions`
-   `sys.sysprocesses` (deprecated)
  
 SET CONTEXT_INFO cannot be specified in a user-defined function. You cannot supply a NULL value to SET CONTEXT_INFO because the views holding the values do not allow for NULL values.  
  
 SET CONTEXT_INFO does not accept expressions other than constants or variable names. To set the context information to the result of a function call, you must first include the result of the function call in a **binary** or **varbinary** variable.  
  
 When you issue SET CONTEXT_INFO in a stored procedure or trigger, unlike in other SET statements, the new value set for the context information persists after the stored procedure or trigger is completed.  
  
## Examples
  
### <a id="a-setting-context-information-by-using-a-constant"></a> A. Set context information by using a constant

 The following example demonstrates `SET CONTEXT_INFO` by setting the value and displaying the results. Querying `sys.dm_exec_sessions` requires SELECT and VIEW SERVER STATE permissions, whereas using the CONTEXT_INFO function does not.  
  
```sql
SET CONTEXT_INFO 0x01010101;  
GO  
SELECT context_info   
FROM sys.dm_exec_sessions  
WHERE session_id = @@SPID;  
GO  
```  
  
### <a id="b-setting-context-information-by-using-a-function"></a> B. Set context information by using a function

 The following example demonstrates using the output of a function to set the context value, where the value from the function must be first placed in a **binary** variable.  
  
```sql
DECLARE @BinVar varbinary(128);  
SET @BinVar = CAST(REPLICATE( 0x20, 128 ) AS varbinary(128) );  
SET CONTEXT_INFO @BinVar;  
  
SELECT CONTEXT_INFO() AS MyContextInfo;  
GO  
```  
  
## Related content

- [Row Level Security](../../relational-databases/security/row-level-security.md)
- [SET Statements (Transact-SQL)](../../t-sql/statements/set-statements-transact-sql.md)
- [CONTEXT_INFO (Transact-SQL)](../../t-sql/functions/context-info-transact-sql.md)
- [SESSION_CONTEXT (Transact-SQL)](../../t-sql/functions/session-context-transact-sql.md)
- [sys.dm_exec_requests (Transact-SQL)](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)
- [sys.dm_exec_sessions (Transact-SQL)](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql.md)
- [sp_set_session_context (Transact-SQL)](../../relational-databases/system-stored-procedures/sp-set-session-context-transact-sql.md)
