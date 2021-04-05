---
title: Felügyeleti műveletek megszakítása
titleSuffix: Azure SQL Managed Instance
description: Megtudhatja, hogyan szakíthatja meg az Azure SQL felügyelt példányok kezelési műveleteit.
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
ms.openlocfilehash: 342491178d55dacbdc68e6c9042623d381dff898
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861544"
---
# <a name="canceling-azure-sql-managed-instance-management-operations"></a>Az Azure SQL felügyelt példányok kezelési műveleteinek megszakítása
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az Azure SQL felügyelt példánya lehetővé teszi bizonyos [felügyeleti műveletek](management-operations-overview.md)megszakítását, például új felügyelt példány vagy frissítési példány tulajdonságainak központi telepítésekor. 

## <a name="overview"></a>Áttekintés

 A felügyeleti műveleteket a következőképpen lehet kategorizálni:

- Példány üzembe helyezése (új példány létrehozása).
- Példány frissítése (példány tulajdonságainak módosítása, például virtuális mag vagy fenntartott tároló).
- Példány törlése.

[Figyelemmel kísérheti a felügyeleti műveletek előrehaladását és állapotát](management-operations-monitor.md) , és szükség esetén megszakíthat néhányat. 

A következő táblázat összefoglalja a felügyeleti műveleteket, függetlenül attól, hogy megszakíthatja őket, és jellemző teljes időtartama:

Kategória  |Művelet  |Kampány  |Becsült megszakítási időtartam  |
|---------|---------|---------|---------|
|Üzembe helyezés |Példány létrehozása |Yes |a műveletek 90%-a 5 percen belül befejeződik. |
|Frissítés |A példány tárolási felskálázása felfelé/lefelé (általános célú) |No |  |
|Frissítés |A példány tárolási felskálázása felfelé/lefelé (üzletileg kritikus) |Yes |a műveletek 90%-a 5 percen belül befejeződik. |
|Frissítés |A példány számítási (virtuális mag) méretezése felfelé és lefelé (általános célú) |Yes |a műveletek 90%-a 5 percen belül befejeződik. |
|Frissítés |A példány számítási (virtuális mag) méretezése felfelé és lefelé (üzletileg kritikus) |Yes |a műveletek 90%-a 5 percen belül befejeződik. |
|Frissítés |Példány szolgáltatási szintjeinek változása (általános célú üzletileg kritikus és fordítva) |Yes |a műveletek 90%-a 5 percen belül befejeződik. |
|Törlés |Példány törlése |No |  |
|Törlés |Virtuális fürt törlése (felhasználó által kezdeményezett művelet) |No |  |

## <a name="cancel-management-operation"></a>Felügyeleti művelet megszakítása

# <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Portal használatával történő felügyeleti műveletek megszakításához kövesse az alábbi lépéseket:

1. Ugrás a [Azure Portal](https://portal.azure.com)
1. Nyissa meg az SQL felügyelt példányának **Áttekintés** paneljét. 
1. Kattintson a folyamatban lévő művelet melletti **értesítési** mezőre a **folyamatban lévő művelet** oldalának megnyitásához. 

   :::image type="content" source="media/management-operations-cancel/open-ongoing-operation.png" alt-text="Kattintson a folyamatban lévő művelet mezőre a folyamatban lévő művelet lap megnyitásához.":::

1. Válassza a lap alján található **művelet megszakítása** lehetőséget. 

   :::image type="content" source="media/management-operations-cancel/cancel-operation.png" alt-text="A művelet megszakításához kattintson a Mégse gombra.":::

1. Erősítse meg, hogy meg kívánja szakítani a műveletet. 


Ha a megszakítási kérelem sikeres, a felügyeleti művelet megszakad, és hibát eredményez. Értesítést kap arról, hogy a megszakítás sikeres vagy sikertelen lesz.

![Művelet eredményének megszakítása](./media/management-operations-cancel/canceling-operation-result.png)


Ha a megszakítási kérelem meghiúsul, vagy a Mégse gomb nem aktív, az azt jelenti, hogy a felügyeleti művelet nem megszakítható állapotba került, és hamarosan befejeződik.  A felügyeleti művelet addig folytatja a végrehajtást, amíg be nem fejeződik.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha még nincs Azure PowerShell telepítve, tekintse meg [a Azure PowerShell modul telepítését](/powershell/azure/install-az-ps)ismertető témakört.

A kezelési művelet megszakításához meg kell adnia a felügyeleti művelet nevét. Ezért először használja a Get parancsot a műveletek listájának lekéréséhez, majd a művelet megszakításához.

```powershell-interactive
$managedInstance = "<Your-instance-name>"
$resourceGroup = "<Your-resource-group-name>"

$managementOperations = Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup

foreach ($mo in $managementOperations ) {
    if($mo.State -eq "InProgress" -and $mo.IsCancellable){
        $cancelRequest = Stop-AzSqlInstanceOperation -ResourceGroupName $resourceGroup -ManagedInstanceName $managedInstance -Name $mo.Name
        Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup -Name $mo.Name
    }
}
```

A részletes parancsok magyarázatát lásd: [Get-AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation) és [stop-AzSqlInstanceOperation](/powershell/module/az.sql/stop-azsqlinstanceoperation).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha még nem telepítette az Azure CLI-t, tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört.

A kezelési művelet megszakításához meg kell adnia a felügyeleti művelet nevét. Ezért először használja a Get parancsot a művelet listájának lekéréséhez, majd szakítsa meg az adott műveletet.

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName |
   --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do

az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

Részletes parancsok magyarázata: [az SQL mi op](/cli/azure/sql/mi/op).

---

## <a name="canceled-deployment-request"></a>Megszakított központi telepítési kérelem

A 2020-02-02-es API-verzióval a példány-létrehozási kérelem elfogadása után a példány erőforrásként kezd megjelenni, függetlenül attól, hogy a telepítési folyamat állapota (felügyelt példány állapota **kiépítés**). Ha megszakítja a példány központi telepítési kérését (az új példány létrehozása), a felügyelt példány a **kiépítési** állapotból a **FailedToCreate**-be kerül.

A nem sikerült létrehozni kívánt példányok továbbra is erőforrásként jelennek meg, és: 

- Nem számítunk fel díjat
- Ne számítson bele az erőforrás-korlátokba (alhálózat vagy virtuális mag kvóta)


> [!NOTE]
> Az erőforrások vagy a felügyelt példányok listáján a zaj csökkentése érdekében törölje azokat a példányokat, amelyek nem tudtak telepíteni vagy leállított központi telepítésekkel rendelkező példányokat. 


## <a name="next-steps"></a>Következő lépések

- Az első felügyelt példány létrehozásával kapcsolatos információkért lásd: gyors [útmutató](instance-create-quickstart.md).
- A szolgáltatások és az összehasonlítások listájáért lásd: [általános SQL-funkciók](../database/features-comparison.md).
- További információ a VNet konfigurálásáról: [SQL felügyelt példány VNet konfigurációja](connectivity-architecture-overview.md).
- Egy felügyelt példányt létrehozó gyors útmutató, valamint egy adatbázis biztonságimásolat-fájlból való visszaállítása: [felügyelt példány létrehozása](instance-create-quickstart.md).
- A Azure Database Migration Service áttelepítésre való használatával kapcsolatos oktatóanyagért lásd: [SQL felügyelt példányok áttelepítése Database Migration Service használatával](../../dms/tutorial-sql-server-to-managed-instance.md).
