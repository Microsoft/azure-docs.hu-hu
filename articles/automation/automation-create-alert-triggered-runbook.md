---
title: Azure Automation runbook elindítására szolgáló riasztás használata
description: Ez a cikk azt ismerteti, hogyan indíthat el egy runbook az Azure-riasztások kiváltásakor.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2021
ms.topic: conceptual
ms.openlocfilehash: ea7979ad4a401d317ec126b7abfe354690475235
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104953102"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Azure Automation runbook elindítására szolgáló riasztás használata

A [Azure monitor](../azure-monitor/overview.md) segítségével figyelheti az Azure-ban a legtöbb szolgáltatáshoz tartozó alapszintű mérőszámokat és naplókat. A feladatok a riasztások alapján történő automatizálásához a [műveleti csoportok](../azure-monitor/alerts/action-groups.md) segítségével Azure Automation runbookok hívható meg. Ebből a cikkből megtudhatja, hogyan konfigurálhat és futtathat egy runbook riasztások használatával.

## <a name="alert-types"></a>Riasztástípusok

Az Automation runbookok háromféle riasztási típust használhat:

* Gyakori riasztások
* Tevékenységnapló-riasztások
* Közel valós idejű metrikus riasztások

> [!NOTE]
> A Common Alert séma szabványosítja a riasztási értesítések fogyasztási élményét az Azure-ban még ma. Az Azure-ban a három riasztási típust (metrikus, log és Activity log) a saját e-mail-sablonjai, webhook-sémái stb. is megkapta. További információ: [Common Alert Schema](../azure-monitor/alerts/alerts-common-schema.md)

Amikor egy riasztás meghívja a runbook, a tényleges hívás egy HTTP POST-kérelem a webhooknak. A POST kérelem törzse egy JSON-formátumú objektumot tartalmaz, amely a riasztáshoz kapcsolódó hasznos tulajdonságokkal rendelkezik. Az alábbi táblázat felsorolja az egyes riasztási típusok adatforgalmi sémájára mutató hivatkozásokat:

