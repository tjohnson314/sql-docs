---
title: SQL Server replication on Linux
description: Learn how SQL Server 2017 (14.x) (CU18) and later support SQL Server Replication for instances of SQL Server on Linux.
author: rwestMSFT
ms.author: randolphwest
ms.reviewer: vanto
ms.date: 11/16/2023
ms.service: sql
ms.subservice: linux
ms.topic: overview
ms.custom:
  - linux-related-content
monikerRange: ">=sql-server-2017 || >=sql-server-linux-2017"
---
# SQL Server replication on Linux

[!INCLUDE [SQL Server - Linux](../includes/applies-to-version/sql-linux.md)]

[!INCLUDE [SQL Server 2017](../includes/sssql17-md.md)] ([CU18](https://support.microsoft.com/help/4527377)) and later support SQL Server Replication for instances of SQL Server on Linux.

Configure replication on Linux with SQL Server Management Studio (SSMS) [replication stored procedures](../relational-databases/system-stored-procedures/replication-stored-procedures-transact-sql.md).

An instance of SQL Server can participate in any replication role:

- Publisher
- Distributor
- Subscriber

A replication schema can mix and match operating system platforms. For example, a replication schema might include an instance of SQL Server on Linux for publisher and distributor, and the subscribers include instances of SQL Server on Windows as well as Linux.

SQL Server instances on Linux can participate in any type of replication.

- Transactional
- Snapshot

For detailed information about replication, see [SQL Server Replication](../relational-databases/replication/sql-server-replication.md).

## Supported features

The following replication features are supported:

- Snapshot replication
- Transactional replication
- Replication with non-default ports <!--Add link to explanation-->
- Replication with Active Directory authentication
- Replication configurations across Windows and Linux
- Immediate updates for transactional replication

## Limitations

The following features aren't supported:

- Merge replication
- Peer-to-Peer replication
- Oracle publishing

## Related content

- [Configure Replication with T-SQL](sql-server-linux-replication-tutorial-tsql.md)
- [Configure SQL Server Replication on Linux](sql-server-linux-replication-configure.md)
