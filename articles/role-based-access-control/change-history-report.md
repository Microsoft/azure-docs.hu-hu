---
title: Az Azure RBAC változásaival kapcsolatos tevékenységek naplóinak megtekintése
description: Tekintse meg az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) Azure-erőforrásokkal kapcsolatos változásait az elmúlt 90 napban.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/27/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: 53b72ac22df845f88dc82b14aa5dfaa57973b0d1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595846"
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

A portálon a tevékenység naplója több szűrőt tartalmaz. Az Azure RBAC-hez kapcsolódó szűrők:

| Szűrő | Érték |
| --------- | --------- |
| Eseménykategória | <ul><li>Adminisztratív</li></ul> |
| Művelet | <ul><li>Szerepkör-hozzárendelés létrehozása</li><li>Szerepkör-hozzárendelés törlése</li><li>Egyéni szerepkör-definíció létrehozása vagy frissítése</li><li>Egyéni szerepkör-definíció törlése</li></ul> |

További információ a tevékenységi naplókról: [tevékenység-naplók megtekintése az erőforrásokon végzett műveletek figyeléséhez](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Ha Azure PowerShellkal szeretné megtekinteni a tevékenység naplóit, használja a [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) parancsot.

Ez a parancs felsorolja az előfizetés összes szerepkör-hozzárendelésének változását az elmúlt hét napban:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Ez a parancs felsorolja az adott erőforráscsoport összes szerepkör-definíciójának változását az elmúlt hét napban:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Ez a parancs felsorolja az összes szerepkör-hozzárendelést és szerepkör-definíciót az előfizetésben az elmúlt hét napban, és megjeleníti az eredményeket egy listában:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111
                          eventCategory  : Administrative

Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

Ha egyszerű szolgáltatásnevet használ a szerepkör-hozzárendelések létrehozásához, a hívó tulajdonság egy objektumazonosító lesz. A [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) használatával információkat kérhet le az egyszerű szolgáltatásról.

```Example
Caller                  : 44444444-4444-4444-4444-444444444444
EventTimestamp          : 6/4/2020 9:43:08 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              : 
                          statusCode     : Created
                          serviceRequestId: 55555555-5555-5555-5555-555555555555
                          category       : Administrative
```

## <a name="azure-cli"></a>Azure CLI

Ha az Azure CLI-vel szeretné megtekinteni a tevékenység naplóit, használja az az [monitor Activity-log List](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) parancsot.

Ez a parancs egy erőforráscsoport tevékenység-naplóit sorolja fel február 27-én, hét nap megvizsgálva:

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2020-02-27 --offset 7d
```

Ez a parancs az engedélyezési erőforrás-szolgáltató tevékenység naplóit sorolja fel február 27-én, a hét napja előre látható:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2020-02-27 --offset 7d
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

1. Igény szerint a [Azure Monitor log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) is használhatja a naplók lekérdezéséhez és megtekintéséhez. További információ: Ismerkedés [a Azure monitor log lekérdezésekkel](../azure-monitor/logs/get-started-queries.md).

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
* [Események megtekintése a tevékenységnaplóban](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Előfizetési tevékenységek monitorozása az Azure-tevékenységnaplóval](../azure-monitor/essentials/platform-logs-overview.md)