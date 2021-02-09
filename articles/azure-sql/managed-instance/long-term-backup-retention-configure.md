---
title: 'Felügyelt Azure SQL-példány: hosszú távú biztonsági mentés megőrzése (PowerShell)'
description: Megtudhatja, hogyan tárolhatja és állíthatja vissza az automatizált biztonsági mentéseket egy Azure SQL felügyelt példány különálló Azure Blob Storage-tárolójában a PowerShell használatával.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 04/29/2020
ms.openlocfilehash: a5a2ff85395a55bcd4e8405e2eb60c6a4645818c
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833439"
---
# <a name="manage-azure-sql-managed-instance-long-term-backup-retention-powershell"></a>Az Azure SQL felügyelt példányainak hosszú távú biztonsági mentési megőrzése (PowerShell) kezelése
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az Azure SQL felügyelt példányain a [biztonsági mentés hosszú távú megőrzési](../database/long-term-retention-overview.md#sql-managed-instance-support) szabályzatát (ltr) korlátozott nyilvános előzetes verzióként is konfigurálhatja. Ez lehetővé teszi az adatbázis biztonsági másolatainak automatikus megőrzését különálló Azure Blob Storage-tárolókban akár 10 évig. Ezt követően a PowerShell használatával helyreállíthat egy adatbázist a biztonsági másolatokkal.

   > [!IMPORTANT]
   > A felügyelt példányok LTR jelenleg korlátozott előzetes verzióban érhető el, és az EA-és CSP-előfizetések esetében eseti alapon érhető el. A regisztráció igényléséhez hozzon létre egy [Azure-támogatási jegyet](https://azure.microsoft.com/support/create-ticket/). A probléma típusa beállításnál válassza a technikai probléma, SQL Database felügyelt példány lehetőséget, majd a probléma típusa beállításnál válassza a **biztonsági mentés, visszaállítás és Üzletmenet-folytonosság/hosszú távú biztonsági mentés** lehetőséget. A kérelemben adja meg, hogy a felügyelt példányhoz tartozó LTR korlátozott nyilvános előzetes verziójával szeretne-e regisztrálni.

A következő részben bemutatjuk, hogyan használható a PowerShell a biztonsági másolatok hosszú távú megőrzésének konfigurálására, a biztonsági mentések megtekintésére az Azure SQL Storage szolgáltatásban, valamint az Azure SQL Storage biztonsági másolatából való visszaállítás.

## <a name="azure-roles-to-manage-long-term-retention"></a>Azure-szerepkörök a hosszú távú adatmegőrzés kezeléséhez

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

## <a name="create-an-ltr-policy"></a>LTR szabályzat létrehozása

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

## <a name="view-ltr-policies"></a>LTR szabályzatok megtekintése

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

## <a name="clear-an-ltr-policy"></a>LTR szabályzat törlése

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

## <a name="view-ltr-backups"></a>LTR biztonsági mentések megtekintése

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

## <a name="delete-ltr-backups"></a>LTR biztonsági mentések törlése

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
> A LTR biztonsági mentésének törlése nem fordítható vissza. Ha törölni szeretne egy LTR biztonsági mentést a példány törlése után, rendelkeznie kell előfizetés-hatókör engedéllyel. Értesítéseket állíthat be az egyes törlésekről Azure Monitor a "hosszú távú adatmegőrzési biztonsági mentés törlése" művelet szűrésével. A tevékenység naplója információt tartalmaz arról, hogy ki és mikor kezdeményezte a kérést. Részletes utasításokért tekintse meg a [Tevékenységnaplók létrehozása – riasztások](../../azure-monitor/platform/alerts-activity-log.md) című témakört.

## <a name="restore-from-ltr-backups"></a>Visszaállítás a LTR biztonsági mentésből

Ez a példa azt szemlélteti, hogyan lehet visszaállítani egy LTR biztonsági másolatból. Vegye figyelembe, hogy ez az illesztőfelület nem változott, de az erőforrás-azonosító paraméter most a LTR biztonsági mentési erőforrás-azonosítóját igényli.

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
