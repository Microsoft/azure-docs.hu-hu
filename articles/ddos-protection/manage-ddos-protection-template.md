---
title: Hozzon létre és engedélyezzen egy Azure DDoS Protection csomagot egy Azure Resource Manager sablon (ARM-sablon) használatával.
description: Megtudhatja, hogyan hozhat létre és engedélyezhet egy Azure DDoS Protection tervet egy Azure Resource Manager sablon (ARM-sablon) használatával.
services: ddos-protection
documentationcenter: na
author: mumian
ms.service: ddos-protection
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.custom: subject-armqs
ms.author: jgao
ms.date: 01/14/2021
ms.openlocfilehash: 75d6c484a0f1d6325aaa7894d8902ff78cadbd74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99092500"
---
# <a name="quickstart-create-an-azure-ddos-protection-standard-using-arm-template"></a>Rövid útmutató: Azure DDoS Protection standard létrehozása ARM-sablonnal

Ez a rövid útmutató azt ismerteti, hogyan használható egy Azure Resource Manager-sablon (ARM-sablon) egy elosztott szolgáltatásmegtagadási (DDoS) védelmi terv és egy virtuális hálózat (VNet) létrehozásához, majd engedélyezi a védelmi tervet a VNet számára. Az Azure DDoS Protection standard szintű csomag olyan virtuális hálózatok készletét határozza meg, amelyekben a DDoS Protection engedélyezve van az előfizetések között. Beállíthat egy DDoS Protection-tervet a szervezet számára, és a virtuális hálózatokat több előfizetésből ugyanahhoz a csomaghoz kapcsolhatja.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-create-and-enable-ddos-protection-plans%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-create-and-enable-ddos-protection-plans) közül származik.

:::code language="json" source="~/quickstart-templates/101-create-and-enable-ddos-protection-plans/azuredeploy.json":::

A sablon két erőforrást határoz meg:

- [Microsoft. Network/ddosProtectionPlans](/azure/templates/microsoft.network/ddosprotectionplans)
- [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Ebben a példában a sablon létrehoz egy új erőforráscsoportot, egy DDoS Protection-tervet és egy VNet.

1. Az Azure-ba való bejelentkezéshez és a sablon megnyitásához válassza az **üzembe helyezés az Azure** -ban gombot.

    [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-create-and-enable-ddos-protection-plans%2Fazuredeploy.json)

1. Adja meg az új erőforráscsoport, a DDoS Protection-terv és a VNet-név létrehozásának értékeit.

    :::image type="content" source="media/manage-ddos-protection-template/ddos-template.png" alt-text="DDoS Gyorsindítás sablon.":::

    - **Előfizetés**: annak az Azure-előfizetésnek a neve, amelyben az erőforrások telepítve lesznek.
    - **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot.
    - **Régió**: az a régió, ahol az erőforráscsoport telepítve van, például az USA keleti régiója.
    - **DDoS Protection-csomag neve**: az új DDoS Protection-csomag neve.
    - **Virtual Network neve**: létrehoz egy nevet az új VNet.
    - **Location**: függvény, amely ugyanazt a régiót használja, mint az erőforrás-telepítéshez használt erőforráscsoport.
    - **Vnet-címek előtagja**: használja az alapértelmezett értéket, vagy adja meg a vnet-címe értékét.
    - **Alhálózat-előtag**: használja az alapértelmezett értéket, vagy adja meg a VNet alhálózatát.
    - A **DDoS elleni védelmi terv engedélyezve**: az alapértelmezett érték `true` a DDoS elleni védelmi terv engedélyezése.

1. Válassza az **Áttekintés + létrehozás** lehetőséget.
1. Ellenőrizze, hogy a sablon érvényesítése sikeres volt-e, majd válassza a **Létrehozás** lehetőséget a telepítés megkezdéséhez.

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

Az Azure CLI vagy Azure PowerShell parancs másolásához kattintson a **Másolás** gombra. Ekkor megnyílik a **kipróbálás** gomb Azure Cloud Shell a parancs futtatásához.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
az network ddos-protection show \
  --resource-group MyResourceGroup \
  --name MyDdosProtectionPlan
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName 'MyResourceGroup' -Name 'MyDdosProtectionPlan'
```

---

A kimenet az új erőforrásokat mutatja.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```Output
{
  "etag": "W/\"abcdefgh-1111-2222-bbbb-987654321098\"",
  "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan",
  "location": "eastus",
  "name": "MyDdosProtectionPlan",
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "resourceGuid": null,
  "tags": null,
  "type": "Microsoft.Network/ddosProtectionPlans",
  "virtualNetworks": [
    {
      "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet",
      "resourceGroup": "MyResourceGroup"
    }
  ]
}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```Output
Name              : MyDdosProtectionPlan
Id                : /subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan
Etag              : W/"abcdefgh-1111-2222-bbbb-987654321098"
ProvisioningState : Succeeded
VirtualNetworks   : [
                      {
                        "Id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet"
                      }
                    ]
```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült, törölheti az erőforrásokat. A parancs törli az erőforráscsoportot és a benne található összes erőforrást.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
az group delete --name MyResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'MyResourceGroup'
```

---

## <a name="next-steps"></a>További lépések

Ha szeretné megtudni, hogyan tekintheti meg és konfigurálhatja a DDoS elleni védelmi terv telemetria, folytassa az oktatóanyagokkal.

> [!div class="nextstepaction"]
> [DDoS Protection-telemetria megtekintése és konfigurálása](telemetry.md)
