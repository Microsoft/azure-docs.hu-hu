---
title: Azure-tevékenységnaplók megtekintése az erőforrások figyelése érdekében
description: A tevékenységnaplók segítségével tekintse át a felhasználói műveleteket és hibákat. Megjeleníti Azure Portal PowerShellt, az Azure CLI-t és a REST-et.
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 7612146a0f9407663631f87c57f30ea4c590c7a4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773926"
---
# <a name="view-activity-logs-to-monitor-actions-on-resources"></a>Tevékenységnaplók megtekintése az erőforrásokon végzett műveletek figyelése érdekében

A tevékenységnaplókból a következők állapíthatók meg:

* milyen műveleteket történt az előfizetés erőforrásain
* ki indította el a műveletet
* a művelet beestekor
* a művelet állapota
* egyéb tulajdonságok értékei, amelyek segíthetnek a művelet kutatásában

A tevékenységnapló az erőforrások összes írási műveletét (PUT, POST, DELETE) tartalmazza. Olvasási műveleteket (GET) nem tartalmaz. Az erőforrás-műveletek listájáért lásd: [Azure erőforrás-szolgáltatói műveletek.](../../role-based-access-control/resource-provider-operations.md) A tevékenységnaplókból hibaelhárításkor megkeresheti a hibákat, vagy nyomon követheti, hogy a szervezete felhasználói hogyan módosították az erőforrásokat.

A tevékenységnaplók 90 napig érhetők el. Bármilyen dátumtartományt lekérdezhet, amíg a kezdő dátum legfeljebb 90 nappal korábbra esik.

