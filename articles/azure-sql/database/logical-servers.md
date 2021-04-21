---
title: Mi az a kiszolgáló a Azure SQL Database és Azure Synapse Analytics?
titleSuffix: ''
description: Ismerje meg a logikai SQL-kiszolgálókat, Azure SQL Database és Azure Synapse Analytics, valamint azok kezelését.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: c76d3ae78bf2b9b4a71d9520f7f1c6c2c322483b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784514"
---
# <a name="what-is-a-logical-sql-server-in-azure-sql-database-and-azure-synapse"></a>Mi az a logikai SQL-kiszolgáló a Azure SQL Database és Azure Synapse?
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

A Azure SQL Database és Azure Synapse Analytics kiszolgálók olyan logikai szerkezetek, amelyek központi felügyeleti pontként szolgálnak az adatbázisok gyűjteményében. Kiszolgálószinten felügyelheti a [](logins-create-manage.md)bejelentkezéseket, a tűzfalszabályokat, [](firewall-configure.md)a naplózási [szabályokat,](../../azure-sql/database/auditing-overview.md)a fenyegetésészlelési szabályzatokat [és](threat-detection-configure.md)az [automatikus feladatátvételi csoportokat.](auto-failover-group-overview.md) Egy kiszolgáló az erőforráscsoporttól eltérő régióban is lehet. A kiszolgálónak léteznie kell, mielőtt adatbázist Azure SQL Database vagy egy adatraktár-adatbázist a Azure Synapse Analytics. Az egyetlen kiszolgáló által kezelt összes adatbázis ugyanabban a régióban jön létre, mint a kiszolgáló.

Ez a kiszolgáló különbözik a SQL Server-példánytól, amely ismerős lehet a helyszíni világban. Pontosabban nem garantálható az adatbázisok vagy az adatraktár-adatbázis helye az azokat kezelő kiszolgálóval kapcsolatban. Továbbá a Azure SQL Database és Azure Synapse sem teszi elérhetővé a példányszintű hozzáférést vagy funkciókat. Ezzel szemben a felügyelt példányok példány-adatbázisai fizikailag közös helyen találhatók, ugyanúgy, mint a SQL Server vagy a virtuális gépek világában.

Amikor létrehoz egy kiszolgálót, meg kell adnia egy kiszolgálói bejelentkezési fiókot és jelszót, amely rendszergazdai jogosultságokkal rendelkezik a kiszolgálón található master adatbázishoz és a kiszolgálón létrehozott összes adatbázishoz. Ez a kezdeti fiók egy SQL bejelentkezési fiók. Azure SQL Database és Azure Synapse Analytics sql-hitelesítést és Azure Active Directory hitelesítést. A bejelentkezésekkel és a hitelesítéssel kapcsolatos információkért lásd: Adatbázisok és bejelentkezések kezelése a [Azure SQL Database.](logins-create-manage.md) A Windows-hitelesítés nem támogatott.

Egy kiszolgáló a SQL Database és Azure Synapse:

- Egy Azure-előfizetésen belül jön létre, de az erőforrásaival együtt áthelyezhető másik előfizetésre.
- Az adatbázisok, rugalmas készletek és adattárházak szülőerőforrása.
- Névteret biztosít az adatbázisokhoz, a rugalmas készletekhez és az adatraktár-adatbázishoz
- Erős élettartam-szemantika mellett egy logikai tároló – töröl egy kiszolgálót, és törli az adatbázisokat, a rugalmas készleteket és az SQK-készleteket
- Részt vesz az [Azure szerepköralapú hozzáférés-vezérlésében (Azure RBAC)](../../role-based-access-control/overview.md) – a kiszolgálón található adatbázisok, rugalmas készletek és adattárház-adatbázisok öröklik a kiszolgáló hozzáférési jogosultságát
- Az adatbázisok, rugalmas készletek és adattárház-adatbázisok identitásának magas rendű eleme az Azure erőforrás-kezelési céljaira (lásd az adatbázisok és készletek URL-sémáit)
- Közösen helyezi el egy adott régió erőforrásait.
- Kapcsolódási végpontot biztosít az adatbázis-hozzáféréshez (`<serverName>`.database.windows.net)
- Egy master adatbázishoz való kapcsolódással hozzáférést biztosít a tárolt erőforrásokra vonatkozó metaadatokhoz a DMV-ken keresztül
- Hatókört biztosít az adatbázisokra vonatkozó felügyeleti szabályzatok számára – bejelentkezések, tűzfal, naplózás, fenyegetésészlelés és hasonlók
- A szülő-előfizetésen belüli kvóta korlátozza (alapértelmezés szerint előfizetésenként hat kiszolgáló – lásd az előfizetés [korlátait itt)](../../azure-resource-manager/management/azure-subscription-service-limits.md)
- Az adatbáziskvóta és a DTU- vagy virtuálismag-kvóta hatókörét biztosítja a benne található erőforrásokhoz (például 45 000 DTU)
- A tartalmazott erőforrásokon engedélyezett képességek verziószámozási hatóköre
- Kiszolgálószintű rendszerbiztonsági tagként bejelentkezve a kiszolgáló minden adatbázisa felügyelhető.
- A helyszíni környezetben található SQL Server-példányokhoz hasonló bejelentkezéseket tartalmazhat, amelyek hozzáférést kapnak a kiszolgálón található egy vagy több adatbázishoz, és korlátozott rendszergazdai jogosultságot kaphatnak. További információk: [Bejelentkezések](logins-create-manage.md).
- A kiszolgálón létrehozott adatbázisok alapértelmezett rendezése a következő: , ahol az `SQL_LATIN1_GENERAL_CP1_CI_AS` `LATIN1_GENERAL` angol (Egyesült Államok), az `CP1` 1252-es kódlap, nem megkülönbözteti a kis- és nagybetűket, és az ékezeteket `CI` `AS` megkülönbözteti.

