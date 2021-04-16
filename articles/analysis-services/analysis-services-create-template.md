---
title: Rövid útmutató – Azure Analysis Services-kiszolgálói erőforrás létrehozása Azure Resource Manager sablon használatával
description: Rövid útmutató, amely bemutatja, hogyan Azure Analysis Services kiszolgálói erőforrást egy Azure Resource Manager használatával.
author: minewiskan
ms.author: owend
ms.date: 08/31/2020
ms.topic: quickstart
ms.service: azure-analysis-services
tags:
- azure-resource-manager
ms.custom:
- subject-armqs
- references_regions
- mode-arm
ms.openlocfilehash: 3e9210bb94cca354a7c0a5cb5c81b6153900b4ec
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538217"
---
# <a name="quickstart-create-a-server---arm-template"></a>Rövid útmutató: Kiszolgáló létrehozása – ARM-sablon

Ez a rövid útmutató azt ismerteti, hogyan hozhat létre Analysis Services-kiszolgálói erőforrást az Azure-előfizetésében egy Azure Resource Manager sablon (ARM-sablon) használatával.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés:** A fiók [létrehozásához látogasson el az Ingyenes Azure-próbaverzió](https://azure.microsoft.com/offers/ms-azr-0044p/) webhelyre.
* **Azure Active Directory**: Előfizetésének egy Azure Active Directory-bérlőhöz kell tartoznia. Emellett be kell jelentkeznie az Azure-ba az adott Azure Active Directoryban található fiókkal. További információ: [Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md).

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-analysis-services-create/) közül származik.

:::code language="json" source="~/quickstart-templates/101-analysis-services-create/azuredeploy.json":::

A [sablonban egyetlen Microsoft.AnalysisServices/servers](/azure/templates/microsoft.analysisservices/servers) erőforrás van meghatározva tűzfalsokkal.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. A következő Üzembe helyezés az Azure-ban hivatkozásra kattintva jelentkezzen be az Azure-ba, és nyisson meg egy sablont. A sablon használatával létrehozhat egy Analysis Services-kiszolgálói erőforrást, és megadhatja a szükséges és választható tulajdonságokat.

   [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json)

2. Válassza ki vagy adja meg a következő értékeket.

    Ha másként nincs megadva, használja az alapértelmezett értékeket.

    * **Előfizetés:** Válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport:** Kattintson az **Új létrehozása** elemre, majd adjon meg egy egyedi nevet az új erőforráscsoportnak.
    * **Hely:** Válassza ki az erőforráscsoportban létrehozott erőforrások alapértelmezett helyét.
    * **Kiszolgáló neve:** Adja meg a kiszolgálói erőforrás nevét. 
    * **Hely:** Hagyja figyelmen kívül Analysis Services. A hely a Kiszolgáló helye helyen van megadva.
    * **Kiszolgáló helye:** Adja meg a Analysis Services helyét. Ez gyakran megegyezik az erőforráscsoporthoz megadott alapértelmezett hely régiójával, de nem kötelező. Például: **USA északi középső középső országa.** A támogatott régiókat lásd: [Analysis Services rendelkezésre állása.](analysis-services-overview.md#availability-by-region)
    * **Termékváltozat neve:** Adja meg a létrehozni Analysis Services termékváltozat nevét. Válasszon a következők közül: B1, B2, D1, S0, S1, S2, S3, S4, S8v2, S9v2. A termékváltozatok rendelkezésre állása a régiótól függ. A kiértékelési és tesztelési folyamathoz az S0 vagy a D1 használata ajánlott.
    * **Kapacitás:** Itt adhatja meg a lekérdezésreplikák horizontális felskálás példányainak teljes számát. A több példányra való horizontális felskálás csak bizonyos régiókban támogatott.
    * **Tűzfalbeállítások:** Adja meg a kiszolgálóra vonatkozó bejövő tűzfalszabályokat. Ha nincs megadva, a tűzfal le van tiltva.
    * **Biztonsági mentési blobtároló** URI-ja: Adja meg egy olvasási, írási és listás Azure Blob Storage rendelkező privát tároló SAS URI-ját. Csak akkor szükséges, ha biztonsági [mentést/visszaállítást kíván használni.](analysis-services-backup.md)
    * **Elfogadom a fenti használati feltételeket**: Válassza ezt.

3. Válassza a **Beszerzés** lehetőséget. A kiszolgáló sikeres üzembe helyezése után értesítést kap:

   ![ARM-sablon, portál üzembe helyezése – értesítés](./media/analysis-services-create-template/notification.png)

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

A Azure Portal vagy Azure PowerShell ellenőrizheti, hogy létrejött-e az erőforráscsoport és a kiszolgálói erőforrás.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
(Get-AzResource -ResourceType "Microsoft.AnalysisServices/servers" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a Azure Portal, az Azure CLI vagy a Azure PowerShell az erőforráscsoport és a kiszolgálói erőforrás törléséhez.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy ARM-sablonnal létrehozott egy új erőforráscsoportot és egy Azure Analysis Services kiszolgálói erőforrást. Miután létrehozta a kiszolgálói erőforrást a sablon használatával, vegye figyelembe a következőket:

> [!div class="nextstepaction"]
> [Rövid útmutató: Kiszolgáló tűzfalának konfigurálása – Portal](analysis-services-qs-firewall.md)   
