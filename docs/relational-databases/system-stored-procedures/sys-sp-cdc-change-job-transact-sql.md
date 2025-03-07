---
title: "sys.sp_cdc_change_job (Transact-SQL)"
description: "Modifies the configuration of a change data capture cleanup or capture job in the current database."
author: markingmyname
ms.author: maghan
ms.reviewer: randolphwest
ms.date: 08/21/2024
ms.service: sql
ms.subservice: system-objects
ms.topic: "reference"
f1_keywords:
  - "sys.sp_cdc_change_job_TSQL"
  - "sys.sp_cdc_change_job"
  - "sp_cdc_change_job_TSQL"
  - "sp_cdc_change_job"
helpviewer_keywords:
  - "sp_cdc_change_job"
dev_langs:
  - "TSQL"
---
# sys.sp_cdc_change_job (Transact-SQL)

[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

Modifies the configuration of a change data capture cleanup or capture job in the current database. To view the current configuration of a job, query the [dbo.cdc_jobs](../system-tables/dbo-cdc-jobs-transact-sql.md) table, or use [sys.sp_cdc_help_jobs](sys-sp-cdc-help-jobs-transact-sql.md).

:::image type="icon" source="../../includes/media/topic-link-icon.svg" border="false"::: [Transact-SQL syntax conventions](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## Syntax

```syntaxsql
sys.sp_cdc_change_job [ [ @job_type = ] N'job_type' ]
    [ , [ @maxtrans = ] max_trans ]
    [ , [ @maxscans = ] max_scans ]
    [ , [ @continuous = ] continuous ]
    [ , [ @pollinginterval = ] polling_interval ]
    [ , [ @retention ] = retention ]
    [ @threshold = ] 'delete threshold'
[ ; ]
```

## Arguments

#### [ @job_type = ] N'*job_type*'

Type of job to modify. *@job_type* is **nvarchar(20)** with a default of `capture`. Valid inputs are `capture` and `cleanup`.

#### [ @maxtrans ] = *max_trans*

Maximum number of transactions to process in each scan cycle. *@maxtrans* is **int**, with a default of `NULL`, which indicates no change for this parameter. If specified, the value must be a positive integer.

*@max_trans* is valid only for capture jobs.

#### [ @maxscans ] = *max_scans*

Maximum number of scan cycles to execute in order to extract all rows from the log. *@maxscans* is **int**, with a default of `NULL`, which indicates no change for this parameter.

*@max_scan* is valid only for capture jobs.

#### [ @continuous ] = *continuous*

Indicates whether the capture job is to run continuously (`1`), or run only once (`0`). *@continuous* is **bit**, with a default of `NULL`, which indicates no change for this parameter.

- When *@continuous* is `1`, the [sys.sp_cdc_scan](sys-sp-cdc-scan-transact-sql.md) job scans the log and processes up to (`@maxtrans * @maxscans`) transactions. It then waits the number of seconds specified in *@pollinginterval* before beginning the next log scan.

- When *@continuous* is `0`, the `sp_cdc_scan` job executes up to *@maxscans* scans of the log, processing up to *@maxtrans* transactions during each scan, and then exits.

- If *@continuous* is changed from `1` to `0`, *@pollinginterval* is automatically set to `0`. A value specified for *@pollinginterval* other than `0` is ignored.

- If *@continuous* is omitted or explicitly set to `NULL` and *@pollinginterval* is explicitly set to a value greater than `0`, *@continuous* is automatically set to `1`.

*@continuous* is valid only for capture jobs.

#### [ @pollinginterval ] = *polling_interval*

Number of seconds between log scan cycles. *@pollinginterval* is **bigint**, with a default of `NULL`, which indicates no change for this parameter.

*@pollinginterval* is valid only for capture jobs when *@continuous* is set to `1`.

#### [ @retention ] = *retention*

Number of minutes that change rows are to be retained in change tables. *@retention* is **bigint**, with a default of `NULL`, which indicates no change for this parameter. The maximum value is `52494800` (100 years). If specified, the value must be a positive integer.

*@retention* is valid only for cleanup jobs.

#### [ @threshold = ] '*delete threshold*'

Maximum number of delete entries that can be deleted using a single statement on cleanup. *@threshold* is **bigint**, with a default of `NULL`, which indicates no change for this parameter. *@threshold* is valid only for cleanup jobs.

## Return code values

`0` (success) or `1` (failure).

## Result set

None.

## Remarks

If a parameter is omitted, the associated value in the [dbo.cdc_jobs](../system-tables/dbo-cdc-jobs-transact-sql.md) table isn't updated. A parameter set explicitly to `NULL` is treated as though the parameter is omitted.

Specifying a parameter that is invalid for the job type causes the statement to fail.

Changes to a job don't take effect until the job is stopped by using [sys.sp_cdc_stop_job](sys-sp-cdc-stop-job-transact-sql.md) and restarted by using [sys.sp_cdc_start_job](sys-sp-cdc-start-job-transact-sql.md).

## Permissions

Requires membership in the **db_owner** fixed database role.

## Examples

### A. Change a capture job

The following example updates the *@job_type*, *@maxscans*, and *@maxtrans* parameters of a capture job in the [!INCLUDE [sssampledbobject-md](../../includes/sssampledbobject-md.md)] database. The other valid parameters for a capture job, *@continuous* and *@pollinginterval*, are omitted; their values aren't modified.

```sql
USE AdventureWorks2022;
GO

EXECUTE sys.sp_cdc_change_job
    @job_type = N'capture',
    @maxscans = 1000,
    @maxtrans = 15;
GO
```

### B. Change a cleanup job

The following example updates a cleanup job in the [!INCLUDE [sssampledbobject-md](../../includes/sssampledbobject-md.md)] database. All valid parameters for this job type, except *@threshold*, are specified. The value of *@threshold* isn't modified.

```sql
USE AdventureWorks2022;
GO

EXECUTE sys.sp_cdc_change_job
    @job_type = N'cleanup',
    @retention = 2880;
GO
```

## Related content

- [dbo.cdc_jobs (Transact-SQL)](../system-tables/dbo-cdc-jobs-transact-sql.md)
- [sys.sp_cdc_enable_table (Transact-SQL)](sys-sp-cdc-enable-table-transact-sql.md)
- [sys.sp_cdc_add_job (Transact-SQL)](sys-sp-cdc-add-job-transact-sql.md)
