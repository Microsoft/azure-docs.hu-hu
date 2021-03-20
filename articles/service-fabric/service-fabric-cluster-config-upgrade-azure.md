---
title: Azure Service Fabric-fürt konfigurációjának frissítése
description: Ismerje meg, hogyan frissítheti az Azure Service Fabric-fürtöt egy Resource Manager-sablon használatával futtató konfigurációt.
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 692dc2162159ab61a3ac527e12dac43438084a60
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91842716"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Fürt konfigurációjának frissítése az Azure-ban 

Ez a cikk azt ismerteti, hogyan szabhatja testre a Service Fabric-fürthöz tartozó különböző hálók beállításait. Az Azure-ban üzemeltetett fürtök esetében a beállításokat a [Azure Portal](https://portal.azure.com) vagy egy Azure Resource Manager sablon segítségével szabhatja testre.

> [!NOTE]
> Nem minden beállítás érhető el a portálon, és az [ajánlott eljárás egy Azure Resource Manager sablon használatával történő Testreszabás](./service-fabric-best-practices-infrastructure-as-code.md). A portál kizárólag Service Fabric Dev\Test forgatókönyvek esetében érhető el.
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Fürtkonfiguráció testreszabása Resource Manager-sablonok használatával
Az Azure-fürtöket a JSON Resource Manager-sablonnal lehet konfigurálni. További információ a különböző beállításokról: [fürtök konfigurációs beállításai](service-fabric-cluster-fabric-settings.md). Az alábbi lépések bemutatják, hogyan adhat hozzá új beállításokat a *diagnosztika* szakaszhoz a Azure erőforrás-kezelő használatával. *MaxDiskQuotaInMB*

1. Nyissa meg a következőt: https://resources.azure.com
2. Navigáljon az **előfizetéshez, és bontsa** ki a  ->  **\<Your Subscription>**  ->    ->  **\<Your Resource Group>**  ->    ->  **Microsoft. ServiceFabric**-  ->  **fürtök** resourceGroups-szolgáltatóit. -> **\<Your Cluster Name>**
3. A jobb felső sarokban válassza az **írás/írás lehetőséget.**
4. Válassza a **Szerkesztés** lehetőséget, és frissítse a `fabricSettings` JSON-elemet, és adjon hozzá egy új elemet:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

A fürt beállításait a következő módokon is testreszabhatja a Azure Resource Manager használatával:

- Az erőforrás-kezelő sablon exportálásához és frissítéséhez használja a [Azure Portal](../azure-resource-manager/templates/export-template-portal.md) .
- A [PowerShell](../azure-resource-manager/management/manage-resources-powershell.md) használatával exportálhatja és frissítheti a Resource Manager-sablont.
- A Resource Manager-sablon exportálásához és frissítéséhez használja az [Azure CLI](../azure-resource-manager/management/manage-resources-cli.md) -t.
- A beállítás közvetlen módosításához használja a Azure PowerShell [set-AzServiceFabricSetting](/powershell/module/az.servicefabric/set-azservicefabricsetting) és a [Remove-AzServiceFabricSetting](/powershell/module/az.servicefabric/remove-azservicefabricsetting) parancsokat.
- Az Azure CLI az [SF cluster Setting](/cli/azure/sf/cluster/setting) paranccsal közvetlenül módosíthatja a beállításokat.

## <a name="next-steps"></a>Következő lépések
* Tudnivalók a [Service Fabric-fürt beállításairól](service-fabric-cluster-fabric-settings.md).
* Ismerje meg, hogyan [méretezheti a fürtöt a és a](service-fabric-cluster-scale-in-out.md)szolgáltatásban.
