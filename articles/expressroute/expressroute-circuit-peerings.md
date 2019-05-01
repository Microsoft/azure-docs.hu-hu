---
title: Az Azure ExpressRoute-Kapcsolatcsoportok és társviszony-létesítés |} A Microsoft Docs
description: Ez az oldal ExpressRoute-Kapcsolatcsoportok és útválasztási tartományok/társviszony-létesítés áttekintést nyújt.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: c4290473a7c1edce02d74a4a787c62ccf0d9c052
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924312"
---
# <a name="expressroute-circuits-and-peering"></a>Az ExpressRoute-Kapcsolatcsoportok és a társviszony-létesítés

Az ExpressRoute-Kapcsolatcsoportok a helyszíni infrastruktúra csatlakoztatni a Microsoft egy kapcsolatszolgáltatón keresztül. Ez a cikk segítségével megismerheti az ExpressRoute-Kapcsolatcsoportok és útválasztási tartományok/társviszony-létesítés. A következő ábrán látható a WAN és a Microsoft közötti kapcsolat logikai megfelelője.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> Az Azure nyilvános társviszony-létesítés elavult, és nem áll rendelkezésre az új ExpressRoute-kapcsolatcsoportot. Új kapcsolatcsoportot támogatja a Microsoft társviszony-létesítés és a privát társviszony-létesítés.  
>

## <a name="circuits"></a>Az ExpressRoute-Kapcsolatcsoportok

ExpressRoute-kapcsolatcsoport a helyszíni infrastruktúra és a egy kapcsolatszolgáltatón keresztül a Microsoft-felhőszolgáltatások közötti logikai kapcsolatot jelöli. Több ExpressRoute-Kapcsolatcsoportok rendezheti. Minden kapcsolatnak az azonos vagy eltérő régiókban is lehetnek, és a helyszíni eredetű különböző kapcsolatszolgáltatók keresztül lehet csatlakoztatni.

Az ExpressRoute-Kapcsolatcsoportok nem feleltethető meg a fizikai entitások. Kapcsolatcsoport egyedileg azonosít egy GUID nevű szolgáltatás kulcsként (s-kulcs), standard. A kulcs az egyetlen adat, Microsoft, a kapcsolatszolgáltató és Ön között. Nincs titkos kulcs biztonsági okokból az s-kulcsot. Van egy 1:1 megfeleltetését között egy ExpressRoute-kapcsolatcsoportot, és az s-kulcsot.

Új ExpressRoute-Kapcsolatcsoportok két független társviszonyok a következők lehetnek: Magánhálózati társviszony-létesítés és Microsoft társviszony-létesítés. Mivel a meglévő ExpressRoute-Kapcsolatcsoportok mindhárom társviszony-létesítést tartalmazza: Azure nyilvános, az Azure-beli privát és a Microsoft. Minden társviszony két független BGP-munkamenetek, azok redundantly magas rendelkezésre állásúként konfigurálva. Van egy 1: n (1 < = N < = 3) az ExpressRoute-Kapcsolatcsoportok közötti megfeleltetés és útválasztási tartományok. ExpressRoute-kapcsolatcsoport is rendelkezik egy, kettő vagy engedélyezve van az ExpressRoute-kapcsolatcsoportonként minden három társviszony.

Minden egyes kapcsolatcsoport rögzített sávszélességű (50 MB/s, 100 MB/s, 200 MB/s, 500 MB/s, 1 GB/s, 10 GB/s), és le van képezve egy kapcsolatszolgáltatón és a egy társviszony-létesítési helyszínen. A sávszélesség választja közösen használja minden kapcsolatcsoport társviszony

### <a name="quotas"></a>Kvótái, korlátai és korlátozásai

Alapértelmezett kvótái és korlátozásai érvényesek minden egyes ExpressRoute-kapcsolatcsoporthoz. Tekintse meg a [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../azure-subscription-service-limits.md) lap kvóták naprakész tájékoztatást.

## <a name="routingdomains"></a>ExpressRoute-társviszony

ExpressRoute-kapcsolatcsoport több útválasztási tartományok/társviszonyok társítva van: Az Azure nyilvános, az Azure privát és a Microsoft. Minden társviszony-létesítés konfigurálva van azonos módon egy útválasztókat pár (aktív – aktív vagy a betöltés megosztása a konfiguráció) a magas rendelkezésre állás érdekében. Azure-szolgáltatások kategóriába sorolt *Azure nyilvános* és *Azure-beli privát* , amelyek az IP-címzési sémát.

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="privatepeering"></a>Az Azure privát társviszony-létesítés

