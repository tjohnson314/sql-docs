---
title: "Back up and restore Reporting Services SharePoint service applications"
description: Learn how to back up and restore a SQL Server Reporting Services service applications using SharePoint Central Administration or PowerShell.
author: maggiesMSFT
ms.author: maggies
ms.date: 09/25/2024
ms.service: reporting-services
ms.subservice: report-server-sharepoint
ms.topic: conceptual
ms.custom:
  - updatefrequency5
monikerRange: ">=sql-server-2016 <=sql-server-2016"
---
# Back up and restore Reporting Services SharePoint service applications

[!INCLUDE[ssrs-appliesto](../../includes/ssrs-appliesto.md)] [!INCLUDE[ssrs-appliesto-2016](../../includes/ssrs-appliesto-2016.md)] [!INCLUDE[ssrs-appliesto-sharepoint-2013-2016i](../../includes/ssrs-appliesto-sharepoint-2013-2016.md)] [!INCLUDE[ssrs-appliesto-not-pbirsi](../../includes/ssrs-appliesto-not-pbirs.md)]

[!INCLUDE [ssrs-previous-versions](../../includes/ssrs-previous-versions.md)]

This article describes how to back up and restore a [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] services application using SharePoint Central Administration or PowerShell.

> [!NOTE]
> Reporting Services integration with SharePoint is no longer available after SQL Server 2016.

## Before you begin

### Limitations and restrictions

> [!NOTE]
>  [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] service applications can partially be backed up and restored using the SharePoint backup and restore functionality. **Additional steps are required** and the steps are documented in this topic. Currently the backup process **does not** back up encryption keys and credentials for unattended execution accounts (UEA) or Windows authentication to the [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] database.

### Recommendations
  
-   Back up the encryption keys before starting the SharePoint backup. If you don't back up the encryption keys, then you can't access your encrypted data, following the restore of the service application. You'll need to delete your encrypted data.  
  
-   Verify if your [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] service application is using UEA or Windows authentication for database access. If it uses either, verify what the proper credentials are, so you can correctly configure the service application after the restore process.  
  
-   Review that the SharePoint backup log is created in the same folder as the backup file. The file is typically named **spbackup.log**  
  
## Back up the service application

 Complete the following steps in order:  
  
1.  Back up the encryption keys.  
  
2.  Back up the service application.  
  
3.  Verify if your service application uses an UEA or Windows authentication for database access. If it does, make a note of the credentials so you can use them to configure the service application after it's restored.  

### Back up the encryption keys using SharePoint Central Administration

For information on backing up the [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] encryption keys, see the "Encryption Keys" section of [Manage a Reporting Services SharePoint Service Application](../../reporting-services/report-server-sharepoint/manage-a-reporting-services-sharepoint-service-application.md).  

### Back up the service application using SharePoint Central Administration

To back up the Service Application, complete the following steps:  
  
1.  In SharePoint Central Administration select **Perform a backup** in the **Backup and Restore** group.  
  
2.  Under the **Shared Services** node, expand **Shared Service Applications** and select your service application. It has a type of **SQL Server Reporting Services Service Application**.  
  
3.  Select **Next**.  
  
4.  Type the path for the **Backup location:** and select **Start Backup**.  
  
5.  Repeat this process but instead of selecting the service application, expand the **Shared Services Proxies** node, and choose service application proxy. It has a type of **SQL Server Reporting Services Service Application Proxy**.  
  
 For more information, see the following articles in the SharePoint documentation:  
  
 [Back up a service application (SharePoint Foundation 2010) in the SharePoint documentation](/previous-versions/office/sharepoint-foundation-2010/ee748601(v=office.14))  
  
 [Back up a service application (SharePoint Server 2010)](/SharePoint/administration/back-up-a-service-application)  
  
### Verify execution account and database authentication

 **Execution Account:** To verify if your service application is using an execution account:  
  
1.  In SharePoint Central Administration, select **Manage Service Applications** in the **Application Management** group.  
  
2.  Select the name of your service application and then choose **Manage** in the SharePoint Ribbon.  
  
3.  Select **Execution Account**.  
  
4.  If an execution account is configured, you need to know the credentials when it's time to restore the service application backup. Don't proceed with the backup and restore procedure until you know the correct credentials.  
  
 **Database Authentication:** To verify if your service application is using Windows Authentication for the database authentication:  
  
1.  In SharePoint Central Administration, select **Manage Service Applications** in the **Application Management** group.  
  
2.  Select the name of your service application and then choose **Properties** in the SharePoint Ribbon.  
  
3.  Review the **Reporting Services (SSRS) Service Database** section.  
  
4.  If Windows Authentication is configured, you need to know the credentials so you can configure the service application after you restore it. Don't proceed with the backup and restore procedure until you know the correct credentials.  
  
## Restore the service application

 Complete the following steps in order:  
  
1.  Restore the [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] service application.  
  
2.  Restore the encryption keys.  
  
3.  If your service application was using an execution account or Windows authentication for database access, configure the credentials.  
  
### Restore the service application using SharePoint Central Administration
  
1.  In SharePoint Central Administration select **Restore from a backup** in the **Backup and Restore** group.  
  
2.  Type the path to your backup file in **Backup Directory Location** box and select **Refresh**.  
  
3.  Select your service application backup from the **Top Component** list and then choose **Next**.  
  
4.  Select your [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] application and then choose **Next**.  
  
5.  In the **Login Names and Passwords** section, type the password for the login name. The login name box should be populated with the login the service application was using prior to the backup.  
  
6.  Select **Start Restore**.  
  
7.  Repeat this process but instead of restoring the service application, expand the **Shared Services** node and then expand the **Shared Service Applications** node.  
  
 For more information, see the following articles in the SharePoint documentation:  
  
 [Restore a service application (SharePoint Foundation 2010)](/previous-versions/office/sharepoint-foundation-2010/ee748615(v=office.14))  
  
 [Restore a service application (SharePoint Server 2010)](/SharePoint/administration/restore-a-service-application)  

### Restore the encryption keys using SharePoint Central Administration

 For information on restoring the [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] encryption keys, see the "Encryption Keys" section of [Manage a Reporting Services SharePoint Service Application](../../reporting-services/report-server-sharepoint/manage-a-reporting-services-sharepoint-service-application.md).  

### Configure the execution account and database authentication

 **Execution Account:** If your service application was using an execution account, complete the following steps to configure it:  
  
1.  In SharePoint Central Administration, select **Manage Service Applications** in the **Application Management** group.  
  
2.  Select the name of your service application and then choose **Manage** in the SharePoint Ribbon.  
  
3.  Select **Execution Account**.  
  
4.  Type the account, password, and select the **Specify and Execution Account** box.  
  
5.  Select **OK**.  
  
 **Database Authentication:** If your service application was using Windows Authentication for the database authentication, complete the following steps:  
  
1.  In SharePoint Central Administration select **Manage Service Applications** in the **Application Management** group.  
  
2.  Select the name of your service application and then choose **Properties** in the SharePoint Ribbon.  
  
3.  Review the **Reporting Services (SSRS) Service Database** section.  
  
4.  Select **Windows Authentication**.  
  
5.  Type the account and password. Select **Use as Windows Credentials** if appropriate.  
  
6.  Select **Ok**.

More questions? [Try asking the Reporting Services forum](/answers/search.html?c=&f=&includeChildren=&q=ssrs+OR+reporting+services&redirect=search%2fsearch&sort=relevance&type=question+OR+idea+OR+kbentry+OR+answer+OR+topic+OR+user)
