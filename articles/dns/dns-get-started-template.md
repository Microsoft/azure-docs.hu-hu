---
title: 'Rövid útmutató: Azure DNS zóna és rekord létrehozása – Azure Resource Manager sablon (ARM-sablon)'
titleSuffix: Azure DNS
description: A cikkből megtudhatja, hogyan hozhat létre DNS-zónát és -rekordot az Azure DNS-ben. Ez egy lépésenként útmutató az első DNS-zóna és -rekord létrehozásához és kezeléséhez Azure Resource Manager sablon (ARM-sablon) használatával.
services: dns
author: duongau
ms.author: duau
ms.date: 09/8/2020
ms.topic: quickstart
ms.service: dns
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 34e810fa8244cf3ff00e57f193624a2c1fa4d755
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107539014"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-an-arm-template"></a>Rövid útmutató: Azure DNS és rekord létrehozása ARM-sablonnal

Ez a rövid útmutató azt ismerteti, hogyan használható Azure Resource Manager sablon (ARM-sablon) egy rekordot tartalmazó DNS-zóna `A` létrehozásához.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-dns-new-zone%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-azure-dns-new-zone) közül származik.

Ebben a rövid útmutatóban egy egyedi DNS-zónát fog létrehozni a `azurequickstart.org` utótaggal. A két IP-címre mutató rekord is a `A` zónába kerül.

:::code language="json" source="~/quickstart-templates/101-azure-dns-new-zone/azuredeploy.json":::

A sablonban két Azure-erőforrás van meghatározva:

- [**Microsoft.Network/dnsZones**](/azure/templates/microsoft.network/dnsZones)
- [**Microsoft.Network/dnsZones/A:**](/azure/templates/microsoft.network/dnsZones/A)Rekord létrehozásához használatos a `A` zónában.

A sablonokkal kapcsolatos további Azure Traffic Manager Az [Azure gyorsindítási sablonjai.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Válassza **a Try it (Próbálja** ki) lehetőséget a következő kódblokkban a Azure Cloud Shell megnyitásához, majd kövesse az utasításokat az Azure-ba való bejelentkezéshez.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-dns-new-zone/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Várjon, amíg látom a parancssort a konzolon.

1. A  PowerShell-szkript másolásához válassza a Másolás lehetőséget az előző kódblokkból.

1. Kattintson a jobb gombbal a rendszerhéj konzolpanelére, majd válassza a **Beillesztés lehetőséget.**

1. Adja meg az értékeket.

    A sablon üzembe helyezése létrehoz egy zónát, amely egy `A` rekordot tartalmaz, és két IP-címre mutat. Az erőforráscsoport neve a projekt neve, az **rg hozzáfűzése** után.

    A sablon üzembe helyezése néhány másodpercet vesz igénybe. Ha elkészült, a kimenet a következő hasonló lesz:

    :::image type="content" source="./media/dns-getstarted-template/create-dns-zone-powershell-output.png" alt-text="Azure DNS sablonhoz Resource Manager PowerShell üzembe helyezési kimenete":::

Azure PowerShell a sablon üzembe helyezéséhez. A Azure PowerShell mellett használhatja a Azure Portal, az Azure CLI és a REST API. További információ az egyéb üzembe helyezési módszerekről: [Sablonok üzembe helyezése.](../azure-resource-manager/templates/deploy-portal.md)

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A **bal oldali panelen** válassza az Erőforráscsoportok lehetőséget.

1. Válassza ki az előző szakaszban létrehozott erőforráscsoportot. Az alapértelmezett erőforráscsoport neve a projekt neve, az **rg hozzáfűzése** után.

1. Az erőforráscsoportnak az itt látható alábbi erőforrásokat kell tartalmaznia:

    :::image type="content" source="./media/dns-getstarted-template/resource-group-dns-zone.png" alt-text="DNS-zóna üzembe helyezési erőforráscsoportja":::

1. Válassza ki a DNS-zónát a utótaggal annak ellenőrzéséhez, hogy a zóna megfelelően jött-e létre a és a értékre hivatkozó `azurequickstart.org` `A` `1.2.3.4` `1.2.3.5` rekordokkal.

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-overview.png" alt-text="DNS-zóna üzembe helyezése":::

1. Másolja ki a névkiszolgálók egyik nevét az előző lépésből.

1. Nyisson meg egy parancssort, és futtassa a következő parancsot:

   ```cmd
   nslookup www.<dns zone name> <name server name>
   ```

   Például:

   ```cmd
   nslookup www.2lwynbseszpam.azurequickstart.org ns1-09.azure-dns.com.
   ```

   Az alábbi képernyőképhez hasonlót kell látnia:

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-validation.png" alt-text="DNS-zóna – nslookup":::

A gazdagépnév `www.2lwynbseszpam.azurequickstart.org` feloldása és lesz, ahogyan Ön `1.2.3.4` `1.2.3.5` konfigurálta. Ez az eredmény ellenőrzi, hogy a névfeloldás megfelelően működik-e.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a DNS-zónával létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja a DNS-zónát és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a következőt hozta létre:

- DNS-zóna
- `A` Rekord

Most, hogy létrehozta első DNS-zónáját és -rekordját egy ARM-sablonnal, létrehozhat rekordokat egy egyéni tartományban található webalkalmazáshoz.

> [!div class="nextstepaction"]
> [Webalkalmazások DNS-rekordjainak létrehozása egyéni tartományban](./dns-web-sites-custom-domain.md)
