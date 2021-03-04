---
title: Az Azure RBAC változásaival kapcsolatos tevékenységek naplóinak megtekintése
description: Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) változásainak megtekintése az elmúlt 90 napban.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: rolyon
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: d9b39bc9a2f00fe83cae0ff78c6346042967e8bf
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042127"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Az Azure RBAC változásaival kapcsolatos tevékenységek naplóinak megtekintése

Előfordulhat, hogy az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) változásairól, például naplózási vagy hibaelhárítási célokra vonatkozó információkra van szüksége. Ha valaki módosítja a szerepkör-hozzárendeléseket vagy a szerepkör-definíciókat az előfizetéseken belül, a módosítások bekerülnek az [Azure-tevékenység naplójába](../azure-monitor/essentials/platform-logs-overview.md). A tevékenység naplóit megtekintve megtekintheti az elmúlt 90 nap összes Azure-RBAC változását.

## <a name="operations-that-are-logged"></a>Naplózott műveletek

Itt láthatók az Azure RBAC kapcsolatos műveletek, amelyek a tevékenység naplójában vannak naplózva:

- Szerepkör-hozzárendelés létrehozása
- Szerepkör-hozzárendelés törlése
- Egyéni szerepkör-definíció létrehozása vagy frissítése
- Egyéni szerepkör-definíció törlése

## <a name="azure-portal"></a>Azure Portal

Első lépésként a legegyszerűbb módszer, ha megtekinti a tevékenységnaplókat az Azure Portalon. Az alábbi képernyőfelvételen egy példa látható a műveleti naplóban található szerepkör-hozzárendelési műveletekre. Emellett lehetőség van arra is, hogy a naplókat CSV-fájlként töltse le.

![A portált használó tevékenységek naplói – képernyőfelvétel](./media/change-history-report/activity-log-portal.png)

További információért kattintson egy bejegyzésre az összefoglalás ablaktábla megnyitásához. A részletes napló megjelenítéséhez kattintson a **JSON** fülre.

![Tevékenységek naplói a portálon az összefoglalás ablaktáblán Megnyitás – képernyőfelvétel](./media/change-history-report/activity-log-summary-portal.png)

A portálon a tevékenység naplója több szűrőt tartalmaz. Az Azure RBAC-hez kapcsolódó szűrők:

| Szűrő | Érték |
| --------- | --------- |
| Eseménykategória | <ul><li>Adminisztratív</li></ul> |
| Művelet | <ul><li>Szerepkör-hozzárendelés létrehozása</li><li>Szerepkör-hozzárendelés törlése</li><li>Egyéni szerepkör-definíció létrehozása vagy frissítése</li><li>Egyéni szerepkör-definíció törlése</li></ul> |

További információ a tevékenységi naplókról: [tevékenység-naplók megtekintése az erőforrásokon végzett műveletek figyeléséhez](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).


## <a name="interpret-a-log-entry"></a>Naplóbejegyzés értelmezése

A JSON lapon, Azure PowerShell vagy az Azure CLI-ből származó napló kimenete számos információt tartalmazhat. Íme néhány olyan fő tulajdonság, amely a naplóbejegyzések értelmezésére tett kísérlet során keres. A naplók kimenetének Azure PowerShell vagy az Azure CLI használatával történő szűréséhez tekintse meg a következő részeket.

> [!div class="mx-tableFixed"]
> | Tulajdonság | Példaértékek | Leírás |
> | --- | --- | --- |
> | engedélyezés: művelet | Microsoft.Authorization/roleAssignments/write | Szerepkör-hozzárendelés létrehozása |
> |  | Microsoft. Authorization/roleAssignments/delete | Szerepkör-hozzárendelés törlése |
> |  | Microsoft. Authorization/roleDefinitions/írás | Szerepkör-definíció létrehozása vagy frissítése |
> |  | Microsoft. Authorization/roleDefinitions/delete | Szerepkör-definíció törlése |
> | engedélyezés: hatókör | /subscriptions/{subscriptionId}<br/>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId} | A művelet hatóköre |
> | hívó | admin@example.com<br/>ObjectId | A műveletet kezdeményező személy |
> | eventTimestamp | 2021-03-01T22:07:41.126243 Z | A művelet elkövetkezett ideje |
> | állapot: érték | Első lépések<br/>Sikeres<br/>Sikertelen | A művelet állapota |

## <a name="azure-powershell"></a>Azure PowerShell

Ha Azure PowerShellkal szeretné megtekinteni a tevékenység naplóit, használja a [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) parancsot.

Ez a parancs felsorolja az előfizetés összes szerepkör-hozzárendelésének változását az elmúlt hét napban:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Ez a parancs felsorolja az adott erőforráscsoport összes szerepkör-definíciójának változását az elmúlt hét napban:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

