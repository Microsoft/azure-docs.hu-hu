---
title: Adatbázis másolása
description: Hozzon létre tranzakciós szempontból konzisztens másolatot egy meglévő adatbázisról Azure SQL Database ugyanazon a kiszolgálón vagy egy másik kiszolgálón.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sashan
ms.reviewer: wiassaf
ms.date: 03/10/2021
ms.openlocfilehash: 3ce07af74c3f01fd78ef15ab0e7d43b91361e556
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784476"
---
# <a name="copy-a-transactionally-consistent-copy-of-a-database-in-azure-sql-database"></a>Adatbázis tranzakciós szempontból konzisztens másolatának másolása a Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database több módszert is kínál egy meglévő [](single-database-overview.md) adatbázis másolatának létrehozására ugyanazon a kiszolgálón vagy egy másik kiszolgálón. Az adatbázisokat a következő parancsokkal másolhatja Azure Portal, PowerShell, Azure CLI vagy T-SQL használatával.

## <a name="overview"></a>Áttekintés

Az adatbázis-másolat a forrásadatbázis tranzakciós szempontból konzisztens pillanatképe a másolási kérelem kezdeményezése utáni időponttól. Kiválaszthatja ugyanazt a kiszolgálót vagy egy másik kiszolgálót a másoláshoz. Dönthet úgy is, hogy megtartja a biztonsági másolatok redundanciát, a szolgáltatási szintet és a forrásadatbázis számítási méretét, vagy eltérő biztonsági mentési tárhelyredundanciát és/vagy számítási méretet használ ugyanazon vagy egy másik szolgáltatási szinten belül. A másolás befejeződik, és teljesen működőképes, független adatbázissá válik. A másolt adatbázisban a bejelentkezéseket, a felhasználókat és az engedélyeket a forrásadatbázistól függetlenül kezeli a rendszer. A másolat a georeplikációs technológiával jön létre. Ha a replika index-összehangolása befejeződött, a georeplikációs hivatkozás automatikusan megszűnik. A georeplikáció használatára vonatkozó követelmények az adatbázis-másolási műveletnél is érvényesülnek. A [részleteket az Aktív georeplikáció áttekintése](active-geo-replication-overview.md) című témakörben olvashatja.

> [!NOTE]
> Azure SQL Database Backup Storage redundanciának konfigurálása jelenleg nyilvános előzetes verzióban érhető el Dél-Brazília területén, és általánosan csak Délkelet-Ázsia Azure-régiójában. Ha az előzetes verzióban a forrásadatbázis helyileg redundáns vagy zónaredundáns biztonsági mentési tárhelyredundanssal van létrehozva, az adatbázis másik Azure-régióban található kiszolgálóra való másolása nem támogatott. 

## <a name="logins-in-the-database-copy"></a>Bejelentkezések az adatbázis másolatában

Amikor adatbázist másol ugyanerre a kiszolgálóra, ugyanezek a bejelentkezések használhatók mindkét adatbázison. Az adatbázis másolásához használt rendszerbiztonsági tag lesz az adatbázis tulajdonosa az új adatbázisban.

Ha egy adatbázist egy másik kiszolgálóra másol, a másolási műveletet a célkiszolgálón kezdeményező rendszerbiztonsági tag lesz az új adatbázis tulajdonosa.

A célkiszolgálótól függetlenül a rendszer minden adatbázis-felhasználót, azok engedélyét és biztonsági azonosítóját (SID) átmásolja az adatbázis-másolatba. A [tartalmazottadatbázis-felhasználók](logins-create-manage.md) adateléréshez való használata biztosítja, hogy a másolt adatbázis ugyanazokkal a felhasználói hitelesítő adatokkal rendelkezik, így a másolás befejezése után azonnal elérheti ugyanezekkel a hitelesítő adatokkal.