## <a name="manage-servers-databases-and-firewalls-using-the-azure-portal"></a>Kiszolgálók, adatbázisok és tűzfalak kezelése a Azure Portal

Az erőforráscsoportot előre vagy maga a kiszolgáló létrehozásakor is létrehozhatja egy kiszolgálóhoz. Egy új SQL Server-űrlaphoz több módszer is létezik, akár egy új SQL-kiszolgáló létrehozásával, akár egy új adatbázis létrehozásával.

### <a name="create-a-blank-server"></a>Üres kiszolgáló létrehozása

Ha adatbázis, rugalmas készlet vagy [adatraktár-adatbázis](https://portal.azure.com)nélkül szeretne kiszolgálót létrehozni a Azure Portal, lépjen egy üres SQL Server (logikai SQL Server) űrlapra.

### <a name="create-a-blank-or-sample-database-in-azure-sql-database"></a>Üres vagy mintaadatbázis létrehozása a Azure SQL Database

Ha adatbázist szeretne létrehozni a SQL Database a Azure Portal [használatával,](https://portal.azure.com)lépjen egy üres SQL Database űrlapra, és adja meg a kért adatokat. Az erőforráscsoportot és a kiszolgálót előre vagy az adatbázis létrehozása során is létrehozhatja. Létrehozhat egy üres adatbázist, vagy létrehozhat egy mintaadatbázist az Adventure Works LT alapján.

  ![adatbázis létrehozása-1](./media/logical-servers/create-database-1.png)

> [!IMPORTANT]
> További információ az adatbázis tarifacsomagja kiválasztásáról: [DTU-alapú](service-tiers-dtu.md) vásárlási modell és [virtuálismag-alapú vásárlási modell.](service-tiers-vcore.md)

Felügyelt példány létrehozásához lásd: [Felügyelt példány létrehozása](../managed-instance/instance-create-quickstart.md)

### <a name="manage-an-existing-server"></a>Meglévő kiszolgáló kezelése

Meglévő kiszolgáló kezeléséhez navigáljon a kiszolgálóhoz számos módszerrel – például egy adott adatbázislapról, az **SQL-kiszolgálók** lapról vagy a **Minden erőforrás lapról.**

Meglévő adatbázis kezeléséhez lépjen az **SQL-adatbázisok** lapra, és kattintson a kezelni kívánt adatbázisra. Az alábbi képernyőkép bemutatja, hogyan lehet kiszolgálószintű tűzfalat konfigurálni egy adatbázishoz az adatbázis **Áttekintés** lapján.

   ![kiszolgálói tűzfalszabály](./media/single-database-create-quickstart/server-firewall-rule.png)

> [!IMPORTANT]
> Egy adatbázis teljesítménytulajdonságának konfigurálása: [DTU-alapú](service-tiers-dtu.md) vásárlási modell és [virtuálismag-alapú vásárlási modell.](service-tiers-vcore.md)
> [!TIP]
> A rövid Azure Portal lásd: Adatbázis létrehozása a SQL Database [a Azure Portal.](single-database-create-quickstart.md)

## <a name="manage-servers-databases-and-firewalls-using-powershell"></a>Kiszolgálók, adatbázisok és tűzfalak kezelése a PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatott, de a jövőbeli fejlesztések az Az.Sql modulra valók. A parancsmagokért lásd: [AzureRM.Sql.](/powershell/module/AzureRM.Sql/) Az Az modulban és az AzureRm-modulokban található parancsok argumentumai jelentősen megegyeznek.

Kiszolgálók, adatbázisok és tűzfalak létrehozásához és kezeléséhez Azure PowerShell PowerShell-parancsmagokat használhatja. Ha telepítenie vagy frissítenie kell a PowerShellt, tekintse meg [a Azure PowerShell modul telepítését.](/powershell/azure/install-az-ps) Rugalmas készletek létrehozásához és kezeléséhez lásd: [Rugalmas készletek.](elastic-pool-overview.md)

| Parancsmag | Leírás |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Létrehoz egy adatbázist |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Lekért egy vagy több adatbázist|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Beállítja egy adatbázis tulajdonságait, vagy áthelyez egy meglévő adatbázist egy rugalmas készletbe|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Eltávolít egy adatbázist|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Létrehoz egy erőforráscsoportot|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Létrehoz egy kiszolgálót|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|A kiszolgálókra vonatkozó adatokat ad vissza|
|[Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver)|Módosítja egy kiszolgáló tulajdonságait|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Eltávolít egy kiszolgálót|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Kiszolgálószintű tűzfalszabályt hoz létre |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Lekért tűzfalszabályok egy kiszolgálóra|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Módosít egy tűzfalszabályt egy kiszolgálón|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Töröl egy tűzfalszabályt a kiszolgálóról.|
| New-AzSqlServerVirtualNetworkRule | Létrehoz egy [*virtuális hálózati szabályt*](vnet-service-endpoint-rule-overview.md)egy alhálózat alapján, amely egy Virtual Network szolgáltatásvégpont. |

> [!TIP]
> A PowerShell rövid útmutatóját lásd: Adatbázis létrehozása a Azure SQL Database [PowerShell használatával.](single-database-create-quickstart.md) A PowerShell-példaszk szkriptekért lásd: Adatbázis létrehozása a Azure SQL Database-ban a [PowerShell](scripts/create-and-configure-database-powershell.md) használatával és tűzfalszabály konfigurálása és Adatbázis figyelése és méretezése a Azure SQL Database [PowerShell használatával.](scripts/monitor-and-scale-database-powershell.md)
>

## <a name="manage-servers-databases-and-firewalls-using-the-azure-cli"></a>Kiszolgálók, adatbázisok és tűzfalak kezelése az Azure CLI használatával

A kiszolgálók, adatbázisok és tűzfalak Azure [CLI-val](/cli/azure)való létrehozásához és kezeléséhez használja a következő [Azure CLI-SQL Database](/cli/azure/sql/db) parancsokat. A [Cloud Shell-lel](../../cloud-shell/overview.md) futtassa a parancssori felületet a böngészőben, vagy [telepítse](/cli/azure/install-azure-cli) macOS, Linux, illetve Windows rendszeren. Rugalmas készletek létrehozásához és kezeléséhez lásd: [Rugalmas készletek.](elastic-pool-overview.md)

| Parancsmag | Leírás |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az_sql_db_create) |Létrehoz egy adatbázist|
|[az sql db list](/cli/azure/sql/db#az_sql_db_list)|Felsorolja a kiszolgáló által kezelt összes adatbázist vagy egy rugalmas készletben található összes adatbázist|
|[az sql db list-editions](/cli/azure/sql/db#az_sql_db_list_editions)|Felsorolja az elérhető szolgáltatási célkitűzéseket és tárolási korlátokat|
|[az sql db list-usages](/cli/azure/sql/db#az_sql_db_list_usages)|Adatbázis-használatot ad vissza|
|[az sql db show](/cli/azure/sql/db#az_sql_db_show)|Adatbázis lekérte
|[az sql db update](/cli/azure/sql/db#az_sql_db_update)|Adatbázis frissítése|
|[az sql db delete](/cli/azure/sql/db#az_sql_db_delete)|Eltávolít egy adatbázist|
|[az group create](/cli/azure/group#az_group_create)|Létrehoz egy erőforráscsoportot|
|[az sql server create](/cli/azure/sql/server#az_sql_server_create)|Létrehoz egy kiszolgálót|
|[az sql server list](/cli/azure/sql/server#az_sql_server_list)|Kiszolgálók listái|
|[az sql server list-usages](/cli/azure/sql/server#az_sql_server_list-usages)|Kiszolgálóhasználatot ad vissza|
|[az sql server show](/cli/azure/sql/server#az_sql_server_show)|Lekért egy kiszolgálót|
|[az sql server update](/cli/azure/sql/server#az_sql_server_update)|Kiszolgáló frissítése|
|[az sql server delete](/cli/azure/sql/server#az_sql_server_delete)|Töröl egy kiszolgálót|
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Létrehoz egy kiszolgálói tűzfalszabályt|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Felsorolja a kiszolgálón található tűzfalszabályokat|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Egy tűzfalszabály részleteit jeleníti meg|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Tűzfalszabály frissítése|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Töröl egy tűzfalszabályt|

> [!TIP]
> Az Azure CLI rövid útmutatóját lásd: Adatbázis létrehozása a Azure SQL Database [Az Azure CLI használatával.](az-cli-script-samples-content-guide.md) Az Azure CLI-példaszk szkriptekért lásd: Adatbázis létrehozása a Azure SQL Database-ban a [CLI](scripts/create-and-configure-database-cli.md) használatával és tűzfalszabály konfigurálása, valamint Az adatbázis figyelése és skálázása a cli használatával [a Azure SQL Database.](scripts/monitor-and-scale-database-cli.md)
>

## <a name="manage-servers-databases-and-firewalls-using-transact-sql"></a>Kiszolgálók, adatbázisok és tűzfalak kezelése a Transact-SQL használatával

A kiszolgálók, adatbázisok és tűzfalak Transact-SQL használatával való létrehozásához és kezeléséhez használja a következő T-SQL-parancsokat. Ezeket a parancsokat a Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)vagy bármely más program használatával használhatja, amely képes csatlakozni egy kiszolgálóhoz és Transact-SQL-parancsokat átadni. A rugalmas készletek kezeléséhez lásd: [Rugalmas készletek.](elastic-pool-overview.md)

> [!IMPORTANT]
> Nem hozhat létre vagy törölhet kiszolgálót a Transact-SQL használatával.

| Parancs | Leírás |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true) | Létrehoz egy új adatbázist a Azure SQL Database. Új adatbázis létrehozásához a master adatbázishoz kell csatlakozva lennie.|
|[CREATE DATABASE (Azure Synapse)](/sql/t-sql/statements/create-database-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Új adatraktár-adatbázist hoz létre a Azure Synapse. Új adatbázis létrehozásához a master adatbázishoz kell csatlakozva lennie.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Módosítja az adatbázist vagy a rugalmas készletet. |
|[ALTER DATABASE (Azure Synapse Analytics)](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest&preserve-view=true&tabs=sqlpool)|Módosít egy adatraktár-adatbázist a Azure Synapse.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Töröl egy adatbázist.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Egy adatbázis kiadását (szolgáltatási szint), szolgáltatási célkitűzését (tarifacsomag) és rugalmas készletnevét (ha van) adja vissza. Ha be van jelentkezve egy kiszolgáló master adatbázisába, a az összes adatbázisra vonatkozó adatokat ad vissza. A Azure Synapse a master adatbázishoz kell csatlakoztatnia.|
|[sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Egy adatbázis processzor-, I/O- és memóriahasználatát adja vissza a Azure SQL Database. 15 másodpercenként létezik egy sor, még akkor is, ha nincs tevékenység az adatbázisban.|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|A processzorhasználati és tárolási adatokat adja vissza egy adatbázishoz a Azure SQL Database. A rendszer ötperces időközönként gyűjti és összesíti az adatokat.|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Az adatbázis-kapcsolati események statisztikáit Azure SQL Database, és áttekintést nyújt az adatbázis-kapcsolatok sikerességről és hibákról. |
|[sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Sikeres adatbázis-Azure SQL Database, csatlakozási hibákat és holtpontokat ad vissza a Azure SQL Database. Ezek az információk az adatbázis-tevékenységek nyomon követésére vagy hibaelhárítására használhatók.|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Létrehozza vagy frissíti a kiszolgáló kiszolgálószintű tűzfalbeállítását. Ez a tárolt eljárás csak a master adatbázisban érhető el a kiszolgálószintű fő bejelentkezéshez. Kiszolgálószintű tűzfalszabály csak akkor használhatja a Transact-SQL-t, ha egy Azure-szintű engedéllyel rendelkező felhasználó létrehozta az első kiszolgálószintű tűzfalszabályt|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|A kiszolgálóhoz társított kiszolgálószintű tűzfalbeállításokra vonatkozó adatokat ad vissza.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Eltávolítja a kiszolgálószintű tűzfalbeállításokat a kiszolgálóról. Ez a tárolt eljárás csak a master adatbázisban érhető el a kiszolgálószintű fő bejelentkezéshez.|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Létrehozza vagy frissíti az adatbázisszintű tűzfalszabályokat a Azure SQL Database. Az adatbázis tűzfalszabályai konfigurálhatóak a master adatbázishoz és a felhasználói adatbázisokhoz a SQL Database. Az adatbázis-tűzfalszabályok akkor hasznosak, ha tartalmazott adatbázis-felhasználókat használ. Az adatbázis-tűzfalszabályok nem támogatottak a Azure Synapse.|
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Egy adatbázis adatbázisszintű tűzfalbeállításával kapcsolatos információkat ad vissza a Azure SQL Database. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Eltávolítja a saját adatbázisának adatbázisszintű tűzfalbeállítását a Azure SQL Database. |

> [!TIP]
> A Microsoft Windowson SQL Server Management Studio rövid útmutatóért lásd: Azure SQL Database: Csatlakozás és adatlekérdezés a SQL Server Management Studio [használatával.](connect-query-ssms.md) A Visual Studio Code macOS, Linux vagy Windows rendszeren való használatával kapcsolatos rövid útmutatóért lásd: Azure SQL Database: Csatlakozás és adatlekérdezés a [Visual Studio Code használatával.](connect-query-vscode.md)

## <a name="manage-servers-databases-and-firewalls-using-the-rest-api"></a>Kiszolgálók, adatbázisok és tűzfalak kezelése a REST API

A kiszolgálók, adatbázisok és tűzfalak létrehozásához és kezeléséhez használja ezeket a REST API kérelmeket.

| Parancs | Leírás |
| --- | --- |
|[Kiszolgálók – Létrehozás vagy frissítés](/rest/api/sql/servers/createorupdate)|Új kiszolgálót hoz létre vagy frissíti.|
|[Kiszolgálók – Törlés](/rest/api/sql/servers/delete)|Töröl egy kiszolgálót.|
|[Kiszolgálók – Lekért](/rest/api/sql/servers/get)|Lekért egy kiszolgálót.|
|[Kiszolgálók – Lista](/rest/api/sql/servers/list)|A kiszolgálók listáját adja vissza.|
|[Kiszolgálók – Lista erőforráscsoport szerint](/rest/api/sql/servers/listbyresourcegroup)|Egy erőforráscsoportban található kiszolgálók listáját adja vissza.|
|[Kiszolgálók – Frissítés](/rest/api/sql/servers/update)|Frissíti a meglévő kiszolgálót.|
|[Adatbázisok – Létrehozás vagy frissítés](/rest/api/sql/databases/createorupdate)|Létrehoz egy új adatbázist, vagy frissíti a meglévő adatbázist.|
|[Adatbázisok – Törlés](/rest/api/sql/databases/delete)|Töröl egy adatbázist.|
|[Adatbázisok – Lekért](/rest/api/sql/databases/get)|Lekért egy adatbázist.|
|[Adatbázisok – Lista rugalmas készlet alapján](/rest/api/sql/databases/listbyelasticpool)|Egy rugalmas készletben található adatbázisok listáját adja vissza.|
|[Adatbázisok – List by server](/rest/api/sql/databases/listbyserver)|A kiszolgálón található adatbázisok listáját adja vissza.|
|[Adatbázisok – Frissítés](/rest/api/sql/databases/update)|Frissíti a meglévő adatbázist.|
|[Tűzfalszabályok – Létrehozás vagy frissítés](/rest/api/sql/firewallrules/createorupdate)|Létrehoz vagy frissíti a tűzfalszabályt.|
|[Tűzfalszabályok – Törlés](/rest/api/sql/firewallrules/delete)|Töröl egy tűzfalszabályt.|
|[Tűzfalszabályok – Lekért](/rest/api/sql/firewallrules/get)|Lekért egy tűzfalszabályt.|
|[Tűzfalszabályok – Listás kiszolgáló szerint](/rest/api/sql/firewallrules/listbyserver)|A tűzfalszabályok listáját adja vissza.|

## <a name="next-steps"></a>Következő lépések

- További információ egy adatbázis SQL Server áttelepítése Azure SQL Database: Áttelepítés a [Azure SQL Database.](migrate-to-database-from-sql-server.md)
- A támogatott funkciókkal kapcsolatos tudnivalókat lásd: [Funkciók](features-comparison.md).