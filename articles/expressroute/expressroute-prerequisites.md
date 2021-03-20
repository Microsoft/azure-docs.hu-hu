---
title: 'Azure ExpressRoute: előfeltételek'
description: Ez az oldal azon követelmények listáját tartalmazza, amelyeknek teljesülniük kell, mielőtt megrendel egy Azure ExpressRoute-kapcsolatcsoportot. Tartalmaz egy ellenőrzőlistát.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 1fad1bca18d16ac3b6a654a3c289d0a14e3cd2e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92204792"
---
# <a name="expressroute-prerequisites--checklist"></a>ExpressRoute-előfeltételek és ellenőrzőlista
Ahhoz, hogy az ExpressRoute-tal tudjon csatlakozni a Microsoft-felhőszolgáltatásokhoz, ellenőriznie kell az alábbi szakaszokban felsorolt követelmények teljesülését.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Azure-fiók
* Egy érvényes és aktív Microsoft Azure-fiók. Erre a fiókra az ExpressRoute-kapcsolatcsoport beállításához van szükség. Az ExpressRoute-kapcsolatcsoportok az Azure-előfizetéseken belüli erőforrások. Az Azure-előfizetés akkor is követelmény, ha a kapcsolat nem Azure-beli Microsoft Cloud Services-szolgáltatásokra (például Microsoft 365) korlátozódik.
* Aktív Microsoft 365-előfizetés (ha Microsoft 365 szolgáltatásokat használ). További információkért tekintse meg a jelen cikk Microsoft 365 speciális követelmények című szakaszát.

## <a name="connectivity-provider"></a>Kapcsolatszolgáltató

* Egy [ExpressRoute kapcsolati partnerrel](expressroute-locations.md#partners) együttműködve csatlakozhat a Microsoft Cloudhoz. A helyszíni hálózata és a Microsoft között [háromféleképpen](expressroute-introduction.md) állíthat be kapcsolatot.
* Ha a szolgáltató nem ExpressRoute-kapcsolatszolgáltató, akkor is csatlakozhat a Microsoft Cloudhoz egy [felhőalapú adatcsere-szolgáltatóval](expressroute-locations.md#connectivity-through-exchange-providers).

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények
* **Redundancia minden egyes egyenrangú helyen**: a Microsoft számára szükséges a redundáns BGP-munkamenetek beállítása a Microsoft útválasztói és a társítási útválasztók között minden ExpressRoute áramkörön (akkor is, ha csak [egy fizikai kapcsolat van a felhőalapú Exchange](expressroute-faqs.md#onep2plink)-hez).
* **Redundancia a vész-helyreállításhoz**: a Microsoft nyomatékosan javasolja, hogy legalább két ExpressRoute-áramkört hozzon létre különböző, egymástól eltérő helyeken, hogy elkerülje az adott meghibásodási pontot.
* **Útválasztás**: attól függően, hogy hogyan csatlakozik a Microsoft Cloudhoz, Ön vagy a szolgáltatónak be kell állítania és kezelnie kell a BGP-munkameneteket az [útválasztási tartományokhoz](expressroute-circuit-peerings.md). Egyes Ethernet-kapcsolati szolgáltatók vagy felhőalapú Exchange-szolgáltatók a BGP-felügyeletet is biztosíthatják értéknövelt szolgáltatásként.
* **NAT**: A Microsoft csak nyilvános IP-címeket fogad el a Microsoft társviszony-létesítésen keresztül. Ha a helyszíni hálózatban magánhálózati IP-címeket használ, Önnek vagy a szolgáltatónak le kell fordítania a magánhálózati IP-címeket a nyilvános IP-címekre [a NAT használatával](expressroute-nat.md).
* **QoS**: A Skype Vállalati verzió különböző szolgáltatásokat tartalmaz (például hang, videó, szöveg), amelyek különböző QoS-kezelést igényelnek. Önnek és a szolgáltatónak teljesítenie kell a [QoS-követelményeket](expressroute-qos.md).
* **Hálózati biztonság**: fontolja meg a [hálózati biztonság](/azure/cloud-adoption-framework/reference/networking-vdc) használatát, amikor ExpressRoute-on keresztül csatlakozik a Microsoft Cloudhoz.

## <a name="microsoft-365"></a>Microsoft 365
Ha azt tervezi, hogy engedélyezi Microsoft 365 a ExpressRoute-on, tekintse át a következő dokumentumokat a Microsoft 365 követelményeivel kapcsolatos további információkért.

* [Microsoft 365 Azure-ExpressRoute](/microsoft-365/enterprise/azure-expressroute)
* [Útválasztás a Microsoft 365 ExpressRoute](/microsoft-365/enterprise/routing-with-expressroute)
* [Magas rendelkezésre állás és feladatátvétel az ExpressRoute használatával](/microsoft-365/enterprise/network-planning-with-expressroute)
* [URL-címek és IP-címtartományok Microsoft 365](/microsoft-365/enterprise/urls-and-ip-address-ranges)
* [Hálózati tervezés és a Microsoft 365 teljesítményének finomhangolása](/microsoft-365/enterprise/network-planning-and-performance)
* [A Microsoft 365 hálózati és áttelepítési tervezése](/microsoft-365/enterprise/network-and-migration-planning)
* [Microsoft 365 integráció helyszíni környezetekkel](/microsoft-365/enterprise/microsoft-365-integration)
* [ExpressRoute az Office 365-ön haladó szintű oktatóvideók](https://channel9.msdn.com/series/aer/)

## <a name="next-steps"></a>Következő lépések
* A ExpressRoute kapcsolatos további információkért tekintse meg a [ExpressRoute gyakori kérdések](expressroute-faqs.md)című témakört.
* Egy ExpressRoute-kapcsolatszolgáltató keresése. Lásd: [ExpressRoute-partnerek és társviszony-létesítési helyszínek](expressroute-locations.md).
* Tekintse meg az [Útválasztás](expressroute-routing.md), a [NAT](expressroute-nat.md) és a [QoS](expressroute-qos.md) követelményeit.
* Az ExpressRoute-kapcsolat konfigurálása.
  * [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-arm.md)
  * [Útválasztás konfigurálása](expressroute-howto-routing-arm.md)
  * [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-arm.md)