### <a name="filter-log-output"></a>Napló kimenetének szűrése

A napló kimenete számos információt tartalmazhat. Ez a parancs felsorolja az összes szerepkör-hozzárendelést és szerepkör-definíciót az előfizetésben az elmúlt hét napban, és szűri a kimenetet:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

A következő példa a szűrt napló kimenetét mutatja be szerepkör-hozzárendelés létrehozásakor:

```azurepowershell
Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:42 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:41 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"{roleAssignmentId}","Properties":{"PrincipalId":"{principalId}","PrincipalType":"User","RoleDefinitionId":"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64","Scope":"/subscriptions/
                          {subscriptionId}/resourceGroups/example-group"}}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

```

Ha egyszerű szolgáltatásnevet használ a szerepkör-hozzárendelések létrehozásához, akkor a hívó tulajdonság egy egyszerű szolgáltatásnév. A [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) használatával információkat kérhet le az egyszerű szolgáltatásról.

```Example
Caller                  : {objectId}
EventTimestamp          : 3/1/2021 9:43:08 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              : 
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
```

## <a name="azure-cli"></a>Azure CLI

Ha az Azure CLI-vel szeretné megtekinteni a tevékenység naplóit, használja az az [monitor Activity-log List](/cli/azure/monitor/activity-log#az_monitor_activity_log_list) parancsot.

Ez a parancs egy erőforráscsoport tevékenység-naplóit sorolja fel március 1-től, hét nap megvizsgálva:

```azurecli
az monitor activity-log list --resource-group example-group --start-time 2021-03-01 --offset 7d
```

Ez a parancs az engedélyezési erőforrás-szolgáltató tevékenység naplóit sorolja fel március 1-től:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d
```

### <a name="filter-log-output"></a>Napló kimenetének szűrése

A napló kimenete számos információt tartalmazhat. Ez a parancs felsorolja az összes szerepkör-hozzárendelést és szerepkör-definíciót az előfizetésben hét nap elteltével, a kimenet szűrésével:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d --query '[].{authorization:authorization, caller:caller, eventTimestamp:eventTimestamp, properties:properties}'
```

A következő példa a szűrt napló kimenetét mutatja be szerepkör-hozzárendelés létrehozásakor:

```azurecli
[
 {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:42.456241+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "serviceRequestId": "{serviceRequestId}",
      "statusCode": "Created"
    }
  },
  {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:41.126243+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "requestbody": "{\"Id\":\"{roleAssignmentId}\",\"Properties\":{\"PrincipalId\":\"{principalId}\",\"PrincipalType\":\"User\",\"RoleDefinitionId\":\"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64\",\"Scope\":\"/subscriptions/{subscriptionId}/resourceGroups/example-group\"}}"
    }
  }
]
```

## <a name="azure-monitor-logs"></a>Azure Monitor-naplók

[Azure monitor a naplók](../azure-monitor/logs/log-query-overview.md) egy másik eszköz, amellyel összegyűjtheti és elemezheti az Azure RBAC módosításait az összes Azure-erőforráshoz. Azure Monitor naplók előnyei a következők:

- Összetett lekérdezések és logika írása
- Integrálás riasztásokkal, Power BIekkel és egyéb eszközökkel
- Adatok mentése hosszabb megőrzési időtartamokra
- Kereszthivatkozás más naplókba, például a biztonság, a virtuális gép és az egyéni

Az első lépésekhez a következő alapvető lépések szükségesek:

1. [Log Analytics munkaterület létrehozása](../azure-monitor/logs/quick-create-workspace.md).

1. [Konfigurálja a Activity log Analytics megoldást](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution) a munkaterületre.

1. [Tekintse meg a tevékenységek naplóit](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution). A Activity Log Analytics megoldás áttekintő oldalának gyors eléréséhez kattintson a **naplók** lehetőségre.

   ![Azure Monitor naplók lehetőség a portálon](./media/change-history-report/azure-log-analytics-option.png)

1. Igény szerint a [Azure Monitor log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) is használhatja a naplók lekérdezéséhez és megtekintéséhez. További információ: Ismerkedés [a Azure monitor-naplózási lekérdezésekkel](../azure-monitor/logs/get-started-queries.md).

A következő egy lekérdezés, amely a célként megadott erőforrás-szolgáltató által szervezett új szerepkör-hozzárendeléseket adja vissza:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Az alábbi lekérdezés a diagramon megjelenített szerepkör-hozzárendelési módosításokat adja vissza:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![Tevékenységek naplói a speciális elemzési portál használatával – képernyőfelvétel](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Következő lépések
* [Tevékenységek naplóinak megtekintése az erőforrásokon végzett műveletek figyeléséhez](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Előfizetési tevékenység figyelése az Azure-beli tevékenység naplójával](../azure-monitor/essentials/platform-logs-overview.md)