---
title: 'Azure ExpressRoute-sablon: ExpressRoute-áramkör létrehozása'
description: Ismerje meg, hogyan hozhat létre egy Azure ExpressRoute-áramkört egy Azure Resource Manager-sablonnak a Azure PowerShell használatával történő üzembe helyezésével.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/13/2019
ms.author: duau
ms.openlocfilehash: 2e9b6ddc9da4467590946af12a47f1473a4ea494
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92202055"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>ExpressRoute-kör létrehozása Azure Resource Manager sablon használatával

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-sablon](expressroute-howto-circuit-resource-manager-template.md)
> * [Videó – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-circuit-classic.md)
>

Megtudhatja, hogyan hozhat létre ExpressRoute áramkört egy Azure Resource Manager-sablonnak a Azure PowerShell használatával történő telepítésével. A Resource Manager-sablonok fejlesztésével kapcsolatos további információkért tekintse meg a [Resource Manager dokumentációját](../azure-resource-manager/index.yml) és a [sablonra vonatkozó referenciát](/azure/templates/microsoft.network/expressroutecircuits).

## <a name="before-you-begin"></a>Előkészületek

* A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](expressroute-prerequisites.md) és a [munkafolyamatokat](expressroute-workflows.md) .
* Győződjön meg arról, hogy rendelkezik az új hálózati erőforrások létrehozásához szükséges engedélyekkel. Ha nem rendelkezik a megfelelő engedélyekkel, forduljon a fiók rendszergazdájához.
* A lépések megkezdése előtt [megtekintheti a videót](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) .

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>ExpressRoute-kör létrehozása és kiépítése

Az Azure rövid útmutató [sablonjai](https://azure.microsoft.com/resources/templates/) a Resource Manager-sablonok megfelelő gyűjteményével rendelkeznek. Az egyik [meglévő sablon](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) segítségével ExpressRoute-áramkört hozhat létre.

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

További kapcsolódó sablonok megjelenítéséhez kattintson [ide](https://azure.microsoft.com/resources/templates/?term=expressroute).

ExpressRoute-kör létrehozása sablon üzembe helyezésével:

1. Válassza a **kipróbálás** a következő kódrészletből lehetőséget, majd kövesse az utasításokat az Azure Cloud shellbe való bejelentkezéshez.

    ```azurepowershell-interactive
    $circuitName = Read-Host -Prompt "Enter a circuit name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${circuitName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-circuit-create/azuredeploy.json"

    $serviceProviderName = "Equinix"
    $peeringLocation = "Silicon Valley"
    $bandwidthInMbps = 500
    $sku_tier = "Premium"
    $sku_family = "MeteredData"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -circuitName $circuitName -serviceProviderName $serviceProviderName -peeringLocation $peeringLocation -bandwidthInMbps $bandwidthInMbps -sku_tier $sku_tier -sku_family $sku_family

    Write-Host "Press [ENTER] to continue ..."
    ```

   * Az **SKU-szint** meghatározza, hogy a ExpressRoute áramkör [helyi](expressroute-faqs.md#expressroute-local), standard vagy [prémium](expressroute-faqs.md#expressroute-premium)szintű-e. Megadhat helyi, * standard vagy *prémium* *szintűeket* is. A SKU nem módosítható a *standard/prémium* szintről a *helyi* értékre.
   * Az **SKU termékcsalád** meghatározza a számlázási típust. Megadhatja a *Metereddata* a forgalmi díjas csomaghoz és a *Unlimiteddata* korlátlan adatcsomag esetén. A számlázási típust a *Metereddata* és a *Unlimiteddata* között is módosíthatja, de a típus nem módosítható a *Unlimiteddata* értékről a *Metereddata*-re. Egy *helyi* áramkör csak *Unlimiteddata* .
   * A társítás **helye** az a fizikai hely, ahol a Microsofttal való együttműködésre van szükség.

     > [!IMPORTANT]
     > A társítási hely azt a [fizikai helyet](expressroute-locations.md) jelzi, ahol a Microsofttal való együttműködésre van szükség. Ez **nem** a "location" tulajdonsághoz van csatolva, amely arra a földrajzi helyre hivatkozik, ahol az Azure hálózati erőforrás-szolgáltató található. Noha nem kapcsolódnak egymáshoz, célszerű kiválasztania a hálózati erőforrás-szolgáltatót földrajzilag közel az áramkör egyenrangú helyéhez.

    Az erőforráscsoport neve a Service Bus-névtér neve, **RG** hozzáfűzéssel.

2. A PowerShell-szkript másolásához válassza a **Másolás** lehetőséget.
3. Kattintson a jobb gombbal a rendszerhéj-konzolra, majd válassza a **Beillesztés** lehetőséget.

Az Event hub létrehozása néhány percet vesz igénybe.

A Azure PowerShell a sablon üzembe helyezésére szolgál ebben az oktatóanyagban. A sablon egyéb telepítési módszereivel kapcsolatban lásd:

* [A Azure Portal használatával](../azure-resource-manager/templates/deploy-portal.md).
* [Az Azure CLI használatával](../azure-resource-manager/templates/deploy-cli.md).
* [REST API használatával](../azure-resource-manager/templates/deploy-rest.md).

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>ExpressRoute-kapcsolatcsoport megszüntetése és törlése

A **Törlés** ikonra kattintva törölheti a ExpressRoute áramkörét. Tekintse meg az alábbi információkat:

* Az összes virtuális hálózatot le kell választania az ExpressRoute-kapcsolatcsoportról. Ha a művelet meghiúsul, ellenőrizze, hogy a virtuális hálózatok az áramkörhöz vannak-e kapcsolva.
* Ha a ExpressRoute Circuit Service Provider kiépítési állapota kiépítés vagy **kiépítve** **van,** akkor a szolgáltatóval kell együttműködni, hogy kiépítse az áramkört a saját oldalán. Továbbra is fenntartjuk az erőforrásokat és számlázunk, amíg a szolgáltató befejezi az áramkör kiépítését, és értesítést küld nekünk.
* Ha a szolgáltató kiépítte az áramkört (a szolgáltató kiépítési állapota **nincs kiépítve**), törölheti az áramkört. Ez leállítja a kapcsolatcsoport számlázását.

A ExpressRoute áramkörét a következő PowerShell-parancs futtatásával törölheti:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>Következő lépések

Az áramkör létrehozása után folytassa a következő lépésekkel:

* [Az ExpressRoute-áramkör útválasztásának létrehozása és módosítása](expressroute-howto-routing-portal-resource-manager.md)
* [A virtuális hálózat összekapcsolása a ExpressRoute-áramkörrel](expressroute-howto-linkvnet-arm.md)