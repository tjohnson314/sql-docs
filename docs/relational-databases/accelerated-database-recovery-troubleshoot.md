---
title: "Troubleshoot Accelerated Database Recovery"
description: "Troubleshoot accelerated database recovery"
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: derekw, dfurman, randolphwest
ms.date: 12/04/2024
ms.service: sql
ms.subservice: backup-restore
ms.topic: conceptual
ms.custom:
  - ignite-2024
helpviewer_keywords:
  - "accelerated database recovery [SQL Server], recovery-only"
  - "database recovery [SQL Server]"
monikerRange: ">=sql-server-ver15 || >=sql-server-linux-ver15 || =azuresqldb-mi-current || =azuresqldb-current || =fabric"
---

# Troubleshoot accelerated database recovery

[!INCLUDE [SQL Server 2019 Azure SQL Database Azure SQL Managed Instance FabricSQLDB](../includes/applies-to-version/sqlserver2019-asdb-asdbmi-fabricsqldb.md)]

This article helps diagnose issues with [accelerated database recovery (ADR)](accelerated-database-recovery-concepts.md) in [!INCLUDE[sssql19-md](../includes/sssql19-md.md)] and later, [!INCLUDE[ssazuremi-md](../includes/ssazuremi-md.md)], [!INCLUDE [ssazure-sqldb](../includes/ssazure-sqldb.md)], and [!INCLUDE[fabric-sqldb](../includes/fabric-sqldb.md)].

## Examine the size of the PVS

Use the [sys.dm_tran_persistent_version_store_stats](system-dynamic-management-views/sys-dm-tran-persistent-version-store-stats.md) DMV to identify if the persistent version store (PVS) size is larger than expected. 

The following sample query shows the information about the current PVS size, the cleanup processes, and other details:

```sql
SELECT  DB_NAME(pvss.database_id) AS database_name,
        pvss.persistent_version_store_size_kb / 1024. / 1024 AS persistent_version_store_size_gb,
        100 * pvss.persistent_version_store_size_kb / df.total_db_size_kb AS pvs_pct_of_database_size,
        df.total_db_size_kb/ 1024. / 1024 AS total_db_size_gb,
        pvss.online_index_version_store_size_kb / 1024. / 1024 AS online_index_version_store_size_gb,
        pvss.current_aborted_transaction_count,
        pvss.aborted_version_cleaner_start_time,
        pvss.aborted_version_cleaner_end_time,
        dt.database_transaction_begin_time AS oldest_transaction_begin_time,
        asdt.session_id AS active_transaction_session_id,
        asdt.elapsed_time_seconds AS active_transaction_elapsed_time_seconds,
        pvss.pvs_off_row_page_skipped_low_water_mark,
        pvss.pvs_off_row_page_skipped_min_useful_xts,
        pvss.pvs_off_row_page_skipped_oldest_aborted_xdesid
FROM sys.dm_tran_persistent_version_store_stats AS pvss
CROSS APPLY (
            SELECT SUM(size * 8.) AS total_db_size_kb
            FROM sys.database_files
            WHERE state = 0
                  AND
                  type = 0
            ) AS df
LEFT JOIN sys.dm_tran_database_transactions AS dt
ON pvss.oldest_active_transaction_id = dt.transaction_id
   AND
   pvss.database_id = dt.database_id
LEFT JOIN sys.dm_tran_active_snapshot_database_transactions AS asdt
ON pvss.min_transaction_timestamp = asdt.transaction_sequence_num
   OR
   pvss.online_index_min_transaction_timestamp = asdt.transaction_sequence_num
WHERE pvss.database_id = DB_ID();
```

Check the `pvs_pct_of_database_size` column to see the size of the PVS relative to the total database size. Note any difference from the typical PVS size compared to baselines seen during other periods of application activity. PVS is considered large if it's significantly larger than the baseline or if it's close to 50% of the database size. Use the following troubleshooting steps to find the reason for large PVS size.

