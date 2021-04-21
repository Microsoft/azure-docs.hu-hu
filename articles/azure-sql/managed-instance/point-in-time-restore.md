---
title: Időponthoz időben való visszaállítás (PITR)
titleSuffix: Azure SQL Managed Instance
description: Visszaállít egy adatbázist Azure SQL Managed Instance egy korábbi időpontra.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: 4c116b378c72d87641157fc453d65e46be9f43ec
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787164"
---
# <a name="restore-a-database-in-azure-sql-managed-instance-to-a-previous-point-in-time"></a>Adatbázis visszaállítása Azure SQL Managed Instance korábbi időpontra
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az időponthoz időben való visszaállítással (PITR) létrehozhat egy adatbázist egy másik adatbázis másolataként egy múltbeli időpontból. Ez a cikk azt ismerteti, hogyan lehet időponthoz időben visszaállítani egy adatbázist a Azure SQL Managed Instance.

Az időponthoz időben való visszaállítás olyan helyreállítási forgatókönyvekben hasznos, mint például a hibák által okozott incidensek, a helytelenül betöltött adatok vagy a kritikus fontosságú adatok törlése. Egyszerűen tesztelésre vagy naplózásra is használhatja. A biztonsági mentési fájlokat az adatbázis beállításaitól függően 7–35 napig tárolja a rendszer.

Az időponthoz időben való visszaállítással visszaállíthat egy adatbázist:

- egy meglévő adatbázisból.
- egy törölt adatbázisból.
- ugyanannak a SQL Managed Instance vagy egy másik SQL Managed Instance. 

## <a name="limitations"></a>Korlátozások

Az időponthoz időben való visszaállítás SQL Managed Instance következő korlátozásokkal rendelkezik:

- Amikor egy példánypéldányból egy másikba SQL Managed Instance példánynak ugyanabban az előfizetésben és régióban kell lennie. A régiók közötti és az előfizetések közötti visszaállítás jelenleg nem támogatott.
- Egy teljes adatbázis időponthoz SQL Managed Instance visszaállítása nem lehetséges. Ez a cikk csak a lehetséges megoldásokat ismerteti: a szolgáltatásban üzemeltetett adatbázis időponthoz SQL Managed Instance.

> [!WARNING]
> Vegye figyelembe a tároló méretét, SQL Managed Instance. A visszaállítani kívánt adatok méretétől függően előfordulhat, hogy elfogy a példány tárterülete. Ha nincs elég hely a visszaállított adatok számára, használjon másik megközelítést.

Az alábbi táblázat az időponthoz időben való visszaállítási forgatókönyveket mutatja be a SQL Managed Instance:

|           |Meglévő adatbázis visszaállítása az adatbázis ugyanazon SQL Managed Instance| Meglévő adatbázis visszaállítása egy másik SQL Managed Instance|Eldobott adatbázis visszaállítása ugyanannak a SQL Managed Instance|Eldobott adatbázis visszaállítása egy másik SQL Managed Instance|
|:----------|:----------|:----------|:----------|:----------|
|**Azure Portal**| Igen|Nem |Igen|Nem|
|**Azure CLI**|Igen |Igen |Nem|Nem|
|**PowerShell**| Igen|Igen |Igen|Yes|

## <a name="restore-an-existing-database"></a>Meglévő adatbázis visszaállítása

Meglévő adatbázis visszaállítása ugyanannak a SQL Managed Instance a Azure Portal, a PowerShell vagy az Azure CLI használatával. Ha egy adatbázist egy másik SQL Managed Instance vissza, használja a PowerShellt vagy az Azure CLI-t, hogy meg tudja adni a célként megadott SQL Managed Instance erőforráscsoport tulajdonságait. Ha nem adja meg ezeket a paramétereket, az adatbázis alapértelmezés szerint a meglévő SQL Managed Instance lesz visszaállítva. A Azure Portal jelenleg nem támogatja a visszaállítást egy másik SQL Managed Instance.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. A saját SQL Managed Instance válassza ki a visszaállítani kívánt adatbázist.
3. Az **adatbázis oldalán** válassza a Visszaállítás lehetőséget:

    ![Adatbázis visszaállítása a Azure Portal](./media/point-in-time-restore/restore-database-to-mi.png)

