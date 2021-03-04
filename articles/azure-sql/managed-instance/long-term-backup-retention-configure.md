---
title: 'Felügyelt Azure SQL-példány: hosszú távú biztonsági másolatok megőrzése'
description: Megtudhatja, hogyan tárolhatja és állíthatja vissza az automatizált biztonsági mentéseket egy Azure SQL felügyelt példány különálló Azure Blob Storage-tárolójában a PowerShell használatával.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: shkale-msft
ms.author: shkale
ms.reviewer: mathoma, sstein
ms.date: 02/25/2021
ms.openlocfilehash: f298f0f9d76750be932db79b5a08b6385e984f88
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102052026"
---
# <a name="manage-azure-sql-managed-instance-long-term-backup-retention-powershell"></a>Az Azure SQL felügyelt példányainak hosszú távú biztonsági mentési megőrzése (PowerShell) kezelése
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az Azure SQL felügyelt példányain egy [hosszú távú biztonsági mentési adatmegőrzési](../database/long-term-retention-overview.md) szabályzatot (ltr) is beállíthat nyilvános előzetes verzióként. Ez lehetővé teszi az adatbázis biztonsági másolatainak automatikus megőrzését különálló Azure Blob Storage-tárolókban akár 10 évig. Ezt követően a PowerShell használatával helyreállíthat egy adatbázist a biztonsági másolatokkal.

   > [!IMPORTANT]
   > A felügyelt példányok LTR jelenleg nyilvános előzetes verzióban érhető el az Azure nyilvános régióiban. 

A következő részben bemutatjuk, hogyan használható a PowerShell a biztonsági másolatok hosszú távú megőrzésének konfigurálására, a biztonsági mentések megtekintésére az Azure SQL Storage szolgáltatásban, valamint az Azure SQL Storage biztonsági másolatából való visszaállítás.


## <a name="using-the-azure-portal"></a>Az Azure Portal használata

A következő részben bemutatjuk, hogyan használhatja a Azure Portal a hosszú távú adatmegőrzési házirendek beállítására, a rendelkezésre álló hosszú távú adatmegőrzési biztonsági másolatok kezelésére, valamint a rendelkezésre álló biztonsági másolatból történő visszaállításra.

### <a name="configure-long-term-retention-policies"></a>Hosszú távú adatmegőrzési szabályzatok konfigurálása

Az SQL felügyelt példányát úgy is beállíthatja, hogy az [automatizált biztonsági mentéseket](../database/long-term-retention-overview.md) a szolgáltatási szinten megőrzött időtartamnál hosszabb ideig őrizze meg.

1. A Azure Portal válassza ki a felügyelt példányt, majd kattintson a **biztonsági mentések** elemre. Az **adatmegőrzési házirendek** lapon válassza ki azokat az adatbázis (oka) t, amelyeken a biztonsági másolatok hosszú távú megőrzési szabályzatait be szeretné állítani vagy módosítani kívánja. A módosítások nem lesznek érvényesek egyetlen nem kijelölt adatbázisra sem. 

   ![biztonsági másolatok kezelése hivatkozás](./media/long-term-backup-retention-configure/ltr-configure-ltr.png)

