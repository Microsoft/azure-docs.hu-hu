---
title: Azure-Tevékenységnaplók megtekintése az erőforrások figyeléséhez
description: A felhasználói műveletek és hibák áttekintéséhez használja a tevékenység naplóit. Azure Portal PowerShellt, az Azure CLI-t és a REST-t jeleníti meg.
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 14015e9b2792515e6818af551b8bd9f54c686bee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91371592"
---
# <a name="view-activity-logs-to-monitor-actions-on-resources"></a>Tevékenységek naplóinak megtekintése az erőforrásokon végzett műveletek figyeléséhez

A tevékenységnaplókból a következők állapíthatók meg:

* milyen műveleteket végeztek az előfizetésében lévő erőforrásokon
* a művelet elindítása
* a művelet bekövetkeztekor
* a művelet állapota
* más tulajdonságok értékei, amelyek segíthetnek a művelet megkutatásában

A tevékenységnapló az erőforrások összes írási műveletét (PUT, POST, DELETE) tartalmazza. Olvasási műveleteket (GET) nem tartalmaz. Az erőforrás-műveletek listáját az [Azure erőforrás-szolgáltatói műveletek](../../role-based-access-control/resource-provider-operations.md)című témakörben tekintheti meg. A tevékenységnaplókból hibaelhárításkor megkeresheti a hibákat, vagy nyomon követheti, hogy a szervezete felhasználói hogyan módosították az erőforrásokat.

A tevékenységnaplók 90 napig érhetők el. Bármilyen dátumtartományt lekérdezhet, amíg a kezdő dátum legfeljebb 90 nappal korábbra esik.

