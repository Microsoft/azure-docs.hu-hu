---
title: 'Azure ExpressRoute: az áramkörök NAT-követelményei'
description: Ez az oldal ExpressRoute-kapcsolatcsoportok NAT-konfigurálásának és -kezelésének részletes követelményeit ismerteti.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: a2c322c765d39a3afe4974ed88bf4dc18fd467a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89569645"
---
# <a name="expressroute-nat-requirements"></a>Az ExpressRoute NAT-követelményei
Ahhoz, hogy az ExpressRoute-tal tudjon csatlakozni a Microsoft-felhőszolgáltatásokhoz, NAT-okat kell beállítania és kezelnie. Egyes kapcsolatszolgáltatók felügyelt szolgáltatásként kínálják a NAT beállítását és kezelését. Ellenőrizze kapcsolatszolgáltatójánál, hogy kínál-e ilyen szolgáltatást. Ha nem, akkor meg kell felelnie az alább ismertetett követelményeknek. 

A különböző útválasztási tartományok áttekintését az [ExpressRoute-kapcsolatcsoportok és útválasztási tartományok](expressroute-circuit-peerings.md) oldalon tekintheti meg. Az Azure nyilvános és a Microsoft társviszony-létesítés nyilvános IP-cím követelményeinek való megfeleléshez javasoljuk, hogy állítsa be a NAT-ot a hálózata és a Microsoft között. Ez a szakasz a beállítandó NAT-infrastruktúra részletes leírását tartalmazza.

## <a name="nat-requirements-for-microsoft-peering"></a>A Microsoft társviszony-létesítésre vonatkozó NAT-követelmények
A Microsoft társviszony-létesítési útvonal lehetővé teszi, hogy az Azure nyilvános társviszony-létesítési útvonalon keresztül nem támogatott Microsoft-felhőszolgáltatásokhoz csatlakozzon. A szolgáltatások listája Microsoft 365 szolgáltatásokat tartalmaz, például az Exchange Online-t, a SharePoint Online-t és a Skype vállalati verzióit. A Microsoft tervezi a kétirányú kapcsolatok támogatását a Microsoft társviszony-létesítésen keresztül. A Microsoft-felhőszolgáltatások felé irányuló forgalmat forrásoldali hálózati címfordítással érvényes nyilvános IPv4-címekké kell alakítani, mielőtt belép a Microsoft hálózatába. A Microsoft felhőszolgáltatásai felől az Ön hálózata felé irányuló forgalmon forrásoldali hálózati címfordítást kell végrehajtani az internetes peremhálózaton az [aszimmetrikus útválasztás](expressroute-asymmetric-routing.md) megelőzése érdekében. Az alábbi ábra magas szintű képet biztosít arról, hogyan kell beállítani a NAT-t a Microsoft-társak számára.

![Magas szintű diagram, amely bemutatja, hogyan kell beállítani a NAT-t a Microsoft-partnerek számára.](./media/expressroute-nat/expressroute-nat-microsoft.png) 

### <a name="traffic-originating-from-your-network-destined-to-microsoft"></a>Az Ön hálózatából eredő, a Microsoft felé irányuló forgalom
* Biztosítania kell, hogy a forgalom a Microsoft társviszony-létesítési útvonalra érvényes nyilvános IPv4-címmel lépjen be. A Microsoftnak képesnek kell lennie ellenőriznie az IPv4 NAT-címkészlet tulajdonosát egy regionális útválasztási internetes jegyzékben (RIR) vagy egy internetes útválasztási jegyzékben (RIR) A rendszer a NAT-tal társviszonyban lévő AS-szám és az ahhoz használt IP-címek alapján ellenőrzést hajt végre. Az útválasztási nyilvántartásokkal kapcsolatos információkért tekintse meg [az ExpressRoute útválasztási követelményeit](expressroute-routing.md) ismertető oldalt.
* Az Azure nyilvános társviszony-létesítési beállításhoz és az egyéb ExpressRoute-kapcsolatcsoportokhoz használt IP-címek nem hirdethetők a Microsoft felé a BGP-munkameneten keresztül. A társviszony-létesítésen keresztül hirdetett NAT IP-előtag hosszára nem vonatkoznak korlátozások.
  
  > [!IMPORTANT]
  > A Microsoft számára hirdetett NAT IP-készlet nem hirdethető meg az interneten. Ez megszakítja a más Microsoft-szolgáltatások kapcsolatait.
  > 
  > 

