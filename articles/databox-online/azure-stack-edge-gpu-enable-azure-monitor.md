---
title: Azure Monitor engedélyezése Azure Stack Edge Pro GPU-eszközön
description: Ismerteti, hogyan engedélyezhető a Azure Monitor egy Azure Stack Edge Pro GPU-eszközön.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: 171b4dbfb2a5852e270c483a28cad31f97dcb42b
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493912"
---
# <a name="enable-azure-monitor-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Monitor engedélyezése a Azure Stack Edge Pro GPU-eszközön

A Azure Stack Edge Pro GPU-eszközön a tárolók figyelése kritikus fontosságú, különösen akkor, ha több számítási alkalmazást futtat. Azure Monitor lehetővé teszi a tároló-naplók és a memória és a processzor metrikáinak összegyűjtését az eszközön futó Kubernetes-fürtből.

Ez a cikk azokat a lépéseket ismerteti, amelyek szükségesek az eszközön való Azure Monitor engedélyezéséhez és a tároló-naplók összegyűjtéséhez Log Analytics munkaterületen. A Azure Monitor metrikák tárolója jelenleg nem támogatott az Azure Stack Edge Pro GPU-eszközön. 


## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt a következőkre lesz szüksége:

- Egy Azure Stack Edge Pro-eszköz. Győződjön meg arról, hogy az eszköz aktiválva van a következő [oktatóanyagban](azure-stack-edge-gpu-deploy-activate.md)ismertetett lépések szerint: az eszköz aktiválása.
- Elvégezte a **számítási lépés konfigurálását** az [oktatóanyagban: a számítás konfigurálása a Azure stack Edge Pro-eszközön](azure-stack-edge-gpu-deploy-configure-compute.md) az eszközön. Az eszköznek rendelkeznie kell egy társított IoT Hub erőforrással, egy IoT eszközzel és egy IoT Edge eszközzel.


## <a name="create-log-analytics-workspace"></a>Log Analytics-munkaterület létrehozása

A log Analytics-munkaterület létrehozásához hajtsa végre a következő lépéseket. A log Analytics-munkaterület egy logikai tárolási egység, amelyben a rendszer összegyűjti és tárolja a naplózási adatokat.

1. A Azure Portal válassza az **+ erőforrás létrehozása** elemet, és keresse meg **log Analytics munkaterületet** , majd válassza a **Létrehozás** lehetőséget. 
1. A **log Analytics létrehozása munkaterületen** konfigurálja a következő beállításokat. Fogadja el a maradékot alapértelmezettként.

    1. Az **alapok** lapon adja meg az előfizetést, az erőforráscsoportot, a nevet és a régiót a munkaterülethez. 

        ![Log Analytics munkaterület alapjai lap](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-basics-1.png)  

    1. A **díjszabási réteg** lapon fogadja el az alapértelmezett utólagos elszámolású **csomagot**.

        ![Log Analytics munkaterület díjszabási lapja](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-pricing-1.png) 

    1. A **felülvizsgálat + létrehozás** lapon tekintse át a munkaterület adatait, és válassza a **Létrehozás** lehetőséget.

        ![Áttekintés + létrehozás Log Analytics munkaterülethez](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-review-create-1.png)

További információ: [log Analytics munkaterület létrehozása Azure Portal használatával](../azure-monitor/learn/quick-create-workspace.md).



## <a name="enable-container-insights"></a>Tároló-felismerés engedélyezése

A következő lépésekkel engedélyezheti a tárolók bepillantást a munkaterületen. 