Ha kiszolgálói szintű bejelentkezési adatokat használ az adatok eléréséhez, és az adatbázist egy másik kiszolgálóra másolja, előfordulhat, hogy a bejelentkezési alapú hozzáférés nem fog működni. Ennek az lehet az oka, hogy a bejelentkezési adatok nem léteznek a célkiszolgálón, vagy a jelszavuk és a biztonsági azonosítóik (SID) eltérnek. Ha többet szeretne megtudni a bejelentkezések kezeléséről, amikor adatbázist másol egy másik kiszolgálóra, tekintse meg a vészhelyreállítás utáni biztonsági Azure SQL Database [kezelését.](active-geo-replication-security-configure.md) Miután a másolási művelet sikeres volt egy másik kiszolgálóra, és a többi felhasználó újra leképezés előtt csak az adatbázis tulajdonosához vagy a kiszolgáló rendszergazdájához társított bejelentkezés tud bejelentkezni a másolt adatbázisba. A bejelentkezések feloldásához és az adatokhoz való hozzáférés a másolási művelet befejezése után történő létrehozására vonatkozó információkért lásd: [Bejelentkezések feloldása.](#resolve-logins)

## <a name="copy-using-the-azure-portal"></a>Másolás az Azure Portal használatával

Az adatbázis másolásához a Azure Portal nyissa meg az adatbázis lapját, majd kattintson a Másolás **gombra.**

   ![Adatbázis-másolat](./media/database-copy/database-copy.png)

## <a name="copy-using-powershell-or-the-azure-cli"></a>Másolás a PowerShell vagy az Azure CLI használatával

Adatbázis másolásához használja az alábbi példákat.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A PowerShellhez használja a [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) parancsmagot.

> [!IMPORTANT]
> A PowerShell Azure Resource Manager (RM) modult továbbra is támogatja a Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modulra lesz kihozva. Az AzureRM modul legalább 2020 decemberéig továbbra is megkapja a hibajavításokat.  Az Az modulban és az AzureRm-modulokban található parancsok argumentumai jelentősen megegyeznek. További információ a kompatibilitásukról: [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az)(Az új Azure PowerShell Az modul).

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

Az adatbázis másolata aszinkron művelet, de a céladatbázis a kérés elfogadás után azonnal létrejön. Ha meg kell szakítania a másolási műveletet, miközben még folyamatban van, a [Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) parancsmag használatával törölje a céladatbázist.

A teljes PowerShell-parancsfájlminta: [Adatbázis másolása új kiszolgálóra.](scripts/copy-database-to-new-server-powershell.md)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

Az adatbázis másolata aszinkron művelet, de a céladatbázis a kérés elfogadás után azonnal létrejön. Ha meg kell szakítania a másolási műveletet, miközben még folyamatban van, az [az sql db delete](/cli/azure/sql/db#az_sql_db_delete) paranccsal törölje a céladatbázist.

* * *

## <a name="copy-using-transact-sql"></a>Másolás a Transact-SQL használatával

Jelentkezzen be a master adatbázisba a kiszolgáló-rendszergazdai bejelentkezéssel vagy a másolni kívánt adatbázist létrehozó bejelentkezéssel. Ahhoz, hogy az adatbázis másolása sikeres legyen, a nem a kiszolgálói rendszergazdai bejelentkezésnek kell a szerepkör tagjának `dbmanager` lennie. További információ a bejelentkezésről és a kiszolgálóhoz való csatlakozásról: [Bejelentkezések kezelése.](logins-create-manage.md)

Kezdje meg a forrásadatbázis másolását a [CREATE DATABASE ... AS COPY OF](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true#copy-a-database) utasítás. A T-SQL-utasítás addig fut, amíg az adatbázis-másolási művelet be nem fejeződik.

> [!NOTE]
> A T-SQL-utasítás megszakítása nem szünteti meg az adatbázis-másolási műveletet. A művelet megszüntetéséhez hagyja el a céladatbázist.
>

> [!IMPORTANT]
> Biztonsági mentési tárhely-redundancia kiválasztása A T-SQL CREATE DATABASE használata során... Az AS COPY OF parancs még nem támogatott. 

### <a name="copy-to-the-same-server"></a>Másolás ugyanerre a kiszolgálóra

Jelentkezzen be a master adatbázisba a kiszolgáló-rendszergazdai bejelentkezéssel vagy a másolni kívánt adatbázist létrehozó bejelentkezéssel. Ahhoz, hogy az adatbázis másolása sikeres legyen, a nem a kiszolgálói rendszergazdai bejelentkezésnek kell a szerepkör tagjának `dbmanager` lennie.

Ez a parancs a Database1 adatbázist egy új, Database2 nevű adatbázisba másolja ugyanazon a kiszolgálón. Az adatbázis méretétől függően a másolási művelet némi időt vehet igénybe.

   ```sql
   -- Execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-to-an-elastic-pool"></a>Másolás rugalmas készletbe

Jelentkezzen be a master adatbázisba a kiszolgáló-rendszergazdai bejelentkezéssel vagy a másolni kívánt adatbázist létrehozó bejelentkezéssel. Ahhoz, hogy az adatbázis másolása sikeres legyen, a nem a kiszolgálói rendszergazdai bejelentkezésnek kell a szerepkör tagjának `dbmanager` lennie.

Ez a parancs a Database1 adatbázist egy új, Database2 nevű adatbázisba másolja egy pool1 nevű rugalmas készletben. Az adatbázis méretétől függően a másolási művelet némi időt vehet igénybe.

A Database1 adatbázis lehet egy vagy egy készletbe oltott adatbázis. A különböző rétegkészletek közötti másolás támogatott, de egyes többrétegű másolatok nem fognak sikerülni. Például átmásolhat egy vagy több rugalmas standard adatbázist egy általános célú készletbe, de standard rugalmas adatbázist nem másolhat prémium szintű készletbe. 

   ```sql
   -- Execute on the master database to start copying
   CREATE DATABASE "Database2"
   AS COPY OF "Database1"
   (SERVICE_OBJECTIVE = ELASTIC_POOL( name = "pool1" ) );
   ```

### <a name="copy-to-a-different-server"></a>Másolás másik kiszolgálóra

Jelentkezzen be annak a célkiszolgálónak a master adatbázisába, ahol az új adatbázist létre kell hozva. Olyan bejelentkezést használjon, amely ugyanazokkal a névvel és jelszóval rendelkezik, mint a forráskiszolgálón található forrásadatbázis tulajdonosa. A célkiszolgálón történő bejelentkezésnek a szerepkörnek is tagja kell lennie, vagy `dbmanager` kiszolgáló-rendszergazdai bejelentkezésnek kell lennie.

Ez a parancs a Database1 adatbázist a server1 kiszolgálón egy új, Database2 nevű adatbázisba másolja a server2 kiszolgálón. Az adatbázis méretétől függően a másolási művelet befejezése némi időt vehet igénybe.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> Mindkét kiszolgáló tűzfalát úgy kell konfigurálni, hogy engedélyezze a T-SQL CREATE DATABASE adatbázist kiállító ügyfél IP-címének bejövő kapcsolatát... AS COPY OF parancs.

### <a name="copy-to-a-different-subscription"></a>Másolás másik előfizetésbe

Az Adatbázis másolása másik [kiszolgálóra SQL Database](#copy-to-a-different-server) szakaszban található lépésekkel másolhatja az adatbázist egy másik előfizetésben található kiszolgálóra a T-SQL használatával. Ügyeljen arra, hogy olyan bejelentkezést használjon, amely ugyanazokkal a névvel és jelszóval rendelkezik, mint a forrásadatbázis adatbázis-tulajdonosa. Emellett a bejelentkezésnek a szerepkör vagy a kiszolgáló rendszergazdájának kell lennie `dbmanager` mind a forrás-, mind a célkiszolgálón.

```sql
--Step# 1
--Create login and user in the master database of the source server.

CREATE LOGIN loginname WITH PASSWORD = 'xxxxxxxxx'
GO
CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE dbmanager ADD MEMBER loginname;
GO

--Step# 2
--Create the user in the source database and grant dbowner permission to the database.

CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE db_owner ADD MEMBER loginname;
GO

--Step# 3
--Capture the SID of the user "loginname" from master database

SELECT [sid] FROM sysusers WHERE [name] = 'loginname';

--Step# 4
--Connect to Destination server.
--Create login and user in the master database, same as of the source server.

CREATE LOGIN loginname WITH PASSWORD = 'xxxxxxxxx', SID = [SID of loginname login on source server];
GO
CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE dbmanager ADD MEMBER loginname;
GO

--Step# 5
--Execute the copy of database script from the destination server using the credentials created

CREATE DATABASE new_database_name
AS COPY OF source_server_name.source_database_name;
```

> [!NOTE]
> A [Azure Portal,](https://portal.azure.com)a PowerShell és az Azure CLI nem támogatja az adatbázis másik előfizetésbe való másolását.

> [!TIP]
> Az adatbázis T-SQL-t használó másolása támogatja az adatbázisok másolását egy másik Azure-bérlő előfizetéséből. Ez csak akkor támogatott, ha SQL-hitelesítéssel jelentkezik be a célkiszolgálóra.

## <a name="monitor-the-progress-of-the-copying-operation"></a>A másolási művelet előrehaladásának figyelése

Figyelje a másolási folyamatot a [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql), [sys.dm_database_copies](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database)és [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) lekérdezéssel. Amíg a másolás folyamatban van, **az state_desc** sys.databases nézetének első oszlopa **COPYING (MÁSOLÁS)** lesz.

* Ha a másolás sikertelen, **state_desc** sys.databases nézetének első oszlopa a **SUSPECT (GYANÚS)** lesz. Hajtsa végre a DROP utasítást az új adatbázison, majd próbálkozzon újra később.
* Ha a másolás sikeres, **state_desc** sys.databases nézetének első oszlopa ONLINE **lesz.** A másolás befejeződött, és az új adatbázis egy normál adatbázis, amely a forrásadatbázistól függetlenül módosítható.

> [!NOTE]
> Ha úgy dönt, hogy megszakítja a másolást, amíg az folyamatban van, hajtsa végre a [DROP DATABASE](/sql/t-sql/statements/drop-database-transact-sql) utasítást az új adatbázison.

> [!IMPORTANT]
> Ha a forrásnál lényegesen kisebb szolgáltatási céllal kell másolatot létrehoznia, előfordulhat, hogy a céladatbázis nem rendelkezik elegendő erőforrással az adatokat létrehozására, és ez a másolási művelet meghiúsulhat. Ebben a forgatókönyvben egy georedúnis visszaállítási kérés használatával másolatot hoz létre egy másik kiszolgálón és/vagy egy másik régióban. További [információ: Recover an Azure SQL Database using database backups](recovery-using-backups.md#geo-restore) (Adatbázis biztonsági másolatai segítségével való helyreállítás).

## <a name="azure-rbac-roles-and-permissions-to-manage-database-copy"></a>Azure RBAC-szerepkörök és -engedélyek az adatbázis-másolás kezeléséhez

Adatbázis-másolat létrehozásához a következő szerepkörökben kell lennie:

* Előfizetés tulajdonosa vagy
* SQL Server közreműködői szerepkör vagy
* Egyéni szerepkör a forrás- és céladatbázison a következő engedéllyel:

   Microsoft.Sql/servers/databases/read Microsoft.Sql/servers/databases/write

Az adatbázis-másolat megszakításához a következő szerepkörökben kell lennie:

* Előfizetés tulajdonosa vagy
* SQL Server közreműködői szerepkör vagy
* Egyéni szerepkör a forrás- és céladatbázison a következő engedéllyel:

   Microsoft.Sql/servers/databases/read Microsoft.Sql/servers/databases/write

Az adatbázis-másolásnak a Azure Portal való kezeléséhez a következő engedélyekre is szüksége lesz:

   Microsoft.Resources/subscriptions/resources/read Microsoft.Resources/subscriptions/resources/write Microsoft.Resources/deployments/read Microsoft.Resources/deployments/write Microsoft.Resources/deployments/operationstatuses/read

Ha látni szeretné a portálon az erőforráscsoport üzemelő példányai alatt, a több erőforrás-szolgáltatón, köztük az SQL-műveleteken keresztüli műveleteket is, szüksége lesz a következő további Azure-szerepkörökre:

   Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read

## <a name="resolve-logins"></a>Bejelentkezések feloldása

Miután az új adatbázis online állapotba jött a célkiszolgálón, az [ALTER USER](/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current&preserve-view=true) utasítással leképezhet felhasználókat az új adatbázisból a célkiszolgálóra való bejelentkezéshez. Az árva felhasználók megoldásához lásd: [Árva felhasználók hibaelhárítása.](/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server) Lásd [még: Biztonsági Azure SQL Database kezelése vészhelyreállítás után.](active-geo-replication-security-configure.md)

Az új adatbázis összes felhasználója megőrzi a forrásadatbázishoz szükséges engedélyeket. Az adatbázis másolatát kezdeményező felhasználó lesz az új adatbázis tulajdonosa. A másolás sikeres és a többi felhasználó újra leképezása előtt csak az adatbázis tulajdonosa jelentkezhet be az új adatbázisba.

Ha meg szeretne ismerkedni a felhasználók és bejelentkezések kezelésével, amikor adatbázist másol egy másik kiszolgálóra, tekintse meg a vészhelyreállítás utáni biztonsági Azure SQL Database [kezelését.](active-geo-replication-security-configure.md)

## <a name="database-copy-errors"></a>Adatbázis-másolási hibák

A következő hibák merülhetnek fel az adatbázis másolása során a Azure SQL Database. További információk az [Azure SQL-adatbázis másolása](database-copy.md) című részben.

| Hibakód | Súlyosság | Leírás |
| ---:| ---:|:--- |
| 40635 |16 |A(z) %.&#x2a;ls IP-címmel megadott ügyfél átmenetileg le van tiltva. |
| 40637 |16 |Az adatbázis-másolat létrehozása jelenleg le van tiltva. |
| 40561 |16 |Az adatbázis másolása sikertelen volt. A forrás- vagy a céladatbázis nem létezik. |
| 40562 |16 |Az adatbázis másolása sikertelen volt. A forrásadatbázis el lett dobva. |
| 40563 |16 |Az adatbázis másolása sikertelen volt. A céladatbázis el lett dobva. |
| 40564 |16 |Az adatbázis másolása belső hiba miatt meghiúsult. Hagyja el a céladatbázist, és próbálkozzon újra. |
| 40565 |16 |Az adatbázis másolása sikertelen volt. Egy forrásból egynél több egyidejű adatbázis-másolat nem engedélyezett. Hagyja el a céladatbázist, és próbálkozzon újra később. |
| 40566 |16 |Az adatbázis másolása belső hiba miatt meghiúsult. Hagyja el a céladatbázist, és próbálkozzon újra. |
| 40567 |16 |Az adatbázis másolása belső hiba miatt meghiúsult. Hagyja el a céladatbázist, és próbálkozzon újra. |
| 40568 |16 |Az adatbázis másolása sikertelen volt. A forrásadatbázis elérhetetlenné vált. Hagyja el a céladatbázist, és próbálkozzon újra. |
| 40569 |16 |Az adatbázis másolása sikertelen volt. A céladatbázis elérhetetlenné vált. Hagyja el a céladatbázist, és próbálkozzon újra. |
| 40570 |16 |Az adatbázis másolása belső hiba miatt meghiúsult. Hagyja el a céladatbázist, és próbálkozzon újra később. |
| 40571 |16 |Az adatbázis másolása belső hiba miatt meghiúsult. Hagyja el a céladatbázist, és próbálkozzon újra később. |

## <a name="next-steps"></a>Következő lépések

* A bejelentkezésekkel kapcsolatos információkért lásd: [Bejelentkezések](logins-create-manage.md) kezelése és Biztonsági Azure SQL Database [kezelése vészhelyreállítás után.](active-geo-replication-security-configure.md)
* Adatbázis exportálásához lásd: Az adatbázis [exportálása BACPAC-fájlba.](database-export.md)