### <a name="traffic-originating-from-microsoft-destined-to-your-network"></a>A Microsofttól eredő, az Ön hálózata felé irányuló forgalom
* Bizonyos forgatókönyvek megkövetelik, hogy a Microsoft kapcsolatot kezdeményezzen az Ön hálózatán üzemeltetett szolgáltatásvégpontokkal. A forgatókönyv tipikus példája a hálózatban üzemeltetett ADFS-kiszolgálókhoz Microsoft 365-ból való kapcsolódás. Ilyen esetekben a megfelelő előtagokat ki kell szivárogtatnia a hálózatából a Microsoft-társviszonyba. 
* Az [aszimmetrikus útválasztás](expressroute-asymmetric-routing.md) kiküszöbölése érdekében a Microsoft-adatforgalmon forrásoldali hálózati címfordítást kell végrehajtani az internetes peremhálózaton, a hálózaton belüli szolgáltatásvégpontok számára. Az ExpressRoute-on keresztül kapott útvonallal megegyező cél IP-címmel ellátott kérelmeket **és válaszokat** a rendszer minden esetben ExpressRoute-on keresztül küldi el. Aszimmetrikus útválasztás akkor jön létre, ha a kérelem interneten keresztül érkezik, a választ viszont ExpressRoute-on keresztül küldjük el. A bejövő Microsoft-adatforgalom internetes peremhálózaton történő forrásoldali hálózati címfordítása úgy küszöböli ki ezt a problémát, hogy a válasz adatforgalmát újra az internetes peremhálózatra kényszeríti.

![Aszimmetrikus útválasztás az ExpressRoute-tal](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="nat-requirements-for-azure-public-peering"></a>Az Azure nyilvános társviszony-létesítés NAT-követelményei

> [!NOTE]
> Az Azure nyilvános társ-összevonása nem érhető el az új áramkörökhöz.
> 

Az Azure nyilvános társviszony-létesítési útvonal használatával az Azure-ban üzemeltetett összes szolgáltatáshoz csatlakozhat a nyilvános IP-címeiken keresztül. Ezen szolgáltatások közé tartoznak az [ExpressRoute – Gyakori kérdések](expressroute-faqs.md) című dokumentumban felsorolt szolgáltatások, valamint az ISV-k által a Microsoft Azure-ban üzemeltetett szolgáltatások. 

> [!IMPORTANT]
> A Microsoft Azure-szolgáltatásokhoz a nyilvános társviszony-létesítéssel létrehozott kapcsolatokat mindig az Ön hálózata kezdeményezi a Microsoft hálózata felé. Ezért a Microsoft Azure-szolgáltatásokból nem indíthatók munkamenetek az Ön hálózatába az ExpressRoute-on keresztül. Ilyen próbálkozás esetén a meghirdetett IP-címekre küldött csomagok az ExpressRoute helyett az internetet használják.
> 

A Microsoft Azure felé a nyilvános társviszony-létesítésen keresztül irányuló forgalmat forrásoldali hálózati címfordítással érvényes nyilvános IPv4-címekké kell alakítani, mielőtt belép a Microsoft hálózatába. Az alábbi ábrán annak magas szintű ábrázolása látható, hogy hogyan kell beállítani a NAT-ot a fenti követelmények teljesítéséhez.

![Magas szintű diagram, amellyel a NAT beállítható úgy, hogy az érvényes nyilvános IPv4-címekre címfordítást, mielőtt belépnek a Microsoft hálózatba.](./media/expressroute-nat/expressroute-nat-azure-public.png) 

### <a name="nat-ip-pool-and-route-advertisements"></a>NAT IP-készlet és útvonalhirdetések
Biztosítania kell, hogy a forgalom az Azure nyilvános társviszony-létesítési útvonalra érvényes nyilvános IPv4-címmel lépjen be. A Microsoftnak képesnek kell lennie ellenőriznie az IPv4 NAT-címkészlet tulajdonjogát egy regionális útválasztási internetes jegyzékben (RIR) vagy egy internetes útválasztási jegyzékben (RIR) A rendszer a NAT-tal társviszonyban lévő AS-szám és az ahhoz használt IP-címek alapján ellenőrzést hajt végre. Az útválasztási nyilvántartásokkal kapcsolatos információkért tekintse meg [az ExpressRoute útválasztási követelményeit](expressroute-routing.md) ismertető oldalt.

A társviszony-létesítésen keresztül hirdetett NAT IP-előtag hosszára nem vonatkoznak korlátozások. Meg kell figyelnie a NAT-készletet, és biztosítania kell, hogy nem fogy ki a NAT-munkamenetekből.

> [!IMPORTANT]
> A Microsoft számára hirdetett NAT IP-készlet nem hirdethető meg az interneten. Ez megszakítja a más Microsoft-szolgáltatások kapcsolatait.
> 
> 

## <a name="next-steps"></a>Következő lépések
* Tekintse meg az [Útválasztás](expressroute-routing.md) és a [QoS](expressroute-qos.md) követelményeit.
* További információkért lásd: [ExpressRoute-kapcsolatcsoportok kiépítési munkafolyamatai és kapcsolatcsoport-állapotok](expressroute-workflows.md).
* Az ExpressRoute-kapcsolat konfigurálása.
  
  * [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-portal-resource-manager.md)
  * [Útválasztás konfigurálása](expressroute-howto-routing-portal-resource-manager.md)
  * [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-portal-resource-manager.md)

