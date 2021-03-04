---
title: 'Oktatóanyag: Azure ExpressRoute – átjáró hozzáadása VNet (Azure Portal)'
description: Ez az oktatóanyag végigvezeti a virtuális hálózati átjárók egy VNet való hozzáadásának lépésein a Azure Portal használatával.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 6c6969fdf413c4eb5e7bbcf046fc397834d6c0a2
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038917"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Oktatóanyag: virtuális hálózati átjáró konfigurálása a ExpressRoute-hez a Azure Portal használatával
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasszikus – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Videó – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

Ez az oktatóanyag végigvezeti a virtuális hálózati átjárók meglévő VNet való hozzáadásának lépésein. Ez a cikk végigvezeti a virtuális hálózati (VNet-) átjáró egy meglévő VNet való hozzáadásának, átméretezésének és eltávolításának lépésein. A konfiguráció lépései kifejezetten olyan virtuális hálózatok, amelyek a Resource Manager-alapú üzemi modellel lettek létrehozva, és egy ExpressRoute-konfigurációban lesznek használva. További információ a ExpressRoute virtuális hálózati átjárókkal és az átjáró konfigurációs beállításaival kapcsolatban: [a ExpressRoute virtuális hálózati átjáróinak ismertetése](expressroute-about-virtual-network-gateways.md). 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - Hozzon létre egy átjáró-alhálózatot.
> - Hozzon létre Virtual Network átjárót.

## <a name="prerequisites"></a>Előfeltételek

A feladat lépései a következő konfigurációs hivatkozási listán szereplő értékek alapján VNet használnak. Ezt a listát a példában szereplő lépésekben használjuk. A listát a hivatkozásként való használatra másolhatja, és az értékeket saját értékre cserélheti.

**Konfigurációs hivatkozások listája**

* Virtual Network neve = "TestVNet"
* Virtual Network címtartomány = 192.168.0.0/16
* Alhálózat neve = "FrontEnd" 
    * Alhálózat címtartomány = "192.168.1.0/24"
* Erőforráscsoport = "TestRG"
* Location = "USA keleti régiója"
* Átjáró alhálózatának neve: "GatewaySubnet" az átjáró alhálózatának *GatewaySubnet* kell lennie.
    * Átjáró-alhálózati címtartomány = "192.168.200.0/26"
* Átjáró neve = "ERGW"
* Átjáró nyilvános IP-neve = "MyERGWVIP"
* Átjáró típusa = "ExpressRoute" Ez a típus szükséges egy ExpressRoute-konfigurációhoz.

A konfigurálás megkezdése előtt tekintse meg az alábbi lépések [videóit](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) .

> [!IMPORTANT]
> A privát társítás IPv6-támogatása jelenleg **nyilvános előzetes** verzióban érhető el. Ha a virtuális hálózatát IPv6-alapú privát ExpressRoute-kapcsolattal szeretné összekapcsolni, győződjön meg arról, hogy a virtuális hálózata kettős verem, és követi az [IPv6 for Azure VNet](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)vonatkozó irányelveket.
> 
> 

## <a name="create-the-gateway-subnet"></a>Az átjáróalhálózat létrehozása

1. A [portálon](https://portal.azure.com)navigáljon ahhoz a Resource Manager-beli virtuális hálózathoz, amelyhez virtuális hálózati átjárót szeretne létrehozni.
1. A VNet **Beállítások** szakaszában válassza az **alhálózatok** elemet az alhálózat beállításainak kibontásához.
1. Az **alhálózatok** beállításainál válassza az **+ átjáró alhálózat** lehetőséget egy átjáró-alhálózat hozzáadásához. 
   
    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-gateway-subnet.png" alt-text="Az átjáró alhálózatának hozzáadása":::

1. Az alhálózat **nevénél** automatikusan megjelenik a „GatewaySubnet” érték. Ez az érték szükséges ahhoz, hogy az Azure felismerje, hogy az alhálózat egy átjáró alhálózata. Állítsa be az automatikusan kitöltött **címtartomány** értékeit, hogy megfeleljenek a konfigurációs követelményeinek. Javasoljuk, hogy hozzon létre egy átjáró-alhálózatot egy/27 vagy nagyobb (/26,/25 stb.).

    Ha kettős veremből álló virtuális hálózatot használ, és az IPv6-alapú privát ExpressRoute-t szeretné használni, kattintson az **IP6-címterület hozzáadása** és a bemeneti **IPv6-címtartomány** értékei elemre.

    Ezután kattintson az **OK** gombra az értékek mentéséhez és az átjáró-alhálózat létrehozásához.

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-subnet-gateway.png" alt-text="Az alhálózat hozzáadása":::

## <a name="create-the-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása

1. A portálon, a bal oldalon válassza az **erőforrás létrehozása** lehetőséget, és írja be a "Virtual Network átjáró" kifejezést a keresőmezőbe. Keresse meg a **virtuális hálózati átjárót** a keresési visszáruban, és válassza ki a bejegyzést. A **virtuális hálózati átjáró** lapon válassza a **Létrehozás** lehetőséget.
1. A **virtuális hálózati átjáró létrehozása** lapon adja meg a következő beállításokat, vagy válassza ki ezeket:

    | Beállítás | Érték |
    | --------| ----- |
    | Előfizetés | Ellenőrizze, hogy a megfelelő előfizetés van-e kiválasztva. |
    | Erőforráscsoport | Ha kiválasztja a virtuális hálózatot, az erőforráscsoport automatikusan lesz kiválasztva. | 
    | Name | Nevezze el az átjárót. Ez nem ugyanaz, mint az átjáró alhálózatának elnevezése. Ez a létrehozandó átjáró-objektum neve.|
    | Region | Módosítsa a **régió** mezőt úgy, hogy arra a helyre mutasson, ahol a virtuális hálózat található. Ha a hely nem arra a régióra mutat, ahol a virtuális hálózata, a virtuális hálózat nem jelenik meg a virtuális hálózat választása legördülő menüben. |
    | Átjáró típusa | **ExpressRoute** kiválasztása|
    | Termékváltozat | Válassza ki az átjáró SKU-t a legördülő listából. |
    | Virtuális hálózat | Válassza a *TestVNet* lehetőséget. |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget.|
    | Nyilvános IP-cím | Adja meg a nyilvános IP-cím nevét. |

    > [!IMPORTANT]
    > Ha IPv6-alapú ExpressRoute-t szeretne használni, győződjön meg arról, hogy az az SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ) elemet használja az **SKU**-hoz.
    > 
    > 

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget, majd **hozza létre** az átjáró létrehozásának megkezdéséhez. A rendszer érvényesíti a beállításokat, és az átjáró üzembe helyezése megtörténik. A virtuális hálózati átjáró létrehozása akár 45 percet is igénybe vehet.

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/gateway.png" alt-text="Virtuális hálózati átjáró létrehozása lap mezői":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a ExpressRoute-átjáróra, keresse meg az átjárót a virtuális hálózat erőforráscsoporthoz, és válassza a **Törlés** lehetőséget. Győződjön meg arról, hogy az átjáró nem rendelkezik kapcsolattal egy áramkörhöz.

:::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/delete-gateway.png" alt-text="Virtuális hálózati átjáró törlése":::

## <a name="next-steps"></a>Következő lépések
Miután létrehozta a VNet-átjárót, összekapcsolhatja a VNet egy ExpressRoute-áramkörrel. 

> [!div class="nextstepaction"]
> [Virtual Network összekapcsolása egy ExpressRoute-áramkörrel](expressroute-howto-linkvnet-portal-resource-manager.md)
