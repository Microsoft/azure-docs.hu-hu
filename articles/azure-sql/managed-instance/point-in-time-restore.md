---
title: Időponthoz tartozó visszaállítás (PITR)
titleSuffix: Azure SQL Managed Instance
description: Adatbázis visszaállítása az Azure SQL felügyelt példányain egy korábbi időpontra.
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
ms.openlocfilehash: 0a56cfc147d4fb5cbdccf13363ad28bc602d8216
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182757"
---
# <a name="restore-a-database-in-azure-sql-managed-instance-to-a-previous-point-in-time"></a>Adatbázis visszaállítása az Azure SQL felügyelt példányában egy korábbi időpontra
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az időponthoz tartozó visszaállítás (PITR) használatával létrehozhat egy adatbázist egy másik adatbázis másolatával egy korábbi időpontban. Ez a cikk azt ismerteti, hogyan végezhető el az adatbázisok adott időpontban történő visszaállítása az Azure SQL felügyelt példányain.

Az időponthoz való visszaállítás hasznos olyan helyreállítási helyzetekben, mint például a hibák által okozott incidensek, az adatok helytelen betöltése vagy a kritikus adatok törlése. Egyszerűen tesztelésre vagy naplózásra is használható. A biztonságimásolat-fájlok az adatbázis beállításaitól függően 7 – 35 napig tartanak.

Az időponthoz tartozó visszaállítás egy adatbázist állíthat vissza:

- egy meglévő adatbázisból.
- egy törölt adatbázisból.
- ugyanahhoz az SQL felügyelt példányhoz vagy egy másik SQL felügyelt példányhoz. 

## <a name="limitations"></a>Korlátozások

Az SQL felügyelt példányának időponthoz való visszaállítása a következő korlátozásokkal jár:

- Ha egy SQL felügyelt példány egy példányáról egy másikra állítja vissza, mindkét példánynak ugyanabban az előfizetésben és régióban kell lennie. A régiók közötti és az előfizetések közötti visszaállítás jelenleg nem támogatott.
- A teljes SQL-alapú felügyelt példányok időponthoz való visszaállítása nem lehetséges. Ez a cikk csak azt ismerteti, hogy milyen lehetséges: az SQL felügyelt példányán üzemeltetett adatbázis időponthoz tartozó visszaállítása.

> [!WARNING]
> Vegye figyelembe az SQL felügyelt példányának tárolási méretét. A visszaállítani kívánt adatmérettől függően elfogyhat a példányok tárolója. Ha nincs elég hely a visszaállított adatmennyiséghez, használjon más megközelítést.

Az alábbi táblázat az SQL felügyelt példányának időponthoz kapcsolódó visszaállítási forgatókönyveit mutatja be:

|           |Meglévő adatbázis visszaállítása a felügyelt SQL-példány ugyanazon példányára| Meglévő adatbázis visszaállítása másik SQL felügyelt példányra|Az eldobott adatbázis visszaállítása ugyanazon SQL felügyelt példányra|Az eldobott adatbázis visszaállítása egy másik felügyelt SQL-példányra|
|:----------|:----------|:----------|:----------|:----------|
|**Azure Portal**| Igen|Nem |Igen|Nem|
|**Azure CLI**|Igen |Igen |Nem|Nem|
|**PowerShell**| Igen|Igen |Igen|Yes|

## <a name="restore-an-existing-database"></a>Meglévő adatbázis visszaállítása

A Azure Portal, a PowerShell vagy az Azure CLI használatával visszaállíthat egy meglévő adatbázist ugyanahhoz az SQL felügyelt példányhoz. Ha egy adatbázist egy másik felügyelt SQL-példányra kíván visszaállítani, használja a PowerShellt vagy az Azure CLI-t, így megadhatja a cél SQL felügyelt példány és erőforráscsoport tulajdonságait. Ha nem határozza meg ezeket a paramétereket, a rendszer alapértelmezés szerint visszaállítja az adatbázist a meglévő SQL felügyelt példányra. A Azure Portal jelenleg nem támogatja a visszaállítást egy másik SQL felügyelt példányra.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. Nyissa meg az SQL felügyelt példányát, és válassza ki a visszaállítani kívánt adatbázist.
3. Válassza a **visszaállítás** lehetőséget az adatbázis lapon:

    ![Adatbázis visszaállítása a Azure Portal használatával](./media/point-in-time-restore/restore-database-to-mi.png)

