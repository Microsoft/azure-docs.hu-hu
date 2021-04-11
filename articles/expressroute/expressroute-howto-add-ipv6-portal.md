---
title: 'Azure ExpressRoute: IPv6-támogatás hozzáadása a Azure Portal használatával'
description: Ismerje meg, hogyan adhat hozzá IPv6-támogatást az Azure-környezetekhez való kapcsolódáshoz a Azure Portal használatával.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/09/2021
ms.author: duau
ms.openlocfilehash: 7f5afc05a8d03d33366a2f76318bcf5e039d4d30
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561661"
---
# <a name="add-ipv6-support-for-private-peering-using-the-azure-portal-preview"></a>IPv6-támogatás hozzáadása a privát társak számára a Azure Portal használatával (előzetes verzió)

Ez a cikk azt ismerteti, hogyan adhat hozzá IPv6-támogatást a ExpressRoute-on keresztül az Azure-beli erőforrásokhoz való kapcsolódáshoz a Azure Portal használatával. 

> [!Note]
> Ez a funkció jelenleg előzetes verzióként érhető el az [Azure-régiókban Availability Zones](../availability-zones/az-region.md#azure-regions-with-availability-zones). Az ExpressRoute-áramkör ezért bármely egymással létrehozott hely használatával hozható létre, de a csatlakozáshoz használt IPv6-alapú központi telepítéseknek Availability Zones-vel rendelkező régiókban kell lenniük.

## <a name="register-for-public-preview"></a>Regisztráljon a nyilvános előzetes verzióra
Az IPv6-támogatás hozzáadása előtt regisztrálnia kell az előfizetését. A regisztráláshoz futtassa a következő parancsokat Azure PowerShellon keresztül:

1.  Jelentkezzen be az Azure-ba, és válassza az előfizetést. Futtassa ezeket a parancsokat a ExpressRoute áramkört tartalmazó előfizetéshez és az Azure-beli üzemelő példányokat tartalmazó előfizetéshez (ha azok eltérnek).

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

1. Regisztrálja az előfizetését a nyilvános előzetes verzióra az alábbi paranccsal:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

A kérést a ExpressRoute csapata 2-3 munkanapon belül jóváhagyja.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Egy böngészőben nyissa meg a [Azure Portal](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>IPv6-alapú privát társak hozzáadása a ExpressRoute-áramkörhöz

1. [Hozzon létre egy ExpressRoute áramkört](expressroute-howto-circuit-portal-resource-manager.md) , vagy navigáljon a módosítani kívánt meglévő áramkörhöz.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-circuit.png" alt-text="Képernyőkép az ExpressRoute Circuit listáról.":::

1. Válassza ki az **Azure privát** társ-összevonási konfigurációját.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-peering.png" alt-text="Képernyőkép a ExpressRoute – áttekintés oldalon.":::

1. Vegyen fel egy IPv6-alapú privát kapcsolatot a meglévő IPv4-alapú privát kapcsolati konfigurációba úgy, hogy a "mindkettő" lehetőséget választja az **alhálózatok** számára, vagy csak az IPv6-alapú privát társat engedélyezi az új áramkörön az "IPv6" választásával. Adjon meg egy pár/126 IPv6-alhálózatot, amelyet Ön az elsődleges és másodlagos hivatkozások számára is tartalmaz. Az egyes alhálózatokból az első használható IP-címet az útválasztóhoz rendeli, mivel a Microsoft a második használható IP-címet használja az útválasztóhoz. **Mentse** a társítási konfigurációt, miután megadta az összes paramétert.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-peering.png" alt-text="Képernyőkép az IPv6 hozzáadásáról a privát partneri oldalon.":::

1. A konfiguráció sikeres elfogadását követően az alábbi példához hasonlónak kell megjelennie.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/view-ipv6-peering.png" alt-text="Képernyőkép a privát kapcsolathoz konfigurált IPv6-hoz.":::

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Meglévő virtuális hálózathoz való kapcsolódás frissítése

Kövesse az alábbi lépéseket, ha az Azure-erőforrások meglévő környezete egy olyan régióban található, amelyben Availability Zones, hogy az IPv6-alapú privát kapcsolatot szeretné használni a használatával.

1. Navigáljon arra a virtuális hálózatra, amelyhez a ExpressRoute áramkör csatlakozik.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-vnet.png" alt-text="Képernyőfelvétel a virtuális hálózatok listájáról.":::

1. Navigáljon a **címterület** lapra, és adjon hozzá egy IPv6-címterületet a virtuális hálózathoz. **Mentse** a címtartományt.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-space.png" alt-text="Képernyőkép az IPv6-címtartomány virtuális hálózathoz való hozzáadásáról.":::

1. Navigáljon az **alhálózatok** lapra, és válassza ki a **GatewaySubnet**. Keresse meg az **IPv6-címtartomány hozzáadása** lehetőséget, és adjon meg IPv6-címtartományt az alhálózat számára. Az átjáró IPv6-alhálózatának/64 vagy nagyobbnak kell lennie. **Mentse** a konfigurációt, miután megadta az összes paramétert.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-gateway-space.png" alt-text="Képernyőfelvétel: IPv6-címterület hozzáadása az alhálózathoz.":::

1. Ha van egy már meglévő zóna – redundáns átjáró, navigáljon az ExpressRoute-átjáróhoz, és frissítse az erőforrást az IPv6-kapcsolat engedélyezéséhez. Ellenkező esetben [hozza létre a virtuális hálózati átjárót](expressroute-howto-add-gateway-portal-resource-manager.md) egy felesleges SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ) használatával. Ha azt tervezi, hogy FastPath használ, használja a ErGw3AZ.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/refresh-gateway.png" alt-text="Képernyőkép az átjáró frissítéséről.":::

## <a name="create-a-connection-to-a-new-virtual-network"></a>Új virtuális hálózathoz való kapcsolódás létrehozása

Kövesse az alábbi lépéseket, ha azt tervezi, hogy új Azure-erőforrás-készlethez szeretne csatlakozni egy olyan régióban, ahol az IPv6-alapú privát társas kapcsolaton keresztül Availability Zones.

1. Hozzon létre egy kettős veremből álló virtuális hálózatot IPv4-és IPv6-címtartomány mellett. További információt a [virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md#create-a-virtual-network)című témakörben talál.

1. [Hozza létre a kettős verem átjárójának alhálózatát](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).

1. [Hozza létre a virtuális hálózati átjárót](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway) egy zóna – redundáns SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ) használatával. Ha azt tervezi, hogy FastPath használ, használja a ErGw3AZ (vegye figyelembe, hogy ez a lehetőség csak az ExpressRoute Direct használatával elérhető áramkörök esetén érhető el).

1. [Csatolja a virtuális hálózatot a ExpressRoute-áramkörhöz](expressroute-howto-linkvnet-portal-resource-manager.md).

## <a name="limitations"></a>Korlátozások
Míg az IPv6-támogatás elérhető az Availability Zones-vel rendelkező régiókban üzemelő példányokhoz való kapcsolódáshoz, a következő használati esetek nem támogatottak:

* Az Azure-beli üzembe helyezések kapcsolatai nem AZ ExpressRoute Gateway SKU használatával
* Kapcsolatok a nem az-régiókban üzemelő példányokhoz
* ExpressRoute-áramkörök közötti Global Reach kapcsolatok
* ExpressRoute használata virtuális WAN-kapcsolattal
* FastPath nem ExpressRoute közvetlen áramkörökkel
* FastPath a következő egyenrangú helyszíneken található áramkörökkel: Dubaj
* Együttélés VPN Gateway

## <a name="next-steps"></a>Következő lépések

A ExpressRoute kapcsolatos problémák elhárításához tekintse meg a következő cikkeket:

* [Az ExpressRoute-kapcsolat ellenőrzése](expressroute-troubleshooting-expressroute-overview.md)
* [A hálózati teljesítmény hibaelhárítása](expressroute-troubleshooting-network-performance.md)