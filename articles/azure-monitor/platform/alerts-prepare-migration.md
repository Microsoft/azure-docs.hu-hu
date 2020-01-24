---
title: Felkészülés Azure Monitor klasszikus riasztások áttelepítésére a Logic apps és a runbookok frissítésével
author: yanivlavi
description: Ismerje meg, hogyan módosítható a webhookok, a Logic apps és a runbookok az önkéntes áttelepítés előkészítéséhez.
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 58ba95ff60ddccf909578a673110c870caf57376
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705564"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>A logikai alkalmazások és runbookok előkészítése a klasszikus riasztási szabályok áttelepítéséhez

Amint azt [korábban bejelentettük](monitoring-classic-retirement.md), a Azure monitor klasszikus riasztásai 2019 szeptemberében megszűnnek (eredetileg 2019. július). Az áttelepítési eszköz a Azure Portalban olyan ügyfelek számára érhető el, akik klasszikus riasztási szabályokat használnak, és magukat a migrációt szeretnék elindítani.

> [!NOTE]
> Az áttelepítési eszköz késése miatt a klasszikus riasztások áttelepítésének lejárati dátuma 2019 augusztus 31-ig, az eredetileg bejelentett, 2019. június 30-ig.

Ha úgy dönt, hogy önként áttelepíti a klasszikus riasztási szabályokat az új riasztási szabályokra, vegye figyelembe, hogy a két rendszer között van néhány különbség. Ez a cikk ismerteti ezeket a különbségeket, és azt, hogy miként lehet előkészíteni a változást.

## <a name="api-changes"></a>API-változások

A klasszikus riasztási szabályokat létrehozó és kezelő API-k (`microsoft.insights/alertrules`) eltérnek az új metrikai riasztásokat létrehozó és kezelő API-kkal (`microsoft.insights/metricalerts`). Ha még ma hozza létre és felügyeli a klasszikus riasztási szabályokat, frissítse az üzembe helyezési parancsfájlokat az új API-kkal való együttműködéshez.

A következő táblázat a klasszikus és az új riasztások programozott interfészeit ismerteti:

