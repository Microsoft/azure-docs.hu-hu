---
title: Azure Security teljesítményteszt v2 – hálózati biztonság
description: Azure Security teljesítményteszt v2 hálózati biztonság
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: b47f833a3b47dae145aa18eb6eda0c6bd95f6689
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042913"
---
# <a name="security-control-v2-network-security"></a>Security Control v2: hálózati biztonság

A hálózati biztonság magában foglalja az Azure-hálózatok biztonságossá tételéhez és védelméhez szükséges vezérlőket. Ez magában foglalja a virtuális hálózatok biztonságossá tételét, a magánhálózati kapcsolatok létrehozását, a külső támadások megelőzését és enyhítését, valamint a DNS biztonságossá tételét.

A megfelelő beépített Azure Policy megjelenítéséhez tekintse meg [Az Azure biztonsági teljesítményteszt szabályozási megfelelőségének beépített kezdeményezésének részleteit: hálózati biztonság](../../governance/policy/samples/azure-security-benchmark.md#network-security)

## <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: a belső forgalom biztonságának megvalósítása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| NS-1 | 9,2, 9,4, 14,1, 14,2, 14,3 | AC-4, CA-3, SC-7 |

Győződjön meg arról, hogy az összes Azure-beli virtuális hálózat olyan vállalati szegmentálási elvet követ, amely az üzleti kockázatokhoz igazodik. A szervezet számára nagyobb kockázatot jelentő rendszereknek el kell különíteni a saját virtuális hálózatán belül, és megfelelően védeni kell őket egy hálózati biztonsági csoporttal (NSG) és/vagy Azure Firewallával.

Az alkalmazások és a vállalati szegmentálási stratégia alapján korlátozhatja vagy engedélyezheti a belső erőforrások közötti forgalmat a hálózati biztonsági csoport szabályai alapján. A meghatározott, jól definiált alkalmazások (például egy 3 rétegű alkalmazás) esetében ez lehet egy nagyon biztonságos "megtagadás alapértelmezés szerint, kivétel" módszer. Előfordulhat, hogy ez nem jól méretezhető, ha sok alkalmazás és végpont kommunikál egymással. A Azure Firewall olyan helyzetekben is használhatja, ahol a központi felügyelet nagyszámú vállalati szegmensben vagy küllőn (egy sugaras vagy küllős topológiában) szükséges.

Azure Security Center adaptív hálózati korlátozással olyan hálózati biztonsági csoport konfigurációit ajánlhatja, amelyek korlátozzák a portokat és a forrás IP-címeket a külső hálózati forgalmi szabályokra való hivatkozás alapján.

Az Azure Sentinel használatával felderítheti az örökölt nem biztonságos protokollok, például az SSL/TLSv1, a SMBv1, az LM/NTLMv1, a wDigest, az aláíratlan LDAP-kötések és a Kerberos-alapú gyenge titkosítás használatát.

- [Hálózati biztonsági csoport létrehozása biztonsági szabályokkal](../../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Firewall üzembe helyezése és konfigurálása](../../firewall/tutorial-firewall-deploy-portal.md)

- [Adaptív hálózati megerősítés Azure Security Center](../../security-center/security-center-adaptive-network-hardening.md)

- [Azure Sentinel – nem biztonságos protokollok – munkafüzet](../../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági architektúra](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Alkalmazások biztonsága és DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-2-connect-private-networks-together"></a>NS-2: magánhálózati hálózatok összekapcsolása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| NS – 2 | N/A | CA-3, AC-17, MA-4 |

Az Azure ExpressRoute vagy az Azure virtual private Network (VPN) használatával privát kapcsolatokat hozhat létre az Azure-adatközpontok és a helyszíni infrastruktúra között egy közös elhelyezésű környezetben. A ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át, így megbízhatóbbak, gyorsabbak és kevesebb késéssel rendelkeznek, mint a szokásos internetkapcsolatok. Pont – hely típusú VPN-és helyek közötti VPN esetén a VPN-beállítások és az Azure ExpressRoute bármely kombinációja segítségével a helyszíni eszközöket vagy hálózatokat egy virtuális hálózathoz is összekapcsolhatja.

Ha két vagy több virtuális hálózatot szeretne összekapcsolni az Azure-ban, használja a virtuális hálózati társ-vagy magánhálózati kapcsolatot. A kiszolgált virtuális hálózatok közötti hálózati forgalom magán, és az Azure-beli gerinces hálózaton marad.

- [A ExpressRoute kapcsolati modelljei](../../expressroute/expressroute-connectivity-models.md)

- [Az Azure VPN áttekintése](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Virtuális hálózati társviszony](../../virtual-network/virtual-network-peering-overview.md)

- [Azure Private Link](../../private-link/private-link-service-overview.md)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági architektúra](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Alkalmazások biztonsága és DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Privát hálózati hozzáférés létesítése Azure-szolgáltatásokhoz

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| NS-3 | 14,1 | AC-4, CA-3, SC-7 |

Az Azure privát hivatkozásával lehetővé teheti az Azure-szolgáltatásokhoz való privát hozzáférést a virtuális hálózatokról az Internet átlépése nélkül. Olyan helyzetekben, amikor az Azure Private-hivatkozás még nem érhető el, használja az Azure Virtual Network szolgáltatás-végpontokat. Az Azure Virtual Network Service-végpontok biztonságos hozzáférést biztosítanak a szolgáltatásokhoz az Azure gerinces hálózaton keresztül optimalizált útvonalon keresztül.

A privát hozzáférés az Azure-szolgáltatások által kínált hitelesítéssel és adatforgalommal kapcsolatos további védelmi részletes mérték.

- [Az Azure privát hivatkozásának megismerése](../../private-link/private-link-overview.md)

- [Virtual Network szolgáltatási végpontok ismertetése](../../virtual-network/virtual-network-service-endpoints-overview.md)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági architektúra](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Alkalmazások biztonsága és DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: a külső hálózati támadásoktól származó alkalmazások és szolgáltatások elleni védelem

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| NS-4 | 9,5, 12,3, 12,9 | SC-5, SC-7 |

Az Azure-erőforrások védelmet nyújthatnak a külső hálózatokból érkező támadások ellen, beleértve az elosztott szolgáltatásmegtagadási (DDoS) támadásokat, az alkalmazás-specifikus támadásokat, valamint a kéretlen és potenciálisan kártékony internetes forgalmat. Az Azure a következő natív képességeket tartalmazza:
-   A Azure Firewall használatával védelmet biztosíthat az alkalmazások és szolgáltatások számára az internetről és más külső helyekről származó, potenciálisan rosszindulatú forgalom ellen. 

-   A webalkalmazási tűzfal (WAF) képességei az Azure Application Gateway, az Azure bejárati ajtó és az Azure Content Delivery Network (CDN) segítségével védik az alkalmazásokat, szolgáltatásokat és API-kat az alkalmazási rétegbeli támadásokkal szemben.

-   Az adatvédelmet a DDoS-támadások ellen biztosíthatja az Azure-beli virtuális hálózatokon a DDoS standard szintű védelem engedélyezésével.
-   A Azure Security Center használatával azonosíthatja a fentiekkel kapcsolatos hibás konfigurációs kockázatokat.

- [Azure Firewall dokumentáció](../../firewall/index.yml)

- [Az Azure WAF üzembe helyezése](../../web-application-firewall/overview.md)

- [Azure DDoS Protection standard kezelése a Azure Portal használatával](../../ddos-protection/manage-ddos-protection.md)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági architektúra](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Alkalmazások biztonsága és DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| NS-5 | 12,6, 12,7 | SI-4 |

Azure Firewall veszélyforrások felderítésére szolgáló szűréssel riasztást használhat, és/vagy blokkolhatja az ismert kártékony IP-címekre és tartományokra irányuló, illetve onnan érkező forgalmat. Az IP-címek és -tartományok forrása a Microsoft veszélyforrás-felderítési hírcsatornája. Ha szükség van a hasznos adatok vizsgálatára, az Azure Marketplace-en üzembe helyezheti az adattartalom-ellenőrzési képességekkel rendelkező harmadik féltől származó behatolás-észlelési/Behatolás-megelőzési rendszereket (AZONOSÍTÓk/IP-címek Másik lehetőségként a gazdagép-alapú AZONOSÍTÓk/IP-címek vagy a gazdagép-alapú végpontok észlelése és válasza (EDR) megoldást is használhat hálózati AZONOSÍTÓk/IP-címek helyett vagy ahelyett.

Megjegyzés: Ha rendelkezik az AZONOSÍTÓk/IP-címek használatára vonatkozó szabályozási vagy egyéb követelményekkel, ügyeljen arra, hogy a rendszer mindig hangolja a magas színvonalú riasztásokat az SIEM-megoldáshoz. 

- [Azure Firewall üzembe helyezése](../../firewall/tutorial-firewall-deploy-portal.md)

- [Az Azure Marketplace harmadik féltől származó azonosító képességeket is tartalmaz](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [A Microsoft Defender for Endpoint funkció](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági architektúra](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Alkalmazások biztonsága és DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-6-simplify-network-security-rules"></a>NS-6: a hálózati biztonsági szabályok egyszerűsítése

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| NS-6 | 1.5 | IA – 4 |

Egyszerűsítse a hálózati biztonsági szabályokat a szolgáltatási címkék és az alkalmazás-biztonsági csoportok (ASG-EK) kihasználásával.

A hálózati biztonsági csoportokon vagy Azure Firewall a hálózati hozzáférés-vezérlések definiálásához használja Virtual Network szolgáltatás címkéit. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatási címke nevének megadásával a szabály forrás vagy cél mezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

Az alkalmazás biztonsági csoportjaival is egyszerűsítheti az összetett biztonsági konfigurációt. A hálózati biztonsági csoportok explicit IP-címein alapuló házirend meghatározása helyett az alkalmazás biztonsági csoportjai lehetővé teszik a hálózati biztonság konfigurálását az alkalmazások struktúrájának természetes kiterjesztéseként, így a virtuális gépek csoportosítása és a csoportok alapján a hálózati biztonsági házirendek definiálása is lehetséges.

- [A szolgáltatási címkék megismerése és használata](../../virtual-network/service-tags-overview.md)

- [Az alkalmazás biztonsági csoportjai megismerése és használata](../../virtual-network/network-security-groups-overview.md#application-security-groups)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági architektúra](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Alkalmazások biztonsága és DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: biztonságos tartománynév szolgáltatás (DNS)

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| NS-7 | N/A | SC-20, SC-21 |

Kövesse a DNS-biztonság ajánlott eljárásait a gyakori támadásokkal szemben, például a DNS-t, a DNS-erősítési támadásokat, a DNS-mérgezést és a hamisítást stb.

Ha a rendszer a mérvadó DNS-szolgáltatásként Azure DNS használja, győződjön meg arról, hogy a DNS-zónák és-rekordok védelme az Azure RBAC és az erőforrás-zárolások használatával a véletlen vagy rosszindulatú módosításokkal szemben.

- [Azure DNS áttekintése](../../dns/dns-overview.md)

- [A biztonságos tartománynévrendszer (DNS) üzembe helyezési útmutatója](https://csrc.nist.gov/publications/detail/sp/800-81/2/final)

- [A DNS-bejegyzések letiltásának és a tartományon belüli átvétel elkerülésének megakadályozása](../fundamentals/subdomain-takeover.md)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági architektúra](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Alkalmazások biztonsága és DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)