If the PVS size is larger than expected, check for: 
- [Long-running active transactions](#check-for-long-running-active-transactions)
- [Long-running active snapshot scans](#check-for-long-running-active-transactions)
- [Long-running queries on secondary replicas](#check-for-long-running-queries-on-secondary-replicas)
- [Aborted transactions](#check-for-large-number-of-aborted-transactions) 

## Check for long-running active transactions

Long-running active transactions can prevent PVS cleanup in databases that have ADR enabled. Check the start time of the oldest active transaction using the `oldest_transaction_begin_time` column. For more information about long-running transactions, use the following sample query. You can set thresholds for transaction duration and the amount of generated transaction log:

```sql
DECLARE @LongTxThreshold int = 1800;  /* The number of seconds to use as a duration threshold for long-running transactions */
DECLARE @LongTransactionLogBytes bigint = 1073741824; /* The number of bytes to use as a log amount threshold for long-running transactions */

SELECT  dbtr.database_id,
        transess.session_id,
        transess.transaction_id,
        atr.name,
        sess.login_time,
        dbtr.database_transaction_log_bytes_used,
        CASE WHEN GETDATE() >= DATEADD(second, @longTxThreshold, tr.transaction_begin_time) THEN 'DurationThresholdExceeded' 
                WHEN dbtr.database_transaction_log_bytes_used >= @LongTransactionLogBytes THEN 'LogThresholdExceeded' 
                ELSE 'Unknown'
        END AS Reason
FROM sys.dm_tran_active_transactions AS tr
INNER JOIN sys.dm_tran_session_transactions AS transess
ON tr.transaction_id = transess.transaction_id
INNER JOIN sys.dm_exec_sessions AS sess
ON transess.session_id = sess.session_id
INNER JOIN sys.dm_tran_database_transactions AS dbtr
ON tr.transaction_id = dbtr.transaction_id
INNER JOIN sys.dm_tran_active_transactions AS atr
ON atr.transaction_id = transess.transaction_id
WHERE GETDATE() >= DATEADD(second, @LongTxThreshold, tr.transaction_begin_time)
        OR
        dbtr.database_transaction_log_bytes_used >= @LongTransactionLogBytes;
```

With the sessions identified, consider killing the session, if allowed. Review the application to determine the nature of the problematic active transactions to avoid the problem in the future.

For more information on troubleshooting long-running queries, see: 
- [Troubleshooting slow running queries in SQL Server](/troubleshoot/sql/performance/troubleshoot-slow-running-queries)
- [Detectable types of query performance bottlenecks in Azure SQL Database](/azure/azure-sql/database/identify-query-performance-issues)
- [Detectable types of query performance bottlenecks in SQL Server and Azure SQL Managed Instance](/azure/azure-sql/managed-instance/identify-query-performance-issues)

<a id="pvs-active-snapshot-scans"></a>

## Check for long-running active snapshot scans

Long-running active snapshot scans can prevent PVS cleanup in databases that have ADR enabled. Statements using `READ COMMITTED` snapshot isolation (RCSI) or `SNAPSHOT` [isolation levels](../t-sql/statements/set-transaction-isolation-level-transact-sql.md) receive instance-level timestamps. A snapshot scan uses the timestamp to decide version row visibility for the RCSI or `SNAPSHOT` transaction. Every statement using RCSI has its own timestamp, whereas `SNAPSHOT` isolation has a transaction-level timestamp.

These instance-level transaction timestamps are used even in single-database transactions, because any transaction might prompt a cross-database transaction. Snapshot scans can, therefore, prevent PVS cleanup in any database on the same database engine instance. Similarly, when ADR isn't enabled, snapshot scans can prevent cleanup of the version store in `tempdb`. As a result, PVS might grow in size when long-running transactions that use `SNAPSHOT` or RCSI are present.

In the [troubleshooting query](#examine-the-size-of-the-pvs) at the beginning of this article, the `pvs_off_row_page_skipped_min_useful_xts` column shows the number of pages skipped for reclaim due to a long snapshot scan. If this column shows a larger value than normal, it means that a long snapshot scan is preventing PVS cleanup.

Use the following sample query to find the session with the long-running `SNAPSHOT` or RCSI transaction:

```sql
SELECT snap.transaction_id,
        snap.transaction_sequence_num,
        session.session_id,
        session.login_time,
        GETUTCDATE() AS [now],
        session.host_name,
        session.program_name,
        session.login_name,
        session.last_request_start_time
FROM sys.dm_tran_active_snapshot_database_transactions AS snap
INNER JOIN sys.dm_exec_sessions AS session
ON snap.session_id = session.session_id
ORDER BY snap.transaction_sequence_num ASC;
```

To prevent PVS cleanup delays:

- Consider killing the long active transaction session that is delaying PVS cleanup, if possible.
- Tune long-running queries to reduce query duration.
- Review the application to determine the nature of the problematic active snapshot scan. Consider a different isolation level, such as `READ COMMITTED`, instead of `SNAPSHOT` or RCSI for long-running queries that are delaying PVS cleanup. This problem occurs more frequently with the `SNAPSHOT` isolation level.
- In Azure SQL Database elastic pools, consider moving databases that have long-running transactions using `SNAPSHOT` isolation or RCSI out of the elastic pool.

## Check for long-running queries on secondary replicas

If the database has secondary replicas, check if the secondary low watermark is advancing. 

Run following DMVs on the primary replica to identify long-running queries on the secondary replica that might be preventing PVS cleanup:  

-  [sys.dm_hadr_database_replica_states](system-dynamic-management-views/sys-dm-hadr-database-replica-states-transact-sql.md) for [!INCLUDE[ssnoversion-md.md](../includes/ssnoversion-md.md)] and [!INCLUDE[ssazuremi-md](../includes/ssazuremi-md.md)] 
- [sys.dm_database_replica_states](system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database.md) (for [!INCLUDE [ssazure-sqldb](../includes/ssazure-sqldb.md)] and [!INCLUDE[fabric-sqldb](../includes/fabric-sqldb.md)]) in the `low_water_mark_for_ghosts` column. 

In the [sys.dm_tran_persistent_version_store_stats](system-dynamic-management-views/sys-dm-tran-persistent-version-store-stats.md) DMV, the `pvs_off_row_page_skipped_low_water_mark` columns can also give an indication of a cleanup delay because of a long-running query on a secondary replica.

Connect to a secondary replica, find the session that is running the long query and consider killing the session, if allowed. The long-running query on the secondary replica can hold up PVS cleanup as well as preventing [ghost cleanup](ghost-record-cleanup-process-guide.md).

## Check for large number of aborted transactions

If none of the previous scenarios apply to your workloads, then it's likely the cleanup is held due to a large number of aborted transactions. Check `aborted_version_cleaner_last_start_time` and `aborted_version_cleaner_last_end_time` columns to see if the last aborted transaction cleanup has completed. The `oldest_aborted_transaction_id` should be moving higher after the aborted transaction cleanup completes. If the `oldest_aborted_transaction_id` is much lower than `oldest_active_transaction_id`, and `current_abort_transaction_count` has a greater value, there's likely an old aborted transaction preventing PVS cleanup. 

To address a large number of aborted transactions, consider the following:

- If possible, stop the workload to let the version cleaner make progress.
- Optimize the workload to reduce object-level locks.
- Review the application to identify the high transaction abort rate issue. The aborts might come from a high rate of deadlocks, duplicate keys, constraint violations, or query timeouts.
- If using [!INCLUDE [ssnoversion-md.md](../includes/ssnoversion-md.md)], disable ADR as an emergency-only step to control PVS size. See [Disable ADR](accelerated-database-recovery-management.md#disable-adr).
- If the aborted transaction cleanup hasn't recently completed successfully, check the error log for messages reporting `VersionCleaner` issues.
- If PVS size isn't reduced as expected even after a cleanup has completed, check the `pvs_off_row_page_skipped_oldest_aborted_xdesid` column. Large values indicate space is still being used by row versions from aborted transactions.

## Start PVS cleanup process manually

If you have a workload with a high volume of DML statements (`INSERT`, `UPDATE`, `DELETE`, `MERGE`), such as high-volume OLTP, it might require a period of rest/recovery for the PVS cleanup process to reclaim space.

To activate the PVS cleanup process manually between workloads or during maintenance windows, use the system stored procedure [sys.sp_persistent_version_cleanup](system-stored-procedures/sys-sp-persistent-version-cleanup-transact-sql.md).

For example:

```sql
EXEC sys.sp_persistent_version_cleanup [WideWorldImporters];
```

## Capture cleanup failures

Beginning with [!INCLUDE [sql-server-2022](../includes/sssql22-md.md)], PVS cleanup behavior is recorded in the error log. Typically this results in a new log event recorded every 10 minutes. Cleanup statistics are also reported by the `tx_mtvc2_sweep_stats` [extended event](extended-events/extended-events.md).

## Related content

- [Accelerated database recovery](accelerated-database-recovery-concepts.md)
- [Manage accelerated database recovery](accelerated-database-recovery-management.md)
- [sys.sp_persistent_version_cleanup](system-stored-procedures/sys-sp-persistent-version-cleanup-transact-sql.md)
- [sys.dm_tran_persistent_version_store_stats](system-dynamic-management-views/sys-dm-tran-persistent-version-store-stats.md)
- [sys.dm_tran_aborted_transactions](system-dynamic-management-views/sys-dm-tran-aborted-transactions.md)