|         |Klasszikus riasztások  |Új metrikai riasztások |
|---------|---------|---------|
|REST API     | [Microsoft. bepillantások/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [Microsoft. bepillantások/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Azure parancssori felület (CLI)     | [az monitor Alert](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [az monitor Metrics Alert](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Referencia](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |  [Referencia](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Azure Resource Manager-sablon | [Klasszikus riasztások esetén](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Új metrikai riasztások esetén](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Értesítési tartalom változásai

Az értesítési hasznos adatok formátuma némileg eltér a [klasszikus riasztási szabályok](alerts-webhooks.md) és az [új metrikai riasztások](alerts-metric-near-real-time.md#payload-schema)között. Ha a klasszikus riasztási szabályok által aktivált webhookok, logikai alkalmazások vagy runbook műveletek vannak, akkor frissítenie kell ezeket az értesítési végpontokat az új metrikai riasztások adattartalom-formátumának elfogadásához.

A következő táblázat segítségével leképezheti a webhook hasznos adatait tartalmazó mezőket a klasszikus formátumból az új formátumba:

|  |Klasszikus riasztások  |Új metrikai riasztások |
|---------|---------|---------|
|Aktiválták vagy megoldották a riasztást?    | **állapot**       | **az adat. status** |
|A riasztás kontextusával kapcsolatos információk     | **összefüggésben**        | **az adat. Context**        |
|A riasztás aktiválásának vagy feloldásának időbélyegzője     | **Context. timestamp**       | **az adat. Context. timestamp**        |
| Riasztási szabály azonosítója | **context.id** | **data.context.id** |
| Riasztási szabály neve | **context.name** | **data.context.name** |
| A riasztási szabály leírása | **Context. Description** | **az adat. Context. Description** |
| Riasztási szabály feltétele | **Context. Condition** | **az adat. Context. Condition** |
| Metrika neve | **Context. Condition. metricName** | **az. Context. Condition. allOf [0]. metricName** |
| Idő összesítése (a mérőszám összesítésének módja a próbaverziós ablakban)| **Context. Condition. timeAggregation** | **Context. Condition. timeAggregation** |
| Kiértékelési időszak | **Context. Condition. windowSize** | **az adat. Context. Condition. windowSize** |
| Operátor (az összesített metrikai érték összehasonlítása a küszöbértékkel) | **Context. Condition. operátor** | **az adat. Context. Condition. operátor** |
| Küszöbérték | **Context. Condition. küszöbérték** | **az. Context. Condition. allOf [0]. küszöbérték** |
| Metrika értéke | **Context. Condition. metricValue** | **az. Context. Condition. allOf [0]. metricValue** |
| Subscription ID (Előfizetés azonosítója) | **Context. subscriptionId** | **az adat. Context. subscriptionId** |
| Az érintett erőforrás erőforráscsoport | **Context. resourceGroup** | **az adat. Context. resourceGroup** |
| Az érintett erőforrás neve | **Context. resourceName** | **az adat. Context. resourceName** |
| Az érintett erőforrás típusa | **Context. resourceType** | **az adat. Context. resourceType** |
| Az érintett erőforrás erőforrás-azonosítója | **Context. resourceId** | **az adat. Context. resourceId** |
| Közvetlen hivatkozás a portál erőforrás-összefoglaló lapjára | **Context. portalLink** | **az adat. Context. portalLink** |
| A webhooknak vagy logikai alkalmazásnak átadandó egyéni adattartalom mezői | **Tulajdonságok** | **az adat. properties** |

A hasznos adatok hasonlóak, mint láthatja. A következő szakasz a következőket kínálja:

- A Logic apps új formátummal való működésének módosítására vonatkozó részletek.
- Egy runbook példa, amely elemzi az értesítési adattartalmat az új riasztásokhoz.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Logikai alkalmazás módosítása metrikus riasztási értesítés fogadásához

Ha klasszikus riasztásokkal rendelkező logikai alkalmazásokat használ, módosítania kell a logikai alkalmazás kódját, hogy elemezze az új metrikai riasztások hasznos adatait. Kövesse az alábbi lépéseket:

1. Hozzon létre egy új logikai alkalmazást.

1. Használja a "Azure Monitor-metrikák riasztási kezelője" sablont. Ennek a sablonnak van egy **http-kérelmi** triggere, amely a megfelelő sémát határozza meg.

    ![logikai alkalmazás – sablon](media/alerts-migration/logic-app-template.png "Metrika riasztási sablonja")

1. Adjon hozzá egy műveletet a feldolgozási logika üzemeltetéséhez.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Metrikus riasztási értesítést fogadó Automation-runbook használata

Az alábbi példa PowerShell-kódot biztosít a runbook való használathoz. Ez a kód elemezheti a klasszikus metrikus riasztási szabályokhoz és az új metrikai riasztási szabályokhoz tartozó hasznos adatokat is.

```PowerShell
## Example PowerShell code to use in a runbook to handle parsing of both classic and new metric alerts.

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData.
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Determine whether the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new metric alert schema.
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic metric alert schema.
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is neither a classic metric alert nor a new metric alert.
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    # Parse fields related to resource affected.
    $ResourceName = $AlertContext.resourceName
    $ResourceType = $AlertContext.resourceType
    $ResourceGroupName = $AlertContext.resourceGroupName
    $ResourceId = $AlertContext.resourceId
    $SubId = $AlertContext.subscriptionId

    ## Your logic to handle the alert here.
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}

```

A virtuális gépet egy riasztás indításakor leállító runbook teljes példáját a [Azure Automation dokumentációjában](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)találja.

## <a name="partner-integration-via-webhooks"></a>Partner-integráció webhookok használatával

A [klasszikus riasztásokkal integrált partnereink](https://docs.microsoft.com/azure/azure-monitor/platform/partners) többsége már támogatja az újabb metrikai riasztásokat az integrációjuk során. Az új metrikai riasztásokkal már működő ismert integrációk a következők:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4 használatával](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Ha olyan partner-integrációt használ, amely itt nem szerepel, erősítse meg az integrációs szolgáltatót, hogy az integráció működik az új metrikai riasztásokkal.

## <a name="next-steps"></a>Következő lépések

- [A migrálási eszköz használata](alerts-using-migration-tool.md)
- [A migrálási eszköz működésének ismertetése](alerts-understand-migration.md)
