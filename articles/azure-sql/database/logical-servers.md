---
title: Mi az Azure SQL Database-kiszolgáló és az Azure szinapszis Analytics?
titleSuffix: ''
description: Ismerje meg a Azure SQL Database és az Azure szinapszis Analytics által használt logikai SQL Server-kiszolgálókat, valamint a kezelésük módját.
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
ms.openlocfilehash: 09e8ef85b6df48b6b442351135d4ae4ed55e9b45
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641068"
---
# <a name="what-is-a-logical-sql-server-in-azure-sql-database-and-azure-synapse"></a>Mi a logikai SQL Server Azure SQL Database és az Azure szinapszis?
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

A Azure SQL Database és az Azure szinapszis Analyticsben a kiszolgálók olyan logikai szerkezetek, amelyek központi felügyeleti pontként működnek az adatbázisok gyűjteményéhez. A kiszolgálói szinten felügyelheti a [bejelentkezéseket](logins-create-manage.md), a [Tűzfalszabályok](firewall-configure.md), a [naplózási szabályokat](../../azure-sql/database/auditing-overview.md), a [veszélyforrások észlelésére vonatkozó házirendeket](threat-detection-configure.md)és az [automatikus feladatátvételi csoportokat](auto-failover-group-overview.md). Egy kiszolgáló lehet más régióban, mint az erőforráscsoport. A kiszolgálónak léteznie kell ahhoz, hogy adatbázist hozzon létre Azure SQL Database vagy adatraktár-adatbázisba az Azure szinapszis Analyticsben. Az egyetlen kiszolgáló által felügyelt összes adatbázis ugyanabban a régióban jön létre, mint a-kiszolgáló.

Ez a kiszolgáló különbözik egy olyan SQL Server-példánytól, amely a helyszíni világban is ismerős lehet. Az adatbázisok vagy az adatraktár-adatbázis helye nem jelent garanciát az azokat kezelő kiszolgálóhoz képest. Emellett sem Azure SQL Database, sem az Azure szinapszis nem tesz elérhetővé semmilyen példány szintű hozzáférést vagy funkciót. Ezzel szemben a felügyelt példányban található példány-adatbázisok mind fizikailag közösen helyezkednek el, ugyanúgy, mint a helyszíni vagy a virtuális gépek világában SQL Server.

Kiszolgáló létrehozásakor meg kell adnia egy kiszolgálói bejelentkezési fiókot és jelszót, amely rendszergazdai jogosultságokkal rendelkezik a kiszolgálón található főadatbázishoz és a kiszolgálón létrehozott összes adatbázishoz. Ez a kezdeti fiók egy SQL-bejelentkezési fiók. A Azure SQL Database és az Azure szinapszis Analytics támogatja az SQL-hitelesítést és Azure Active Directory hitelesítést a hitelesítéshez. További információ a bejelentkezésekről és a hitelesítésről: [adatbázisok és bejelentkezések kezelése Azure SQL Databaseban](logins-create-manage.md). A Windows-hitelesítés nem támogatott.

Egy kiszolgáló a SQL Database és az Azure Szinapszisban:

- Egy Azure-előfizetésen belül jön létre, de az erőforrásaival együtt áthelyezhető másik előfizetésre.
- Az adatbázisok, rugalmas készletek és adattárházak szülőerőforrása.
- Névteret biztosít az adatbázisok, a rugalmas készletek és az adatraktár-adatbázis számára
- Egy erős élettartamú szemantikai logikai tároló, amely töröl egy kiszolgálót, és törli az adatbázisait, rugalmas készleteit és SQK-készleteit
- Részt vesz az [Azure szerepköralapú hozzáférés-vezérlésében (Azure RBAC)](../../role-based-access-control/overview.md) – a kiszolgálókon belüli adatbázisok, rugalmas készletek és adatraktár-adatbázisok öröklik a hozzáférési jogosultságokat a kiszolgálóról
- A az adatbázisok, rugalmas készletek és adatraktár-adatbázisok identitásának magas rendű eleme az Azure erőforrás-kezelési célokra (lásd: adatbázisok és készletek URL-sémája)
- Közösen helyezi el egy adott régió erőforrásait.
- Kapcsolódási végpontot biztosít az adatbázis-hozzáféréshez (`<serverName>`.database.windows.net)
- Egy master adatbázishoz való kapcsolódással hozzáférést biztosít a tárolt erőforrásokra vonatkozó metaadatokhoz a DMV-ken keresztül
- Hatókört biztosít a felügyeleti házirendek számára, amelyek az adatbázisaira vonatkoznak – bejelentkezések, tűzfal, naplózás, veszélyforrások észlelése és hasonlók
- A szülő-előfizetésben lévő kvóta korlátozza (alapértelmezés szerint hat kiszolgáló/előfizetés – [itt az előfizetési korlátok itt láthatók](../../azure-resource-manager/management/azure-subscription-service-limits.md))
- Az adatbázis-kvóta-és DTU-vagy virtuális mag-kvóta hatókörét biztosítja a benne található erőforrások számára (például 45 000 DTU).
- A foglalt erőforrásokon engedélyezett funkciók verziószámozási hatóköre
- Kiszolgálószintű rendszerbiztonsági tagként bejelentkezve a kiszolgáló minden adatbázisa felügyelhető.
- A olyan bejelentkezéseket tartalmazhat, amelyek hasonlóak a helyszíni környezetben lévő SQL Server példányaihoz, amelyek hozzáférést kapnak a kiszolgálón lévő egy vagy több adatbázishoz, és korlátozott rendszergazdai jogosultságokat kapnak. További információk: [Bejelentkezések](logins-create-manage.md).
- A kiszolgálón létrehozott összes adatbázis alapértelmezett rendezése: a (z `SQL_LATIN1_GENERAL_CP1_CI_AS` `LATIN1_GENERAL` ) angol (Egyesült Államok), a `CP1` 1252-es kódlap, `CI` a kis-és nagybetűk megkülönböztetése, és `AS` a hangsúly-érzékeny.

