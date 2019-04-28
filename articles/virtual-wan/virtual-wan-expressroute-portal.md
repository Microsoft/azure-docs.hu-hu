---
title: Az Azure és helyszíni környezeteket az ExpressRoute-kapcsolatok létrehozásához használja az Azure virtuális WAN |} A Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre az Azure és helyszíni környezeteket az ExpressRoute-kapcsolatok használata Azure virtuális WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 04/02/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 7b7adcc85b9274af45ddab653e875377e959e40c
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766741"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan-preview"></a>Oktatóanyag: Azure virtuális WAN (előzetes verzió) használatával egy ExpressRoute-társítás létrehozása

Az oktatóanyag bemutatja, hogyan kapcsolódhat a Virtual WAN használatával az Azure-ban lévő erőforrásaihoz egy ExpressRoute-kapcsolatcsoport és társítás segítségével. A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintését](virtual-wan-about.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Virtuális WAN létrehozása
> * Elosztó létrehozása
> * Egy kapcsolatcsoport keresése és társítása a központhoz
> * A kapcsolatcsoport társítása a központ(ok)hoz
> * Virtuális hálózat csatlakoztatása elosztóhoz
> * A virtuális WAN megtekintése
> * Erőforrás állapotának megtekintése
> * Kapcsolatok monitorozása

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Előzetes teendők

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="register"></a>A funkció regisztrálása

A Virtual WAN konfigurálása előtt regisztrálnia kell az előfizetését az előzetes verzióban. Máskülönben nem használhatja a Virtual WAN-t a portálon. E-mail küldése a regisztrációhoz **azurevirtualwan\@microsoft.com** az előfizetés-azonosítóval. Az előfizetés regisztrációja után egy e-mailt fog kapni.

**Előzetes verzióval kapcsolatos szempontok:**

Az ExpressRoute-kapcsolatcsoport engedélyezni kell, amely támogatja az adott országban [ExpressRoute globális elérhetőségű](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#where-is-expressroute-global-reach-supported).

## <a name="vnet"></a>1. Virtuális hálózat létrehozása

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="openvwan"></a>2. Virtuális WAN létrehozása

Egy böngészőből lépjen az [Azure Portalra (előzetes verzió)](https://aka.ms/azurevirtualwanpreviewfeatures), majd jelentkezzen be Azure-fiókjával.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

### <a name="getting-started-page"></a>Első lépések oldal

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-gettingstarted-include.md)]

## <a name="hub"></a>3. Elosztó létrehozása

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="hub"></a>4. Egy kapcsolatcsoport keresése és társítása a központhoz

1. Válassza ki a vWAN és **virtuális WAN architektúra**válassza **ExpressRoute-Kapcsolatcsoportok**.
1. Ha az ExpressRoute-kapcsolatcsoport a vWAN ugyanazt az előfizetést, kattintson a **válassza ki az ExpressRoute-kapcsolatcsoport** az előfizetéséhez vagy előfizetéseihez. 
1. A legördülő használ, válassza ki az ExpressRoute, amelyet szeretne hozzárendelni a hub.
1. Ha az ExpressRoute-kapcsolatcsoport nem ugyanabban az előfizetésben, vagy hogy adtak meg [hitelesítési kulcsot és a társ azonosító](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)válassza **váltja be egy engedélyezési kulcsot expressroute-kapcsolatcsoporthoz keresése**
1. Adja meg a következő részleteket:
1. **Hitelesítési kulcs** – A kapcsolatcsoport tulajdonosa hozza létre a fent leírt módon
1. **Társ-kapcsolatcsoport URI** – A kapcsolatcsoport URI-je, amelyet a kapcsolatcsoport tulajdonosa ad meg, és a kapcsolatcsoport egyedi azonosítójaként szolgál.
1. **Útválasztás súly** - [útválasztás súly](../expressroute/expressroute-optimize-routing.md) lehetővé teszi, hogy inkább az egyes elérési utakat, több Kapcsolatcsoportok társviszony-létesítési különböző helyekről ugyanahhoz a hubhoz csatlakoztatott
1. Kattintson a **keresse meg a kapcsolatcsoport** , és válassza ki a kapcsolatcsoportot, ha található.
1. Válassza ki a legördülő listából legalább 1 hubs le, és kattintson a **mentése**.

## <a name="vnet"></a>5. A virtuális hálózat csatlakoztatása egy elosztóhoz

Ebben a lépésben a társviszony-kapcsolatot hozzuk létre az elosztó és egy virtuális hálózat között. Ismételje meg a fenti lépéseket minden csatlakoztatni kívánt virtuális hálózat esetében.

1. A virtuális WAN lapján kattintson a **Virtuális hálózati kapcsolat** elemre.
2. A virtuális hálózati kapcsolat lapján kattintson a **+Kapcsolat hozzáadása** elemre.
3. A **Kapcsolat hozzáadása** lapon töltse ki a következő mezőket:

    * **Kapcsolat neve** – Nevezze el a kapcsolatot.
    * **Elosztók** – Válassza ki azt az elosztót, amelyet a kapcsolattal társítani kíván.
    * **Előfizetés** – Ellenőrizze az előfizetést.
    * **Virtuális hálózat** – Válassza ki azt a virtuális hálózatot, amelyet az elosztóhoz csatlakoztatni kíván. A virtuális hálózat nem rendelkezhet már meglévő virtuális hálózati átjáróval.


## <a name="viewwan"></a>6. A virtuális WAN megtekintése

1. Lépjen a virtuális WAN-ra.
2. Az Áttekintés lapon a térképen látható pontok mindegyike egy elosztót jelöl. Az elosztók állapotösszegzéséért vigye a mutatót az egyes pontok fölé.
3. Az elosztók és kapcsolatok szakaszában láthatja az elosztók állapotát, helyét, régióját, VPN-kapcsolati állapotát, valamint a bájtban kifejezett be- és kimenő forgalmát.

## <a name="viewhealth"></a>7. Az erőforrás állapotának megtekintése

1. Lépjen a WAN-ra.
2. A WAN lapjának **TÁMOGATÁS + hibaelhárítás** szakaszában kattintson az **Állapot** lehetőségre, és tekintse meg az erőforrást.

## <a name="connectmon"></a>8. Kapcsolatok monitorozása

Létrehozhat egy kapcsolatot az Azure-beli virtuális gépek és a távoli helyek közötti kommunikáció monitorozására. A kapcsolatmonitor beállításával kapcsolatos információkért lásd a [hálózati kommunikáció monitorozását](~/articles/network-watcher/connection-monitor.md) ismertető szakaszt. A forrásmező a virtuális gép IP-címe az Azure-ban, a cél IP-cím pedig a hely IP-címe.

## <a name="cleanup"></a>9. Az erőforrások eltávolítása

Ha ezekre az erőforrásokra már nincs szüksége, használhatja [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) , távolítsa el az erőforráscsoportot és az összes benne található erőforrást. A „myResourceGroup” helyére írja be az erőforráscsoport nevét, és futtassa a következő PowerShell-parancsot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Virtuális WAN létrehozása
> * Elosztó létrehozása
> * Egy kapcsolatcsoport keresése és társítása a központhoz
> * A kapcsolatcsoport társítása a központ(ok)hoz
> * Virtuális hálózat csatlakoztatása elosztóhoz
> * A virtuális WAN megtekintése
> * Erőforrás állapotának megtekintése
> * Kapcsolatok monitorozása

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.