Azure számítási szolgáltatások, azaz a virtuális gépek (IaaS), és a egy virtuális hálózaton belül üzembe helyezett cloud services (PaaS), a privát társviszony-létesítési tartományon keresztül csatlakozhat. A privát társviszony-létesítési tartomány egy megbízható kiterjesztése, a Microsoft Azure-bA az alaphálózat minősül. Beállíthatja a kétirányú kapcsolatokat a központi hálózat és az Azure virtuális hálózatok (Vnetek) között. A társviszony lehetővé teszi, hogy csatlakozhat a virtuális gépek és felhőszolgáltatások közvetlenül a saját magánhálózati IP-címek.  

A privát társviszony-létesítési tartomány egynél több virtuális hálózat kapcsolódhat. Tekintse át a [gyakori kérdéseket tartalmazó oldal](expressroute-faqs.md) korlátok és korlátozások kapcsolatos információkat. Keresse fel a [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../azure-subscription-service-limits.md) naprakész információkat a korlátok lapját.  Tekintse meg a [útválasztás](expressroute-routing.md) lap útválasztási konfigurációja részletes információt.

### <a name="microsoftpeering"></a>Microsoft társviszony-létesítés

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

A Microsoft online szolgáltatásaihoz (az Office 365, Dynamics 365 és Azure PaaS-szolgáltatások) kapcsolatot a Microsoft társviszony-létesítésen keresztül történik. Engedélyezzük a kétirányú kapcsolatokat a WAN és a Microsoft cloud services révén a Microsoft társviszony-létesítési útválasztási tartomány között. Csak keresztül, vagy a kapcsolatszolgáltató tulajdonában lévő nyilvános IP-címek és a Microsoft felhőszolgáltatásai csatlakoznia kell, és az összes meghatározott szabálynak meg kell felelnie. További információkért lásd: a [ExpressRoute-Előfeltételek](expressroute-prerequisites.md) lapot.

Tekintse meg a [gyakori kérdéseket tartalmazó oldal](expressroute-faqs.md) vonatkozó további információ a támogatott szolgáltatások, a költségek és a konfiguráció részleteit. Tekintse meg a [ExpressRoute-helyek](expressroute-locations.md) oldal kapcsolatszolgáltatók ajánlat a Microsoft társviszony-létesítés támogatása a listán szereplő információkat.

### <a name="publicpeering"></a>Azure nyilvános társviszony-létesítés (új kapcsolatcsoportot elavult)