## <a name="manage-servers-databases-and-firewalls-using-the-azure-portal"></a>Kiszolgálók, adatbázisok és tűzfalak kezelése a Azure Portal használatával

Létrehozhat egy adott kiszolgálóhoz tartozó erőforráscsoportot, vagy a kiszolgáló létrehozásakor is. Az SQL Server új űrlapjának beolvasására több módszer is rendelkezésre áll egy új SQL Server létrehozásával vagy egy új adatbázis létrehozásának részeként.

### <a name="create-a-blank-server"></a>Üres kiszolgáló létrehozása

Ha a [Azure Portal](https://portal.azure.com)használatával szeretne létrehozni egy kiszolgálót (adatbázis, rugalmas készlet vagy adatraktár-adatbázis nélkül), akkor navigáljon egy üres SQL Server-(logikai SQL Server-) űrlapra.

### <a name="create-a-blank-or-sample-database-in-azure-sql-database"></a>Hozzon létre egy üres vagy egy mintaadatbázis-adatbázist Azure SQL Database

Ha SQL Database-adatbázist szeretne létrehozni a [Azure Portal](https://portal.azure.com)használatával, nyisson meg egy üres SQL Database űrlapot, és adja meg a kért információkat. Az erőforráscsoportot és a kiszolgálót az idő előtt vagy maga az adatbázis létrehozásakor is létrehozhatja. Létrehozhat egy üres adatbázist, vagy létrehozhat egy minta-adatbázist az Adventure Works LT alapján.

  ![adatbázis létrehozása-1](./media/logical-servers/create-database-1.png)

> [!IMPORTANT]
> Az adatbázis díjszabási rétegének kiválasztásával kapcsolatos információkért lásd: [DTU-alapú vásárlási modell](service-tiers-dtu.md) és [virtuális mag-alapú vásárlási modell](service-tiers-vcore.md).

Felügyelt példány létrehozásához tekintse meg [a felügyelt példány létrehozása](../managed-instance/instance-create-quickstart.md) című témakört.

### <a name="manage-an-existing-server"></a>Meglévő kiszolgáló kezelése

Egy meglévő kiszolgáló kezeléséhez navigáljon a kiszolgálóhoz számos módszer használatával – például egy adott adatbázis oldaláról, az **SQL Server** -oldalról vagy a **minden erőforrás** lapról.

Meglévő adatbázis kezeléséhez navigáljon az SQL- **adatbázisok** oldalra, és kattintson a felügyelni kívánt adatbázisra. Az alábbi képernyőfelvételen megtudhatja, hogyan kezdheti el az adatbázishoz tartozó kiszolgálói szintű tűzfal beállítását az adatbázis **Áttekintés** lapján.

   ![kiszolgálói tűzfalszabály](./media/single-database-create-quickstart/server-firewall-rule.png)

> [!IMPORTANT]
> Az adatbázis teljesítmény-tulajdonságainak konfigurálásához tekintse meg a [DTU-alapú vásárlási modellt](service-tiers-dtu.md) és a [virtuális mag-alapú vásárlási modellt](service-tiers-vcore.md).
> [!TIP]
> Azure Portal gyors útmutatóért lásd: [adatbázis létrehozása a SQL Databaseban a Azure Portal](single-database-create-quickstart.md).

## <a name="manage-servers-databases-and-firewalls-using-powershell"></a>Kiszolgálók, adatbázisok és tűzfalak kezelése a PowerShell-lel

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatott, de a jövőbeli fejlesztés az az. SQL modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

Kiszolgálók, adatbázisok és tűzfalak Azure PowerShell használatával történő létrehozásához és kezeléséhez használja a következő PowerShell-parancsmagokat. Ha telepítenie vagy frissítenie kell a PowerShellt, olvassa el a [Azure PowerShell modul telepítése](/powershell/azure/install-az-ps)című témakört. Rugalmas készletek létrehozásához és kezeléséhez lásd: [rugalmas készletek](elastic-pool-overview.md).

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

> [!TIP]
> PowerShell-útmutató: [adatbázis létrehozása Azure SQL Database a PowerShell használatával](single-database-create-quickstart.md). A PowerShell-parancsfájlok esetében lásd: [a PowerShell használatával hozzon létre egy adatbázist a Azure SQL Databaseban, és konfiguráljon egy tűzfalszabály használatát](scripts/create-and-configure-database-powershell.md) , és [Figyelje meg és méretezze át az adatbázist a Azure SQL Database a PowerShell használatával](scripts/monitor-and-scale-database-powershell.md).
>

## <a name="manage-servers-databases-and-firewalls-using-the-azure-cli"></a>Kiszolgálók, adatbázisok és tűzfalak kezelése az Azure CLI használatával

A kiszolgálók, adatbázisok és tűzfalak [Azure CLI](/cli/azure)-vel való létrehozásához és kezeléséhez használja az alábbi [Azure CLI SQL Database](/cli/azure/sql/db) parancsokat. A [Cloud Shell-lel](../../cloud-shell/overview.md) futtassa a parancssori felületet a böngészőben, vagy [telepítse](/cli/azure/install-azure-cli) macOS, Linux, illetve Windows rendszeren. Rugalmas készletek létrehozásához és kezeléséhez lásd: [rugalmas készletek](elastic-pool-overview.md).

| Parancsmag | Leírás |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Létrehoz egy adatbázist|
|[az SQL db List](/cli/azure/sql/db#az-sql-db-list)|A kiszolgáló által kezelt összes adatbázis vagy egy rugalmas készletben lévő összes adatbázis listája|
|[az SQL db List-Editions](/cli/azure/sql/db#az-sql-db-list-editions)|Az elérhető szolgáltatási célkitűzések és a tárolási korlátok listája|
|[az SQL db List-használat](/cli/azure/sql/db#az-sql-db-list-usages)|Adatbázis-használatot ad vissza|
|[az SQL db show](/cli/azure/sql/db#az-sql-db-show)|Adatbázis beolvasása
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

> [!TIP]
> Azure CLI-gyors útmutató: [adatbázis létrehozása Azure SQL Database az Azure CLI használatával](az-cli-script-samples-content-guide.md). Az Azure CLI példa parancsfájljaihoz lásd: [a CLI használata adatbázis létrehozásához a Azure SQL Database és a tűzfalszabály konfigurálása](scripts/create-and-configure-database-cli.md) , valamint [a CLI használata az adatbázisok figyelésére és méretezésére Azure SQL Databaseokban](scripts/monitor-and-scale-database-cli.md).
>

## <a name="manage-servers-databases-and-firewalls-using-transact-sql"></a>Kiszolgálók, adatbázisok és tűzfalak kezelése a Transact-SQL használatával

Kiszolgálók, adatbázisok és tűzfalak a Transact-SQL használatával történő létrehozásához és kezeléséhez használja a következő T-SQL-parancsokat. Ezeket a parancsokat a Azure Portal, a [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), a [Visual Studio Code](https://code.visualstudio.com/docs)vagy bármely más olyan program használatával lehet kiadni, amely csatlakozhat egy kiszolgálóhoz, és átadhatja a Transact-SQL-parancsokat. Rugalmas készletek kezeléséhez lásd: [rugalmas készletek](elastic-pool-overview.md).

> [!IMPORTANT]
> A Transact-SQL használatával nem lehet kiszolgálót létrehozni vagy törölni.

| Parancs | Leírás |
| --- | --- |
|[ADATBÁZIS létrehozása (Azure SQL Database)](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true) | Egy új adatbázist hoz létre Azure SQL Databaseban. Új adatbázis létrehozásához csatlakoznia kell a Master adatbázishoz.|
|[ADATBÁZIS létrehozása (Azure szinapszis)](/sql/t-sql/statements/create-database-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Új adatraktár-adatbázist hoz létre az Azure Szinapszisban. Új adatbázis létrehozásához csatlakoznia kell a Master adatbázishoz.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Módosítja az adatbázist vagy a rugalmas készletet. |
|[ALTER DATABASE (Azure szinapszis Analytics)](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest&preserve-view=true&tabs=sqlpool)|Módosít egy adatraktár-adatbázist az Azure Szinapszisban.|
|[ADATBÁZIS eldobása (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Töröl egy adatbázist.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Visszaadja a kiadást (szolgáltatási szintet), a szolgáltatási célt (árképzési szintet) és a rugalmas készlet nevét (ha van ilyen) egy adatbázishoz. Ha bejelentkezett a kiszolgáló főadatbázisára, az az összes adatbázisra vonatkozó információt adja vissza. Az Azure szinapszis esetében csatlakoznia kell a Master adatbázishoz.|
|[sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| A CPU-t, az i/o-t és a memória-felhasználást adja vissza egy Azure SQL Database adatbázisában. 15 másodpercenként egy sor létezik, még akkor is, ha nincs tevékenység az adatbázisban.|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Egy adatbázis CPU-használati és tárolási adatait adja vissza Azure SQL Database. Az adatok gyűjtése és összesítése öt percen belül történik.|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|A Azure SQL Database adatbázis-csatlakozási eseményeinek statisztikáit tartalmazza, áttekintést nyújtva az adatbázis-kapcsolat sikerességéről és a hibákról. |
|[sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|A sikeres Azure SQL Database adatbázis-kapcsolatok, csatlakozási hibák és a Azure SQL Database holtpontok visszaadása. Ezen információk segítségével nyomon követheti és elháríthatja az adatbázis-tevékenységeket.|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Létrehozza vagy frissíti a kiszolgáló szintű tűzfalbeállítások beállításait a kiszolgálón. Ez a tárolt eljárás csak a főadatbázisban érhető el a kiszolgálói szintű rendszerbiztonsági tag-bejelentkezéshez. A kiszolgálói szintű tűzfalszabályok csak akkor hozhatók létre a Transact-SQL használatával, ha az első kiszolgálói szintű tűzfalszabály az Azure-szintű engedélyekkel rendelkező felhasználó által lett létrehozva.|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|A kiszolgálóhoz társított kiszolgálói szintű tűzfalbeállítások adatait adja vissza.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Eltávolítja a kiszolgáló szintű tűzfal beállításait a kiszolgálóról. Ez a tárolt eljárás csak a főadatbázisban érhető el a kiszolgálói szintű rendszerbiztonsági tag-bejelentkezéshez.|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Az adatbázis-szintű tűzfalszabályok létrehozása vagy frissítése Azure SQL Database-adatbázisban. Az adatbázis-tűzfalszabályok konfigurálhatók a Master adatbázishoz, valamint SQL Database felhasználói adatbázisaihoz. Az adatbázis-tűzfalszabályok a tárolt adatbázis-felhasználók használata esetén hasznosak. Az adatbázis-tűzfalszabályok nem támogatottak az Azure Szinapszisban.|
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Az adatbázis-szintű tűzfalbeállítások adatait adja vissza Azure SQL Databaseban. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Az adatbázis-szintű tűzfalbeállítások eltávolítása az Ön Azure SQL Database-adatbázisához. |

> [!TIP]
> A Microsoft Windows SQL Server Management Studiot használó rövid útmutatókat a [Azure SQL Database: az SQL Server Management Studio használata a kapcsolódáshoz és az adatlekérdezéshez](connect-query-ssms.md)című témakörben talál. A macOS, Linux vagy Windows rendszeren a Visual Studio Code használatával történő gyors útmutatóért lásd [Azure SQL Database: a Visual Studio Code használata a kapcsolódáshoz és az adatlekérdezéshez](connect-query-vscode.md).

## <a name="manage-servers-databases-and-firewalls-using-the-rest-api"></a>Kiszolgálók, adatbázisok és tűzfalak kezelése a REST API használatával

Kiszolgálók, adatbázisok és tűzfalak létrehozásához és kezeléséhez használja ezeket a REST API kérelmeket.

| Parancs | Leírás |
| --- | --- |
|[Kiszolgálók – létrehozás vagy frissítés](/rest/api/sql/servers/createorupdate)|Új kiszolgáló létrehozása vagy frissítése.|
|[Kiszolgálók – törlés](/rest/api/sql/servers/delete)|Töröl egy kiszolgálót.|
|[Kiszolgálók – Get](/rest/api/sql/servers/get)|Kiszolgáló beolvasása.|
|[Kiszolgálók – lista](/rest/api/sql/servers/list)|A kiszolgálók listáját adja vissza.|
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

- A SQL Server-adatbázisok Azure SQL Databaseba való áttelepítésével kapcsolatos információkért lásd: [áttelepítés Azure SQL Databasere](migrate-to-database-from-sql-server.md).
- A támogatott funkciókkal kapcsolatos tudnivalókat lásd: [Funkciók](features-comparison.md).