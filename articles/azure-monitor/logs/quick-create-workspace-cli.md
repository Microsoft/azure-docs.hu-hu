---
title: Log Analytics munkaterület létrehozása az Azure CLI használatával | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre Log Analytics munkaterületet a Felhőbeli és a helyszíni környezetekben az Azure CLI-vel való felügyeleti megoldások és adatgyűjtés engedélyezéséhez.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: 09e98429b42ced58849851f5bb26477adaec9d68
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100619545"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Log Analytics munkaterület létrehozása az Azure CLI 2,0-vel

Az Azure CLI 2.0 az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe egy Log Analytics munkaterületet a Azure Monitorban az Azure CLI 2,0 használatával. A Log Analytics munkaterület egy egyedi környezet, amely Azure Monitor naplózza az adatnaplót. Az egyes munkaterületek saját adattárral és konfigurációval rendelkeznek, és az adatforrások és megoldások úgy vannak konfigurálva, hogy egy adott munkaterületen tárolják az adataikat. Ha a következő forrásokból szeretne adatgyűjtést végrehajtani, Log Analytics munkaterületre van szüksége:

* Azure-erőforrások az előfizetésében  
* System Center Operations Manager által figyelt helyszíni számítógépek  
* Configuration Managerból származó eszközök gyűjteményei  
* Diagnosztikai vagy naplóadatok az Azure Storage-ból  

Más forrásokhoz, például az Azure-beli virtuális gépekhez és a környezetben található Windows-vagy Linux-alapú virtuális gépekhez a következő témakörökben talál további információt:

* [Adatok gyűjtése az Azure Virtual Machines szolgáltatásból](../vm/quick-collect-azurevm.md)
* [Adatok gyűjtése hibrid Linux rendszerű számítógépről](../vm/quick-collect-linux-computer.md)
* [Adatok gyűjtése hibrid Windows-számítógépről](../vm/quick-collect-windows-computer.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.30 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-workspace"></a>Munkaterület létrehozása
Hozzon létre egy munkaterületet az [az Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create)paranccsal. Az alábbi példa egy munkaterületet hoz létre a *eastus* helyen a helyi számítógép Resource Manager-sablonjának használatával. A JSON-sablon úgy van konfigurálva, hogy csak a munkaterület nevére Kérdezzen, és a többi olyan paraméter alapértelmezett értékét adja meg, amely valószínűleg a környezetben megszokott konfigurációként lenne felhasználva. A sablont egy Azure Storage-fiókban is tárolhatja a szervezet megosztott hozzáféréséhez. További információ a sablonok használatáról: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md)

További információ a támogatott régiókkal kapcsolatban: [régiók log Analytics](https://azure.microsoft.com/regions/services/) a (Azure monitor), és keressen rá a **termék** keresése elemre.

A következő paraméterek alapértelmezett értéket állítanak be:

* Location (alapértelmezett) – az USA keleti régiója
* SKU – alapértelmezett érték az új GB-os díjszabási szinten, amely az áprilisi 2018 díjszabási modellben jelent meg

>[!WARNING]
>Ha Log Analytics munkaterületet hoz létre vagy konfigurál egy olyan előfizetésben, amely az új, április 2018 díjszabási modellbe van lefoglalva, az egyetlen érvényes Log Analytics díjszabási csomag **PerGB2018**.
>

### <a name="create-and-deploy-template"></a>Sablon létrehozása és üzembe helyezése

1. Másolja és illessze be a következő JSON-szintaxist a létrehozott fájlba:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. Szerkessze a sablont, hogy megfeleljen a követelményeinek. Tekintse át a [Microsoft. OperationalInsights/munkaterületek sablonjának](/azure/templates/microsoft.operationalinsights/2015-11-01-preview/workspaces) hivatkozását, hogy megtudja, milyen tulajdonságokat és értékeket támogat a rendszer.
3. Mentse ezt a fájlt **deploylaworkspacetemplate.jsként** egy helyi mappába.   
4. Készen áll a sablon üzembe helyezésére. Használja az alábbi parancsokat a sablont tartalmazó mappából. Ha a rendszer a munkaterület nevének megadását kéri, adjon meg egy olyan nevet, amely globálisan egyedi az összes Azure-előfizetésen belül.

    ```azurecli
    az deployment group create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

Az üzembe helyezés eltarthat néhány percig. Amikor befejeződik, a következőhöz hasonló üzenet jelenik meg, amely tartalmazza az eredményt:

![Példa az üzembe helyezés befejezésekor bekövetkezett eredményre](media/quick-create-workspace-cli/template-output-01.png)

## <a name="troubleshooting"></a>Hibaelhárítás
Ha olyan munkaterületet hoz létre, amely az elmúlt 14 napban törölve lett, és nem [törölhető állapotban](../logs/delete-workspace.md#soft-delete-behavior)van, a művelet a munkaterület konfigurációjától függően eltérő eredménnyel járhat:
1. Ha ugyanazt a munkaterület-nevet, erőforráscsoportot, előfizetést és régiót adja meg, mint a törölt munkaterületen, a munkaterület helyreállítva lesz, beleértve az adatait, a konfigurációját és a csatlakoztatott ügynököket is.
2. Ha ugyanazt a munkaterület-nevet használja, de a másik erőforráscsoport, előfizetés vagy régió, hibaüzenet jelenik meg, ha *a munkaterület neve nem egyedi*, vagy az *ütközés*. Az alábbi lépéseket követve felülbírálhatja a munkaterület helyreállítható törlését és végleges törlését, és létrehozhatja az új munkaterületet ugyanazzal a névvel, a munkaterület helyreállításához és az állandó törlés végrehajtásához hajtsa végre a következő lépéseket:
   * Munkaterület [helyreállítása](../logs/delete-workspace.md#recover-workspace)
   * Munkaterület [végleges törlése](../logs/delete-workspace.md#permanent-workspace-delete)
   * Új munkaterület létrehozása ugyanazzal a munkaterület-névvel

## <a name="next-steps"></a>Következő lépések
Most, hogy elérhetővé tett egy munkaterületet, beállíthatja a figyelési telemetria gyűjteményét, futtathatja a naplók kereséseit az adatelemzéshez, és hozzáadhat egy felügyeleti megoldást további információk és elemzési elemzések megadásához.  

* Az Azure-erőforrások Azure Diagnostics vagy az Azure Storage szolgáltatással történő gyűjtésének engedélyezéséhez lásd: az [Azure-szolgáltatások naplófájljainak és metrikáinak gyűjtése a log Analytics való használatra](../essentials/resource-logs.md#send-to-log-analytics-workspace).  
* Adja hozzá [System Center Operations Manager adatforrásként](../agents/om-agents.md) az Operations Manager felügyeleti csoportot jelentő ügynököktől származó adatok gyűjtéséhez, és tárolja azt a log Analytics munkaterületen.  
* [Configuration Manager](../logs/collect-sccm.md) összekapcsolásával importálhatja azokat a számítógépeket, amelyek a hierarchiában gyűjtemények tagjai.  
* Tekintse át az elérhető [figyelési megoldásokat](../insights/solutions.md) , valamint azt, hogyan adhat hozzá vagy távolíthat el egy megoldást a munkaterületről.