> [!Note]
> 1 NAT IP-címmel társított minden BGP-munkamenet az Azure nyilvános társviszony-létesítés rendelkezik. A nagyobb, mint 2 NAT IP-címeket helyezze át a Microsoft társviszony-létesítésre. Microsoft társviszony-létesítés lehetővé teszi a saját NAT-hozzárendelések konfigurálása, valamint a szelektív előtag hirdetmények útvonalszűrők használni. További információkért lásd: [helyezze át a Microsoft társviszony-létesítés](https://docs.microsoft.com/azure/expressroute/how-to-move-peering).
>

Például az Azure Storage, SQL Database és Websites szolgáltatásokhoz a nyilvános IP-címek érhetők el. A nyilvános IP-címeket, beleértve a virtuális IP-címek a felhőalapú szolgáltatások keresztül a nyilvános társviszony-létesítési útválasztási tartomány-ban üzemeltetett szolgáltatások privát módon csatlakozhat. A nyilvános társviszony-létesítési tartományhoz csatlakozni a DMZ-t, és az összes Azure-szolgáltatások a saját nyilvános IP-címek kapcsolat WAN hálózatból nélkül az interneten keresztül.

Kapcsolat WAN hálózatból mindig kezdeményezett a Microsoft Azure-szolgáltatásokhoz. Microsoft Azure-szolgáltatások nem lesz képes kezdeményeznek kapcsolatokat a hálózatban az útválasztási tartomány segítségével. Nyilvános társviszony-létesítés engedélyezését követően csatlakozhat Azure-szolgáltatásokhoz. Azt teszi lehetővé külön-külön válassza ki a szolgáltatásokat, amelyhez a Microsoft felé haladó útvonalak meghirdetéséhez.

Egyéni útvonalszűrőket meghatározhatja csak a szükséges útvonalakat használhat a hálózaton belül. Tekintse meg a [útválasztás](expressroute-routing.md) lap útválasztási konfigurációja részletes információt.

A nyilvános társviszony-létesítési útválasztási tartomány keresztül támogatott szolgáltatások kapcsolatos további információkért lásd: a [– gyakori kérdések](expressroute-faqs.md).

## <a name="peeringcompare"></a>Társviszony-létesítési összehasonlítása

Az alábbi táblázat összehasonlítja a mindhárom társviszony-létesítést:

|  | **Magánhálózati társviszony-létesítés** | **Microsoft társviszony-létesítés** |  **Nyilvános társviszony-létesítés** (új Kapcsolatcsoportok elavult) |
| --- | --- | --- | --- |
| **Maximális száma. társviszony-létesítés támogatott # előtagok** |4000 alapértelmezésben 10 000-et az ExpressRoute Premium szolgáltatással |200 |200 |
| **Támogatott IP-címtartományok** |Bármely érvényes IP-cím a WAN belül. |Nyilvános IP-címek, vagy a kapcsolatszolgáltató a tulajdonosa. |Nyilvános IP-címek, vagy a kapcsolatszolgáltató a tulajdonosa. |
| **Követelmények száma szerint** |Privát és nyilvános AS-számokat. A nyilvános kell a saját AS-szám, ha használni kívánja. |Privát és nyilvános AS-számokat. Nyilvános IP-címek tulajdonjogát kell igazolnia. |Privát és nyilvános AS-számokat. Nyilvános IP-címek tulajdonjogát kell igazolnia. |
| **Támogatott IP protokollok**| IPv4 |  IPv4, IPv6 | IPv4 |
| **Útválasztási adapter IP-címek** |Az RFC1918 és nyilvános IP-címek |Nyilvános IP-címek útválasztási beállításjegyzékekben regisztrálva. |Nyilvános IP-címek útválasztási beállításjegyzékekben regisztrálva. |
| **MD5-kivonat-támogatás** |Igen |Igen |Igen |

Egy vagy több útválasztási tartománynak lehet, hogy engedélyezi az ExpressRoute-kapcsolatcsoport részeként. Ha szeretné az útválasztási tartományok az azonos VPN elhelyezése, ha azt szeretné, úgy, hogy egyetlen útválasztási tartomány rendelkezik. Helyezheti őket a különböző útválasztási tartományok, a diagram hasonló. Az ajánlott konfigurációs, hogy a privát társviszony-létesítés közvetlenül kapcsolódik az alapvető hálózatra, és a nyilvános és Microsoft társviszony-létesítési csatolások csatlakozik a DMZ-t.

Minden társviszony-létesítés külön BGP-munkamenetek (mindegyik társviszony-létesítési típus egy pár) van szükség. A BGP-munkamenet párok adja meg a magas rendelkezésre állású hivatkozást. Réteg 2 kapcsolatszolgáltatók keresztül kapcsolódik, ha Ön felelős útválasztási konfigurálásának és kezelésének. További információkért tekintse át a [munkafolyamatok](expressroute-workflows.md) ExpressRoute beállításához.

## <a name="health"></a>Az ExpressRoute állapota

Az ExpressRoute-Kapcsolatcsoportok figyelhetik a rendelkezésre állás érdekében a virtuális hálózatok és a sávszélesség kihasználtsága használatával való kapcsolódás [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM).

Npm-et az Azure privát társviszony-létesítés és a Microsoft társviszony-létesítés állapotát figyeli. Tekintse meg a [közzététele](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/) további információt.

## <a name="next-steps"></a>További lépések

* Találjon egy szolgáltatót. Lásd: [ExpressRoute-szolgáltatókat és -helyeket szolgáltatás](expressroute-locations.md).
* Ellenőrizze, hogy minden előfeltétel teljesül-e. Lásd: [ExpressRoute-előfeltételek](expressroute-prerequisites.md).
* Az ExpressRoute-kapcsolat konfigurálása.
  * [ExpressRoute-kapcsolatcsoportok létrehozása és kezelése](expressroute-howto-circuit-portal-resource-manager.md)
  * [Útválasztás (társviszony-létesítés) konfigurálása ExpressRoute-kapcsolatcsoportok számára](expressroute-howto-routing-portal-resource-manager.md)
