---
title: Az Azure ExpressRoute használata a távoli felhasználók támogatásához
description: Ez az oldal azt ismerteti, hogyan használhatja az Azure ExpressRoute-t a COVID-19 világjárvány miatti távoli működéshez.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/22/2020
ms.author: duau
ms.openlocfilehash: d51f47b73fe572ce81d3e7b54f902f94fcd11b5b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89567673"
---
# <a name="using-azure-expressroute-to-create-hybrid-connectivity-to-support-remote-users"></a>Hibrid kapcsolat létrehozása az Azure ExpressRoute használatával a távoli felhasználók támogatásához

Ez a cikk azt ismerteti, hogyan használható a ExpressRoute, az Azure, a Microsoft Network és az Azure partneri ökoszisztéma a távoli működéshez.

## <a name="connecting-to-azure-services-with-expressroute"></a>Csatlakozás az Azure-szolgáltatásokhoz a ExpressRoute

>[!NOTE]
>Ez a cikk azt ismerteti, hogyan használhatja a ExpressRoute, az Azure, a Microsoft Network és az Azure partner-ökoszisztémát a COVID-19-válság miatt felmerülő hálózati problémák megoldásához.
>

A [ExpressRoute](expressroute-introduction.md) egy olyan Azure-szolgáltatás, amely lehetővé teszi a Microsoft-adatközpontok és a helyszíni vagy közös elhelyezési létesítményben lévő infrastruktúra közötti magánhálózati kapcsolódást. Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át. Biztonságos kapcsolattal, megbízhatósággal és sebességgel rendelkeznek, és az interneten keresztüli szokásos kapcsolatoknál alacsonyabb és konzisztens késéssel rendelkeznek.

## <a name="useful-links"></a>Hasznos hivatkozások

* [A meglévő ExpressRoute-áramkör sávszélességének javítása](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [ExpressRoute-figyelés, mérőszámok és riasztások](expressroute-monitoring-metrics-alerts.md#expressroute-gateway-connections-in-bitsseconds)
* [Útvonal-optimalizálás ExpressRoute](expressroute-optimize-routing.md)
* [Microsoft 365 Azure-ExpressRoute](/microsoft-365/enterprise/azure-expressroute)
* [Aszimmetrikus útválasztási megfontolások](expressroute-asymmetric-routing.md)
* [Támogatási kérelem megnyitása a Azure Portalban](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="troubleshoot"></a>Hibaelhárítás

* [Az ExpressRoute-kapcsolat ellenőrzése](expressroute-troubleshooting-expressroute-overview.md)
* ARP-tábla lekérése a [Resource Managerben](expressroute-troubleshooting-arp-resource-manager.md) és a [Klasszikusban](expressroute-troubleshooting-arp-classic.md)
* [Sikertelen áramkör alaphelyzetbe állítása](reset-circuit.md)
* [Hálózati teljesítmény – problémamegoldás](expressroute-troubleshooting-network-performance.md)

## <a name="next-steps"></a>Következő lépések

* [További tudnivalók a ExpressRoute kapcsolati modelljeiről](expressroute-connectivity-models.md)
* Ismerje meg az ExpressRoute-kapcsolatokat és útválasztási tartományokat. Lásd: [ExpressRoute-áramkörök és útválasztási tartományok](expressroute-circuit-peerings.md)
* Találjon egy szolgáltatót. Lásd: [ExpressRoute Partners és peering Locations](expressroute-locations.md)
* Ellenőrizze, hogy minden előfeltétel teljesül-e. Lásd: [ExpressRoute-előfeltételek](expressroute-prerequisites.md).
* [Az ExpressRoute-kapcsolatcsoport létrehozása és módosítása](expressroute-howto-circuit-portal-resource-manager.md)
* [ExpressRoute-kapcsolatcsoport társviszonyának létrehozása és módosítása](expressroute-howto-routing-portal-resource-manager.md)
* [Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporttal](expressroute-howto-linkvnet-portal-resource-manager.md)