4. A **visszaállítás** lapon válassza ki azt a dátumot és időpontot, amelyre vissza kívánja állítani az adatbázist.
5. Az adatbázis visszaállításához kattintson a **Confirm (megerősítés** ) gombra. Ez a művelet elindítja a visszaállítási folyamatot, amely létrehoz egy új adatbázist, és feltölti azt az eredeti adatbázisból származó adatokkal a megadott időpontban. A helyreállítási folyamattal kapcsolatos további információkért lásd: [helyreállítás ideje](../database/recovery-using-backups.md#recovery-time).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha még nincs Azure PowerShell telepítve, tekintse meg [a Azure PowerShell modul telepítését](/powershell/azure/install-az-ps)ismertető témakört.

Az adatbázis PowerShell használatával történő visszaállításához adja meg a paraméterek értékét a következő parancsban. Ezután futtassa a parancsot:

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

Ha vissza szeretné állítani az adatbázist egy másik SQL-felügyelt példányra, adja meg a cél erőforráscsoport és a célként megadott SQL felügyelt példány nevét is:  

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

Részletekért lásd: [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha még nem telepítette az Azure CLI-t, tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört.

Az adatbázis Azure CLI használatával történő visszaállításához adja meg a paraméterek értékét a következő parancsban. Ezután futtassa a parancsot:

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Ha vissza szeretné állítani az adatbázist egy másik felügyelt SQL-példányra, adja meg a cél erőforráscsoport és az SQL felügyelt példány nevét is:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Az elérhető paraméterek részletes ismertetését lásd a [CLI dokumentációjában, amely az adatbázisok SQL felügyelt példányban történő visszaállítását](/cli/azure/sql/midb#az-sql-midb-restore)ismerteti.

---

## <a name="restore-a-deleted-database"></a>Törölt adatbázis visszaállítása

A törölt adatbázisok visszaállítása a PowerShell vagy a Azure Portal használatával végezhető el. Ha egy törölt adatbázist ugyanarra a példányra kíván visszaállítani, használja a Azure Portal vagy a PowerShellt. A törölt adatbázisok másik példányra való visszaállításához használja a PowerShellt. 

### <a name="portal"></a>Portál 


Felügyelt adatbázis helyreállításához a Azure Portal segítségével nyissa meg az SQL felügyelt példányának áttekintés lapját, és válassza a **törölt adatbázisok** lehetőséget. Válassza ki a visszaállítani kívánt törölt adatbázist, és írja be az új adatbázis nevét, amely a biztonsági másolatból visszaállított adatokkal lesz létrehozva.

  ![Képernyőkép a törölt Azure SQL-példány-adatbázis visszaállításáról](./media/point-in-time-restore/restore-deleted-sql-managed-instance-annotated.png)

.. /.. /sql-database

### <a name="powershell"></a>PowerShell

Ha az adatbázist ugyanarra a példányra szeretné visszaállítani, frissítse a paramétereket, majd futtassa a következő PowerShell-parancsot: 

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

Ha vissza szeretné állítani az adatbázist egy másik SQL-felügyelt példányra, adja meg a cél erőforráscsoport és a célként megadott SQL felügyelt példány nevét is:

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

Meglévő adatbázis felülírásához a következőket kell tennie:

1. Dobja el a felülírni kívánt meglévő adatbázist.
2. Nevezze át az időponthoz visszaállított adatbázist az eldobott adatbázis nevére.

### <a name="drop-the-original-database"></a>Az eredeti adatbázis eldobása

Az adatbázist a Azure Portal, a PowerShell vagy az Azure CLI használatával lehet eldobni.

Az adatbázist közvetlenül az SQL felügyelt példányhoz való csatlakozással is elvégezheti, SQL Server Management Studio elindításával (SSMS), majd futtathatja a következő Transact-SQL (T-SQL) parancsot:

```sql
DROP DATABASE WorldWideImporters;
```

Az alábbi módszerek egyikével csatlakozhat az adatbázishoz az SQL felügyelt példányában:

- [SSMS/Azure Data Studio Azure-beli virtuális gépen keresztül](./connect-vm-instance-configure.md)
- [Pont–hely kapcsolat](./point-to-site-p2s-configure.md)
- [Nyilvános végpont](./public-endpoint-configure.md)

# <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Portal válassza ki az adatbázist a felügyelt SQL-példányból, majd válassza a **Törlés** lehetőséget.

   ![Adatbázis törlése a Azure Portal használatával](./media/point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A következő PowerShell-paranccsal elhúzhatja a meglévő adatbázist egy SQL-felügyelt példányból:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A következő Azure CLI-paranccsal elhúzhatja a meglévő adatbázist egy SQL-felügyelt példányból:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Módosítsa az új adatbázisnevet, hogy az megfeleljen az eredeti adatbázis nevének.

Kapcsolódjon közvetlenül az SQL felügyelt példányához, és indítsa el SQL Server Management Studio. Ezután futtassa a következő Transact-SQL (T-SQL) lekérdezést. A lekérdezés módosítja a visszaállított adatbázis nevét a felülírni kívánt eldobott adatbázisra.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Az alábbi módszerek egyikével csatlakozhat az adatbázishoz az SQL felügyelt példányában:

- [Azure-beli virtuális gép](./connect-vm-instance-configure.md)
- [Pont–hely kapcsolat](./point-to-site-p2s-configure.md)
- [Nyilvános végpont](./public-endpoint-configure.md)

## <a name="next-steps"></a>Következő lépések

További információ az [automatizált biztonsági mentésekről](../database/automated-backups-overview.md).
