---
title: 'Azure SQL Database: a biztonsági másolatok hosszú távú megőrzésének kezelése'
description: Ismerje meg, hogyan tárolhatja és állíthatja vissza a Azure SQL Database Azure Storage-ban (akár 10 évig) a Azure Portal és a PowerShell használatával történő automatikus biztonsági mentéseket
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 12/16/2020
ms.openlocfilehash: fad19d360f7c476ba71a9bbe00b58387b92f8ac4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101690556"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>A biztonsági másolatok hosszú távú megőrzésének Azure SQL Database kezelése
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A Azure SQL Database segítségével beállíthatja a [biztonsági másolatok hosszú távú megőrzési](long-term-retention-overview.md) szabályát (ltr), hogy a biztonsági mentések automatikusan megmaradjanak a különálló Azure Blob Storage-tárolókban akár 10 évig. Ezután a Azure Portal vagy a PowerShell használatával helyreállíthat egy adatbázist a biztonsági másolatokkal. A hosszú távú adatmegőrzési szabályzatok az [Azure SQL felügyelt példányai](../managed-instance/long-term-backup-retention-configure.md)esetében is támogatottak.

## <a name="using-the-azure-portal"></a>Az Azure Portal használata

A következő részben bemutatjuk, hogyan használhatja a Azure Portal a hosszú távú adatmegőrzési házirendek beállítására, a rendelkezésre álló hosszú távú adatmegőrzési biztonsági másolatok kezelésére, valamint a rendelkezésre álló biztonsági másolatból történő visszaállításra.

### <a name="configure-long-term-retention-policies"></a>Hosszú távú adatmegőrzési szabályzatok konfigurálása

A SQL Database konfigurálhatja úgy, hogy az [automatizált biztonsági mentéseket](long-term-retention-overview.md) a szolgáltatási szinten megőrzött időtartamnál hosszabb ideig is megőrizze.

1. A Azure Portal navigáljon a kiszolgálóhoz, majd válassza a **biztonsági mentések** lehetőséget. Válassza a **megőrzési szabályzatok** fület a biztonsági másolatok megőrzési beállításainak módosításához.

   ![adatmegőrzési szabályzatok](./media/long-term-backup-retention-configure/ltr-policies-tab.png)

2. Az adatmegőrzési házirendek lapon válassza ki azokat az adatbázis (oka) t, amelyeken a biztonsági másolatok hosszú távú megőrzési szabályzatait be szeretné állítani vagy módosítani kívánja. A nem kijelölt adatbázisokat a rendszer nem érinti.

   ![adatbázis kiválasztása a biztonsági mentési adatmegőrzési szabályzatok konfigurálásához](./media/long-term-backup-retention-configure/ltr-policies-tab-configure.png)

