---
title: 'Azure ExpressRoute: Global Reach konfigurálása a Azure Portal használatával'
description: Ennek a cikknek a segítségével összekapcsolhatja a ExpressRoute-áramköröket, hogy magánhálózat legyen a helyszíni hálózatok között, és lehetővé tegye a Global Reach használatát a Azure Portal használatával.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/05/2021
ms.author: duau
ms.openlocfilehash: 336bd4aaf881b7315921ef374c92a2ac95ff3c8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102431315"
---
# <a name="configure-expressroute-global-reach-using-the-azure-portal"></a>ExpressRoute-Global Reach konfigurálása a Azure Portal használatával

Ez a cikk segítséget nyújt a ExpressRoute-Global Reach konfigurálásához a PowerShell használatával. További információ: [ExpressRouteRoute Global REACH](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt erősítse meg a következő feltételeket:

* Megismerheti az ExpressRoute áramkör kiépítési [munkafolyamatait](expressroute-workflows.md).
* A ExpressRoute-áramkörök kiépített állapotban vannak.
* Az Azure-beli privát kapcsolatok konfigurálása a ExpressRoute-áramkörökben történik.
* Ha helyileg szeretné futtatni a PowerShellt, ellenőrizze, hogy a Azure PowerShell legújabb verziója van-e telepítve a számítógépen.

## <a name="identify-circuits"></a>Áramkörök azonosítása

1. Egy böngészőből lépjen az [Azure Portalra](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

2. Azonosítsa a használni kívánt ExpressRoute-áramköröket. Engedélyezheti a ExpressRoute Global Reach a két ExpressRoute-áramkör privát társítása között, ha azok a támogatott országokban/régiókban találhatók. Az áramköröket különböző, egymástól eltérő helyeken kell létrehozni. 

   * Ha az előfizetés mindkét áramkört birtokolja, akkor a következő részekben választhatja ki a konfiguráció futtatásához.
   * Ha a két áramkör különböző Azure-előfizetésekben található, akkor egy Azure-előfizetésre van szükség. Ezután adja meg az engedélyezési kulcsot, amikor a másik Azure-előfizetésben futtatja a konfigurációs parancsot.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-global-reach-list.png" alt-text="Képernyőkép a ExpressRoute-áramkörök listájáról.":::

## <a name="enable-connectivity"></a>Kapcsolat engedélyezése

Engedélyezze a kapcsolatot a helyszíni hálózatok között. Az azonos Azure-előfizetésben és a különböző előfizetésekben lévő áramkörökhöz külön utasítások tartoznak.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>ExpressRoute-áramkörök ugyanabban az Azure-előfizetésben

1. Válassza ki az **Azure privát** társ-összevonási konfigurációját. 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="Képernyőkép a ExpressRoute – áttekintés oldalon.":::

1. Válassza a **hozzáadás Global REACH** lehetőséget a *Global REACH konfiguráció hozzáadása* lap megnyitásához.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="Globális elérhetőség engedélyezése a privát társak számára":::

1. A Global Reach konfigurációjának *hozzáadása* lapon adjon meg egy nevet a konfigurációnak. Válassza ki azt a *ExpressRoute-áramkört* , amelyhez kapcsolódni kíván az áramkörhöz, és adja meg a **/29 IPv4** -t a *Global REACH alhálózat* számára. Ebben az alhálózatban az IP-címeket használjuk a két ExpressRoute-áramkör közötti kapcsolat létesítéséhez. Ne használja az ebben az alhálózatban található címeket az Azure-beli virtuális hálózatokban vagy a helyszíni hálózaton. A **Hozzáadás** gombra kattintva adja hozzá az áramkört a privát társ-létrehozási konfigurációhoz.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration.png" alt-text="Képernyőkép a Global Reach hozzáadásáról a privát társak között.":::

1. A Global Reach konfigurációjának befejezéséhez válassza a **Mentés** lehetőséget. Ha a művelet befejeződik, a két helyszíni hálózat között a ExpressRoute-áramköröket is csatlakoztathatja.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="Képernyőkép a privát társas konfigurációk mentéséről.":::

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>ExpressRoute-áramkörök különböző Azure-előfizetésekben

Ha a két áramkör nem ugyanabban az Azure-előfizetésben található, akkor engedélyre van szüksége. A következő konfigurációban az engedélyezést a 2. áramkör előfizetése hozza létre. Ezután az engedélyezési kulcsot átadja az 1-es áramkörnek.

1. Engedélyezési kulcs létrehozása.

   :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/create-authorization-expressroute-circuit.png" alt-text="Képernyőkép az engedélyezési kulcs létrehozásáról."::: 

   Jegyezze fel a Circuit 2 áramköri erőforrás-AZONOSÍTÓját és az engedélyezési kulcsot.

1. Válassza ki az **Azure privát** társ-összevonási konfigurációját. 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="Képernyőkép az Áttekintés oldalon található privát partnerekről.":::

1. Válassza a **hozzáadás Global REACH** lehetőséget a *Global REACH konfiguráció hozzáadása* lap megnyitásához.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="Képernyőkép a Global Reach hozzáadása a privát társak között.":::

1. A Global Reach konfigurációjának *hozzáadása* lapon adjon meg egy nevet a konfigurációnak. Jelölje be a **beváltási engedélyezés** jelölőnégyzetet. Adja meg **az 1** . lépésben generált és beszerzett **EXPRESSROUTE Circuit-azonosítót** . Ezután adjon meg egy **/29 IPv4-** t a *Global REACH alhálózat* számára. Ebben az alhálózatban az IP-címeket használjuk a két ExpressRoute-áramkör közötti kapcsolat létesítéséhez. Ne használja az ebben az alhálózatban található címeket az Azure-beli virtuális hálózatokban vagy a helyszíni hálózaton. A **Hozzáadás** gombra kattintva adja hozzá az áramkört a privát társ-létrehozási konfigurációhoz.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration-with-authorization.png" alt-text="Képernyőfelvétel a Global Reach engedélyezési kulccsal való hozzáadásáról.":::

1. A Global Reach konfigurációjának befejezéséhez válassza a **Mentés** lehetőséget. Ha a művelet befejeződik, a két helyszíni hálózat között a ExpressRoute-áramköröket is csatlakoztathatja.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="Képernyőfelvétel: privát társas konfiguráció mentése Global Reachsal.":::

## <a name="verify-the-configuration"></a>A konfiguráció ellenőrzése

A ExpressRoute-áramköri konfiguráció *alatt ellenőrizze* a Global REACH konfigurációját. Ha helyesen konfigurálta a konfigurációt, a következőképpen kell kinéznie:

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/verify-global-reach-configuration.png" alt-text="Képernyőfelvétel Global Reach konfigurálva.":::

## <a name="disable-connectivity"></a>Az összekapcsolhatóság letiltása

Egy adott áramkör kapcsolatának letiltásához kattintson a *Global REACH neve* melletti Törlés gombra a közöttük lévő kapcsolat eltávolításához. Ezután válassza a **Mentés** lehetőséget a művelet befejezéséhez.

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/disable-global-reach-configuration.png" alt-text="A Global Reach letiltását bemutató képernyőkép.":::

A művelet befejezése után már nincs kapcsolat a helyszíni hálózat és a ExpressRoute-áramkörök között.

## <a name="next-steps"></a>Következő lépések
- [További információ a ExpressRoute Global Reach](expressroute-global-reach.md)
- [ExpressRoute-kapcsolat ellenőrzése](expressroute-troubleshooting-expressroute-overview.md)
- [ExpressRoute-áramkör összekapcsolása egy Azure-beli virtuális hálózattal](expressroute-howto-linkvnet-arm.md)