1. Kövesse az [Azure monitor containers megoldás hozzáadása](../azure-monitor/insights/container-insights-hybrid-setup.md#how-to-add-the-azure-monitor-containers-solution)című témakör részletes lépéseit. Használja a következő sablonfájlt `containerSolution.json` :

    ```yml
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
            }
        ]
    }
    ```

1. Szerezze be az erőforrás-azonosítót és a helyet. Nyissa meg a következőt: `Your Log Analytics workspace > General > Properties`. Másolja a következő adatokat:

    - **erőforrás-azonosító**, amely az Azure log Analytics munkaterület teljes mértékben minősített Azure-erőforrás-azonosítója. 
    - a **hely**, amely az Azure-régió.

    ![Log Analytics munkaterület tulajdonságai](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-properties-1.png) 

1. Használja az alábbi paramétereket tartalmazó fájlt `containerSolutionParams.json` . Cserélje le az értékét `workspaceResourceId` az erőforrás-azonosítóra, és `workspaceRegion` az előző lépésben másolt helyre.

    ```yaml
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "workspaceResourceId": {
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
        },
        "workspaceRegion": {
        "value": "westus"
        }
        }
    }
    ```
    
    Itt látható egy Log Analytics munkaterület kimenete, amelynek a tároló-felismerések engedélyezve vannak:
    
    ```powershell
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...
    MOTD: Switch to Bash from PowerShell: bash
    VERBOSE: Authenticating to Azure ...
    VERBOSE: Building your Azure drive ...
    
    PS /home/alpa> az account set -s fa68082f-8ff7-4a25-95c7-ce9da541242f
    PS /home/alpa> ls
    clouddrive  containerSolution.json
    PS /home/alpa> ls
    clouddrive  containerSolution.json  containerSolutionParams.json
    PS /home/alpa> az deployment group create --resource-group myaserg --name Testdeployment1 --template-file containerSolution.json --parameters containerSolutionParams.json
    {- Finished ..
        "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.Resources/deployments/Testdeployment1",
        "location": null,
        "name": "Testdeployment1",
        "properties": {
        "correlationId": "3a9045fe-2de0-428c-b17b-057508a8c575",
        "debugSetting": null,
        "dependencies": [],
        "duration": "PT11.1588316S",
        "error": null,
        "mode": "Incremental",
        "onErrorDeployment": null,
        "outputResources": [
            {
            "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.OperationsManagement/solutions/ContainerInsights(myaseloganalyticsws)",
            "resourceGroup": "myaserg"
            }
        ],
        "outputs": null,
        "parameters": {
            "workspaceRegion": {
            "type": "String",
            "value": "westus"
            },
            "workspaceResourceId": {
            "type": "String",
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
            }
        },
        "parametersLink": null,
        "providers": [
            {
            "id": null,
            "namespace": "Microsoft.Resources",
            "registrationPolicy": null,
            "registrationState": null,
            "resourceTypes": [
                {
                "aliases": null,
                "apiProfiles": null,
                "apiVersions": null,
                "capabilities": null,
                "defaultApiVersion": null,
                "locations": [
                    null
                ],
                "properties": null,
                "resourceType": "deployments"
                }
            ]
            }
        ],
        "provisioningState": "Succeeded",
        "templateHash": "10500027184662969395",
        "templateLink": null,
        "timestamp": "2020-11-06T22:09:56.908983+00:00",
        "validatedResources": null
        },
        "resourceGroup": "myaserg",
        "tags": null,
        "type": "Microsoft.Resources/deployments"
    }
    PS /home/alpa>
    ```

## <a name="configure-azure-monitor-on-your-device"></a>Azure Monitor konfigurálása az eszközön

1. Nyissa meg az újonnan létrehozott Log Analytics erőforrást, és másolja a **munkaterület-azonosítót** és az **elsődleges kulcsot** (a munkaterület kulcsát).

    ![Ügynökök kezelése Log Analytics munkaterületen](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-agents-management-1.png)

    Mentse ezt az információt, ahogy azt egy későbbi lépésben fogja használni.

1. [Kapcsolódjon az eszköz PowerShell-felületéhez](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 
    
1. Használja a log Analytics-munkaterület AZONOSÍTÓját és a munkaterület kulcsát a következő parancsmaggal:

    `Set-HcsKubernetesAzureMonitorConfiguration -WorkspaceId <> -WorkspaceKey <>`

1. A Azure Monitor engedélyezése után a naplók a Log Analytics munkaterületen jelennek meg. Az eszközön üzembe helyezett Kubernetes-fürt állapotának megtekintéséhez nyissa meg **Azure Monitor > > tárolókat**. A környezet beállításnál válassza az **összes** lehetőséget. 

    ![Metrikák Log Analytics munkaterületen](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-metrics-1.png)

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [figyelheti a Kubernetes számítási feladatokat a Kubernetes-irányítópulton keresztül](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).
- Ismerje meg, hogyan [kezelheti az eszköz-riasztási értesítéseket](azure-stack-edge-gpu-manage-device-event-alert-notifications.md). 