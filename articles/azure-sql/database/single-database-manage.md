---
title: '& létrehozása kiszolgálók és önálló adatbázisok kezeléséhez'
description: Ismerkedjen meg a kiszolgálók és az önálló adatbázisok létrehozásával és kezelésével Azure SQL Database a Azure Portal, a PowerShell, az Azure CLI, a Transact-SQL (T-SQL) és a REST-API használatával.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: e50cce74f7291a6673e5d43f3485a1c63c81d827
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93319287"
---
# <a name="create-and-manage-servers-and-single-databases-in-azure-sql-database"></a>Kiszolgálók és önálló adatbázisok létrehozása és kezelése Azure SQL Database

Azure SQL Database-kiszolgálókat és önálló adatbázisokat a Azure Portal, a PowerShell, az Azure CLI, a REST API és a Transact-SQL használatával hozhatja létre és kezelheti.

## <a name="the-azure-portal"></a>Azure Portal

Létrehozhat egy erőforráscsoportot Azure SQL Database az idő előtt, vagy saját maga is létrehozhatja a kiszolgálót.

### <a name="create-a-server"></a>A kiszolgáló létrehozása

Ha a [Azure Portal](https://portal.azure.com)használatával szeretne kiszolgálót létrehozni, hozzon létre egy új [kiszolgálói](logical-servers.md) erőforrást az Azure Marketplace-ről. Azt is megteheti, hogy a kiszolgálót Azure SQL Database telepítésekor hozza létre.

  ![kiszolgáló létrehozása](./media/single-database-manage/create-logical-sql-server.png)

### <a name="create-a-blank-or-sample-database"></a>Üres vagy minta adatbázis létrehozása

Ha egyetlen Azure SQL Database szeretne létrehozni a [Azure Portal](https://portal.azure.com)használatával, válassza ki a Azure SQL Database erőforrást az Azure Marketplace-en. Létrehozhatja az erőforráscsoportot és a kiszolgálót az idő előtt, vagy maga az önálló adatbázist is létrehozhatja. Létrehozhat egy üres adatbázist, vagy létrehozhat egy minta-adatbázist az Adventure Works LT alapján.

  ![adatbázis létrehozása-1](./media/single-database-manage/create-database-1.png)

> [!IMPORTANT]
> Az adatbázis díjszabási rétegének kiválasztásával kapcsolatos információkért lásd: [DTU-alapú vásárlási modell](service-tiers-dtu.md) és [virtuális mag-alapú vásárlási modell](service-tiers-vcore.md).

## <a name="manage-an-existing-server"></a>Meglévő kiszolgáló kezelése

Egy meglévő kiszolgáló kezeléséhez navigáljon a kiszolgálóhoz számos módszer használatával – például egy adott adatbázis-oldalról, az **SQL-kiszolgálók** lapról vagy a **minden erőforrás** lapról.

Meglévő adatbázis kezeléséhez navigáljon az SQL- **adatbázisok** lapra, és válassza ki a kezelni kívánt adatbázist. Az alábbi képernyőfelvételen megtudhatja, hogyan kezdheti el az adatbázishoz tartozó kiszolgálói szintű tűzfal beállítását az adatbázis **Áttekintés** lapján.

   ![kiszolgálói tűzfalszabály](./media/single-database-manage/server-firewall-rule.png)

> [!IMPORTANT]
> Az adatbázis teljesítmény-tulajdonságainak konfigurálásához tekintse meg a [DTU-alapú vásárlási modellt](service-tiers-dtu.md) és a [virtuális mag-alapú vásárlási modellt](service-tiers-vcore.md).
> [!TIP]
> Azure Portal gyors útmutatóért lásd: [adatbázis létrehozása a SQL Databaseban a Azure Portal](single-database-create-quickstart.md).

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

A következő PowerShell-parancsmagokkal hozhat létre és kezelhet kiszolgálókat, az önálló és a készletezett adatbázisokat, valamint a Azure PowerShell kiszolgáló szintű tűzfalakat. Ha telepítenie vagy frissítenie kell a PowerShellt, olvassa el a [Azure PowerShell modul telepítése](/powershell/azure/install-az-ps)című témakört.

> [!TIP]
> A PowerShell-parancsfájlok esetében lásd: [a PowerShell használata a SQL Database adatbázis létrehozásához és a kiszolgáló szintű tűzfalszabály konfigurálásához](scripts/create-and-configure-database-powershell.md) és a [SQL Database-adatbázis figyeléséhez és méretezéséhez a PowerShell használatával](scripts/monitor-and-scale-database-powershell.md).

| Parancsmag | Leírás |
| --- | --- |
|[Új – AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Létrehoz egy adatbázist |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Egy vagy több adatbázis beolvasása|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Beállítja egy adatbázis tulajdonságait, vagy áthelyezi egy meglévő adatbázist egy rugalmas készletbe.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Adatbázis eltávolítása|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Létrehoz egy erőforráscsoportot|
|[Új – AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Létrehoz egy kiszolgálót|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|A kiszolgálókkal kapcsolatos információkat ad vissza.|
|[Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver)|Egy kiszolgáló tulajdonságainak módosítása|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Kiszolgáló eltávolítása|
|[Új – AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Kiszolgálói szintű tűzfalszabály létrehozása |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Egy kiszolgáló tűzfalszabályok beolvasása|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Tűzfalszabály módosítása egy kiszolgálón|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Tűzfalszabály törlése egy kiszolgálóról.|
| New-AzSqlServerVirtualNetworkRule | Létrehoz egy [*virtuális hálózati szabályt*](vnet-service-endpoint-rule-overview.md), amely egy Virtual Network szolgáltatási végponton alapuló alhálózat. |

## <a name="the-azure-cli"></a>Azure CLI

A kiszolgálók, adatbázisok és tűzfalak [Azure CLI](/cli/azure)-vel történő létrehozásához és kezeléséhez használja az alábbi [Azure CLI](/cli/azure/sql/db) -parancsokat. A [Cloud Shell-lel](../../cloud-shell/overview.md) futtassa a parancssori felületet a böngészőben, vagy [telepítse](/cli/azure/install-azure-cli) macOS, Linux, illetve Windows rendszeren. Rugalmas készletek létrehozásához és kezeléséhez lásd: [rugalmas készletek](elastic-pool-overview.md).

> [!TIP]
> Azure CLI-gyors útmutató: [egyetlen Azure SQL Database létrehozása az Azure CLI használatával](az-cli-script-samples-content-guide.md). Az Azure CLI példa parancsfájljai esetében lásd: [a CLI használata adatbázis létrehozásához Azure SQL Database és SQL Database tűzfalszabály konfigurálása](scripts/create-and-configure-database-cli.md) , valamint a [CLI használata az adatbázisok figyeléséhez és méretezéséhez Azure SQL Databaseban](scripts/monitor-and-scale-database-cli.md).
>

| Parancsmag | Leírás |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Létrehoz egy adatbázist|
|[az SQL db List](/cli/azure/sql/db#az-sql-db-list)|Egy kiszolgáló összes adatbázisát és adattárházát, vagy egy rugalmas készletben lévő összes adatbázist listáz|
|[az SQL db List-Editions](/cli/azure/sql/db#az-sql-db-list-editions)|Az elérhető szolgáltatási célkitűzések és a tárolási korlátok listája|
|[az SQL db List-használat](/cli/azure/sql/db#az-sql-db-list-usages)|Adatbázis-használatot ad vissza|
|[az SQL db show](/cli/azure/sql/db#az-sql-db-show)|Adatbázis vagy adatraktár beolvasása|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|Adatbázis frissítése|
|[az SQL db delete](/cli/azure/sql/db#az-sql-db-delete)|Adatbázis eltávolítása|
|[az group create](/cli/azure/group#az-group-create)|Létrehoz egy erőforráscsoportot|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|Létrehoz egy kiszolgálót|
|[az SQL Server List](/cli/azure/sql/server#az-sql-server-list)|Kiszolgálók felsorolása|
|[az SQL Server List-használati adatok](/cli/azure/sql/server#az-sql-server-list-usages)|Kiszolgáló-használatot ad vissza|
|[az SQL Server show](/cli/azure/sql/server#az-sql-server-show)|Kiszolgáló beolvasása|
|[az SQL Server Update](/cli/azure/sql/server#az-sql-server-update)|Kiszolgáló frissítése|
|[az SQL Server delete](/cli/azure/sql/server#az-sql-server-delete)|Kiszolgáló törlése|
|[az SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Létrehoz egy kiszolgálói tűzfalszabály-szabályt|
|[az SQL Server Firewall-Rule List](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|A kiszolgálón található tűzfalszabályok felsorolása|
|[az SQL Server Firewall-Rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Egy tűzfalszabály részleteit jeleníti meg|
|[az SQL Server Firewall-Rule Update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Tűzfalszabály frissítése|
|[az SQL Server Firewall-Rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Tűzfalszabály törlése|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

A kiszolgálók, adatbázisok és tűzfalak a Transact-SQL használatával történő létrehozásához és kezeléséhez használja a következő T-SQL-parancsokat. Ezeket a parancsokat a Azure Portal, a [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), a [Visual Studio Code](https://code.visualstudio.com/docs)vagy bármely más olyan program használatával lehet kiadni, amely csatlakozhat egy SQL Database-kiszolgálóhoz, és hogyan ADHAT át Transact-SQL-parancsokat. Rugalmas készletek kezeléséhez lásd: [rugalmas készletek](elastic-pool-overview.md).

> [!TIP]
> A Microsoft Windows SQL Server Management Studiot használó rövid útmutatókat a [Azure SQL Database: az SQL Server Management Studio használata a kapcsolódáshoz és az adatlekérdezéshez](connect-query-ssms.md)című témakörben talál. A macOS, Linux vagy Windows rendszeren a Visual Studio Code használatával történő gyors útmutatóért lásd [Azure SQL Database: a Visual Studio Code használata a kapcsolódáshoz és az adatlekérdezéshez](connect-query-vscode.md).
> [!IMPORTANT]
> A Transact-SQL használatával nem lehet kiszolgálót létrehozni vagy törölni.

| Parancs | Leírás |
| --- | --- |
|[CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true)|Létrehoz egy új önálló adatbázist. Új adatbázis létrehozásához csatlakoznia kell a Master adatbázishoz.|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Módosít egy adatbázist vagy egy rugalmas készletet. |
|[DROP DATABASE](/sql/t-sql/statements/drop-database-transact-sql)|Töröl egy adatbázist.|
|[sys.database_service_objectives](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Visszaadja a kiadást (szolgáltatási szintet), a szolgáltatási célt (árképzési szintet) és a rugalmas készlet nevét (ha van ilyen) Azure SQL Database vagy egy dedikált SQL-készletet az Azure szinapszis Analyticsben. Ha bejelentkezett a főadatbázisba egy SQL Database-kiszolgálón, az összes adatbázisra vonatkozó információt adja vissza. Az Azure szinapszis Analytics esetében csatlakoznia kell a Master adatbázishoz.|
|[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| A CPU-t, az i/o-t és a memória-felhasználást adja vissza egy Azure SQL Database adatbázisában. 15 másodpercenként egy sor létezik, még akkor is, ha nincs tevékenység az adatbázisban.|
|[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Egy adatbázis CPU-használati és tárolási adatait adja vissza Azure SQL Database. Az adatok gyűjtése és összesítése öt percen belül történik.|
|[sys.database_connection_stats](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|A SQL Database kapcsolódási események statisztikáit tartalmazza, áttekintést nyújt az adatbázis-kapcsolat sikerességéről és a hibákról. |
|[sys.event_log](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|A sikeres Azure SQL Database kapcsolatok, kapcsolódási hibák és holtpontok visszaadása. Ezekkel az információkkal nyomon követheti és elháríthatja az adatbázis-tevékenységeket SQL Database használatával.|
|[sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Létrehozza vagy frissíti a kiszolgáló szintű tűzfalbeállítások beállításait a kiszolgálón. Ez a tárolt eljárás csak a főadatbázisban érhető el a kiszolgálói szintű rendszerbiztonsági tag-bejelentkezéshez. A kiszolgálói szintű tűzfalszabályok csak akkor hozhatók létre a Transact-SQL használatával, ha az első kiszolgálói szintű tűzfalszabály az Azure-szintű engedélyekkel rendelkező felhasználó által lett létrehozva.|
|[sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|A Azure SQL Database-adatbázishoz társított kiszolgálói szintű tűzfalbeállítások adatait adja vissza.|
|[sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Eltávolítja a kiszolgáló szintű tűzfal beállításait a kiszolgálóról. Ez a tárolt eljárás csak a főadatbázisban érhető el a kiszolgálói szintű rendszerbiztonsági tag-bejelentkezéshez.|
|[sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Az adatbázis-szintű tűzfalszabályok létrehozása vagy frissítése Azure SQL Databaseban. Az adatbázis-tűzfalszabályok konfigurálhatók a Master adatbázishoz, valamint SQL Database felhasználói adatbázisaihoz. Az adatbázis-tűzfalszabályok a tárolt adatbázis-felhasználók használata esetén hasznosak. |
|[sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|A Azure SQL Database-adatbázishoz társított adatbázis-szintű tűzfalbeállítások adatait adja vissza. |
|[sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Az adatbázis-szintű tűzfal-beállítás eltávolítása egy adatbázisból. |

## <a name="rest-api"></a>REST API

A kiszolgálók, adatbázisok és tűzfalak létrehozásához és kezeléséhez használja ezeket a REST API kérelmeket.

| Parancs | Leírás |
| --- | --- |
|[Kiszolgálók – létrehozás vagy frissítés](/rest/api/sql/servers/createorupdate)|Új kiszolgáló létrehozása vagy frissítése.|
|[Kiszolgálók – törlés](/rest/api/sql/servers/delete)|Töröl egy SQL-kiszolgálót.|
|[Kiszolgálók – Get](/rest/api/sql/servers/get)|Kiszolgáló beolvasása.|
|[Kiszolgálók – lista](/rest/api/sql/servers/list)|Egy előfizetésben található kiszolgálók listáját adja vissza.|
|[Kiszolgálók – erőforráscsoport szerint listázva](/rest/api/sql/servers/listbyresourcegroup)|Egy erőforráscsoport kiszolgálóinak listáját adja vissza.|
|[Kiszolgálók – frissítés](/rest/api/sql/servers/update)|Frissít egy meglévő kiszolgálót.|
|[Adatbázisok – létrehozás vagy frissítés](/rest/api/sql/databases/createorupdate)|Létrehoz egy új adatbázist, vagy frissít egy meglévő adatbázist.|
|[Adatbázisok – törlés](/rest/api/sql/databases/delete)|Töröl egy adatbázist.|
|[Adatbázisok – Get](/rest/api/sql/databases/get)|Adatbázis beolvasása.|
|[Adatbázisok – lista rugalmas készlet alapján](/rest/api/sql/databases/listbyelasticpool)|Egy rugalmas készletben lévő adatbázisok listáját adja vissza.|
|[Adatbázisok – kiszolgálók listázása](/rest/api/sql/databases/listbyserver)|Egy kiszolgáló adatbázisainak listáját adja vissza.|
|[Adatbázisok – frissítés](/rest/api/sql/databases/update)|Frissít egy meglévő adatbázist.|
|[Tűzfalszabályok – létrehozás vagy frissítés](/rest/api/sql/firewallrules/createorupdate)|Tűzfalszabály létrehozása vagy frissítése.|
|[Tűzfalszabályok – törlés](/rest/api/sql/firewallrules/delete)|Tűzfalszabály törlése.|
|[Tűzfalszabályok – Get](/rest/api/sql/firewallrules/get)|Tűzfalszabály beolvasása.|
|[Tűzfalszabályok – kiszolgálók listázása](/rest/api/sql/firewallrules/listbyserver)|A tűzfalszabályok listáját adja vissza.|

## <a name="next-steps"></a>Következő lépések

- A SQL Server-adatbázisok Azure-ba történő áttelepítésével kapcsolatos információkért lásd: [áttelepítés Azure SQL Databasere](migrate-to-database-from-sql-server.md).
- A támogatott funkciókkal kapcsolatos tudnivalókat lásd: [Funkciók](features-comparison.md).
 
