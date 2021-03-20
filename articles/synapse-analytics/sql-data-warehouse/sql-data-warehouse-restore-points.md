---
title: Felhasználó által definiált visszaállítási pontok
description: Visszaállítási pont létrehozása dedikált SQL-készlethez (korábban SQL DW).
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 097a3132208eee98b3f95291e414263e637bc265
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96545587"
---
# <a name="user-defined-restore-points-for-a-dedicated-sql-pool-formerly-sql-dw"></a>Felhasználó által definiált visszaállítási pontok egy dedikált SQL-készlethez (korábban SQL DW)

Ebből a cikkből megtudhatja, hogyan hozhat létre egy új, felhasználó által definiált visszaállítási pontot egy dedikált SQL-készlethez (korábbi nevén SQL DW) az Azure szinapszis Analyticsben a PowerShell és a Azure Portal használatával.

## <a name="create-user-defined-restore-points-through-powershell"></a>Felhasználó által definiált visszaállítási pontok létrehozása a PowerShell-lel

Felhasználó által definiált visszaállítási pont létrehozásához használja a [New-AzSqlDatabaseRestorePoint PowerShell-](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) parancsmagot.

1. Mielőtt elkezdené, győződjön meg arról, hogy a [Azure PowerShell telepítését](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)végzi.
2. Nyissa meg a PowerShellt.
3. Kapcsolódjon az Azure-fiókjához, és sorolja fel a fiókjához társított összes előfizetést.
4. Válassza ki azt az előfizetést, amely a visszaállítani kívánt adatbázist tartalmazza.
5. Hozzon létre egy visszaállítási pontot az adattárház azonnali másolásához.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

```

6. Tekintse meg a meglévő visszaállítási pontok listáját.

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Felhasználó által definiált visszaállítási pontok létrehozása a Azure Portal

A felhasználó által definiált visszaállítási pontok a Azure Portal használatával is létrehozhatók.

1. Jelentkezzen be [Azure Portal](https://portal.azure.com/) -fiókjába.

2. Navigáljon arra a dedikált SQL-készletre (korábban SQL DW), amelyhez visszaállítási pontot kíván létrehozni.

3. Válassza az **Áttekintés** lehetőséget a bal oldali ablaktáblán, majd válassza az **+ új visszaállítási pont** lehetőséget. Ha az új visszaállítási pont gomb nincs engedélyezve, győződjön meg arról, hogy a dedikált SQL-készlet (korábbi nevén SQL DW) nincs szüneteltetve.

    ![Új visszaállítási pont](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Adja meg a felhasználó által definiált visszaállítási pont nevét, majd kattintson az **alkalmaz** gombra. A felhasználó által definiált visszaállítási pontok alapértelmezett megőrzési időtartama hét nap.

    ![A visszaállítási pont neve](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Következő lépések

- [Meglévő dedikált SQL-készlet visszaállítása (korábban SQL DW)](sql-data-warehouse-restore-active-paused-dw.md)
- [Törölt dedikált SQL-készlet visszaállítása (korábban SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
- [Visszaállítás egy földrajzi biztonsági másolattal rendelkező dedikált SQL-készletből (korábban SQL DW)](sql-data-warehouse-restore-from-geo-backup.md)
