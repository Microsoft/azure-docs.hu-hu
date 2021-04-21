---
title: Riasztás használata egy runbook Azure Automation aktiválása
description: Ez a cikk bemutatja, hogyan aktiválhat egy Runbookot, hogy az Azure-riasztások kiváltásakor fusson.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 36f1881ddd10498e7736de2d117a42021075abdc
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834875"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Riasztás használata egy runbook Azure Automation aktiválása

Az [Azure-Azure Monitor](../azure-monitor/overview.md) alapszintű metrikák és naplók figyelése a legtöbb szolgáltatáshoz használható. A runbookok Azure Automation műveletcsoportok használatával [](../azure-monitor/alerts/action-groups.md) automatizálhatók a feladatok a riasztások alapján. Ez a cikk bemutatja, hogyan konfigurálhat és futtathat runbookokat riasztások használatával.

## <a name="alert-types"></a>Riasztástípusok

Az Automation-runbookok három riasztástípussal használhatók:

* Gyakori riasztások
* Tevékenységnapló-riasztások
* Közel valós idejű metrikákra vonatkozó riasztások

> [!NOTE]
> A gyakori riasztási séma szabványosítja az Azure-beli riasztási értesítések használatának élményét. Korábban az Azure három riasztástípusa (metrika, napló és tevékenységnapló) saját e-mail-sablonokkal, webhooksémákat stb. tartalmazott. További információ: Gyakori [riasztási séma](../azure-monitor/alerts/alerts-common-schema.md)

Amikor egy riasztás runbookot hív meg, a tényleges hívás egy HTTP POST kérés a webhooknak. A POST-kérés törzse egy JSON-formátumú objektumot tartalmaz, amely a riasztáshoz kapcsolódó hasznos tulajdonságokkal rendelkezik. A következő táblázat az egyes riasztási típus hasznos adatsémáira mutató hivatkozásokat sorolja fel:

