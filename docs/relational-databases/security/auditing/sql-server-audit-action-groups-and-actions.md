---
title: "SQL Server Audit Action Groups and Actions"
description: Learn about server-level, database-level, and audit-level groups of actions and individual actions in SQL Server Audit.
author: sravanisaluru
ms.author: srsaluru
ms.reviewer: vanto, randolphwest
ms.date: 11/25/2024
ms.service: sql
ms.subservice: security
ms.topic: conceptual
helpviewer_keywords:
  - "audit actions [SQL Server]"
  - "audits [SQL Server], groups"
  - "server-level audit actions [SQL Server]"
  - "SQL Server Audit"
  - "audit-level audit actions [SQL Server]"
  - "database-level audit actions [SQL Server]"
  - "audit action groups [SQL Server]"
  - "audits [SQL Server], actions"
---
# SQL Server Audit action groups and actions

[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

The [!INCLUDE [ssNoVersion](../../../includes/ssnoversion-md.md)] Audit feature enables you to audit server-level and database-level groups of events and individual events. For more information, see [SQL Server Audit (Database Engine)](sql-server-audit-database-engine.md).

[!INCLUDE [ssNoVersion](../../../includes/ssnoversion-md.md)] audits consist of zero or more audit action items. These audit action items can be either a group of actions, such as `SERVER_OBJECT_CHANGE_GROUP`, or individual actions such as `SELECT` operations on a table.

> [!NOTE]  
> `SERVER_OBJECT_CHANGE_GROUP` includes `CREATE`, `ALTER`, and `DROP` for any server object (Database or Endpoint).

Audits can have the following categories of actions:

- Server-level. These actions include server operations, such as management changes and logon and logoff operations.

- Database-level. These actions encompass data manipulation languages (DML) and data definition language (DDL) operations.

- Audit-level. These actions include actions in the auditing process.

Some actions performed on [!INCLUDE [ssNoVersion](../../../includes/ssnoversion-md.md)] auditing components are intrinsically audited in a specific audit, and in these cases audit events occur automatically because the event occurred on the parent object.

The following actions are intrinsically audited:

- Server Audit state change (setting state to `ON` or `OFF`)

The following events aren't intrinsically audited:

- `CREATE SERVER AUDIT SPECIFICATION`
- `ALTER SERVER AUDIT SPECIFICATION`
- `DROP SERVER AUDIT SPECIFICATION`
- `CREATE DATABASE AUDIT SPECIFICATION`
- `ALTER DATABASE AUDIT SPECIFICATION`
- `DROP DATABASE AUDIT SPECIFICATION`

All audits are disabled when initially created.

## Server-level audit action groups

Server-level audit action groups are actions similar to [!INCLUDE [ssNoVersion](../../../includes/ssnoversion-md.md)] security audit event classes. For more information, see [SQL Server Event Class Reference](../../event-classes/sql-server-event-class-reference.md).

The following table describes the server-level audit action groups and provides the equivalent SQL Server Event Class where applicable.

| Action group name | Description |
| --- | --- |
| `APPLICATION_ROLE_CHANGE_PASSWORD_GROUP` | This event is raised whenever a password is changed for an application role. Equivalent to the [Audit App Role Change Password Event Class](../../event-classes/audit-app-role-change-password-event-class.md). |
| `AUDIT_CHANGE_GROUP` | This event is raised whenever any audit is created, modified, or deleted. This event is raised whenever any audit specification is created, modified, or deleted. Any change to an audit is audited in that audit. Equivalent to the [Audit Change Audit Event Class](../../event-classes/audit-change-audit-event-class.md). |
| `BACKUP_RESTORE_GROUP` | This event is raised whenever a backup or restore command is issued. Equivalent to the [Audit Backup and Restore Event Class](../../event-classes/audit-backup-and-restore-event-class.md). |
| `BATCH_COMPLETED_GROUP` | This event is raised whenever any batch text, stored procedure, or transaction management operation completes executing. It's raised after the batch completes and audits the entire batch or stored procedure text, as sent from the client, including the result. Equivalent to the [SQL:BatchCompleted Event Class](../../event-classes/sql-batchcompleted-event-class.md).<br /><br />**Applies to:** [!INCLUDE [sssql22-md](../../../includes/sssql22-md.md)] and later versions. |
| `BATCH_STARTED_GROUP` | This event is raised whenever any batch text, stored procedure, or transaction management operation starts to execute. It's raised before execution and audits the entire batch or stored procedure text, as sent from the client. Equivalent to the [SQL:BatchStarting Event Class](../../event-classes/sql-batchstarting-event-class.md).<br /><br />**Applies to:** [!INCLUDE [sssql22-md](../../../includes/sssql22-md.md)] and later versions. |
| `BROKER_LOGIN_GROUP` | This event is raised to report audit messages related to Service Broker transport security. Equivalent to the [Audit Broker Login Event Class](../../event-classes/audit-broker-login-event-class.md). |
| `DATABASE_CHANGE_GROUP` | This event is raised when a database is created, altered, or dropped. This event is raised whenever any database is created, altered, or dropped. Equivalent to the [Audit Database Management Event Class](../../event-classes/audit-database-management-event-class.md). |
| `DATABASE_LOGOUT_GROUP` | This event is raised when a contained database user signs out of a database. Equivalent to the [Audit Logout Event Class](../../event-classes/audit-logout-event-class.md). |
| `DATABASE_MIRRORING_LOGIN_GROUP` | This event is raised to report audit messages related to database mirroring transport security. Equivalent to the [Audit Database Mirroring Login Event Class](../../event-classes/audit-database-mirroring-login-event-class.md). |
| `DATABASE_OBJECT_ACCESS_GROUP` | This event is raised whenever database objects such as message type, assembly, contract are accessed. This event is raised for any access to any database. Note: This could potentially lead to large audit records.<br /><br />Equivalent to the [Audit Database Object Access Event Class](../../event-classes/audit-database-object-access-event-class.md). |
| `DATABASE_OBJECT_CHANGE_GROUP` | This event is raised when a `CREATE`, `ALTER`, or `DROP` statement is executed on database objects, such as schemas. This event is raised whenever any database object is created, altered, or dropped. Note: This could lead to large quantities of audit records.<br /><br />Equivalent to the [Audit Database Object Management Event Class](../../event-classes/audit-database-object-management-event-class.md). |
| `DATABASE_OBJECT_OWNERSHIP_CHANGE_GROUP` | This event is raised when a change of owner for objects within database scope. This event is raised for any object ownership change in any database on the server. Equivalent to the [Audit Database Object Take Ownership Event Class](../../event-classes/audit-database-object-take-ownership-event-class.md). |
| `DATABASE_OBJECT_PERMISSION_CHANGE_GROUP` | This event is raised when a `GRANT`, `REVOKE`, or `DENY` is issued for database objects, such as assemblies and schemas. This event is raised for any object permission change for any database on the server. Equivalent to the [Audit Database Object GDR Event Class](../../event-classes/audit-database-object-gdr-event-class.md). |
| `DATABASE_OPERATION_GROUP` | This event is raised when operations in a database, such as checkpoint or subscribe query notification, occur. This event is raised on any database operation on any database. Equivalent to the [Audit Database Operation Event Class](../../event-classes/audit-database-operation-event-class.md). |
| `DATABASE_OWNERSHIP_CHANGE_GROUP` | This event is raised when you use the `ALTER AUTHORIZATION` statement to change the owner of a database, and the permissions that are required to do that are checked. This event is raised for any database ownership change on any database on the server. Equivalent to the [Audit Change Database Owner Event Class](../../event-classes/audit-change-database-owner-event-class.md). |
| `DATABASE_PERMISSION_CHANGE_GROUP` | This event is raised whenever a `GRANT`, `REVOKE`, or `DENY` is issued for a statement permission by any principal in [!INCLUDE [ssNoVersion](../../../includes/ssnoversion-md.md)] (This applies to database-only events, such as granting permissions on a database).<br /><br />This event is raised for any database permission change (GDR) for any database in the server. Equivalent to the [Audit Database Scope GDR Event Class](../../event-classes/audit-database-scope-gdr-event-class.md). |
| `DATABASE_PRINCIPAL_CHANGE_GROUP` | This event is raised when principals, such as users, are created, altered, or dropped from a database. Equivalent to the [Audit Database Principal Management Event Class](../../event-classes/audit-database-principal-management-event-class.md). (Also equivalent to the Audit Add DB Principal Event Class, which occurs on the deprecated `sp_grantdbaccess,` `sp_revokedbaccess,` `sp_addPrincipal,` and `sp_dropPrincipal` stored procedures.)<br /><br />This event is raised whenever a database role is added to or removed by using the `sp_addrole,` `sp_droprole` stored procedures. This event is raised whenever any database principals are created, altered, or dropped from any database. Equivalent to the [Audit Add Role Event Class](../../event-classes/audit-add-role-event-class.md). |
| `DATABASE_PRINCIPAL_IMPERSONATION_GROUP` | This event is raised when there's an impersonation operation in the database scope such as `EXECUTE AS <principal>` or `SETPRINCIPAL`. This event is raised for impersonations done in any database. Equivalent to the [Audit Database Principal Impersonation Event Class](../../event-classes/audit-database-principal-impersonation-event-class.md). |
| `DATABASE_ROLE_MEMBER_CHANGE_GROUP` | This event is raised whenever a login is added to or removed from a database role. This event class is raised for the `sp_addrolemember,` `sp_changegroup,` and `sp_droprolemember` stored procedures. This event is raised on any Database role member change in any database. Equivalent to the [Audit Add Member to DB Role Event Class](../../event-classes/audit-add-member-to-db-role-event-class.md). |
| `DBCC_GROUP` | This event is raised whenever a principal issues any DBCC command. Equivalent to the [Audit DBCC Event Class](../../event-classes/audit-dbcc-event-class.md). |
| `EXTGOV_OPERATION_GROUP` | This event is raised on external governance feature enablement, external governance feature disablement, external governance policies synchronization, and external governance policies based permissions enforcement. |
| `FAILED_DATABASE_AUTHENTICATION_GROUP` | Indicates that a principal tried to log on to a contained database and failed. Events in this class are raised by new connections or by connections that are reused from a connection pool. Equivalent to the [Audit Login Failed Event Class](../../event-classes/audit-login-failed-event-class.md). |
| `FAILED_LOGIN_GROUP` | Indicates that a principal tried to log on to [!INCLUDE [ssNoVersion](../../../includes/ssnoversion-md.md)] and failed. Events in this class are raised by new connections or by connections that are reused from a connection pool. Equivalent to the [Audit Login Failed Event Class](../../event-classes/audit-login-failed-event-class.md). This audit doesn't apply to Azure SQL Database. |
| `FULLTEXT_GROUP` | Indicates fulltext event occurred. Equivalent to the [Audit Fulltext Event Class](../../event-classes/audit-fulltext-event-class.md). |
| `LOGIN_CHANGE_PASSWORD_GROUP` | This event is raised whenever a login password is changed by way of `ALTER LOGIN` statement or `sp_password` stored procedure. Equivalent to the [Audit Login Change Password Event Class](../../event-classes/audit-login-change-password-event-class.md). |
| `LOGOUT_GROUP` | Indicates that a principal has logged out of [!INCLUDE [ssNoVersion](../../../includes/ssnoversion-md.md)]. Events in this class are raised by new connections or by connections that are reused from a connection pool. Equivalent to the [Audit Logout Event Class](../../event-classes/audit-logout-event-class.md). |
| `SCHEMA_OBJECT_ACCESS_GROUP` | This event is raised whenever an object permission has been used in the schema. Equivalent to the [Audit Schema Object Access Event Class](../../event-classes/audit-schema-object-access-event-class.md). |
| `SCHEMA_OBJECT_CHANGE_GROUP` | This event is raised when a `CREATE`, `ALTER`, or `DROP` operation is performed on a schema. Equivalent to the [Audit Schema Object Management Event Class](../../event-classes/audit-schema-object-management-event-class.md).<br /><br />This event is raised on schema objects. Equivalent to the [Audit Object Derived Permission Event Class](../../event-classes/audit-object-derived-permission-event-class.md).<br /><br />This event is raised whenever any schema of any database changes. Equivalent to the [Audit Statement Permission Event Class](../../event-classes/audit-statement-permission-event-class.md). |
| `SCHEMA_OBJECT_OWNERSHIP_CHANGE_GROUP` | This event is raised when the permissions to change the owner of schema object (such as a table, procedure, or function) is checked. This occurs when the `ALTER AUTHORIZATION` statement is used to assign an owner to an object. This event is raised for any schema ownership change for any database on the server. Equivalent to the [Audit Schema Object Take Ownership Event Class](../../event-classes/audit-schema-object-take-ownership-event-class.md). |
| `SCHEMA_OBJECT_PERMISSION_CHANGE_GROUP` | This event is raised whenever a grant, deny, revoke is performed against a schema object. Equivalent to the [Audit Schema Object GDR Event Class](../../event-classes/audit-schema-object-gdr-event-class.md). |
| `SERVER_OBJECT_CHANGE_GROUP` | This event is raised for `CREATE`, `ALTER`, or `DROP` operations on server objects. Equivalent to the [Audit Server Object Management Event Class](../../event-classes/audit-server-object-management-event-class.md). |
| `SERVER_OBJECT_OWNERSHIP_CHANGE_GROUP` | This event is raised when the owner is changed for objects in server scope. Equivalent to the [Audit Server Object Take Ownership Event Class](../../event-classes/audit-server-object-take-ownership-event-class.md). |
| `SERVER_OBJECT_PERMISSION_CHANGE_GROUP` | This event is raised whenever a `GRANT`, `REVOKE`, or `DENY` is issued for a server object permission by any principal in [!INCLUDE [ssNoVersion](../../../includes/ssnoversion-md.md)]. Equivalent to the [Audit Server Object GDR Event Class](../../event-classes/audit-server-object-gdr-event-class.md). |
| `SERVER_OPERATION_GROUP` | This event is raised when Security Audit operations such as altering settings, resources, external access, or authorization are used, or a statement requiring the `VIEW SERVER STATE` permission is run. Equivalent to the [Audit Server Operation Event Class](../../event-classes/audit-server-operation-event-class.md). |
| `SERVER_PERMISSION_CHANGE_GROUP` | This event is raised when a `GRANT`, `REVOKE`, or `DENY` is issued for permissions in the server scope. Equivalent to the [Audit Server Scope GDR Event Class](../../event-classes/audit-server-scope-gdr-event-class.md). |
| `SERVER_PRINCIPAL_CHANGE_GROUP` | This event is raised when server principals are created, altered, or dropped. Equivalent to the [Audit Server Principal Management Event Class](../../event-classes/audit-server-principal-management-event-class.md).<br /><br />This event is raised when a principal issues the `sp_defaultdb` or `sp_defaultlanguage` stored procedures or `ALTER LOGIN` statements. Equivalent to the [Audit Addlogin Event Class](../../event-classes/audit-addlogin-event-class.md).<br /><br />This event is raised on the `sp_addlogin` and `sp_droplogin` stored procedures. Also equivalent to the [Audit Login Change Property Event Class](../../event-classes/audit-login-change-property-event-class.md).<br /><br />This event is raised for the `sp_grantlogin` or `sp_revokelogin` stored procedures. Equivalent to the [Audit Login GDR Event Class](../../event-classes/audit-login-gdr-event-class.md). |
| `SERVER_PRINCIPAL_IMPERSONATION_GROUP` | This event is raised when there's an impersonation within server scope, such as `EXECUTE AS <login>`. Equivalent to the [Audit Server Principal Impersonation Event Class](../../event-classes/audit-server-principal-impersonation-event-class.md). |
| `SERVER_ROLE_MEMBER_CHANGE_GROUP` | This event is raised whenever a login is added or removed from a fixed server role. This event is raised for the `sp_addsrvrolemember` and `sp_dropsrvrolemember` stored procedures. Equivalent to the [Audit Add Login to Server Role Event Class](../../event-classes/audit-add-login-to-server-role-event-class.md). |
| `SERVER_STATE_CHANGE_GROUP` | This event is raised when the [!INCLUDE [ssNoVersion](../../../includes/ssnoversion-md.md)] service state is modified. Equivalent to the [Audit Server Starts and Stops Event Class](../../event-classes/audit-server-starts-and-stops-event-class.md). |
| `SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP` | Indicates that a principal successfully logged in to a contained database. |
| `SUCCESSFUL_LOGIN_GROUP` | Indicates that a principal has successfully logged in to [!INCLUDE [ssNoVersion](../../../includes/ssnoversion-md.md)]. Events in this class are raised by new connections or by connections that are reused from a connection pool. Equivalent to the [Audit Login Event Class](../../event-classes/audit-login-event-class.md). |
| `TRACE_CHANGE_GROUP` | This event is raised for all statements that check for the `ALTER TRACE` permission. Equivalent to the [Audit Server Alter Trace Event Class](../../event-classes/audit-server-alter-trace-event-class.md). |
| `TRANSACTION_GROUP` | This event is raised for `BEGIN TRANSACTION`, `ROLLBACK TRANSACTION`, and `COMMIT TRANSACTION` operations, both for explicit calls to those statements and implicit transaction operations. This event is also raised for `UNDO` operations for individual statements caused by the rollback of a transaction. |
| `USER_CHANGE_PASSWORD_GROUP` | This event is raised whenever the password of a contained database user is changed by using the `ALTER USER` statement. |
| `USER_DEFINED_AUDIT_GROUP` | This group monitors events raised by using [sp_audit_write](../../system-stored-procedures/sp-audit-write-transact-sql.md). Typically triggers or stored procedures include calls to `sp_audit_write` to enable auditing of important events. |
| `LEDGER_OPERATION_GROUP` | This event is raised for following actions:<br /><br />`GENERATE LEDGER DIGEST` - When you generate a ledger digest<br /><br />`VERIFY LEDGER` - When you verify a ledger digest.<br /><br />**Applies to:** Azure SQL Database |

### Considerations

Server-level action groups cover actions across a [!INCLUDE [ssNoVersion](../../../includes/ssnoversion-md.md)] instance. For example, any schema object access check in any database is recorded if the appropriate action group is added to a server audit specification. In a database audit specification, only schema object accesses in that database are recorded.

Server-level actions don't allow for detailed filtering on database-level actions. A database-level audit, such as audit of `SELECT` actions on the Customers table for logins in the Employee group is required to implement detailed action filtering. Don't include server-scoped objects, such as the system views, in a user database audit specification.

> [!NOTE]  
> Because of the overhead involved in enabling transaction-level auditing, starting with [!INCLUDE [sssql16-md](../../../includes/sssql16-md.md)] SP 2 CU 3 and [!INCLUDE [ssSQL17](../../../includes/sssql17-md.md)] CU 4, transaction-level auditing is disabled by default unless you have Common Criteria Compliance enabled. If Common Criteria Compliance is disabled, you'll still be able to add an action from `TRANSACTION_GROUP` to an audit specification, but it doesn't actually collect any transaction actions. If you intend to configure any auditing actions from `TRANSACTION_GROUP`, be sure that the transaction-level auditing infrastructure is enabled by enabling Common Criteria Compliance starting with [!INCLUDE [sssql16-md](../../../includes/sssql16-md.md)] SP 2 CU 3 and [!INCLUDE [ssSQL17](../../../includes/sssql17-md.md)] CU 4 and later versions. Transaction-level auditing can also be disabled with Trace Flag 3427 in [!INCLUDE [sssql16-md](../../../includes/sssql16-md.md)] with SP 1 CU 2 and later versions.

## Database-level audit action groups

Database-level audit action groups are actions similar to [!INCLUDE [ssNoVersion](../../../includes/ssnoversion-md.md)] Security Audit Event classes. For more information about event classes, see [SQL Server Event Class Reference](../../event-classes/sql-server-event-class-reference.md).

The following table describes the database-level audit action groups and provides their equivalent SQL Server Event Class where applicable.

| Action group name | Description |
| --- | --- |
| `APPLICATION_ROLE_CHANGE_PASSWORD_GROUP` | This event is raised whenever a password is changed for an application role. Equivalent to the [Audit App Role Change Password Event Class](../../event-classes/audit-app-role-change-password-event-class.md). |
| `AUDIT_CHANGE_GROUP` | This event is raised whenever any audit is created, modified, or deleted. This event is raised whenever any audit specification is created, modified, or deleted. Any change to an audit is audited in that audit. Equivalent to the [Audit Change Audit Event Class](../../event-classes/audit-change-audit-event-class.md). |
| `BACKUP_RESTORE_GROUP` | This event is raised whenever a backup or restore command is issued. Equivalent to the [Audit Backup and Restore Event Class](../../event-classes/audit-backup-and-restore-event-class.md). |
| `BATCH_COMPLETED_GROUP` | This event is raised whenever any batch text, stored procedure, or transaction management operation completes executing. It's raised after the batch completes and audits the entire batch or stored procedure text, as sent from the client, including the result.<br /><br />**Applies to:** [!INCLUDE [sssql19-md](../../../includes/sssql19-md.md)] and later versions. |
| `BATCH_STARTED_GROUP` | This event is raised whenever any batch text, stored procedure, or transaction management operation starts to execute. It's raised before execution and audits the entire batch or stored procedure text, as sent from the client.<br /><br />**Applies to:** [!INCLUDE [sssql19-md](../../../includes/sssql19-md.md)] and later versions. |
| `DATABASE_CHANGE_GROUP` | This event is raised when a database is created, altered, or dropped. Equivalent to the [Audit Database Management Event Class](../../event-classes/audit-database-management-event-class.md). |
| `DATABASE_LOGOUT_GROUP` | This event is raised when a contained database user signs out from a database. |
| `DATABASE_OBJECT_ACCESS_GROUP` | This event is raised whenever database objects such as certificates and asymmetric keys are accessed. Equivalent to the [Audit Database Object Access Event Class](../../event-classes/audit-database-object-access-event-class.md). |
| `DATABASE_OBJECT_CHANGE_GROUP` | This event is raised when a `CREATE`, `ALTER`, or `DROP` statement is executed on database objects, such as schemas. Equivalent to the [Audit Database Object Management Event Class](../../event-classes/audit-database-object-management-event-class.md). |
| `DATABASE_OBJECT_OWNERSHIP_CHANGE_GROUP` | This event is raised when a change of owner for objects within database scope occurs. Equivalent to the [Audit Database Object Take Ownership Event Class](../../event-classes/audit-database-object-take-ownership-event-class.md). |
| `DATABASE_OBJECT_PERMISSION_CHANGE_GROUP` | This event is raised when a `GRANT`, `REVOKE`, or `DENY` is issued for database objects, such as assemblies and schemas. Equivalent to the [Audit Database Object GDR Event Class](../../event-classes/audit-database-object-gdr-event-class.md). |
| `DATABASE_OPERATION_GROUP` | This event is raised when operations in a database, such as checkpoint or subscribe query notification, occur. Equivalent to the [Audit Database Operation Event Class](../../event-classes/audit-database-operation-event-class.md). |
| `DATABASE_OWNERSHIP_CHANGE_GROUP` | This event is raised when you use the `ALTER AUTHORIZATION` statement to change the owner of a database, and the permissions that are required to do that are checked. Equivalent to the [Audit Change Database Owner Event Class](../../event-classes/audit-change-database-owner-event-class.md). |
| `DATABASE_PERMISSION_CHANGE_GROUP` | This event is raised whenever a `GRANT`, `REVOKE`, or `DENY` is issued for a statement permission by any user in [!INCLUDE [ssNoVersion](../../../includes/ssnoversion-md.md)] for database-only events such as granting permissions on a database. Equivalent to the [Audit Database Scope GDR Event Class](../../event-classes/audit-database-scope-gdr-event-class.md). |
| `DATABASE_PRINCIPAL_CHANGE_GROUP` | This event is raised when principals, such as users, are created, altered, or dropped from a database. Equivalent to the [Audit Database Principal Management Event Class](../../event-classes/audit-database-principal-management-event-class.md). Also equivalent to the [Audit Add DB User Event Class](../../event-classes/audit-add-db-user-event-class.md), which occurs on deprecated `sp_grantdbaccess,` `sp_revokedbaccess,` `sp_adduser,` and `sp_dropuser` stored procedures.<br /><br />This event is raised whenever a database role is added to or removed using deprecated `sp_addrole` and `sp_droprole` stored procedures. Equivalent to the [Audit Add Role Event Class](../../event-classes/audit-add-role-event-class.md). |
| `DATABASE_PRINCIPAL_IMPERSONATION_GROUP` | This event is raised when there's an impersonation within database scope such as `EXECUTE AS <user>`. Equivalent to the [Audit Database Principal Impersonation Event Class](../../event-classes/audit-database-principal-impersonation-event-class.md). |
| `DATABASE_ROLE_MEMBER_CHANGE_GROUP` | This event is raised whenever a login is added to or removed from a database role. This event class is used with the `sp_addrolemember,` `sp_changegroup,` and `sp_droprolemember` stored procedures. Equivalent to the [Audit Add Member to DB Role Event Class](../../event-classes/audit-add-member-to-db-role-event-class.md) |
| `DBCC_GROUP` | This event is raised whenever a principal issues any DBCC command. Equivalent to the [Audit DBCC Event Class](../../event-classes/audit-dbcc-event-class.md). |
| `FAILED_DATABASE_AUTHENTICATION_GROUP` | Indicates that a principal tried to log on to a contained database and failed. Events in this class are raised by new connections or by connections that are reused from a connection pool. This event is raised. |
| `SCHEMA_OBJECT_ACCESS_GROUP` | This event is raised whenever an object permission has been used in the schema. Equivalent to the [Audit Schema Object Access Event Class](../../event-classes/audit-schema-object-access-event-class.md). |
| `SCHEMA_OBJECT_CHANGE_GROUP` | This event is raised when a `CREATE`, `ALTER`, or `DROP` operation is performed on a schema. Equivalent to the [Audit Schema Object Management Event Class](../../event-classes/audit-schema-object-management-event-class.md).<br /><br />This event is raised on schema objects. Equivalent to the [Audit Object Derived Permission Event Class](../../event-classes/audit-object-derived-permission-event-class.md). Also equivalent to the [Audit Statement Permission Event Class](../../event-classes/audit-statement-permission-event-class.md). |
| `SCHEMA_OBJECT_OWNERSHIP_CHANGE_GROUP` | This event is raised when the permissions to change the owner of schema object such as a table, procedure, or function is checked. This occurs when the `ALTER AUTHORIZATION` statement is used to assign an owner to an object. Equivalent to the [Audit Schema Object Take Ownership Event Class](../../event-classes/audit-schema-object-take-ownership-event-class.md). |
| `SCHEMA_OBJECT_PERMISSION_CHANGE_GROUP` | This event is raised whenever a grant, deny, or revoke is issued for a schema object. Equivalent to the [Audit Schema Object GDR Event Class](../../event-classes/audit-schema-object-gdr-event-class.md). |
| `SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP` | Indicates that a principal successfully logged in to a contained database. |
| `USER_CHANGE_PASSWORD_GROUP` | This event is raised whenever the password of a contained database user is changed by using the ALTER USER statement. |
| `USER_DEFINED_AUDIT_GROUP` | This group monitors events raised by using [sp_audit_write](../../system-stored-procedures/sp-audit-write-transact-sql.md). |
| `LEDGER_OPERATION_GROUP` | This event is raised for following actions:<br /><br />`ENABLE LEDGER` - When you create a new ledger table<br /><br />`ALTER LEDGER` - When you drop a ledger table<br /><br />`ALTER LEDGER CONFIGURATION`.<br /><br />**Applies to:** Azure SQL Database. |

## Database-level audit actions

Database-level actions support the auditing of specific actions directly on database schema and schema objects, such as tables, views, stored procedures, functions, extended stored procedures, queues, synonyms. Types, XML schema collection, database, and schema aren't audited. The audit of schema objects might be configured on schema and database, which means that events on all schema objects contained by the specified schema or database are audited. The following table describes database-level audit actions.

| Action | Description |
| --- | --- |
| `SELECT` | This event is raised whenever a `SELECT` is issued. |
| `UPDATE` | This event is raised whenever an `UPDATE` is issued. |
| `INSERT` | This event is raised whenever an `INSERT` is issued. |
| `DELETE` | This event is raised whenever a `DELETE` is issued. |
| `EXECUTE` | This event is raised whenever an `EXECUTE` is issued. |
| `RECEIVE` | This event is raised whenever a `RECEIVE` is issued. |
| `REFERENCES` | This event is raised whenever a `REFERENCES` permission is checked. |

### Considerations

Database-level audit actions don't apply to columns.

When the query processor parameterizes the query, the parameter can appear in the audit event log instead of the column values of the query.

## Audit-level audit action groups

You can also audit the actions in the auditing process. This can be in the server scope or the database scope. In the database scope, it only occurs for database audit specifications. The following table describes audit-level audit action groups.

| Action group name | Description |
| --- | --- |
| `AUDIT_CHANGE_GROUP` | This event is raised whenever one of the following commands is issued:<br /><br />`CREATE SERVER AUDIT`<br />`ALTER SERVER AUDIT`<br />`DROP SERVER AUDIT`<br />`CREATE SERVER AUDIT SPECIFICATION`<br />`ALTER SERVER AUDIT SPECIFICATION`<br />`DROP SERVER AUDIT SPECIFICATION`<br />`CREATE DATABASE AUDIT SPECIFICATION`<br />`ALTER DATABASE AUDIT SPECIFICATION`<br />`DROP DATABASE AUDIT SPECIFICATION` |

## Related content

- [Create a Server Audit and Server Audit Specification](create-a-server-audit-and-server-audit-specification.md)
- [Create a server audit and database audit specification](create-a-server-audit-and-database-audit-specification.md)
- [CREATE SERVER AUDIT (Transact-SQL)](../../../t-sql/statements/create-server-audit-transact-sql.md)
- [ALTER SERVER AUDIT (Transact-SQL)](../../../t-sql/statements/alter-server-audit-transact-sql.md)
- [DROP SERVER AUDIT (Transact-SQL)](../../../t-sql/statements/drop-server-audit-transact-sql.md)
- [CREATE SERVER AUDIT SPECIFICATION (Transact-SQL)](../../../t-sql/statements/create-server-audit-specification-transact-sql.md)
- [ALTER SERVER AUDIT SPECIFICATION (Transact-SQL)](../../../t-sql/statements/alter-server-audit-specification-transact-sql.md)
- [DROP SERVER AUDIT SPECIFICATION (Transact-SQL)](../../../t-sql/statements/drop-server-audit-specification-transact-sql.md)
- [CREATE DATABASE AUDIT SPECIFICATION (Transact-SQL)](../../../t-sql/statements/create-database-audit-specification-transact-sql.md)
- [ALTER DATABASE AUDIT SPECIFICATION (Transact-SQL)](../../../t-sql/statements/alter-database-audit-specification-transact-sql.md)
- [DROP DATABASE AUDIT SPECIFICATION (Transact-SQL)](../../../t-sql/statements/drop-database-audit-specification-transact-sql.md)
- [ALTER AUTHORIZATION (Transact-SQL)](../../../t-sql/statements/alter-authorization-transact-sql.md)
- [sys.fn_get_audit_file (Transact-SQL)](../../system-functions/sys-fn-get-audit-file-transact-sql.md)
- [sys.server_audits (Transact-SQL)](../../system-catalog-views/sys-server-audits-transact-sql.md)
- [sys.server_file_audits (Transact-SQL)](../../system-catalog-views/sys-server-file-audits-transact-sql.md)
- [sys.server_audit_specifications (Transact-SQL)](../../system-catalog-views/sys-server-audit-specifications-transact-sql.md)
- [sys.server_audit_specification_details (Transact-SQL)](../../system-catalog-views/sys-server-audit-specification-details-transact-sql.md)
- [sys.database_audit_specifications (Transact-SQL)](../../system-catalog-views/sys-database-audit-specifications-transact-sql.md)
- [sys.database_audit_specification_details (Transact-SQL)](../../system-catalog-views/sys-database-audit-specification-details-transact-sql.md)
- [sys.dm_server_audit_status (Transact-SQL)](../../system-dynamic-management-views/sys-dm-server-audit-status-transact-sql.md)
- [sys.dm_audit_actions (Transact-SQL)](../../system-dynamic-management-views/sys-dm-audit-actions-transact-sql.md)
- [sys.dm_audit_class_type_map (Transact-SQL)](../../system-dynamic-management-views/sys-dm-audit-class-type-map-transact-sql.md)
