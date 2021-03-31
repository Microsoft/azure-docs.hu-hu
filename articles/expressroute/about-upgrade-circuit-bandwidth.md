---
title: Tudnivalók az áramköri sávszélesség frissítéséről | Azure-ExpressRoute
description: Ez a cikk a ExpressRoute-áramköri sávszélesség frissítésének ajánlott eljárásait ismerteti.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: duau
ms.openlocfilehash: 7831e7944321e074c312853e1534c47970ebdfdb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89397950"
---
# <a name="about-upgrading-expressroute-circuit-bandwidth"></a>Tudnivalók a ExpressRoute-áramkör sávszélességének frissítéséről

A ExpressRoute dedikált és privát kapcsolatot tesz lehetővé a Microsoft globális hálózatához. A kapcsolódást egy ExpressRoute-partner hálózata, illetve a Microsoft Enterprise Edge (MSEE) eszközeinek közvetlen kapcsolata könnyíti meg. A fizikai kapcsolat konfigurálását és tesztelését követően engedélyezheti a 2. rétegbeli és a 3. rétegbeli kapcsolatot egy ExpressRoute áramkör létrehozásával és a társítás konfigurálásával.

## <a name="upgrade-circuit-bandwidth"></a><a name="upgrade"></a>Frissítési áramköri sávszélesség

Az áramköri sávszélesség frissítéséhez a ExpressRoute Direct vagy a ExpressRoute-partnernek [elegendő sávszélességet kell biztosítania](#considerations) ahhoz, hogy a frissítés sikeres legyen.

Ha a kapacitás elérhető, a következő módszerekkel frissítheti az áramkört:

* [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [PowerShell](expressroute-howto-circuit-arm.md#modify)
* [Azure CLI](howto-circuit-cli.md#modify)

## <a name="capacity-considerations"></a><a name="considerations"></a>Kapacitással kapcsolatos szempontok

### <a name="insufficient-expressroute-partner-bandwidth"></a><a name="bandwidth"></a>Elégtelen ExpressRoute-partneri sávszélesség

Ha a ExpressRoute-partner nem rendelkezik elegendő kapacitással, létre kell hoznia egy új áramkört, amely a kívánt sávszélességre van konfigurálva. A kapcsolat fenntartása érdekében ne törölje a régi áramkört addig, amíg az újonnan létrehozott áramkör nincs kiépítve, a társítás be van állítva, és (a magánjellegű kapcsolattal kapcsolatban) a ExpressRoute virtuális hálózati átjáróhoz tartozó kapcsolati objektum lett kiépítve.

Ha a ExpressRoute-partnernek nincs elegendő rendelkezésre álló kapacitása, további kapacitást kell kérnie a kívánt társítási helyen. Az új kapacitás kiépítése után a [frissítési áramköri sávszélesség](#upgrade) szakaszban található cikkekben ismertetett lépéseket követve létrehozhat egy új áramkört, konfigurálhatja a kapcsolatot, és törölheti a régi áramkört.


### <a name="insufficient-expressroute-direct-bandwidth"></a><a name="bandwidth"></a>Nem elegendő ExpressRoute közvetlen sávszélesség

Ha a közvetlen ExpressRoute nem rendelkezik elegendő kapacitással, törölheti a ExpressRoute közvetlen erőforráshoz társított áramköröket, amelyekre már nincs szükség, vagy létrehozhat egy új ExpressRoute Direct-erőforrást is. A ExpressRoute Direct erőforrás kezelésével kapcsolatos útmutatásért tekintse meg a [ExpressRoute Direct konfigurálását ismertető témakört](how-to-expressroute-direct-portal.md).

## <a name="next-steps"></a>Következő lépések

* [Kapcsolatcsoport létrehozása és módosítása](expressroute-howto-circuit-portal-resource-manager.md)
* [Társhálózati konfiguráció létrehozása és módosítása](expressroute-howto-routing-portal-resource-manager.md)
* [Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporttal](expressroute-howto-linkvnet-portal-resource-manager.md)