|Riasztás  |Description|Hasznos adatséma  |
|---------|---------|---------|
|[Gyakori riasztás](../azure-monitor/alerts/alerts-common-schema.md)|A gyakori riasztási séma, amely szabványosítja az Azure-beli riasztási értesítések használatának élményét.|Gyakori riasztási hasznos adatsémák|
|[Tevékenységnapló-riasztás](../azure-monitor/alerts/activity-log-alerts.md)    |Értesítést küld, ha az Azure-tevékenységnaplóban bármely új esemény megfelel bizonyos feltételeknek. Például ha egy művelet a `Delete VM` **myProductionResourceGroup** erőforráscsoportban történik, vagy Azure Service Health aktív állapotú új esemény jelenik meg.| [Tevékenységnapló-riasztás hasznos adatséma](../azure-monitor/alerts/activity-log-alerts-webhook.md)        |
|[Közel valós idejű metrikák riasztása](../azure-monitor/alerts/alerts-metric-near-real-time.md)    |Gyorsabban küld értesítést, mint a metrikariasztás, ha egy vagy több platformszintű metrika megfelel a megadott feltételeknek. Ha például egy virtuális gépen a **processzorhasználat (%** ) értéke nagyobb 90-nél, és a **Network In** (Hálózati terhelés) értéke nagyobb 500 MB-nál az elmúlt 5 percben.| [Közel valós idejű metrikák riasztásának hasznos adatsémára](../azure-monitor/alerts/alerts-webhooks.md#payload-schema)          |

Mivel az egyes riasztási típus által biztosított adatok eltérőek, az egyes riasztási típusokat eltérő módon kezeli a rendszer. A következő szakaszban megtudhatja, hogyan hozhat létre olyan runbookot, amely különböző típusú riasztásokat kezel.

## <a name="create-a-runbook-to-handle-alerts"></a>Runbook létrehozása a riasztások kezeléshez

Ahhoz, hogy az Automationt riasztásokkal használjuk, szüksége lesz egy olyan runbookra, amely a runbooknak átadott JSON-riasztás hasznos 7-et kezelő logikával rendelkezik. Az alábbi példa runbookot egy Azure-riasztásból kell meghívni.

Az előző szakaszban leírtak szerint minden riasztástípus más sémával rendelkezik. A szkript a runbook bemeneti paraméterében található riasztásból veszi át a `WebhookData` webhookadatokat. Ezután a szkript kiértékeli a JSON-hasznos okat, hogy megállapítsa, melyik riasztástípust használja.

Ez a példa egy virtuális gépről származó riasztást használ. Lekéri a virtuális gép adatait a hasznos adatokból, majd ezeket az adatokat használja a virtuális gép leállítására. A kapcsolatot abban az Automation-fiókban kell beállítani, ahol a runbook fut. Ha riasztásokat használ a runbookok aktiváláshoz, fontos ellenőrizni a riasztás állapotát az aktivált runbookban. A runbook minden alkalommal aktiválódik, amikor a riasztás állapota megváltozik. A riasztások több állammal is rendelkezők, amelyek közül a két leggyakoribb az Aktiválva és a Feloldva. Ellenőrizze, hogy a runbook logikájában van-e állapot, és győződjön meg arról, hogy a runbook nem fut egynél több alkalommal. A cikkben látható példa bemutatja, hogyan kereshet csak aktivált állapotú riasztásokat.

A runbook a kapcsolati eszközként futó fiókot használja az Azure-ral való hitelesítéshez, hogy végrehajtsa a felügyeleti `AzureRunAsConnection` [](./automation-security-overview.md) műveletet a virtuális gépen.

Ebben a példában egy **Stop-AzureVmInResponsetoVMAlert nevű runbookot hozhat létre.** Módosíthatja a PowerShell-szkriptet, és számos különböző erőforrással használhatja.

1. Ugrás az Azure Automation fiókra.
2. A **Folyamatautomatizálás alatt** válassza a **Runbookok lehetőséget.**
3. A runbookok listájának tetején válassza a **+ Runbook létrehozása lehetőséget.**
4. A **Runbook hozzáadása lapon** adja meg a **Stop-AzureVmInResponsetoVMAlert** nevet a runbook neveként. A runbook típusaként válassza a **PowerShell lehetőséget.** Ezután kattintson a **Létrehozás** elemre.  
5. Másolja az alábbi PowerShell-példát a **Szerkesztés lapra.**

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

6. A **runbook mentéshez** és közzétételéhez válassza a Közzététel lehetőséget.

## <a name="create-the-alert"></a>A riasztás létrehozása

A riasztások műveletcsoportokat használnak, amelyek a riasztás által aktivált műveletek gyűjteményei. A runbookok csak egyike a műveletcsoportokkal használható számos műveletnek.

1. Az Automation-fiókjában válassza a **Figyelés alatt a** **Riasztások lehetőséget.**
1. Válassza a **+ Új riasztási szabály** lehetőséget.
1. Kattintson **a Select (Kijelölés)** elemre a **Resource (Erőforrás) alatt.** Az Erőforrás kiválasztása lapon válassza **ki azt a** virtuális gépet, amelyről riasztást küld, majd kattintson a Kész **gombra.**
1. Kattintson **a Feltétel hozzáadása lehetőségre** a Feltétel **alatt.** Válassza ki a használni kívánt jelet( például **Százalékos processzorhasználat)** és kattintson a **Kész gombra.**
1. A **Jellogika konfigurálása** lapon  adja meg a Küszöbértéket a **Riasztási logika alatt,** majd kattintson a Kész **gombra.**
1. A **Műveletcsoportok alatt válassza** az Új létrehozása **lehetőséget.**
1. A **Műveletcsoport hozzáadása lapon** adjon meg egy nevet és egy rövid nevet a műveletcsoportnak.
1. Nevezze el a műveletet. A művelet típusaként válassza az **Automation-runbook lehetőséget.**
1. Válassza **a Részletek szerkesztése lehetőséget.** A **Runbook konfigurálása lapon,** a **Runbook forrása alatt válassza** a **Felhasználó lehetőséget.**  
1. Válassza ki **előfizetését** és **Automation-fiókját,** majd a **Stop-AzureVmInResponsetoVMAlert** runbookot.  
1. Válassza **az Igen** lehetőséget a Gyakori **riasztási séma engedélyezése beállításhoz.**
1. A műveletcsoport létrehozásához kattintson az **OK gombra.**

    ![Műveletcsoport hozzáadása oldal](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    Ezt a műveletcsoportot a [létrehozott](../azure-monitor/alerts/activity-log-alerts.md) tevékenységnapló-riasztások és közel [valós](../azure-monitor/alerts/alerts-overview.md) idejű riasztások között használhatja.

1. A **Riasztás részletei alatt** adja meg a riasztási szabály nevét és leírását, majd kattintson a **Riasztási szabály létrehozása elemre.**

## <a name="next-steps"></a>Következő lépések

* Runbook webhookkal való futtatásához lásd: Runbook futtatása [webhookból.](automation-webhooks.md)
* A runbookok indításának különböző módjaiért [lásd: Runbookok futtatása.](./start-runbooks.md)
* Tevékenységnapló-riasztás létrehozásához lásd: [Tevékenységnapló-riasztások létrehozása.](../azure-monitor/alerts/activity-log-alerts.md)
* A közel valós idejű riasztások létrehozásáról a Riasztási szabály létrehozása a következő [Azure Portal.](../azure-monitor/alerts/alerts-metric.md?toc=/azure/azure-monitor/toc.json)
* A PowerShell-parancsmagok referenciáját lásd: [Az.Automation](/powershell/module/az.automation).