4. A Visszaállítás **lapon** válassza ki annak a dátumnak és időpontnak a pontját, amelybe vissza szeretné állítani az adatbázist.
5. Az **adatbázis visszaállításához** válassza a Megerősítés lehetőséget. Ez a művelet elindítja a visszaállítási folyamatot, amely létrehoz egy új adatbázist, és feltölti az eredeti adatbázis adataival a megadott időpontban. A helyreállítási folyamattal kapcsolatos további információkért lásd: [Helyreállítási idő.](../database/recovery-using-backups.md#recovery-time)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha még nincs telepítve Azure PowerShell, tekintse meg az Install the Azure PowerShell module (Az Azure PowerShell [telepítése) modult.](/powershell/azure/install-az-ps)

Az adatbázis PowerShell használatával való visszaállításához adja meg a paraméterek értékeit a következő parancsban. Ezután futtassa a következő parancsot:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$databaseName = "<Source-database>"
$pointInTime = "2018-06-27T08:51:39.3882806Z"
$targetDatabase = "<Name of new database to be created>"

Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
```

Ha az adatbázist egy másik SQL Managed Instance vissza, adja meg a célként megadott erőforráscsoport és célként megadott SQL Managed Instance:  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target SQL Managed Instance>"
$targetInstanceName = "<Target SQL Managed Instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

Részletekért lásd: [Restore-AzSqlInstanceDatabase.](/powershell/module/az.sql/restore-azsqlinstancedatabase)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha még nincs telepítve az Azure CLI, tekintse meg [az Azure CLI telepítését.](/cli/azure/install-azure-cli)

Az adatbázis Azure CLI használatával való visszaállításához adja meg a paraméterek értékeit a következő parancsban. Ezután futtassa az parancsot:

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Ha az adatbázist egy másik SQL Managed Instance helyre, adja meg a célként megadott erőforráscsoport nevét, és adja meg SQL Managed Instance:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Az elérhető paraméterek részletes magyarázatát [](/cli/azure/sql/midb#az_sql_midb_restore)a cli-dokumentációban találhatja meg, amely egy adatbázis visszaállítását SQL Managed Instance.

---

## <a name="restore-a-deleted-database"></a>Törölt adatbázis visszaállítása

A törölt adatbázisok visszaállítása a PowerShell vagy a Azure Portal. Törölt adatbázis ugyanazon példányra való visszaállításához használja a Azure Portal Vagy a PowerShellt. Törölt adatbázis másik példányra való visszaállításához használja a PowerShellt. 

### <a name="portal"></a>Portál 


Felügyelt adatbázis helyreállításához a Azure Portal nyissa meg a SQL Managed Instance áttekintési lapját, és válassza a **Törölt adatbázisok lehetőséget.** Válassza ki a visszaállítani kívánt törölt adatbázist, és írja be annak az új adatbázisnak a nevét, amely a biztonsági másolatból visszaállított adatokkal jön létre.

  ![Képernyőkép a törölt Azure SQL példány-adatbázisról](./media/point-in-time-restore/restore-deleted-sql-managed-instance-annotated.png)

.. /.. /sql-database

### <a name="powershell"></a>PowerShell

Egy adatbázis ugyanazon példányra való visszaállításához frissítse a paraméterértékeket, majd futtassa a következő PowerShell-parancsot: 

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$deletedDatabaseName = "<Source database name>"
$targetDatabaseName = "<target database name>"

$deletedDatabase = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
-InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName

Restore-AzSqlinstanceDatabase -FromPointInTimeBackup -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName
```

Ha az adatbázist egy másik SQL Managed Instance vissza, adja meg a célként megadott erőforráscsoport és célként megadott SQL Managed Instance:

```powershell-interactive
$targetResourceGroupName = "<Resource group of target SQL Managed Instance>"
$targetInstanceName = "<Target SQL Managed Instance name>"

Restore-AzSqlinstanceDatabase -FromPointInTimeBackup -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName `
   -TargetResourceGroupName $targetResourceGroupName `
   -TargetInstanceName $targetInstanceName 
```

## <a name="overwrite-an-existing-database"></a>Meglévő adatbázis felülírása

Meglévő adatbázis felülírása a következő:

1. Hagyja el a felülírni kívánt meglévő adatbázist.
2. Nevezze át az időponthoz időben visszaállított adatbázist az eldobott adatbázis nevére.

### <a name="drop-the-original-database"></a>Az eredeti adatbázis eldobása

Az adatbázist az Azure Portal, a PowerShell vagy az Azure CLI használatával is eldobhatja.

Az adatbázist közvetlenül az adatbázishoz való csatlakozással is SQL Managed Instance, a SQL Server Management Studio (SSMS) futtatásával, majd a következő Transact-SQL (T-SQL) parancs futtatásával:

```sql
DROP DATABASE WorldWideImporters;
```

A következő módszerek egyikével csatlakozhat az adatbázishoz a SQL Managed Instance:

- [SSMS/Azure Data Studio Azure-beli virtuális gépen keresztül](./connect-vm-instance-configure.md)
- [Pont–hely kapcsolat](./point-to-site-p2s-configure.md)
- [Nyilvános végpont](./public-endpoint-configure.md)

# <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Portal válassza ki az adatbázist a SQL Managed Instance, majd válassza a **Törlés lehetőséget.**

   ![Adatbázis törlése a Azure Portal](./media/point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A következő PowerShell-paranccsal eldob egy meglévő adatbázist egy SQL Managed Instance:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A következő Azure CLI-paranccsal eldob egy meglévő adatbázist egy SQL Managed Instance:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Az új adatbázis nevének megváltoztatása az eredeti adatbázis nevével egyezőre

Csatlakozzon közvetlenül a SQL Managed Instance, és indítsa el SQL Server Management Studio. Ezután futtassa a következő Transact-SQL- (T-SQL-) lekérdezést. A lekérdezés módosítja a visszaállított adatbázis nevét arra az eldobott adatbázisra, amely felülírni szeretné.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Az alábbi módszerek egyikével csatlakozhat az adatbázishoz a SQL Managed Instance:

- [Azure-beli virtuális gép](./connect-vm-instance-configure.md)
- [Pont–hely kapcsolat](./point-to-site-p2s-configure.md)
- [Nyilvános végpont](./public-endpoint-configure.md)

## <a name="next-steps"></a>Következő lépések

Tudnivalók az [automatikus biztonsági mentésről.](../database/automated-backups-overview.md)
