---
title: 'Rövid útmutató: Virtuális központ biztonságossá Azure Firewall Manager – Resource Manager sablonnal'
description: Ebből a rövid útmutatóból megtudhatja, hogyan biztosíthatja a virtuális központ biztonságossá Azure Firewall Manager.
services: firewall-manager
author: vhorne
ms.author: victorh
ms.date: 08/28/2020
ms.topic: quickstart
ms.service: firewall-manager
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 88b961f269c89a1e19c0fd4419ffdc33706423de
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529653"
---
# <a name="quickstart-secure-your-virtual-hub-using-azure-firewall-manager---arm-template"></a>Rövid útmutató: A virtuális központ biztonságossá Azure Firewall Manager – ARM-sablonnal

Ebben a rövid útmutatóban egy Azure Resource Manager (ARM-sablon) használatával biztosíthatja a virtuális központ biztonságossá Azure Firewall Manager. Az üzembe helyezett tűzfal rendelkezik egy olyan alkalmazássokkal, amelyek engedélyezik a csatlakozást a `www.microsoft.com` szolgáltatáshoz. A tűzfal teszteléséhez két Windows Server 2019 rendszerű virtuális gép van telepítve. A számítási feladat kiszolgálóhoz egy jump serverrel lehet csatlakozni. A számítási feladat kiszolgálóról csak a kiszolgálóhoz `www.microsoft.com` csatlakozhat.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

További információ a [Azure Firewall Manager: Mi a Azure Firewall Manager?](overview.md).

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>A sablon áttekintése

Ez a sablon egy biztonságos virtuális központot hoz Azure Firewall Manager használatával, valamint a forgatókönyv támogatásához szükséges erőforrásokat.

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/fwm-docs-qs/) közül származik.

:::code language="json" source="~/quickstart-templates/fwm-docs-qs/azuredeploy.json":::

A sablonban több Azure-erőforrás is definiálva van:

- [**Microsoft.Network/virtualWans**](/azure/templates/microsoft.network/virtualWans)
- [**Microsoft.Network/virtualHubs**](/azure/templates/microsoft.network/virtualHubs)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Az ARM-sablon üzembe helyezése az Azure-ban:

1. Az **Azure-ba való bejelentkezéshez** és a sablon megnyitásához válassza az Üzembe helyezés az Azure-ban lehetőséget. A sablon létrehoz egy Azure Firewall, egy virtuális WAN-t és egy virtuális központot, a hálózati infrastruktúrát és két virtuális gépet.

   [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

2. A portál Biztonságos virtuális **központok** lapján írja be vagy válassza ki a következő értékeket:
   - Előfizetés: Válasszon a meglévő előfizetések közül 
   - Erőforráscsoport: Válasszon a meglévő erőforráscsoportok közül, vagy válassza az **Új létrehozása** lehetőséget, majd kattintson az **OK gombra.**
   - Hely: Válasszon ki egy helyet
   - Rendszergazdai felhasználónév: Írja be a rendszergazdai felhasználói fiók felhasználónevét 
   - Rendszergazdai jelszó: Adjon meg egy rendszergazdai jelszót vagy kulcsot

3. Válassza az **Áttekintés + létrehozás**, majd a **Létrehozás** lehetőséget. Az üzembe helyezés akár 10 percet vagy hosszabb időt is igénybe vehet.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Most tesztelje a tűzfalszabályokat, és ellenőrizze, hogy a várt módon működik-e.

1. A Azure Portal tekintse át a **Workload-Srv** virtuális gép hálózati beállításait, és jegyezze fel a magánhálózati IP-címet.
2. Csatlakoztassa a távoli asztalt a **Jump-Srv virtuális** géphez, és jelentkezzen be. Innen nyisson meg egy távoli asztali kapcsolatot a **Workload-Srv magánhálózati IP-címhez.**

3. Nyissa meg az Internet Explorert, és navigáljon a következő címre: `www.microsoft.com`.
4. Válassza **az OK**  >  **Bezárás** lehetőséget Internet Explorer riasztások között.

   Meg kell jelenni a Microsoft kezdőlapján.

5. Nyissa meg a következő címet: `www.google.com`.

   A tűzfal blokkolja a hozzáférést.

Ezzel ellenőrizte, hogy a tűzfalszabályok működnek-e:

- Az egyetlen engedélyezett FQDN-t el tudja érni, de másokat nem.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a tűzfallal létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja a tűzfalat és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tudnivalók a biztonságipartner-szolgáltatókról](trusted-security-partners.md)
