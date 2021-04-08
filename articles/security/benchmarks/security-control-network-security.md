---
title: Azure Security Control – hálózati biztonság
description: Azure Security Control hálózati biztonság
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: cc52d1f6eec5f1aedb0db37b3945f7be6d9f62a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595629"
---
# <a name="security-control-network-security"></a>Biztonsági ellenőrzés: hálózati biztonság

A hálózati biztonsági javaslatok azt határozzák meg, hogy mely hálózati protokollok, TCP/UDP-portok és hálózati csatlakoztatott szolgáltatások engedélyezettek vagy tagadják meg az Azure-szolgáltatások elérését.

## <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.1 | 9,2, 9,4, 14,1, 14,2, 14,3 | Ügyfél |

Győződjön meg arról, hogy az összes Virtual Network alhálózati üzemelő példány rendelkezik egy hálózati biztonsági csoporttal, amely az alkalmazás megbízható portjaira és forrásaira jellemző hálózati hozzáférés-vezérléssel rendelkezik. Ha elérhető, privát végpontok használatával gondoskodhat az Azure-szolgáltatások erőforrásainak virtuális hálózatra való védelméről, ha a VNet-identitást kiterjesztjük a szolgáltatásra. Ha a privát végpontok és a privát hivatkozások nem érhetők el, használja a szolgáltatási végpontokat. A szolgáltatásra vonatkozó követelményekért tekintse meg az adott szolgáltatásra vonatkozó biztonsági javaslatot. 

Ha konkrét használati esettel rendelkezik, a követelmények teljesítése Azure Firewall megvalósításával lehetséges.

- [Virtual Network szolgáltatási végpontok ismertetése](../../virtual-network/virtual-network-service-endpoints-overview.md)

- [Az Azure privát hivatkozásának megismerése](../../private-link/private-link-overview.md)

- [Virtual Network létrehozása](../../virtual-network/quick-create-portal.md)

- [Biztonsági konfigurációval rendelkező NSG létrehozása](../../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Firewall üzembe helyezése és konfigurálása](../../firewall/tutorial-firewall-deploy-portal.md)

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.2 | 9,3, 12,2, 12,8 | Ügyfél |

Az Azure-ban a hálózati erőforrások biztonságossá tételéhez használja a Azure Security Centert, és kövesse a hálózati védelmi javaslatok című témakört. Engedélyezze a NSG folyamat naplóit, és küldje el a naplókat egy Storage-fiókba a forgalom naplózása érdekében. NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

- [A NSG folyamat naplófájljainak engedélyezése](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../../network-watcher/traffic-analytics.md)

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../../security-center/security-center-network-recommendations.md)

## <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.3 | 9,5 | Ügyfél |

Telepítse az Azure webalkalmazási tűzfalat (WAF) a kritikus webalkalmazások előtt a bejövő forgalom további ellenőrzéséhez. Diagnosztikai beállítás engedélyezése a WAF és a naplók betöltéséhez egy Storage-fiókba, az Event hub-ba vagy a Log Analytics-munkaterületre.

- [Az Azure WAF üzembe helyezése](../../web-application-firewall/ag/create-waf-policy-ag.md)

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.4 | 12,3 | Ügyfél |

Az Azure-beli virtuális hálózatok DDoS standard szintű védelmének engedélyezése a DDoS-támadások elleni védelem érdekében. Az ismert kártékony IP-címekkel folytatott kommunikáció megtagadásához használja Azure Security Center integrált veszélyforrások felderítését.

Azure Firewall üzembe helyezése minden szervezet hálózati határain, a fenyegetések felderítése engedélyezve van, és a "riasztás és megtagadás" értékre van állítva a kártékony hálózati forgalom esetében.

A NSG konfigurálásához használja a Azure Security Center igény szerinti hálózati hozzáférését, hogy korlátozza a végpontok a jóváhagyott IP-címekre való kitettségét korlátozott időtartamra.

Azure Security Center adaptív hálózati korlátozással olyan NSG-konfigurációkat javasolhat, amelyek a portok és a forrás IP-címeket korlátozzák a tényleges forgalom és a veszélyforrások felderítése alapján.

- [A DDoS Protection konfigurálása](../../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall üzembe helyezése](../../firewall/tutorial-firewall-deploy-portal.md)

- [Az Azure Security Center integrált veszélyforrások felderítésének megismerése](../../security-center/azure-defender.md)

- [Azure Security Center adaptív hálózat megerősítésének ismertetése](../../security-center/security-center-adaptive-network-hardening.md)

- [Azure Security Center az időponthoz tartozó hálózati Access Control ismertetése](../../security-center/security-center-just-in-time.md)

## <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.5 | 12,5 | Ügyfél |

