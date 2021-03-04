---
title: 'Azure ExpressRoute: IPv6-támogatás hozzáadása a Azure Portal használatával'
description: Ismerje meg, hogyan adhat hozzá IPv6-támogatást az Azure-környezetekhez való kapcsolódáshoz a Azure Portal használatával.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 2/9/2021
ms.author: duau
ms.openlocfilehash: 640e13261fa76fe89c9f5fbd038f20766c509025
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039045"
---
# <a name="add-ipv6-support-for-private-peering-using-the-azure-portal-preview"></a>IPv6-támogatás hozzáadása a privát társak számára a Azure Portal használatával (előzetes verzió)

Ez a cikk azt ismerteti, hogyan adhat hozzá IPv6-támogatást a ExpressRoute-on keresztül az Azure-beli erőforrásokhoz való kapcsolódáshoz a Azure Portal használatával. 

> [!Note]
> Ez a funkció jelenleg előzetes verzióként érhető el az [Azure-régiókban Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-region#azure-regions-with-availability-zones). Az ExpressRoute-áramkör ezért bármely egymással létrehozott hely használatával hozható létre, de a csatlakozáshoz használt IPv6-alapú központi telepítéseknek Availability Zones-vel rendelkező régiókban kell lenniük.

## <a name="register-for-public-preview"></a>Regisztráljon a nyilvános előzetes verzióra
Az IPv6-támogatás hozzáadása előtt regisztrálnia kell az előfizetését. A regisztráláshoz tegye a következőket Azure PowerShellon keresztül:
1.  Jelentkezzen be az Azure-ba, és válassza ki az előfizetést. Ezt a ExpressRoute áramkört tartalmazó előfizetéshez, valamint az Azure-beli üzemelő példányokat tartalmazó előfizetéshez kell elvégeznie (ha azok eltérnek).

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. Regisztrálja az előfizetését a nyilvános előzetes verzióra az alábbi paranccsal:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

A kérést a ExpressRoute csapata 2-3 munkanapon belül jóváhagyja.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Egy böngészőben nyissa meg a [Azure Portal](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>IPv6-alapú privát társak hozzáadása a ExpressRoute-áramkörhöz

1. [Hozzon létre egy ExpressRoute áramkört](https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager) , vagy navigáljon a módosítani kívánt meglévő áramkörhöz.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-circuit.png" alt-text="Navigáljon az áramkörhöz":::

2. Válassza ki az **Azure privát** társ-összevonási konfigurációját.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-peering.png" alt-text="Navigáljon a társításhoz":::

3. Vegyen fel egy IPv6-alapú privát kapcsolatot a meglévő IPv4-alapú privát kapcsolati konfigurációba úgy, hogy a "mindkettő" lehetőséget választja az **alhálózatok** számára, vagy csak az IPv6-alapú privát társat engedélyezi az új áramkörön az "IPv6" választásával. Adjon meg egy pár/126 IPv6-alhálózatot, amelyet Ön az elsődleges és másodlagos hivatkozások számára is tartalmaz. Az egyes alhálózatokból az első használható IP-címet az útválasztóhoz rendeli hozzá, mivel a Microsoft a második használható IP-címet használja az útválasztóhoz. **Mentse** a társítási konfigurációt, miután megadta az összes paramétert.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-peering.png" alt-text="IPv6-alapú privát társak hozzáadása":::

4. A konfiguráció sikeres elfogadását követően az alábbi példához hasonlónak kell megjelennie.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/view-ipv6-peering.png" alt-text="IPv6-alapú privát társak megtekintése":::

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Meglévő virtuális hálózathoz való kapcsolódás frissítése

Kövesse az alábbi lépéseket, ha az Azure-erőforrások meglévő környezete egy olyan régióban található, amelyhez Availability Zones szeretné használni az IPv6-alapú privát kapcsolatot.

1. Navigáljon arra a virtuális hálózatra, amelyhez a ExpressRoute áramkör csatlakozik.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-vnet.png" alt-text="Navigáljon a vnet":::

2. Navigáljon a **címterület** lapra, és adjon hozzá egy IPv6-címterületet a virtuális hálózathoz. **Mentse** a címtartományt.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-space.png" alt-text="IPv6-címtartomány hozzáadása":::

3. Navigáljon az **alhálózatok** lapra, és válassza ki a **GatewaySubnet**. Keresse meg az **IPv6-címtartomány hozzáadása** lehetőséget, és adjon meg IPv6-címtartományt az alhálózat számára. Az átjáró IPv6-alhálózatának/64 vagy nagyobbnak kell lennie. **Mentse** a konfigurációt, miután megadta az összes paramétert.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-gateway-space.png" alt-text="IPv6-címtartomány hozzáadása az alhálózathoz":::

4. Ha van egy már meglévő zóna – redundáns átjáró, navigáljon az ExpressRoute-átjáróhoz, és frissítse az erőforrást az IPv6-kapcsolat engedélyezéséhez. Ellenkező esetben [hozza létre a virtuális hálózati átjárót](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager) egy felesleges SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ) használatával. Ha azt tervezi, hogy FastPath használ, használja a ErGw3AZ.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/refresh-gateway.png" alt-text="Az átjáró frissítése":::

## <a name="create-a-connection-to-a-new-virtual-network"></a>Új virtuális hálózathoz való kapcsolódás létrehozása

Kövesse az alábbi lépéseket, ha azt tervezi, hogy új Azure-erőforrás-készlethez szeretne csatlakozni egy olyan régióban, ahol az IPv6-alapú privát társas kapcsolaton keresztül Availability Zones.

1. Hozzon létre egy kettős veremből álló virtuális hálózatot IPv4-és IPv6-címtartomány mellett. További információt a [virtuális hálózat létrehozása](https://docs.microsoft.com/azure/virtual-network/quick-create-portal#create-a-virtual-network)című témakörben talál.

2. [Hozza létre a kettős verem átjárójának alhálózatát](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager#create-the-gateway-subnet).

3. [Hozza létre a virtuális hálózati átjárót](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager#create-the-virtual-network-gateway) egy zóna – redundáns SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ) használatával. Ha azt tervezi, hogy FastPath használ, használja a ErGw3AZ.

4. [Csatolja a virtuális hálózatot a ExpressRoute-áramkörhöz](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager).

## <a name="limitations"></a>Korlátozások
Míg az IPv6-támogatás elérhető az Availability Zones-vel rendelkező régiókban üzemelő példányokhoz való kapcsolódáshoz, nem támogatja a következő használati eseteket:

* Az Azure-beli üzembe helyezések kapcsolatai nem AZ ExpressRoute Gateway SKU használatával
* Kapcsolatok a nem az-régiókban üzemelő példányokhoz
* ExpressRoute-áramkörök közötti Global Reach kapcsolatok
* ExpressRoute használata a vWAN

## <a name="next-steps"></a>Következő lépések

A ExpressRoute kapcsolatos problémák elhárításához tekintse meg a következő cikkeket:

* [Az ExpressRoute-kapcsolat ellenőrzése](expressroute-troubleshooting-expressroute-overview.md)
* [A hálózati teljesítmény hibaelhárítása](expressroute-troubleshooting-network-performance.md)