2. A **házirendek konfigurálása** panelen adja meg a heti, havi vagy éves biztonsági mentések kívánt megőrzési időtartamát. Válasszon egy "0" megőrzési időtartamot annak jelzésére, hogy a biztonsági másolatok hosszú távú megőrzését nem kell beállítani.

   ![házirendek konfigurálása](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

3. Ha elkészült, kattintson az **alkalmaz** gombra.

> [!IMPORTANT]
> Ha engedélyezi a biztonsági másolatok hosszú távú megőrzési szabályát, akkor akár 7 napig is eltarthat, amíg az első biztonsági mentés láthatóvá válik, és visszaállítható. A LTR biztonsági mentési cadance kapcsolatos részletekért lásd a [biztonsági másolatok hosszú távú megőrzését](../database/long-term-retention-overview.md)ismertető témakört.

### <a name="view-backups-and-restore-from-a-backup"></a>Biztonsági másolatok megtekintése és visszaállítás biztonsági másolatból

Megtekintheti az adott adatbázis számára a LTR házirenddel megőrzött biztonsági másolatokat, és visszaállíthatja azokat a biztonsági másolatokból.

1. A Azure Portal válassza ki a felügyelt példányt, majd kattintson a **biztonsági mentések** elemre. A **rendelkezésre álló biztonsági másolatok** lapon válassza ki azt az adatbázist, amelynek elérhető biztonsági másolatait szeretné megtekinteni. Kattintson a **Kezelés** gombra.

   ![adatbázis kiválasztása](./media/long-term-backup-retention-configure/ltr-available-backups-select-database.png)

1. A **biztonsági másolatok kezelése** panelen tekintse át az elérhető biztonsági másolatokat.

   ![biztonsági másolatok megtekintése](./media/long-term-backup-retention-configure/ltr-available-backups.png)

1. Válassza ki azt a biztonsági másolatot, amelyről vissza kívánja állítani a visszaállítást **, kattintson a visszaállítás** elemre, majd a visszaállítás lapon adja meg az új adatbázis nevét. A biztonsági mentés és a forrás előre fel lesz töltve ezen az oldalon. 

   ![biztonsági másolat kiválasztása visszaállításhoz](./media/long-term-backup-retention-configure/ltr-available-backups-restore.png)
   
   ![visszaállítás](./media/long-term-backup-retention-configure/ltr-restore.png)

1. A visszaállítási részletek áttekintéséhez kattintson a **felülvizsgálat + létrehozás** elemre. Ezután kattintson a **Létrehozás** gombra az adatbázis visszaállításához a kiválasztott biztonsági másolatból.

1. Az eszköztáron kattintson az értesítési ikonra a visszaállítási feladat állapotának megtekintéséhez.

   ![visszaállítási feladat állapota](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. A visszaállítási feladatok befejezése után nyissa meg a **felügyelt példány áttekintése** lapot az újonnan visszaállított adatbázis megtekintéséhez.

> [!NOTE]
> Innen az SQL Server Management Studióval csatlakozhat a visszaállított adatbázishoz a szükséges feladatok végrehajtásához, például [egy adatelem kinyeréséhez a visszaállított adatbázisból a meglévő adatbázisba való beillesztés érdekében, vagy a meglévő adatbázis törléséhez és a visszaállított adatbázis átnevezéséhez a meglévő adatbázis nevére](../database/recovery-using-backups.md#point-in-time-restore).


## <a name="using-powershell"></a>A PowerShell használata
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulban lesz végrehajtva. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

A következő részben bemutatjuk, hogyan használható a PowerShell a biztonsági másolatok hosszú távú megőrzésének konfigurálására, a biztonsági mentések megtekintésére az Azure Storage-ban, és hogyan lehet visszaállítani az Azure Storage biztonsági másolatából.

### <a name="azure-rbac-roles-to-manage-long-term-retention"></a>Azure RBAC-szerepkörök a hosszú távú adatmegőrzés kezeléséhez

A **Get-AzSqlInstanceDatabaseLongTermRetentionBackup** és a **Restore-AzSqlInstanceDatabase** esetében a következő szerepkörök egyikének kell lennie:

- Előfizetés tulajdonosi szerepköre vagy
- Felügyelt példány közreműködői szerepkör vagy
- Egyéni szerepkör a következő engedélyekkel:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read`

A **Remove-AzSqlInstanceDatabaseLongTermRetentionBackup** esetében a következő szerepkörök egyikének kell lennie:

- Előfizetés tulajdonosi szerepköre vagy
- Egyéni szerepkör a következő engedélyekkel:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

> [!NOTE]
> A felügyelt példány közreműködői szerepkör nem rendelkezik engedéllyel a LTR biztonsági mentések törléséhez.

Az Azure RBAC engedélyei az *előfizetés* vagy az *erőforráscsoport* hatókörében is megadhatók. Az eldobott példányhoz tartozó LTR biztonsági mentések eléréséhez azonban az engedélyt az adott példány *előfizetési* hatókörében kell megadni.

- `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

### <a name="create-an-ltr-policy"></a>LTR szabályzat létrehozása

```powershell
# get the Managed Instance
$subId = "<subscriptionId>"
$instanceName = "<instanceName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount

Select-AzSubscription -SubscriptionId $subId

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W' 
    YearlyRetention = 'P5Y' 
    WeekOfYear = '16'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

### <a name="view-ltr-policies"></a>LTR szabályzatok megtekintése

Ebből a példából megtudhatja, hogyan listázhatja egy példányon belüli LTR-házirendeket egy adott adatbázishoz

```powershell
# gets the current version of LTR policy for a database
$LTRPolicies = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup
}
Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy 
```

Ebből a példából megtudhatja, hogyan listázhatja a példányok összes adatbázisának LTR szabályzatait

```powershell
# gets the current version of LTR policy for all of the databases on an instance

$Databases = Get-AzSqlInstanceDatabase -ResourceGroupName $resourceGroup -InstanceName $instanceName

$LTRParams = @{
    InstanceName = $instanceName
    ResourceGroupName = $resourceGroup
}

foreach($database in $Databases.Name){
    Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRParams  -DatabaseName $database
 }
```

### <a name="clear-an-ltr-policy"></a>LTR szabályzat törlése

Ez a példa bemutatja, hogyan törölhet egy LTR-szabályzatot egy adatbázisból.

```powershell
# remove the LTR policy from a database
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    RemovePolicy = $true
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

### <a name="view-ltr-backups"></a>LTR biztonsági mentések megtekintése

Ez a példa bemutatja, hogyan listázhatja a LTR biztonsági másolatait egy példányon belül.

```powershell

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$LTRBackupParam = @{
    Location = $instance.Location 
    DatabaseState = 'Live'
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# only list the latest LTR backup for each database
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    OnlyLatestPerDatabase = $true
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 
```

### <a name="delete-ltr-backups"></a>LTR biztonsági mentések törlése

Ebből a példából megtudhatja, hogyan törölhet egy LTR biztonsági másolatot a biztonsági mentések listájáról.

```powershell
# remove the earliest backup
# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> A LTR biztonsági mentésének törlése nem fordítható vissza. Ha törölni szeretne egy LTR biztonsági mentést a példány törlése után, rendelkeznie kell előfizetés-hatókör engedéllyel. Értesítéseket állíthat be az egyes törlésekről Azure Monitor a "hosszú távú adatmegőrzési biztonsági mentés törlése" művelet szűrésével. A tevékenység naplója információt tartalmaz arról, hogy ki és mikor kezdeményezte a kérést. Részletes utasításokért tekintse meg a [Tevékenységnaplók létrehozása – riasztások](../../azure-monitor/alerts/alerts-activity-log.md) című témakört.

### <a name="restore-from-ltr-backups"></a>Visszaállítás a LTR biztonsági mentésből

Ez a példa azt szemlélteti, hogyan lehet visszaállítani egy LTR biztonsági másolatból. Vegye figyelembe, hogy ez az illesztőfelület nem változott, de az erőforrás-azonosító paraméter most a LTR biztonsági mentési erőforrás-AZONOSÍTÓját igényli.

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbname
    OnlyLatestPerDatabase = $true
}
$ltrBackup = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 

$RestoreLTRParam = @{
    TargetInstanceName          = $instanceName 
    TargetResourceGroupName     = $resourceGroup 
    TargetInstanceDatabaseName  = $dbName
    FromLongTermRetentionBackup = $true
    ResourceId                  = $ltrBackup.ResourceId 
}
Restore-AzSqlInstanceDatabase @RestoreLTRParam
```

> [!IMPORTANT]
> Ha a példányt a példány törlése után szeretné visszaállítani egy LTR biztonsági másolatból, rendelkeznie kell a példány előfizetéséhez tartozó engedélyekkel, és az előfizetésnek aktívnak kell lennie. A nem kötelező-ResourceGroupName paramétert is el kell hagyni.

> [!NOTE]
> Innen az SQL Server Management Studióval csatlakozhat a visszaállított adatbázishoz a szükséges feladatok végrehajtásához, például egy adatelem kinyeréséhez a visszaállított adatbázisból a meglévő adatbázisba való beillesztés érdekében, vagy a meglévő adatbázis törléséhez és a visszaállított adatbázis átnevezéséhez a meglévő adatbázis nevére. Lásd: [időponthoz való visszaállítás](../database/recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Következő lépések

- A szolgáltatás által létrehozott automatikus biztonsági másolatokkal kapcsolatos további információkért lásd az [automatikus biztonsági másolatokkal](../database/automated-backups-overview.md) foglalkozó témakört.
- A biztonsági másolatok hosszú távú megőrzésével kapcsolatos további információkért lásd: [biztonsági másolatok hosszú távú megőrzése](../database/long-term-retention-overview.md)
