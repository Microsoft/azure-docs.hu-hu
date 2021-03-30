---
title: 'Rövid útmutató: nyilvános Load Balancer létrehozása – Azure-sablon'
titleSuffix: Azure Load Balancer
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre egy terheléselosztó Azure Resource Manager sablon használatával.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer by using an Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/09/2020
ms.author: allensu
ms.custom: mvc,subject-armqs
ms.openlocfilehash: 378ab88f4dee0c725e89f77cc6b2ffe049ff877a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97008435"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-by-using-an-arm-template"></a>Rövid útmutató: nyilvános terheléselosztó létrehozása a virtuális gépek terheléselosztásához ARM-sablon használatával

A terheléselosztás magasabb szintű rendelkezésre állást és méretezést biztosít, mivel a bejövő kérelmeket több virtuális gép között osztja szét. 

Ez a rövid útmutató bemutatja, hogyan helyezheti üzembe a standard Load balancert a virtuális gépek terheléselosztásához.

Az ARM-sablon használata kevesebb lépést vesz igénybe, mint a többi üzembe helyezési módszer.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-load-balancer-standard-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-load-balancer-standard-create/) közül származik.

A Load balancernek és a nyilvános IP-SKU-nak egyeznie kell. A standard Load Balancer létrehozásakor létre kell hoznia egy új szabványos nyilvános IP-címet is, amely a standard Load Balancer előtérben van konfigurálva. Ha alapszintű Load balancert szeretne létrehozni, használja [ezt a sablont](https://azure.microsoft.com/resources/templates/201-2-vms-loadbalancer-natrules/). A Microsoft a standard SKU használatát javasolja üzemi számítási feladatokhoz.

:::code language="json" source="~/quickstart-templates/101-load-balancer-standard-create/azuredeploy.json":::

Több Azure-erőforrás van definiálva a sablonban:

- [**Microsoft. Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft. Network/nyilvános IP**](/azure/templates/microsoft.network/publicipaddresses): a terheléselosztó, a megerősített gazdagép és a három virtuális gép mindegyike számára.
- [**Microsoft. Network/bastionHosts**](/azure/templates/microsoft.network/bastionhosts)
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. számítási/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines) (3).
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) (3).
- [**Microsoft. számítás/virtualMachine/Extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) (3): az Internet Information Server (IIS) és a weblapok konfigurálásához használható.

A Azure Load Balancer kapcsolódó további sablonok kereséséhez tekintse meg az [Azure Gyorsindítás sablonjait](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Válassza a **kipróbálás** a következő kódrészletből lehetőséget a Azure Cloud Shell megnyitásához, majd kövesse az utasításokat az Azure-ba való bejelentkezéshez.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name with 12 or less letters or numbers that is used to generate Azure resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $adminUserName = Read-Host -Prompt "Enter the virtual machine administrator account name"
   $adminPassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -location $location -adminUsername $adminUsername -adminPassword $adminPassword

   Write-Host "Press [ENTER] to continue."
   ```

   Várjon, amíg megjelenik a-konzolon megjelenő kérdés.

1. A PowerShell-szkript másolásához válassza a **Másolás** az előző kódrészletből lehetőséget.

1. Kattintson a jobb gombbal a rendszerhéj-konzol ablaktáblára, majd válassza a **Beillesztés** lehetőséget.

1. Adja meg az értékeket.

   A sablon központi telepítése három rendelkezésre állási zónát hoz létre. A rendelkezésre állási zónák csak [bizonyos régiókban](../availability-zones/az-overview.md)támogatottak. Használja az egyik támogatott régiót. Ha nem biztos benne, adja meg a **CentralUS**.

   Az erőforráscsoport neve a projekt neve **RG** hozzáfűzéssel. A következő szakaszban szüksége lesz az erőforráscsoport nevére.

A sablon üzembe helyezése körülbelül 10 percet vesz igénybe. Ha elkészült, a kimenet a következőhöz hasonló:

![Azure standard Load Balancer Resource Manager-sablon PowerShell üzembe helyezési kimenete](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-powershell-output.png)

A Azure PowerShell a sablon üzembe helyezésére szolgál. Használhatja a Azure Portal, az Azure CLI és a REST API is. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../azure-resource-manager/templates/deploy-portal.md).

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki az **erőforráscsoportok** elemet a bal oldali ablaktáblán.

1. Válassza ki az előző szakaszban létrehozott erőforráscsoportot. Az erőforráscsoport alapértelmezett neve a projekt neve **RG** hozzáfűzéssel.

1. Válassza ki a Load balancert. Az alapértelmezett neve a projekt neve, a **-LB** hozzáfűzéssel.

1. Másolja csak a nyilvános IP-cím IP-címének részét, majd illessze be a böngésző címsorába.

   ![Azure standard Load Balancer Resource Manager-sablon nyilvános IP-címe](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-deployment-public-ip.png)

    A böngésző megjeleníti a Internet Information Services (IIS) webkiszolgáló alapértelmezett oldalát.

   ![IIS-webkiszolgáló](./media/quickstart-load-balancer-standard-public-template/load-balancer-test-web-page.png)

Ha látni szeretné, hogy a terheléselosztó mindhárom virtuális gépen osztja szét a forgalmat, kényszerítheti a webböngésző frissítését az ügyfélgépről.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége rájuk, törölje a következőket: 

* Erőforráscsoport
* Terheléselosztóval
* Kapcsolódó források (lehet, hogy a cikkek angol nyelvűek)

Nyissa meg a Azure Portalt, válassza ki azt az erőforráscsoportot, amely tartalmazza a terheléselosztó elemet, majd válassza az **erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a következőket hajtja végre:

* Létrehozott egy virtuális hálózatot a terheléselosztó és a virtuális gépek számára.
* Létrehozott egy Azure Bastion-gazdagépet a felügyelethez.
* Létrehozott egy standard Load balancert és csatlakoztatott virtuális gépeket.
* Konfigurálta a terheléselosztó forgalmi szabályát és az állapot mintavételét.
* Tesztelte a terheléselosztó.

További információért folytassa a Azure Load Balancer oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Azure Load Balancer-oktatóanyagok](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
