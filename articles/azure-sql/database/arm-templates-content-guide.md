---
title: Azure Resource Manager sablonok – Azure SQL Database & SQL felügyelt példánya
description: Azure SQL Database és az Azure SQL felügyelt példányának létrehozásához és konfigurálásához használjon Azure Resource Manager sablonokat.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: overview-samples sqldbrb=2
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: 5202145af6f60f34bdce57aa94f4fec92a8b227f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92677270"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database--sql-managed-instance"></a>Azure SQL Database & SQL felügyelt példányának Azure Resource Manager sablonjai
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Az Azure Resource Manager-sablonok lehetővé teszik az infrastruktúra kódként történő meghatározását és a megoldások üzembe helyezését az Azure-felhőben az Azure SQL Database és a felügyelt Azure SQL-példány esetében.

## <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

Az alábbi táblázat a Azure SQL Database Azure Resource Manager sablonjaira mutató hivatkozásokat tartalmaz.

|Hivatkozás |Description|
|---|---|
| [SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Ez a Azure Resource Manager sablon egyetlen adatbázist hoz létre a Azure SQL Databaseban, és konfigurálja a kiszolgálói szintű IP-tűzfalszabályok beállításait. |
| [Kiszolgáló](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Ez a Azure Resource Manager-sablon létrehoz egy kiszolgálót a Azure SQL Databasehoz. |
| [Rugalmas készlet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Ez a sablon lehetővé teszi egy rugalmas készlet üzembe helyezését és az adatbázisok hozzárendelését. |
| [Feladatátvételi csoportok](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Ez a sablon két kiszolgálót, egyetlen adatbázist és egy feladatátvételi csoportot hoz létre Azure SQL Databaseban.|
| [Fenyegetések észlelése](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Ez a sablon lehetővé teszi, hogy egy-kiszolgálót és egy olyan adatbázist helyezzen üzembe, amelyen engedélyezve van a fenyegetések észlelése, és e-mail-címmel kell rendelkeznie az egyes adatbázisokra vonatkozó riasztásokhoz. A veszélyforrások észlelése az SQL Advanced Threat Protection (ATP) ajánlat része, és olyan biztonsági réteget biztosít, amely a kiszolgálók és adatbázisok lehetséges fenyegetésekre reagál.|
| [Naplózás az Azure Blob Storage-ba](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Ez a sablon lehetővé teszi, hogy olyan kiszolgálót helyezzen üzembe, amelyen engedélyezve van a naplózás, hogy a naplófájlokat blob-tárolóba írja. A Azure SQL Database naplózása nyomon követi az adatbázis eseményeit, és az Azure Storage-fiókba, OMS-munkaterületbe vagy Event Hubsba helyezhető naplófájlba írja azokat.|
| [Naplózás az Azure Event hub-ba](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Ez a sablon lehetővé teszi, hogy olyan kiszolgálót helyezzen üzembe, amelyen engedélyezve van a naplózás, hogy a naplókat egy meglévő Event hubhoz írja. A naplózási események Event Hubsre való küldéséhez állítsa be a naplózási beállításokat a következőre `Enabled` `State` , majd állítsa be: `IsAzureMonitorTargetEnabled` `true` . Továbbá konfigurálja a diagnosztikai beállításokat a `SQLSecurityAuditEvents` naplózási kategóriával az `master` adatbázisban (a kiszolgálói szintű naplózáshoz). A naplózás nyomon követi az adatbázis eseményeit, és az Azure Storage-fiókba, a OMS-munkaterületbe vagy a Event Hubsba helyezhető naplófájlba írja azokat.|
| [Azure-webalkalmazás és SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Ez a példa létrehoz egy ingyenes Azure-webalkalmazást és egy Azure SQL Database-adatbázist az "alapszintű" szolgáltatási szinten.|
| [Azure-webalkalmazás és Redis Cache a következővel SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Ez a sablon létrehoz egy webalkalmazást, egy Redis Cache és egy adatbázist ugyanabban az erőforráscsoporthoz, és két kapcsolati karakterláncot hoz létre a webalkalmazásban az adatbázishoz és Redis Cachehoz.|
| [Adatok importálása a blob Storage-ból az ADF V2 használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Ez a Azure Resource Manager-sablon létrehoz egy Azure Data Factory v2-példányt, amely az Azure Blob Storage-ból másolja a SQL Databaseba.|
| [HDInsight-fürt adatbázissal](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Ez a sablon lehetővé teszi, hogy HDInsight-fürtöt, logikai SQL Servert, adatbázist és két táblát hozzon létre. Ezt a sablont a [Sqoop használata a Hadoop használatával című HDInsight cikkben](../../hdinsight/hadoop/hdinsight-use-sqoop.md)használja. |
| [Az Azure logikai alkalmazás, amely egy ütemezett SQL tárolt eljárást futtat](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Ez a sablon lehetővé teszi, hogy olyan logikai alkalmazást hozzon létre, amely egy ütemezett SQL tárolt eljárást fog futtatni. Az eljáráshoz tartozó argumentumok a sablon törzs szakaszába helyezhetők.|

## <a name="azure-sql-managed-instance"></a>[Felügyelt Azure SQL-példány](#tab/managed-instance)

A következő táblázat hivatkozásokat tartalmaz az Azure SQL felügyelt példányának Azure Resource Manager sablonjaira.

|Hivatkozás|Description|
|---|---|
| [SQL felügyelt példány egy új VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Ez a Azure Resource Manager sablon létrehoz egy új, konfigurált Azure-beli virtuális hálózatot és felügyelt példányt a virtuális hálózaton. |
| [Az SQL felügyelt példányának hálózati környezete](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Ez a központi telepítés létrehoz egy konfigurált Azure-beli virtuális hálózatot két alhálózattal, amelyek közül az egyik a felügyelt példányokhoz lesz hozzárendelve, és egy másik, ahol más erőforrásokat (például virtuális gépeket, App Service környezeteket stb.) helyezhet el. Ez a sablon egy megfelelően konfigurált hálózati környezetet hoz létre, ahol felügyelt példányokat helyezhet üzembe. |
| [SQL felügyelt példány P2S-kapcsolatban](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Ez a központi telepítés létrehoz egy Azure-beli virtuális hálózatot két alhálózattal `ManagedInstance` és `GatewaySubnet` . Az SQL felügyelt példánya a ManagedInstance alhálózatban lesz telepítve. A rendszer létrehoz egy virtuális hálózati átjárót az `GatewaySubnet` alhálózaton, és a pont – hely típusú VPN-kapcsolathoz van konfigurálva. |
| [SQL felügyelt példány virtuális géppel](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Ez a központi telepítés létrehoz egy Azure-beli virtuális hálózatot két alhálózattal `ManagedInstance` és `Management` . Az SQL felügyelt példánya üzembe lesz helyezve az `ManagedInstance` alhálózatban. Az alhálózaton a SQL Server Management Studio (SSMS) legújabb verzióját tartalmazó virtuális gép lesz telepítve `Management` . |

---