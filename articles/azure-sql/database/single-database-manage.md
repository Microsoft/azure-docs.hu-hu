---
title: Kiszolgálók és & adatbázisok kezelése
description: Megtudhatja, hogyan lehet kiszolgálókat és egyedi adatbázisokat létrehozni és Azure SQL Database a Azure Portal, a PowerShell, az Azure CLI, a Transact-SQL (T-SQL) és a Rest-API használatával.
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
ms.openlocfilehash: 4aaabdb3d21c41b973b21e6e52442be132796196
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781594"
---
# <a name="create-and-manage-servers-and-single-databases-in-azure-sql-database"></a>Kiszolgálók és egyedi adatbázisok létrehozása és kezelése a Azure SQL Database

A Azure SQL Database, a PowerShell Azure Portal, az Azure CLI, a REST API és a Transact-SQL használatával kiszolgálókat és egyetlen adatbázist hozhat létre és kezelhet a REST API-ban.

## <a name="the-azure-portal"></a>Azure Portal

Az erőforráscsoportot előre is Azure SQL Database hozhatja létre a kiszolgáló létrehozásakor.

### <a name="create-a-server"></a>A kiszolgáló létrehozása

Kiszolgáló létrehozásához a Azure Portal [hozzon](https://portal.azure.com)létre egy új [kiszolgálói](logical-servers.md) erőforrást a Azure Marketplace. Másik lehetőségként létrehozhatja a kiszolgálót a virtuális gép Azure SQL Database.

  ![kiszolgáló létrehozása](./media/single-database-manage/create-logical-sql-server.png)

### <a name="create-a-blank-or-sample-database"></a>Üres vagy mintaadatbázis létrehozása

Ha egyetlen erőforráscsoportot Azure SQL Database a [Azure Portal,](https://portal.azure.com)válassza ki Azure SQL Database erőforrást a Azure Marketplace. Az erőforráscsoportot és a kiszolgálót létrehozhatja előre, vagy maga az adatbázis létrehozásakor is. Létrehozhat egy üres adatbázist, vagy létrehozhat egy mintaadatbázist az Adventure Works LT alapján.

  ![adatbázis létrehozása-1](./media/single-database-manage/create-database-1.png)

> [!IMPORTANT]
> További információ az adatbázis tarifacsomagja kiválasztásáról: [DTU-alapú](service-tiers-dtu.md) vásárlási modell és [virtuálismag-alapú vásárlási modell.](service-tiers-vcore.md)

## <a name="manage-an-existing-server"></a>Meglévő kiszolgáló kezelése

Meglévő kiszolgáló kezeléséhez navigáljon a kiszolgálóhoz számos módszerrel – például egy adott adatbázislapról, az **SQL-kiszolgálók** lapról vagy a **Minden erőforrás lapról.**

Meglévő adatbázis kezeléséhez lépjen az **SQL-adatbázisok** oldalra, és válassza ki a kezelni kívánt adatbázist. Az alábbi képernyőkép bemutatja, hogyan lehet kiszolgálószintű tűzfalat konfigurálni egy adatbázishoz az adatbázis **Áttekintés** lapján.

   ![kiszolgálói tűzfalszabály](./media/single-database-manage/server-firewall-rule.png)

> [!IMPORTANT]
> Egy adatbázis teljesítménytulajdonságának konfigurálása: [DTU-alapú](service-tiers-dtu.md) vásárlási modell és [virtuálismag-alapú vásárlási modell.](service-tiers-vcore.md)
> [!TIP]
> A rövid Azure Portal lásd: Adatbázis létrehozása a SQL Database [a Azure Portal.](single-database-create-quickstart.md)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager modult továbbra is támogatja a Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modulra lesz kihozva. A parancsmagokért lásd: [AzureRM.Sql.](/powershell/module/AzureRM.Sql/) Az Az modulban és az AzureRm-modulokban található parancsok argumentumai jelentősen megegyeznek.

Az alábbi PowerShell-parancsmagokkal kiszolgálókat, egy- és készletbekészletett adatbázisokat és kiszolgálószintű tűzfalakat Azure PowerShell és kezelhet. Ha telepítenie vagy frissítenie kell a PowerShellt, tekintse meg [a Azure PowerShell modul telepítését.](/powershell/azure/install-az-ps)

> [!TIP]
> A PowerShell-példaszk szkriptekért lásd: Adatbázis létrehozása a SQL Database-ban a [PowerShell](scripts/create-and-configure-database-powershell.md) használatával és kiszolgálószintű tűzfalszabály konfigurálása és Adatbázis figyelése és méretezése a SQL Database [PowerShell](scripts/monitor-and-scale-database-powershell.md)használatával.

| Parancsmag | Leírás |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Létrehoz egy adatbázist |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Lekért egy vagy több adatbázist|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Beállítja egy adatbázis tulajdonságait, vagy áthelyez egy meglévő adatbázist egy rugalmas készletbe|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Eltávolít egy adatbázist|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Létrehoz egy erőforráscsoportot|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Kiszolgálót hoz létre|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Kiszolgálókra vonatkozó adatokat ad vissza|
|[Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver)|Módosítja egy kiszolgáló tulajdonságait|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Eltávolít egy kiszolgálót|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Kiszolgálószintű tűzfalszabályt hoz létre |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Lekért tűzfalszabályok egy kiszolgálóra|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Módosít egy tűzfalszabályt egy kiszolgálón|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Töröl egy tűzfalszabályt a kiszolgálóról.|
| New-AzSqlServerVirtualNetworkRule | Létrehoz egy [*virtuális hálózati szabályt*](vnet-service-endpoint-rule-overview.md)egy olyan alhálózat alapján, amely Virtual Network szolgáltatásvégpont. |

## <a name="the-azure-cli"></a>Azure CLI

A kiszolgálók, adatbázisok és tűzfalak Azure [CLI-val](/cli/azure)való létrehozásához és kezeléséhez használja a következő [Azure CLI-parancsokat.](/cli/azure/sql/db) A [Cloud Shell-lel](../../cloud-shell/overview.md) futtassa a parancssori felületet a böngészőben, vagy [telepítse](/cli/azure/install-azure-cli) macOS, Linux, illetve Windows rendszeren. A rugalmas készletek létrehozásáról és kezeléséről lásd: [Rugalmas készletek.](elastic-pool-overview.md)

> [!TIP]
> Az Azure CLI rövid útmutatóját lásd: Create a single Azure SQL Database using the Azure CLI (Egyetlen virtuális Azure SQL Database [létrehozása az Azure CLI használatával).](az-cli-script-samples-content-guide.md) Az Azure CLI-példaszk szkriptekért lásd: Adatbázis létrehozása a Azure SQL Database-ban a [CLI](scripts/create-and-configure-database-cli.md) használatával és SQL Database-tűzfalszabály konfigurálása, valamint Az adatbázis figyelése és skálázása a cli használatával [a Azure SQL Database.](scripts/monitor-and-scale-database-cli.md)
>

| Parancsmag | Leírás |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az_sql_db_create) |Létrehoz egy adatbázist|
|[az sql db list](/cli/azure/sql/db#az_sql_db_list)|Felsorolja a kiszolgálón található összes adatbázist és adattárházat, illetve a rugalmas készletben található összes adatbázist|
|[az sql db list-editions](/cli/azure/sql/db#az_sql_db_list_editions)|Felsorolja az elérhető szolgáltatási célkitűzéseket és tárolási korlátokat|
|[az sql db list-usages](/cli/azure/sql/db#az_sql_db_list_usages)|Adatbázis-használatot ad vissza|
|[az sql db show](/cli/azure/sql/db#az_sql_db_show)|Lekért egy adatbázist vagy adattárházat|
|[az sql db update](/cli/azure/sql/db#az_sql_db_update)|Adatbázis frissítése|
|[az sql db delete](/cli/azure/sql/db#az_sql_db_delete)|Eltávolít egy adatbázist|
|[az group create](/cli/azure/group#az_group_create)|Létrehoz egy erőforráscsoportot|
|[az sql server create](/cli/azure/sql/server#az_sql_server_create)|Kiszolgálót hoz létre|
|[az sql server list](/cli/azure/sql/server#az_sql_server_list)|Felsorolja a kiszolgálókat|
|[az sql server list-usages](/cli/azure/sql/server#az_sql_server_list-usages)|Kiszolgálóhasználatot ad vissza|
|[az sql server show](/cli/azure/sql/server#az_sql_server_show)|Lekért egy kiszolgálót|
|[az sql server update](/cli/azure/sql/server#az_sql_server_update)|Kiszolgáló frissítése|
|[az sql server delete](/cli/azure/sql/server#az_sql_server_delete)|Töröl egy kiszolgálót|
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Létrehoz egy kiszolgálói tűzfalszabályt|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Felsorolja a kiszolgálón található tűzfalszabályokat|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Egy tűzfalszabály részleteit jeleníti meg|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Tűzfalszabály frissítése|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Töröl egy tűzfalszabályt|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

A kiszolgálók, adatbázisok és tűzfalak Transact-SQL használatával való létrehozásához és kezeléséhez használja a következő T-SQL-parancsokat. Ezeket a parancsokat a Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), Visual Studio [Code](https://code.visualstudio.com/docs)vagy bármely más program használatával használhatja, amely képes csatlakozni a SQL Database-kiszolgálóhoz és Transact-SQL-parancsokat átadni. A rugalmas készletek kezeléséhez lásd: [Rugalmas készletek.](elastic-pool-overview.md)

> [!TIP]
> A microsoftos windowsos SQL Server Management Studio rövid útmutatóért lásd: [Azure SQL Database:](connect-query-ssms.md)Csatlakozás és adatlekérdezés SQL Server Management Studio használatával. A Visual Studio Code macOS, Linux vagy Windows rendszeren való használatával kapcsolatos rövid útmutatóért lásd: Azure SQL Database: Csatlakozás és adatlekérdezés Visual Studio [Code használatával.](connect-query-vscode.md)
> [!IMPORTANT]
> Nem hozhat létre vagy törölhet kiszolgálót a Transact-SQL használatával.

| Parancs | Leírás |
| --- | --- |
|[CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true)|Létrehoz egy új, egyetlen adatbázist. Új adatbázis létrehozásához csatlakoztatnia kell a master adatbázist.|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Módosít egy adatbázist vagy rugalmas készletet. |
|[DROP DATABASE](/sql/t-sql/statements/drop-database-transact-sql)|Töröl egy adatbázist.|
|[sys.database_service_objectives](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|A kiadást (szolgáltatási szint), szolgáltatási célt (tarifacsomag) és a rugalmas készlet nevét adja vissza (ha van ilyen), Azure SQL Database egy dedikált SQL-készlethez a Azure Synapse Analytics. Ha be van jelentkezve a master adatbázisba egy SQL Database-kiszolgálón, az összes adatbázisra vonatkozó információkat ad vissza. A Azure Synapse Analytics a master adatbázishoz kell csatlakoztatnia.|
|[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Egy adatbázis PROCESSZOR-, I/O- és memóriahasználatát adja vissza a Azure SQL Database. 15 másodpercenként egy sor is létezik, még akkor is, ha az adatbázisban nincs tevékenység.|
|[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|A processzorhasználati és tárolási adatokat adja vissza egy adatbázishoz a Azure SQL Database. A rendszer ötperces időközönként gyűjti és összesíti az adatokat.|
|[sys.database_connection_stats](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Az adatbázis-SQL Database események statisztikáit tartalmazza, áttekintést nyújtva az adatbázis-kapcsolatok sikerességről és hibákról. |
|[sys.event_log](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Visszaadja a sikeres Azure SQL Database, csatlakozási hibákat és holtpontokat. Ezek az információk az adatbázis-tevékenységek nyomon követésére vagy hibaelhárítására használhatók SQL Database.|
|[sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Létrehozza vagy frissíti a kiszolgáló kiszolgálószintű tűzfalbeállítását. Ez a tárolt eljárás csak a master adatbázisban érhető el a kiszolgálószintű fő bejelentkezéshez. Kiszolgálószintű tűzfalszabály csak akkor használhatja a Transact-SQL-t, ha egy Azure-szintű engedéllyel rendelkező felhasználó létrehozta az első kiszolgálószintű tűzfalszabályt|
|[sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Információkat ad vissza az adatbázishoz társított kiszolgálószintű tűzfalbeállításokról a Azure SQL Database.|
|[sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Eltávolítja a kiszolgálószintű tűzfalbeállításokat a kiszolgálóról. Ez a tárolt eljárás csak a master adatbázisban érhető el a kiszolgálószintű fő bejelentkezéshez.|
|[sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Létrehozza vagy frissíti az adatbázis adatbázisszintű tűzfalszabályát a Azure SQL Database. Az adatbázis tűzfalszabályai konfigurálhatóak a master adatbázishoz és a felhasználói adatbázisokhoz a SQL Database. Az adatbázis-tűzfalszabályok akkor hasznosak, ha tartalmazott adatbázis-felhasználókat használ. |
|[sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Információkat ad vissza az adatbázishoz társított adatbázisszintű tűzfalbeállításokról a Azure SQL Database. |
|[sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Eltávolítja az adatbázisszintű tűzfalbeállítást az adatbázisból. |

## <a name="rest-api"></a>REST API

A kiszolgálók, adatbázisok és tűzfalak létrehozásához és kezeléséhez használja ezeket a REST API kérelmeket.

| Parancs | Leírás |
| --- | --- |
|[Kiszolgálók – Létrehozás vagy frissítés](/rest/api/sql/servers/createorupdate)|Új kiszolgálót hoz létre vagy frissíti.|
|[Kiszolgálók – Törlés](/rest/api/sql/servers/delete)|Töröl egy SQL-kiszolgálót.|
|[Kiszolgálók – Lekért](/rest/api/sql/servers/get)|Lekért egy kiszolgálót.|
|[Kiszolgálók – Lista](/rest/api/sql/servers/list)|Egy előfizetésben található kiszolgálók listáját adja vissza.|
|[Kiszolgálók – Lista erőforráscsoport szerint](/rest/api/sql/servers/listbyresourcegroup)|Egy erőforráscsoportban található kiszolgálók listáját adja vissza.|
|[Kiszolgálók – Frissítés](/rest/api/sql/servers/update)|Frissíti a meglévő kiszolgálót.|
|[Adatbázisok – Létrehozás vagy frissítés](/rest/api/sql/databases/createorupdate)|Létrehoz egy új adatbázist, vagy frissíti a meglévő adatbázist.|
|[Adatbázisok – Törlés](/rest/api/sql/databases/delete)|Töröl egy adatbázist.|
|[Adatbázisok – Lekért](/rest/api/sql/databases/get)|Lekért egy adatbázist.|
|[Adatbázisok – Lista rugalmas készlet alapján](/rest/api/sql/databases/listbyelasticpool)|Egy rugalmas készletben található adatbázisok listáját adja vissza.|
|[Adatbázisok – Listás kiszolgáló szerint](/rest/api/sql/databases/listbyserver)|Egy kiszolgálón található adatbázisok listáját adja vissza.|
|[Adatbázisok – Frissítés](/rest/api/sql/databases/update)|Frissíti a meglévő adatbázist.|
|[Tűzfalszabályok – Létrehozás vagy frissítés](/rest/api/sql/firewallrules/createorupdate)|Létrehoz vagy frissíti a tűzfalszabályt.|
|[Tűzfalszabályok – Törlés](/rest/api/sql/firewallrules/delete)|Töröl egy tűzfalszabályt.|
|[Tűzfalszabályok – Lekért](/rest/api/sql/firewallrules/get)|Lekért egy tűzfalszabályt.|
|[Tűzfalszabályok – List by server](/rest/api/sql/firewallrules/listbyserver)|A tűzfalszabályok listáját adja vissza.|

## <a name="next-steps"></a>Következő lépések

- A virtuális adatbázis Azure SQL Server ba való áttelepítését lásd: [Áttelepítés](migrate-to-database-from-sql-server.md)Azure SQL Database.
- A támogatott funkciókkal kapcsolatos tudnivalókat lásd: [Funkciók](features-comparison.md).
