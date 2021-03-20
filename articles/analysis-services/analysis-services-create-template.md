---
title: Rövid útmutató – Azure Analysis Services Server-erőforrás létrehozása Azure Resource Manager sablon használatával
description: Útmutató egy Azure Analysis Services Server-erőforrás Azure Resource Manager-sablon használatával történő megjelenítéséhez.
author: minewiskan
ms.author: owend
tags: azure-resource-manager
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 08/31/2020
ms.custom: subject-armqs, references_regions
ms.openlocfilehash: 3e776bf41420d38a1b208ce11a6a34e97fa92a15
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89230772"
---
# <a name="quickstart-create-a-server---arm-template"></a>Gyors útmutató: kiszolgálói ARM-sablon létrehozása

Ez a rövid útmutató azt ismerteti, hogyan hozhat létre Analysis Services Server-erőforrást az Azure-előfizetésben egy Azure Resource Manager-sablon (ARM-sablon) használatával.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: a fiók létrehozásához látogasson el az [Azure ingyenes próbaverzióra](https://azure.microsoft.com/offers/ms-azr-0044p/) .
* **Azure Active Directory**: Előfizetésének egy Azure Active Directory-bérlőhöz kell tartoznia. Emellett be kell jelentkeznie az Azure-ba az adott Azure Active Directoryban található fiókkal. További információ: [Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md).

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-analysis-services-create/) közül származik.

:::code language="json" source="~/quickstart-templates/101-analysis-services-create/azuredeploy.json":::

A sablonban definiált egyetlen [Microsoft. AnalysisServices/Servers](/azure/templates/microsoft.analysisservices/servers) erőforrás egy tűzfalszabály használatával.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. A következő üzembe helyezés az Azure-ban hivatkozásra kattintva jelentkezzen be az Azure-ba, és nyisson meg egy sablont. A sablon Analysis Services kiszolgáló-erőforrás létrehozására, valamint a szükséges és választható tulajdonságok megadására szolgál.

   [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json)

2. Válassza ki vagy adja meg a következő értékeket.

    Ha másként nincs megadva, használja az alapértelmezett értékeket.

    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport**: kattintson az **új létrehozása** elemre, majd adjon meg egy egyedi nevet az új erőforráscsoport számára.
    * **Hely**: válassza ki az erőforráscsoport számára létrehozott erőforrások alapértelmezett helyét.
    * **Kiszolgáló neve**: adja meg a kiszolgálói erőforrás nevét. 
    * **Hely**: a Analysis Services figyelmen kívül hagyása. A hely a kiszolgáló helyén van megadva.
    * **Kiszolgáló helye**: adja meg a Analysis Services-kiszolgáló helyét. Ez gyakran ugyanaz a régió, mint az erőforráscsoport számára megadott alapértelmezett hely, de nem kötelező. Például az **USA északi középső** régiója. A támogatott régiók esetében lásd: [Analysis Services elérhetőség régiónként](analysis-services-overview.md#availability-by-region).
    * **SKU neve**: adja meg a létrehozandó Analysis Services-kiszolgáló SKU-nevét. Közül választhat: B1, B2, D1, S0, S1, S2, S3, S4, S8v2, S9v2. Az SKU rendelkezésre állása a régiótól függ. A S0 vagy a D1 használata ajánlott a kiértékeléshez és teszteléshez.
    * **Kapacitás**: adja meg a lekérdezési replika kibővíthető példányainak teljes számát. Egynél több példány kiskálázása csak a régiók kiválasztása esetén támogatott.
    * **Tűzfalbeállítások**: adja meg a kiszolgálóhoz definiálni kívánt bejövő tűzfalszabályok beállításait. Ha nincs megadva, a tűzfal le van tiltva.
    * **Biztonsági mentési blob Container URI**: írja be az SAS URI-t egy privát Azure Blob Storage-tárolóba olvasási, írási és listázási engedélyekkel. Csak akkor szükséges, ha a [Backup/Restore szolgáltatást](analysis-services-backup.md)szeretné használni.
    * **Elfogadom a fenti használati feltételeket**: Válassza ezt.

3. Válassza a **Beszerzés** lehetőséget. A kiszolgáló sikeres üzembe helyezését követően értesítést kap:

   ![ARM-sablon, portál-értesítés üzembe helyezése](./media/analysis-services-create-template/notification.png)

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Az Azure Portal vagy a Azure PowerShell segítségével ellenőrizze, hogy az erőforráscsoport és a kiszolgáló erőforrás létrejött-e.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
(Get-AzResource -ResourceType "Microsoft.AnalysisServices/servers" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használja az Azure Portalt, az Azure CLI-t vagy Azure PowerShell az erőforráscsoport és a kiszolgálói erőforrás törléséhez.

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

Ebben a rövid útmutatóban egy ARM-sablont használt egy új erőforráscsoport és egy Azure Analysis Services kiszolgálói erőforrás létrehozásához. Miután létrehozott egy kiszolgálói erőforrást a sablon használatával, vegye figyelembe a következőket:

> [!div class="nextstepaction"]
> [Rövid útmutató: Kiszolgáló tűzfalának konfigurálása – Portal](analysis-services-qs-firewall.md)   
