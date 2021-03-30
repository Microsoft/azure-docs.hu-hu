---
title: 'Gyors útmutató: virtuális központ biztonságossá tétele a Azure Firewall Managerrel – Resource Manager-sablon'
description: Ebből a rövid útmutatóból megtudhatja, hogyan védheti meg virtuális központját a Azure Firewall Manager használatával.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: 050dfa737ad04127f8c72158fbecb2e8e70e05c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89075341"
---
# <a name="quickstart-secure-your-virtual-hub-using-azure-firewall-manager---arm-template"></a>Rövid útmutató: a virtuális központ biztonságossá tétele Azure Firewall Manager-ARM sablon használatával

Ebben a rövid útmutatóban egy Azure Resource Manager sablont (ARM-sablont) használ a virtuális központ biztonságossá tételéhez a Azure Firewall Manager használatával. Az üzembe helyezett tűzfalon van egy alkalmazás-szabály, amely lehetővé teszi a kapcsolódást `www.microsoft.com` . A tűzfal teszteléséhez két Windows Server 2019 virtuális gép van telepítve. Az egyik ugrási kiszolgáló a munkaterhelés-kiszolgálóhoz való kapcsolódásra szolgál. A munkaterhelés-kiszolgálóról csak a következőhöz csatlakozhat: `www.microsoft.com` .

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

A Azure Firewall Managerrel kapcsolatos további információkért lásd: [Mi az a Azure Firewall Manager?](overview.md).

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>A sablon áttekintése

Ez a sablon egy biztonságos virtuális hubot hoz létre a Azure Firewall Manager használatával, valamint a forgatókönyv támogatásához szükséges erőforrásokat.

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/fwm-docs-qs/) közül származik.

:::code language="json" source="~/quickstart-templates/fwm-docs-qs/azuredeploy.json":::

Több Azure-erőforrás van definiálva a sablonban:

- [**Microsoft. Network/virtualWans**](/azure/templates/microsoft.network/virtualWans)
- [**Microsoft. Network/virtualHubs**](/azure/templates/microsoft.network/virtualHubs)
- [**Microsoft. Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft. Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. számítás/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/nyilvános IP**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft. Network/routeTables**](/azure/templates/microsoft.network/routeTables)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Az ARM-sablon üzembe helyezése az Azure-ban:

1. Válassza az **üzembe helyezés az Azure** -ban lehetőséget az Azure-ba való bejelentkezéshez és a sablon megnyitásához. A sablon létrehoz egy Azure Firewall, egy virtuális WAN-t és egy virtuális hubot, a hálózati infrastruktúrát és két virtuális gépet.

   [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

2. A portál **biztonságos virtuális hubok** lapján írja be vagy válassza ki a következő értékeket:
   - Előfizetés: válasszon a meglévő előfizetések közül 
   - Erőforráscsoport: válasszon a meglévő erőforráscsoportok közül, vagy válassza az **új létrehozása** lehetőséget, majd kattintson **az OK gombra**.
   - Hely: válasszon egy helyet
   - Rendszergazdai Felhasználónév: írja be a felhasználónevet a rendszergazda felhasználói fiókhoz. 
   - Rendszergazdai jelszó: írja be a rendszergazda jelszavát vagy kulcsát

3. Válassza az **Áttekintés + létrehozás**, majd a **Létrehozás** lehetőséget. Az üzembe helyezés akár 10 percet is igénybe vehet.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

A tűzfalszabályok tesztelésével ellenőrizze, hogy az a vártnak megfelelően működik-e.

1. A Azure Portal tekintse át a **munkaterhelés-SRV** virtuális gép hálózati beállításait, és jegyezze fel a magánhálózati IP-címet.
2. Csatlakoztasson egy távoli asztalt a **Jump-SRV** virtuális géphez, és jelentkezzen be. Onnan nyisson meg egy távoli asztali kapcsolattal a **munkaterhelés-SRV** magánhálózati IP-címet.

3. Nyissa meg az Internet Explorert, és navigáljon a következő címre: `www.microsoft.com`.
4.   >  Az Internet Explorer biztonsági riasztások ablakában kattintson az OK **Bezárás** gombra.

   A Microsoft kezdőlapjának kell megjelennie.

5. Nyissa meg a következő címet: `www.google.com`.

   A tűzfal blokkolja a hozzáférést.

Most ellenőrizte, hogy a tűzfalszabályok működnek-e:

- Az egyetlen engedélyezett FQDN-t el tudja érni, de másokat nem.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a tűzfallal létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja a tűzfalat és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a következő `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További tudnivalók a biztonsági partner-szolgáltatókról](trusted-security-partners.md)