3. A **házirendek konfigurálása** panelen adja meg a heti, havi vagy éves biztonsági mentések kívánt megőrzési időtartamát. Válasszon egy "0" megőrzési időtartamot annak jelzésére, hogy a biztonsági másolatok hosszú távú megőrzését nem kell beállítani.

   ![házirendek konfigurálása panel](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

4. Válassza az **alkalmaz** lehetőséget a kiválasztott adatmegőrzési beállítások az összes kiválasztott adatbázisra való alkalmazásához.

> [!IMPORTANT]
> Ha engedélyezi a biztonsági másolatok hosszú távú megőrzési szabályát, akkor akár 7 napig is eltarthat, amíg az első biztonsági mentés láthatóvá válik, és visszaállítható. A LTR biztonsági mentési cadance kapcsolatos részletekért lásd a [biztonsági másolatok hosszú távú megőrzését](long-term-retention-overview.md)ismertető témakört.

### <a name="view-backups-and-restore-from-a-backup"></a>Biztonsági másolatok megtekintése és visszaállítás biztonsági másolatból

Megtekintheti az adott adatbázis számára a LTR házirenddel megőrzött biztonsági másolatokat, és visszaállíthatja azokat a biztonsági másolatokból.

1. A Azure Portal navigáljon a kiszolgálóhoz, majd válassza a **biztonsági mentések** lehetőséget. Egy adott adatbázis rendelkezésre álló LTR biztonsági másolatainak megtekintéséhez válassza a **kezelés** lehetőséget a rendelkezésre álló ltr biztonsági mentések oszlopban. Ekkor megjelenik egy ablaktábla a kiválasztott adatbázishoz elérhető LTR biztonsági mentések listájával.

   ![az elérhető biztonsági másolatok felhasználói felülete](./media/long-term-backup-retention-configure/ltr-available-backups-tab.png)

1. A megjelenő **elérhető ltr biztonsági másolatok** panelen tekintse át az elérhető biztonsági másolatokat. Kiválaszthat egy biztonsági mentést, amelyről vissza lehet állítani vagy törölni.

   ![elérhető LTR biztonsági mentések megtekintése](./media/long-term-backup-retention-configure/ltr-available-backups-manage.png)

1. Az elérhető LTR biztonsági másolatból való visszaállításhoz válassza ki azt a biztonsági másolatot, amelyről vissza szeretne állítani, majd válassza a **visszaállítás** lehetőséget.

   ![Visszaállítás az elérhető LTR biztonsági másolatból](./media/long-term-backup-retention-configure/ltr-available-backups-restore.png)

1. Válassza ki az új adatbázis nevét, majd válassza a **felülvizsgálat + létrehozás** lehetőséget a visszaállítás részleteinek áttekintéséhez. A **Létrehozás** gombra kattintva állíthatja vissza az adatbázist a kiválasztott biztonsági másolatból.

   ![visszaállítási részletek konfigurálása](./media/long-term-backup-retention-configure/restore-ltr.png)

1. Az eszköztáron kattintson az értesítés ikonra a visszaállítási feladatok állapotának megtekintéséhez.

   ![visszaállítási feladat állapota](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. A visszaállítási feladatok befejezése után nyissa meg az **SQL-adatbázisok** lapot az újonnan visszaállított adatbázis megtekintéséhez.

> [!NOTE]
> Innen az SQL Server Management Studióval csatlakozhat a visszaállított adatbázishoz a szükséges feladatok végrehajtásához, például [egy adatelem kinyeréséhez a visszaállított adatbázisból a meglévő adatbázisba való beillesztés érdekében, vagy a meglévő adatbázis törléséhez és a visszaállított adatbázis átnevezéséhez a meglévő adatbázis nevére](recovery-using-backups.md#point-in-time-restore).

## <a name="using-powershell"></a>A PowerShell használata

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

A következő részben bemutatjuk, hogyan használható a PowerShell a biztonsági másolatok hosszú távú megőrzésének konfigurálására, a biztonsági mentések megtekintésére az Azure Storage-ban, és hogyan lehet visszaállítani az Azure Storage biztonsági másolatából.

### <a name="azure-roles-to-manage-long-term-retention"></a>Azure-szerepkörök a hosszú távú adatmegőrzés kezeléséhez

A **Get-AzSqlDatabaseLongTermRetentionBackup** és a **Restore-AzSqlDatabase** esetében a következő szerepkörök egyikének kell lennie:

- Előfizetés tulajdonosi szerepköre vagy
- SQL Server közreműködő szerepkör vagy
- Egyéni szerepkör a következő engedélyekkel:

   Microsoft. SQL/Locations/longTermRetentionBackups/Read Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionBackups/Read Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/READ

A **Remove-AzSqlDatabaseLongTermRetentionBackup** esetében a következő szerepkörök egyikének kell lennie:

- Előfizetés tulajdonosi szerepköre vagy
- Egyéni szerepkör a következő engedélyekkel:

   Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

> [!NOTE]
> A SQL Server közreműködő szerepkör nem rendelkezik engedéllyel a LTR biztonsági mentések törléséhez.

Az Azure RBAC engedélyei az *előfizetés* vagy az *erőforráscsoport* hatókörében is megadhatók. Az eldobott kiszolgálóhoz tartozó LTR biztonsági mentések eléréséhez azonban az engedélyt az adott kiszolgáló *előfizetési* hatókörében kell megadni.

- Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

### <a name="create-an-ltr-policy"></a>LTR szabályzat létrehozása

```powershell
# get the SQL server
$subId = "<subscriptionId>"
$serverName = "<serverName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>LTR szabályzatok megtekintése

Ez a példa azt mutatja be, hogyan listázható a LTR szabályzatok a kiszolgálón belül

```powershell
# get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $serverName | `
    Get-AzSqlDatabaseLongTermRetentionPolicy

# get the LTR policy of a specific database
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup
```

### <a name="clear-an-ltr-policy"></a>LTR szabályzat törlése

Ez a példa bemutatja, hogyan törölhet egy LTR-szabályzatot egy adatbázisból.

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>LTR biztonsági mentések megtekintése

Ez a példa azt mutatja be, hogyan listázható a LTR biztonsági mentése egy kiszolgálón belül.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location

# get the list of LTR backups from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# get the LTR backups for a specific database from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>LTR biztonsági mentések törlése

Ebből a példából megtudhatja, hogyan törölhet egy LTR biztonsági másolatot a biztonsági mentések listájáról.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> A LTR biztonsági mentésének törlése nem fordítható vissza. Ha törölni szeretne egy LTR biztonsági mentést a kiszolgáló törlése után, rendelkeznie kell előfizetés-hatókör engedéllyel. Értesítéseket állíthat be az egyes törlésekről Azure Monitor a "hosszú távú adatmegőrzési biztonsági mentés törlése" művelet szűrésével. A tevékenység naplója információt tartalmaz arról, hogy ki és mikor kezdeményezte a kérést. Részletes utasításokért tekintse meg a [Tevékenységnaplók létrehozása – riasztások](../../azure-monitor/alerts/alerts-activity-log.md) című témakört.

### <a name="restore-from-ltr-backups"></a>Visszaállítás a LTR biztonsági mentésből

Ez a példa azt szemlélteti, hogyan lehet visszaállítani egy LTR biztonsági másolatból. Vegye figyelembe, hogy ez az illesztőfelület nem változott, de az erőforrás-azonosító paraméter most a LTR biztonsági mentési erőforrás-AZONOSÍTÓját igényli.

```powershell
# restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup `
    -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> Ahhoz, hogy a kiszolgáló vagy az erőforráscsoport törlése után LTR biztonsági másolatból, a kiszolgáló előfizetéséhez tartozó engedélyekkel kell rendelkeznie, és az előfizetésnek aktívnak kell lennie. A nem kötelező-ResourceGroupName paramétert is el kell hagyni.

> [!NOTE]
> Innen az SQL Server Management Studióval csatlakozhat a visszaállított adatbázishoz a szükséges feladatok végrehajtásához, például egy adatelem kinyeréséhez a visszaállított adatbázisból a meglévő adatbázisba való beillesztés érdekében, vagy a meglévő adatbázis törléséhez és a visszaállított adatbázis átnevezéséhez a meglévő adatbázis nevére. Lásd: [időponthoz való visszaállítás](recovery-using-backups.md#point-in-time-restore).

## <a name="limitations"></a>Korlátozások
- A LTR biztonsági másolatból történő visszaállításkor a rendszer letiltja az olvasási méretezési tulajdonságot. Ha engedélyezni szeretné a visszaállítást, olvassa el a visszaállított adatbázis méretét, majd frissítse az adatbázist a létrehozás után.
- Meg kell adnia a cél szolgáltatási szint célkitűzését, amikor egy LTR biztonsági másolatból végez visszaállítást, amely akkor jött létre, amikor az adatbázis egy rugalmas készletben volt. 

## <a name="next-steps"></a>Következő lépések

- A szolgáltatás által létrehozott automatikus biztonsági másolatokkal kapcsolatos további információkért lásd az [automatikus biztonsági másolatokkal](automated-backups-overview.md) foglalkozó témakört.
- A biztonsági másolatok hosszú távú megőrzésével kapcsolatos további információkért lásd: [biztonsági másolatok hosszú távú megőrzése](long-term-retention-overview.md)