A tevékenységnaplókból a portálon, a PowerShellen, az Azure CLI-en, az Insights-REST API vagy az [Insights .NET-kódtáron](https://www.nuget.org/packages/Microsoft.Azure.Insights/)keresztül lehet információkat lekérni.

## <a name="azure-portal"></a>Azure Portal

A tevékenységnaplók portálon keresztüli megtekintéséhez kövesse az alábbi lépéseket:

1. A Azure Portal válassza a **Figyelás** lehetőséget, vagy keresse meg és válassza a **Figyelás** lehetőséget bármely oldalon.

    ![Monitor kiválasztása](./media/view-activity-logs/select-monitor-from-menu.png)

1. Válassza **a Tevékenységnapló lehetőséget.**

    ![Tevékenységnapló kiválasztása](./media/view-activity-logs/select-activity-log.png)

1. Itt láthatja a legutóbbi műveletek összegzését. A rendszer az alapértelmezett szűrőkészletet alkalmazza a műveletekre. Figyelje meg, hogy az összefoglalásban szerepel, hogy ki és mikor indította el a műveletet.

    ![A legutóbbi műveletek összegzésének megtekintése](./media/view-activity-logs/audit-summary.png)

1. Előre meghatározott szűrők gyors futtatásához válassza a **gyors elemzések.**

    ![Gyors elemzések kiválasztása](./media/view-activity-logs/select-quick-insights.png)

1. Válassza ki az egyik lehetőséget. Válassza például a **Sikertelen üzembe helyezések lehetőséget** az üzemelő példányok hibáinak stb.

    ![Sikertelen üzembe helyezések kiválasztása](./media/view-activity-logs/select-failed-deployments.png)

1. Figyelje meg, hogy a szűrők úgy módosultak, hogy az üzembe helyezési hibákra összpontosítson az elmúlt 24 órában. Csak a szűrőknek megfelelő műveletek jelennek meg.

    ![Szűrők megtekintése](./media/view-activity-logs/view-filters.png)

1. Ha adott műveletekre összpontosít, módosítsa a szűrőket, vagy alkalmazson újakat. Az alábbi képen például egy új érték látható, amely a **Timespan** (Idő) és az **Resource type** (Erőforrástípus) értéke storage accounts (tárfiókok) értékre van állítva.

    ![Szűrőbeállítások megadása](./media/view-activity-logs/set-filter.png)

1. Ha később újra futtatnia kell a lekérdezést, válassza az **Aktuális szűrők rögzítése lehetőséget.**

    ![Szűrők kitűzve](./media/view-activity-logs/pin-filters.png)

1. Nevezze el a szűrőt.

    ![Névszűrők](./media/view-activity-logs/name-filters.png)

1. A szűrő az irányítópulton érhető el. Az Azure Portal menüjében válassza az **Irányítópult** lehetőséget.

    ![Szűrő megjelenítése az irányítópulton](./media/view-activity-logs/activity-log-on-dashboard.png)

1. A portálon megtekintheti egy erőforrás módosításait. Vissza alapértelmezett nézetre vált a Monitorban, és válasszon ki egy műveletet, amely egy erőforrás módosításával kapcsolatos.

    ![Művelet kiválasztása](./media/view-activity-logs/select-operation.png)

1. Válassza **az Előzmények módosítása (előzetes verzió) lehetőséget,** és válasszon egyet az elérhető műveletek közül.

    ![Változáselőzmények kiválasztása](./media/view-activity-logs/select-change-history.png)

1. Megjelennek az erőforrás változásai.

    ![Módosítások megjelenítése](./media/view-activity-logs/show-changes.png)

A módosítási előzményekről további információt az [Erőforrás-módosítások lekérte.](../../governance/resource-graph/how-to/get-resource-changes.md)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A naplóbejegyzések lekéréshez futtassa a **Get-AzLog parancsot.** További paramétereket is meg kell adnia a bejegyzések listájának szűréséhez. Ha nem ad meg kezdő és záró időpontokat, a visszaadott bejegyzések az utolsó hét napra esnek.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup
```

Az alábbi példa bemutatja, hogyan használható a tevékenységnapló a megadott időtartam alatt végzett műveletek kutatására. A kezdő és záró dátumok dátumformátumban vannak megadva.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-05-05T06:00 -EndTime 2019-05-09T06:00
```

Dátum típusú függvényekkel is megadhatja a dátumtartományt, például az elmúlt 14 napot.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
```

Egy adott felhasználó által tett műveleteket is ki lehet keresni.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
```

Szűrhet a sikertelen műveletekre.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -Status Failed
```

Egyetlen hibára összpontosíthat, ha az adott bejegyzéshez az állapotüzenetet nézi.

```azurepowershell-interactive
(Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties.Content.statusMessage | ConvertFrom-Json
```

Meghatározott értékeket kiválasztva korlátozhatja a visszaadott adatokat.

```azurepowershell-interactive
Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
```

A megadott kezdési időponttól függően az előző parancsok az erőforráscsoport műveleteinek hosszú listáját is visszaadják. Keresési feltételek megszűrése alapján szűrheti a keresett eredményeket. Szűrhet például a művelet típusa alapján.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
```

Az erőforrás Resource Graph az erőforrás módosítási előzményeit a Resource Graph használhatja. További információ: [Erőforrás-módosítások lekérte.](../../governance/resource-graph/how-to/get-resource-changes.md)

## <a name="azure-cli"></a>Azure CLI

A naplóbejegyzések lekéréséhez futtassa [az az monitor activity-log list](/cli/azure/monitor/activity-log#az_monitor_activity_log_list) parancsot eltolással az időtartomány jelzéséhez.

```azurecli-interactive
az monitor activity-log list --resource-group ExampleGroup --offset 7d
```

Az alábbi példa bemutatja, hogyan használható a tevékenységnapló az adott időszakban végzett műveletek kutatására. A kezdő és záró dátumok dátumformátumban vannak megadva.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --start-time 2019-05-01 --end-time 2019-05-15
```

Egy adott felhasználó által tett műveleteket akkor is meg lehet keresni, ha egy erőforráscsoport már nem létezik.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
```

Szűrhet a sikertelen műveletekre.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d
```

Egyetlen hibára összpontosíthat az adott bejegyzés állapotüzenetének megtekintésekor.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
```

Meghatározott értékeket kiválasztva korlátozhatja a visszaadott adatokat.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
```

A megadott kezdési időponttól függően az előző parancsok az erőforráscsoport műveleteinek hosszú listáját is visszaadják. Keresési feltételek megszűrése alapján szűrheti az eredményeket. Szűrhet például a művelet típusa alapján.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
```

Az erőforrás Resource Graph erőforrás változáselőzményének megtekintése a következővel: . További információ: [Erőforrás-módosítások lekérte.](../../governance/resource-graph/how-to/get-resource-changes.md)

## <a name="rest-api"></a>REST API

A tevékenységnaplóval végzett munka REST-műveletei az [Insights REST API](/rest/api/monitor/) részét képezik. A tevékenységnapló eseményeinek lekérésével kapcsolatban lásd: [A felügyeleti események listázása egy előfizetésben](/rest/api/monitor/activitylogs).

## <a name="next-steps"></a>Következő lépések

* Az Azure-tevékenységnaplók és a Power BI az előfizetésben végzett műveletek részletesebb elemzéséhez. Lásd: [Az Azure-tevékenységnaplók megtekintése és elemzése Power BI és egyéb szolgáltatásokban.](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)
* További információ a biztonsági szabályzatok beállításával kapcsolatban: [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC).](../../role-based-access-control/role-assignments-portal.md)
* Az alkalmazásoknak az infrastruktúrarétegtől az alkalmazástelepítésig végrehajtott változásaival kapcsolatos további részletekért lásd: [Use alkalmazásváltozási elemzés in Azure Monitor](../../azure-monitor/app/change-analysis.md).
* Az üzembe helyezési műveletek megtekintésére használható parancsokkal kapcsolatos további információkért lásd: [Telepítési műveletek megtekintése.](../templates/deployment-history.md)
* Ha szeretné megtudni, hogyan előzheti meg egy erőforrás törlését minden felhasználónál, olvassa el az Erőforrások zárolása a [Azure Resource Manager.](lock-resources.md)
* Az egyes szolgáltatókhoz elérhető műveletek listájának Microsoft Azure Resource Manager Azure erőforrás-szolgáltatói [műveleteket.](../../role-based-access-control/resource-provider-operations.md)
