---
title: "Working with Database Objects"
description: "Creating, Altering, and Removing Database Objects"
author: "markingmyname"
ms.author: "maghan"
ms.date: "03/14/2017"
ms.service: sql
ms.topic: "reference"
ms.custom:
  - ignite-2024
helpviewer_keywords:
  - "database objects [SMO]"
  - "objects [SMO]"
monikerRange: "=azuresqldb-current || =azure-sqldw-latest || >=sql-server-2016 || >=sql-server-linux-2017 || =azuresqldb-mi-current || =fabric"
---
# Creating, Altering, and Removing Database Objects
[!INCLUDE [SQL Server Azure SQL Database Azure SQL Managed Instance Synapse Analytics FabricSQLDB](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-fabricsqldb.md)]

  The stages of SMO object creation are as follows:  
  
1.  Create an instance of the object.  
  
2.  Set the object properties.  
  
3.  Create instances of the child objects.  
  
4.  Set the child object properties.  
  
5.  Create the object.  

 When instances of SMO objects are created in an SMO application, they do not exist on the instance of [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] until the **Create** method is issued. However, it is not necessary to issue a **Create** method for every individual object. If an object has a set of child objects, only the parent object is required to run the **Create** method. For example, the definition of a table requires that it contains at least one column to exist. Also, a column cannot exist in isolation without a table. There is a codependent relationship between the table and its columns.  
  
 The <xref:Microsoft.SqlServer.Management.Dmf.Policy.Alter%2A> method lets you make changes to an object. Several changes to an object, such as adding child objects to one of the object's collections or changing a property value, are batched together and run as one. The **Alter** method reduces network traffic and improves overall performance.  
  
 The **Drop** statement is used to remove an object and all its codependent child objects that were required to create the object initially.  
  
## See Also  
 [SMO Object Model](../../../relational-databases/server-management-objects-smo/smo-object-model.md)  
  
  
