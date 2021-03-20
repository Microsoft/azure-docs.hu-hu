---
title: 'Rövid útmutató: Azure Firewall és IP-csoportok létrehozása – Resource Manager-sablon'
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhat egy Azure Resource Manager sablont (ARM-sablon) Azure Firewall és IP-csoportok létrehozásához.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: 7966cbe44fc8984cf3c3d9e2d08a11bf599ff158
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89079132"
---
# <a name="quickstart-create-an-azure-firewall-and-ip-groups---arm-template"></a>Rövid útmutató: Azure Firewall és IP-csoportok létrehozása – ARM-sablon

Ebben a rövid útmutatóban egy Azure Resource Manager sablont (ARM-sablont) használ egy olyan Azure Firewall üzembe helyezéséhez, amely egy hálózati szabályban és alkalmazási szabályban használt minta IP-csoportokkal rendelkezik. Az IP-csoport egy legfelső szintű erőforrás, amely lehetővé teszi, hogy az IP-címeket, tartományokat és alhálózatokat egyetlen objektumba definiálja és csoportosítsa. Ez hasznos az IP-címek Azure Firewall szabályokban való kezeléséhez. Manuálisan is megadhatja az IP-címeket, vagy importálhatja őket egy fájlból.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>A sablon áttekintése

Ez a sablon létrehoz egy Azure Firewall és IP-csoportokat, valamint a szükséges erőforrásokat a Azure Firewall támogatásához.

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox) közül származik.

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json":::

Több Azure-erőforrás van definiálva a sablonban:

- [**Microsoft. Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft. Network/routeTables**](/azure/templates/microsoft.network/routeTables)
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. Network/nyilvános IP**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft. számítás/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft. Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Az ARM-sablon üzembe helyezése az Azure-ban:

1. Válassza az **üzembe helyezés az Azure** -ban lehetőséget az Azure-ba való bejelentkezéshez és a sablon megnyitásához. A sablon létrehoz egy Azure Firewall, a hálózati infrastruktúrát és két virtuális gépet.

   [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json)

2. A portálon az **Azure Firewall létrehozása a IpGroups** lapon írja be vagy válassza ki a következő értékeket:
   - Előfizetés: válasszon a meglévő előfizetések közül 
   - Erőforráscsoport: válasszon a meglévő erőforráscsoportok közül, vagy válassza az **új létrehozása** lehetőséget, majd kattintson **az OK gombra**.
   - Hely: válasszon egy helyet
   - Virtual Network neve: írja be az új virtuális hálózat (VNet) nevét. 
   - 1. IP-csoport neve: írja be az 1. IP-csoport nevét 
   - IP-csoportnév 2: írja be a 2. IP-csoport nevét 
   - Rendszergazdai Felhasználónév: írja be a felhasználónevet a rendszergazda felhasználói fiókhoz. 
   - Hitelesítés: válassza a sshPublicKey vagy a jelszó lehetőséget 
   - Rendszergazdai jelszó: írja be a rendszergazda jelszavát vagy kulcsát

3. Jelölje be **az Elfogadom a fenti feltételeket és kikötéseket** , majd válassza a **vásárlás** lehetőséget. Az üzembe helyezés akár 10 percet is igénybe vehet.

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A Azure Portal tekintse át az üzembe helyezett erőforrásokat, különösen az IP-csoportokat használó tűzfalszabályok.

:::image type="content" source="media/quick-create-ipgroup-template/ipgroups.png" alt-text="IP-csoportok.":::

:::image type="content" source="media/quick-create-ipgroup-template/network-rule.png" alt-text="Hálózati szabályok.":::

A sablonban található tűzfal JSON-szintaxisáról és tulajdonságairól további információt a [Microsoft. Network azureFirewalls-sablon referenciája](/azure/templates/Microsoft.Network/2019-11-01/azureFirewalls)című témakörben talál.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a tűzfallal létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja a tűzfalat és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a következő `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Azure Firewall üzembe helyezése és konfigurálása hibrid hálózaton a Azure Portal használatával](tutorial-hybrid-portal.md)