Network Watcher csomagok rögzítésének engedélyezése a rendellenes tevékenységek kivizsgálásához.

- [A Network Watcher engedélyezése](../../network-watcher/network-watcher-create.md)

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati alapú behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.6 | 12,6, 12,7 | Ügyfél |

Válasszon ki egy olyan ajánlatot az Azure piactéren, amely támogatja az AZONOSÍTÓk/IP-címek funkciót a hasznos adatok ellenőrzésére szolgáló funkciókkal.  Ha a betörési észlelés és/vagy a tartalom-ellenőrzésen alapuló megelőzés nem követelmény, akkor az Azure Firewall a veszélyforrások felderítésére használható. Azure Firewall fenyegetés intelligencián alapuló szűréssel riasztást kaphat, és megtagadhatja az ismert kártékony IP-címek és tartományok felé irányuló forgalmat. Az IP-címek és -tartományok forrása a Microsoft veszélyforrás-felderítési hírcsatornája.

Az Ön által választott tűzfal-megoldás üzembe helyezésével azonosíthatja és/vagy megtagadhatja a kártékony forgalmat.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure Firewall üzembe helyezése](../../firewall/tutorial-firewall-deploy-portal.md)

- [Riasztások konfigurálása Azure Firewall](../../firewall/threat-intel.md)

## <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.7 | 12,9, 12,10 | Ügyfél |

Az Azure Application Gateway üzembe helyezése a megbízható tanúsítványok számára engedélyezett HTTPS/TLS-alapú webalkalmazásokhoz.

- [Application Gateway üzembe helyezése](../../application-gateway/quick-create-portal.md)

- [A Application Gateway konfigurálása a HTTPS használatára](../../application-gateway/create-ssl-portal.md)

- [A 7. rétegbeli terheléselosztás és az Azure-webalkalmazás-átjárók ismertetése](../../application-gateway/overview.md)

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.8 | 1.5 | Ügyfél |

A hálózati biztonsági csoportokon vagy Azure Firewall a hálózati hozzáférés-vezérlések definiálásához használja Virtual Network szolgáltatás címkéit. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például ApiManagement), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

Emellett az alkalmazás biztonsági csoportjaival is egyszerűsítheti az összetett biztonsági konfigurációt. Az alkalmazásbiztonsági csoportokkal az alkalmazás struktúrájának természetes bővítményeként konfigurálhatja a hálózati biztonságot, így csoportosíthatja a virtuális gépeket, és ezen csoportok alapján meghatározhatja a hálózati biztonsági szabályokat.

- [A szolgáltatási címkék megismerése és használata](../../virtual-network/service-tags-overview.md)

- [Az alkalmazás biztonsági csoportjai megismerése és használata](../../virtual-network/network-security-groups-overview.md#application-security-groups)

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.9 | 11,1 | Ügyfél |

A hálózati erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy.

Az Azure-tervezetek segítségével leegyszerűsítheti a nagyméretű Azure-környezetek nagy léptékű üzembe helyezését, például az Azure Resources Manager-sablonokat, az Azure RBAC-vezérlőket és a szabályzatokat egyetlen terv definíciójában. A tervrajzot új előfizetésekre alkalmazhatja, és az irányítás és felügyelet finomhangolását a verziószámozás segítségével végezheti el.

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy minták a hálózatkezeléshez](../../governance/policy/samples/built-in-policies.md#network)

- [Azure Blueprint létrehozása](../../governance/blueprints/create-blueprint-portal.md)

## <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.10 | 11,2 | Ügyfél |

Címkék használata a NSG és a hálózati biztonsággal és a forgalommal kapcsolatos egyéb erőforrásokhoz. Az egyes NSG-szabályok esetében a "Leírás" mezővel adhatja meg az üzleti igényeket és/vagy időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatra irányuló adatforgalmat.

A címkézéshez kapcsolódó beépített Azure Policy definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.

A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon.

- [Címkék létrehozása és használata](../../azure-resource-manager/management/tag-resources.md)

- [Virtual Network létrehozása](../../virtual-network/quick-create-portal.md)

- [NSG létrehozása biztonsági konfigurációval](../../virtual-network/tutorial-filter-network-traffic.md)

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1,11 | 11,3 | Ügyfél |

Az Azure-tevékenység naplójának használatával figyelheti az erőforrás-konfigurációkat, és felderítheti az Azure-erőforrások módosításait. Hozzon létre riasztásokat a Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus erőforrások változásai megváltoznak.

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitorban](../../azure-monitor/alerts/alerts-activity-log.md)

## <a name="next-steps"></a>Következő lépések

- Tekintse meg a következő biztonsági vezérlőt: [naplózás és figyelés](security-control-logging-monitoring.md)