|Riasztás  |Leírás|Hasznos adatok sémája  |
|---------|---------|---------|
|[Gyakori riasztás](../azure-monitor/alerts/alerts-common-schema.md)|A gyakori riasztási séma, amely szabványosítja a riasztási értesítések fogyasztási élményét az Azure-ban még ma.|Gyakori riasztási adattartalom sémája|
|[Tevékenység naplójának riasztása](../azure-monitor/alerts/activity-log-alerts.md)    |Értesítés küldése, ha az Azure-tevékenység naplójában minden új esemény megfelel bizonyos feltételeknek. Ha például egy művelet a `Delete VM` **myProductionResourceGroup** -ban vagy egy új, aktív állapotú Azure Service Health eseményt jelenít meg.| [Műveletnapló riasztási hasznos sémája](../azure-monitor/alerts/activity-log-alerts-webhook.md)        |
|[Közel valós idejű metrika riasztása](../azure-monitor/alerts/alerts-metric-near-real-time.md)    |A metrikus riasztások gyorsabban küld értesítést, ha egy vagy több platform szintű metrika megfelel a megadott feltételeknek. Ha például egy virtuális gépen a **CPU%** értéke nagyobb, mint 90, és a **hálózat** értéke nagyobb, mint 500 MB az elmúlt 5 percben.| [A közel valós idejű metrikus riasztás hasznos adattartalma sémája](../azure-monitor/alerts/alerts-webhooks.md#payload-schema)          |

Mivel az egyes riasztási típusok által megadott információk eltérnek, az egyes riasztási típusokat a rendszer eltérően kezeli. A következő szakaszban megtudhatja, hogyan hozhat létre egy runbook a különböző típusú riasztások kezeléséhez.

## <a name="create-a-runbook-to-handle-alerts"></a>Riasztások kezelésére szolgáló runbook létrehozása

Az Automation riasztásokkal való használatához olyan runbook van szüksége, amely a runbook továbbított riasztási JSON-adattartalmat kezeli. Az alábbi példában szereplő runbook egy Azure-riasztásból kell meghívni.

Az előző szakaszban leírtaknak megfelelően a riasztások típusai eltérő sémával rendelkeznek. A parancsfájl a `WebhookData` runbook bemeneti paraméterében lévő riasztásból veszi fel a webhook-adatokat. Ezután a parancsfájl kiértékeli a JSON-adattartalmat, hogy meghatározza, melyik riasztási típust használja.

Ez a példa egy virtuális gépről származó riasztást használ. Lekéri a virtuális gép adatait a hasznos adatokból, majd ezt az információt használja a virtuális gép leállításához. A kapcsolatokat a runbook futtató Automation-fiókban kell beállítani. Ha riasztásokat használ a runbookok elindításához, fontos, hogy a riasztási állapotot az aktivált runbook vizsgálja. A runbook minden alkalommal aktiválódik, amikor a riasztás állapota megváltozik. A riasztások több állapottal rendelkeznek, és a két leggyakoribb aktiválás és megoldás történik. Ellenőrizze, hogy van-e olyan állapot a runbook-logikában, amely biztosítja, hogy a runbook egynél többször ne fusson. A cikkben szereplő példa azt mutatja be, hogyan lehet megkeresni a riasztásokat csak az állapot aktiválásával.

A runbook a kapcsolati eszköz `AzureRunAsConnection` [futtató fiók](./automation-security-overview.md) használatával hitelesíti az Azure-t, hogy végrehajtsa a felügyeleti műveletet a virtuális gépen.

Ez a példa egy **stop-AzureVmInResponsetoVMAlert** nevű runbook létrehozására használható. Módosíthatja a PowerShell-parancsfájlt, és számos különböző erőforrással használhatja azt.

1. Nyissa meg Azure Automation-fiókját.
2. A **folyamat automatizálása** területen válassza a **runbookok** lehetőséget.
3. A runbookok listájának tetején válassza a **+ Runbook létrehozása** lehetőséget.
4. A **Runbook hozzáadása** lapon adja meg a **stop-AzureVmInResponsetoVMAlert** értéket a Runbook nevéhez. A runbook típusnál válassza a **PowerShell** lehetőséget. Ezután kattintson a **Létrehozás** elemre.  
5. Másolja a következő PowerShell-példát a **Szerkesztés** lapra.

    ```powershell-interactive
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )
    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema)
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "azureMonitorCommonAlertSchema") {
            # This is the common Metric Alert schema (released March 2019)
            $Essentials = [object] ($WebhookBody.data).essentials
            # Get the first target only as this script doesn't handle multiple
            $alertTargetIdArray = (($Essentials.alertTargetIds)[0]).Split("/")
            $SubId = ($alertTargetIdArray)[2]
            $ResourceGroupName = ($alertTargetIdArray)[4]
            $ResourceType = ($alertTargetIdArray)[6] + "/" + ($alertTargetIdArray)[7]
            $ResourceName = ($alertTargetIdArray)[-1]
            $status = $Essentials.monitorCondition
        }
        elseif ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # Schema not supported
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if (($status -eq "Activated") -or ($status -eq "Fired"))
        {
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Determine code path depending on the resourceType
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is an Resource Manager VM
                Write-Verbose "This is an Resource Manager VM." -Verbose

                # Authenticate to Azure with service principal and certificate and set subscription
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType not supported
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated' or 'Fired' so no action taken
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```

6. Válassza a **Közzététel** lehetőséget a runbook mentéséhez és közzétételéhez.

## <a name="create-the-alert"></a>A riasztás létrehozása

A riasztások olyan műveleti csoportokat használnak, amelyek a riasztás által aktivált műveletek gyűjteményei. A runbookok csak a műveletek csoportjaival használható számos művelet egyike.

1. Az Automation-fiókjában válassza a **figyelés** alatt a **riasztások** lehetőséget.
1. Válassza a **+ Új riasztási szabály** lehetőséget.
1. Kattintson a **kiválasztás** az **erőforrás** alatt elemre. Az **erőforrás kiválasztása** lapon válassza ki a virtuális gépet a riasztáshoz, majd kattintson a **kész** gombra.
1. Kattintson  a feltétel hozzáadása **elemre**. Válassza ki a használni kívánt jelet, például a **százalékos CPU** -t, majd kattintson a **kész** gombra.
1. A **jel logikai beállítása** lapon adja meg a **küszöbérték értékét** a **riasztási logika** területen, majd kattintson a **kész** gombra.
1. A **műveleti csoportok** területen válassza az **új létrehozása** lehetőséget.
1. A **műveleti csoport hozzáadása** lapon adjon meg egy nevet és egy rövid nevet a műveleti csoportnak.
1. Adjon nevet a műveletnek. A Művelettípus mezőben válassza az **Automation Runbook** lehetőséget.
1. Válassza a **részletek szerkesztése** lehetőséget. A **Runbook konfigurálása** lap **Runbook forrása** területén válassza a **felhasználó** lehetőséget.  
1. Válassza ki az **előfizetést** és az **Automation-fiókot**, majd válassza a **stop-AzureVmInResponsetoVMAlert** runbook.  
1. Válassza az **Igen** lehetőséget **az általános riasztási séma engedélyezéséhez**.
1. A műveleti csoport létrehozásához kattintson **az OK gombra**.

    ![Műveleti csoport hozzáadása lap](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    Ezt a műveleti csoportot a [tevékenység naplójának riasztásai](../azure-monitor/alerts/activity-log-alerts.md) és a létrehozott [valós idejű riasztások mellett](../azure-monitor/alerts/alerts-overview.md) is használhatja.

1. A **riasztás részletei** területen adja meg a riasztási szabály nevét és leírását, majd kattintson a **riasztási szabály létrehozása** elemre.

## <a name="next-steps"></a>Következő lépések

* A runbook webhook használatával történő indításához tekintse meg [a Runbook indítása webhookból](automation-webhooks.md)című témakört.
* A runbook elindításának különböző módjaival kapcsolatban lásd: [Runbook elindítása](./start-runbooks.md).
* A műveletnapló riasztásának létrehozásával kapcsolatban lásd: [műveletnapló riasztásai](../azure-monitor/alerts/activity-log-alerts.md).
* A közel valós idejű riasztás létrehozásáról további információt [a riasztási szabály létrehozása a Azure Portalban](../azure-monitor/alerts/alerts-metric.md?toc=/azure/azure-monitor/toc.json)című témakörben talál.
* A PowerShell-parancsmagok leírása: [az. Automation](/powershell/module/az.automation).