A tevékenység naplóiból információkat kérhet le a portálon, a PowerShellen, az Azure CLI-n, az elemzések REST APIon vagy a [.net-könyvtárban](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="azure-portal"></a>Azure Portal

Az alábbi lépéseket követve tekintheti meg a tevékenységek naplóit a portálon:

1. A Azure Portal menüben válassza a **figyelés** lehetőséget, vagy keresse meg, majd válassza a **figyelés** lehetőséget bármelyik oldalon.

    ![Figyelő kiválasztása](./media/view-activity-logs/select-monitor-from-menu.png)

1. Válassza a **műveletnapló** lehetőséget.

    ![Műveletnapló kiválasztása](./media/view-activity-logs/select-activity-log.png)

1. Ekkor megjelenik a legutóbbi műveletek összegzése. A műveletekre a szűrők alapértelmezett készlete lesz alkalmazva. Figyelje meg, hogy az összefoglalás tartalmazza-e a műveletet, és hogy mikor történt.

    ![Legutóbbi műveletek összegzésének megtekintése](./media/view-activity-logs/audit-summary.png)

1. Előre definiált szűrők gyors futtatásához válassza a **gyors elemzések** lehetőséget.

    ![Gyors áttekintések kiválasztása](./media/view-activity-logs/select-quick-insights.png)

1. Válasszon egyet a lehetőségek közül. Válassza például a **sikertelen központi telepítések** lehetőséget a központi telepítések hibáinak megtekintéséhez.

    ![Sikertelen központi telepítések kiválasztása](./media/view-activity-logs/select-failed-deployments.png)

1. Figyelje meg, hogy a szűrők úgy lettek módosítva, hogy az elmúlt 24 órában az üzembe helyezési hibákra összpontosítsanak. Csak a szűrőknek megfelelő műveletek jelennek meg.

    ![Szűrők megtekintése](./media/view-activity-logs/view-filters.png)

1. Ha konkrét műveletekre szeretne összpontosítani, módosítsa a szűrőket, vagy alkalmazzon újakat. Az alábbi képen például a **TimeSpan** és az **erőforrástípus** új értéke látható a Storage-fiókok beállításnál.

    ![Szűrő beállításainak megadása](./media/view-activity-logs/set-filter.png)

1. Ha később újra kell futtatnia a lekérdezést, válassza az **aktuális szűrők rögzítése** lehetőséget.

    ![Szűrők rögzítése](./media/view-activity-logs/pin-filters.png)

1. Adja meg a szűrő nevét.

    ![Nevek szűrői](./media/view-activity-logs/name-filters.png)

1. A szűrő elérhető az irányítópulton. Az Azure Portal menüjében válassza az **Irányítópult** lehetőséget.

    ![Szűrő megjelenítése az irányítópulton](./media/view-activity-logs/activity-log-on-dashboard.png)

1. A portálon megtekintheti az erőforrások módosításait. Térjen vissza a figyelő alapértelmezett nézetéhez, és válasszon olyan műveletet, amely egy erőforrás módosítását érintette.

    ![Művelet kiválasztása](./media/view-activity-logs/select-operation.png)

1. Válassza az **Előzmények módosítása (előzetes verzió)** lehetőséget, és válasszon egyet az elérhető műveletek közül.

    ![Változási előzmények kiválasztása](./media/view-activity-logs/select-change-history.png)

1. Megjelenik az erőforrás változásai.

    ![Változások megjelenítése](./media/view-activity-logs/show-changes.png)

További információ a változási előzményekről: [erőforrás-változások beolvasása](../../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A naplóbejegyzések beolvasásához futtassa a **Get-AzLog** parancsot. A bejegyzések listájának szűréséhez további paramétereket kell megadnia. Ha nem ad meg kezdési és befejezési időpontot, a rendszer az elmúlt hét nap bejegyzéseit adja vissza.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup
```

Az alábbi példa bemutatja, hogyan használható a tevékenység naplója egy adott időszakban végrehajtott kutatási műveletekhez. A kezdő és a záró dátumokat dátumformátum formájában kell megadni.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-05-05T06:00 -EndTime 2019-05-09T06:00
```

Emellett a Date functions használatával is megadhatja a dátumtartományt, például az elmúlt 14 napban.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
```

Egy adott felhasználó által végrehajtott műveleteket is megkeresheti.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
```

A sikertelen műveletek szűrésére is lehetőség van.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -Status Failed
```

Az adott bejegyzés állapotára vonatkozó üzenet megtekintésével az egyik hibára koncentrálhat.

```azurepowershell-interactive
(Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties.Content.statusMessage | ConvertFrom-Json
```

Kiválaszthat meghatározott értékeket a visszaadott adatok korlátozásához.

```azurepowershell-interactive
Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
```

A megadott kezdési időponttól függően az előző parancsok az erőforráscsoport műveleteinek hosszú listáját adhatják vissza. A keresési feltételek megadásával szűrheti a keresett eredményeket. Például szűrheti a művelet típusát.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
```

Az erőforrások változási előzményeit az erőforrás-gráf használatával tekintheti meg. További információ: erőforrás- [változások beolvasása](../../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="azure-cli"></a>Azure CLI

A naplóbejegyzések beolvasásához futtassa az az [monitor Activity-log List](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) parancsot egy eltolással, hogy jelezze az időtartományt.

```azurecli-interactive
az monitor activity-log list --resource-group ExampleGroup --offset 7d
```

Az alábbi példa bemutatja, hogyan használható a tevékenység naplója egy adott időszakban végrehajtott kutatási műveletekhez. A kezdő és a záró dátumokat dátumformátum formájában kell megadni.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --start-time 2019-05-01 --end-time 2019-05-15
```

Megkeresheti egy adott felhasználó által végrehajtott műveleteket, akár egy már nem létező erőforráscsoport esetében is.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
```

A sikertelen műveletek szűrésére is lehetőség van.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d
```

Az adott bejegyzés állapotára vonatkozó üzenet megtekintésével az egyik hibára koncentrálhat.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
```

Kiválaszthat meghatározott értékeket a visszaadott adatok korlátozásához.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
```

A megadott kezdési időponttól függően az előző parancsok az erőforráscsoport műveleteinek hosszú listáját adhatják vissza. A keresési feltételek megadásával szűrheti a keresett eredményeket. Például szűrheti a művelet típusát.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
```

Az erőforrások változási előzményeit az erőforrás-gráf használatával tekintheti meg. További információ: erőforrás- [változások beolvasása](../../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="rest-api"></a>REST API

A tevékenységnaplóval végzett munka REST-műveletei az [Insights REST API](/rest/api/monitor/) részét képezik. A tevékenységnapló eseményeinek lekérésével kapcsolatban lásd: [A felügyeleti események listázása egy előfizetésben](/rest/api/monitor/activitylogs).

## <a name="next-steps"></a>Következő lépések

* Az Azure-Tevékenységnaplók a Power BI használatával nagyobb információkhoz juthatnak az előfizetése műveleteivel kapcsolatban. További információ: Azure-beli [tevékenységek naplóinak megtekintése és elemzése Power bi](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* A biztonsági szabályzatok beállításával kapcsolatos további tudnivalókért tekintse meg az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)című témakört.
* Az alkalmazásoknak az infrastruktúra-rétegből való módosításaival kapcsolatos további részletekért tekintse meg az alkalmazások telepítésének az [alkalmazás-módosítási elemzés használata a Azure monitorban](../../azure-monitor/app/change-analysis.md)című témakört.
* Az üzembe helyezési műveletek megtekintésére szolgáló parancsokról az [üzembe helyezési műveletek megtekintése](../templates/deployment-history.md)című témakörben olvashat bővebben.
* Ha meg szeretné tudni, hogyan akadályozza meg az erőforrások törlését az összes felhasználó számára, tekintse meg [az erőforrások zárolása Azure Resource Manager](lock-resources.md)használatával című témakört.
* Az egyes Microsoft Azure Resource Manager-szolgáltatók számára elérhető műveletek listájának megtekintéséhez tekintse meg az [Azure erőforrás-szolgáltatói műveletek](../../role-based-access-control/resource-provider-operations.md) című témakört.
