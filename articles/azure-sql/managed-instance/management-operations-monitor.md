---
title: Felügyeleti műveletek figyelése
titleSuffix: Azure SQL Managed Instance
description: Ismerje meg az Azure SQL felügyelt példányok kezelési műveleteinek figyelésének különböző módszereit.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, bonova, MashaMSFT
ms.date: 09/03/2020
ms.openlocfilehash: 6cdd3137798e221974dadda78dd55b6ae944bc78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589346"
---
# <a name="monitoring-azure-sql-managed-instance-management-operations"></a>Az Azure SQL felügyelt példányok kezelési műveleteinek figyelése
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az Azure SQL felügyelt példánya az új felügyelt példányok telepítéséhez, a példány tulajdonságainak frissítéséhez vagy a példányok törléséhez használt [felügyeleti műveletek](management-operations-overview.md) figyelését biztosítja, ha már nincs rá szükség. 

## <a name="overview"></a>Áttekintés

A felügyeleti műveleteket a következőképpen lehet kategorizálni:

- Példány üzembe helyezése (új példány létrehozása).
- Példány frissítése (példány tulajdonságainak módosítása, például virtuális mag vagy fenntartott tároló).
- Példány törlése.

A legtöbb felügyeleti művelet [hosszú ideig futó művelet](management-operations-overview.md#duration). Ezért figyelnie kell az állapotot, vagy követnie kell a művelet lépéseit. 

A felügyelt példányok felügyeleti műveleteinek figyelésére többféle módszer áll rendelkezésre:

- [Erőforráscsoport-telepítések](../../azure-resource-manager/templates/deployment-history.md)
- [Tevékenységnapló](../../azure-monitor/essentials/activity-log.md)
- [Felügyelt példány műveleti API](#managed-instance-operations-api)


A következő táblázat összehasonlítja a felügyeleti művelet figyelési lehetőségeit: 

| Beállítás | Megőrzés | A megszakítás támogatása | Létrehozás | Frissítés | Törlés | Mégse | Lépések |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Erőforráscsoport-telepítések | Végtelen<sup>1</sup> | Nem<sup>2</sup> | Látható | Látható | Nem látható | Látható | Nem látható |
| Tevékenységnapló | 90 nap | No | Látható | Látható | Látható | Látható |  Nem látható |
| Felügyelt példány műveleti API | 24 óra | [Igen](management-operations-cancel.md) | Látható | Látható | Látható | Látható | Látható |
|  |  |  |  |  |  |  | |

<sup>1</sup> az erőforráscsoport üzembe helyezési előzményei a 800-es üzemelő példányokra korlátozódnak.

<sup>2</sup> az erőforráscsoport-telepítések támogatják a megszakítási műveletet. A megszakítási logika miatt azonban csak a telepítésre ütemezett művelet lesz megszakítva a megszakítási művelet végrehajtása után. A folyamatos üzembe helyezés nem szakad meg, ha az erőforráscsoport központi telepítését megszakították. Mivel a felügyelt példányok üzembe helyezése egy hosszan futó lépésből áll (az Azure Resource Manger perspektívából), az erőforráscsoport központi telepítésének megszakítása nem szakítja meg a felügyelt példányok központi telepítését, és a művelet befejeződik. 

## <a name="managed-instance-operations-api"></a>Felügyelt példány műveleti API

A felügyeleti operatív API-k kifejezetten a műveletek figyelésére lettek kialakítva. A felügyelt példányok működésének figyelése a művelet paramétereinek és a művelet lépéseinek, valamint az [adott műveletek megszakításának](management-operations-cancel.md)megadását teszi lehetővé. A művelet részletei és a Mégse parancs mellett ez az API a többerőforrásos telepítésekkel rendelkező Automation-parancsfájlokban is használható – a folyamat lépéseit követve elindíthat néhány függő erőforrás-telepítést.

Ezek az API-k: 

| Parancs | Leírás |
| --- | --- |
|[Felügyelt példányok műveletei – Get](/rest/api/sql/managedinstanceoperations/get)|Felügyeleti művelet beolvasása felügyelt példányon.|
|[Felügyelt példányok műveletei – Mégse](/rest/api/sql/managedinstanceoperations/cancel)|Megszakítja az aszinkron műveletet a felügyelt példányon.|
|[Felügyelt példányok műveletei – lista felügyelt példány alapján](/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|Lekéri a felügyelt példányon végrehajtott műveletek listáját.|

> [!NOTE]
> Az API 2020-02-02-es verziójának használatával megtekintheti a felügyelt példány létrehozása műveletet a műveletek listájában. Ez a Azure Portal és a legújabb PowerShell-és Azure CLI-csomagokban használt alapértelmezett verzió.

## <a name="monitor-operations"></a>Figyelési műveletek

# <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Portal a felügyelt példányok **áttekintő** lapja segítségével figyelheti a felügyelt példányok műveleteit. 

A **létrehozási művelet** például látható a létrehozási folyamat elején az **Áttekintés** oldalon: 

![Felügyelt példány létrehozási folyamata](./media/management-operations-monitor/monitoring-create-operation.png)

Válassza a **folyamatban lévő művelet** lehetőséget a **folyamatban lévő művelet** lap megnyitásához és a **létrehozási** vagy **frissítési** műveletek megtekintéséhez. Ezen a lapon is [megszakíthatja](management-operations-cancel.md) a műveleteket.  

![Felügyelt példány műveletének részletei](./media/management-operations-monitor/monitoring-operation-details.png)

> [!NOTE]
> A Azure Portal, a PowerShell, az Azure CLI vagy más, az REST API 2020-02-02-es verzióját használó eszközökön keresztül elküldött műveletek [megvonhatók](management-operations-cancel.md). A 2020-02-02-nál régebbi, a létrehozási művelet elküldéséhez használt verziók elindítják a példány központi telepítését, de a központi telepítés nem jelenik meg az operatív API-ban, és nem lehet megszakítani. REST API

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A Get-AzSqlInstanceOperation parancsmag a felügyelt példányokon végzett műveletekre vonatkozó információkat olvas be. Egy felügyelt példányon megtekintheti az összes műveletet, vagy megtekintheti az adott műveletet a művelet nevének megadásával.

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

$managementOperations = Get-AzSqlInstanceOperation `
    -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup
```

A részletes parancsok magyarázatát lásd: [Get-AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az az SQL mi op-lista lekéri a felügyelt példányon végrehajtott műveletek listáját. Ha még nem telepítette az Azure CLI-t, tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört.

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName 
```

Részletes parancsok magyarázata: [az SQL mi op](/cli/azure/sql/mi/op).

---

## <a name="next-steps"></a>Következő lépések

- Az első felügyelt példány létrehozásával kapcsolatos információkért lásd: gyors [útmutató](instance-create-quickstart.md).
- A szolgáltatások és az összehasonlítások listájáért lásd: [általános SQL-funkciók](../database/features-comparison.md).
- További információ a VNet konfigurálásáról: [SQL felügyelt példány VNet konfigurációja](connectivity-architecture-overview.md).
- Egy felügyelt példányt létrehozó gyors útmutató, valamint egy adatbázis biztonságimásolat-fájlból való visszaállítása: [felügyelt példány létrehozása](instance-create-quickstart.md).
- A Azure Database Migration Service áttelepítésre való használatával kapcsolatos oktatóanyagért lásd: [SQL felügyelt példányok áttelepítése Database Migration Service használatával](../../dms/tutorial-sql-server-to-managed-instance.md).