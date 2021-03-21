---
title: Virtual Network NAT Azure biztonsági alapterve
description: A Virtual Network NAT biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 501d5b17358501f17d17e8884ceec0bcbbb2ab1c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102612405"
---
# <a name="azure-security-baseline-for-virtual-network-nat"></a>Virtual Network NAT Azure biztonsági alapterve

Ez a biztonsági alapterv az [Azure biztonsági teljesítményteszt 1,0-es verziójáról](../security/benchmarks/overview-v1.md) a Microsoft Virtual Network NAT-ra vonatkozó útmutatást alkalmazza. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** és a Virtual Network NAT-ra vonatkozó kapcsolódó útmutatás szerint van csoportosítva. A Virtual Network NAT-ra nem alkalmazható **vezérlők** ki lettek zárva.

 
Ha szeretné megtekinteni, hogy Virtual Network NAT hogyan teljes mértékben leképezi az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Virtual Network NAT biztonsági alapterv-leképezési fájl](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: Virtual Network NAT nem támogatja a kimenő forgalom vezérlését hálózati biztonsági csoportokkal (NSG).  A bejövő forgalom csak kimenő folyamatra adott válaszként engedélyezett.

A hálózati biztonsági csoport (NSG) folyamatábrái azonban a NAT-átjáró erőforrásaival is használhatók a kimenő forgalom figyelésére.

Az Azure-hálózati erőforrások biztonságossá tételéhez használja a Azure Security Centert, és kövesse a hálózati védelemre vonatkozó ajánlásokat. Engedélyezze a hálózati biztonsági csoport adatfolyam-naplóit, és küldje el a naplókat egy Azure Storage-fiókba a naplózáshoz. A flow-naplókat Log Analytics munkaterületre is elküldheti, majd a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalmi mintákba. A Traffic Analytics néhány előnye, hogy képes megjeleníteni a hálózati tevékenységeket, azonosíthatja a gyakori helyeket és a biztonsági fenyegetéseket, megismerheti a forgalmi folyamatok mintáit, és meghatározhatja a hálózati helytelen konfigurációkat. 

- [Virtual Network NAT áttekintése](./nat-overview.md)

- [NAT-átjáró-erőforrás](./nat-gateway-resource.md)

- [Hálózati biztonsági csoport folyamatábráinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: Network Watcher csomagok rögzítésének engedélyezése a rendellenes tevékenységek kivizsgálásához. 

- [Network Watcher példány létrehozása](../network-watcher/network-watcher-create.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: a NAT-átjáró erőforrásaival konfigurált alhálózatok szabványos biztonsági konfigurációinak definiálása és implementálása beépített vagy egyéni Azure Policy definíciók és hozzárendelések használatával.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy minták a hálózatkezeléshez](../governance/policy/samples/built-in-policies.md#network)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-Tevékenységnaplók használatával figyelheti az erőforrás-konfigurációkat, és azonosíthatja a NAT-átjáró erőforrásainak és a virtuális hálózati erőforrásoknak a változásait. Riasztásokat hozhat létre a Azure Monitorban, hogy értesítést kapjon a kritikus erőforrások megváltozásakor.

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitorban](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: Virtual Network NAT-hoz kapcsolódó naplók beolvasása Azure monitor használatával a végponti eszközök, a hálózati erőforrások és más biztonsági rendszerek által generált biztonsági adatokat összesítve. Azure Monitor a Log Analytics-munkaterületek használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat hosszú távú és archiválási tároláshoz.

Alternatív megoldásként engedélyezheti és elvégezheti ezeket az adattáblákat az Azure Sentinel vagy egy harmadik féltől származó SIEM-nek.

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md) 

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Ismerkedés a Azure Monitor és a harmadik féltől származó SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a automatikusan elérhető tevékenység-naplók tartalmazzák a NAT-átjáró erőforrásainak összes írási műveletét (Put, post, DELETE), kivéve az olvasási MŰVELETEKET (Get). A Tevékenységnaplók segítségével hibát kereshet a hibaelhárítás során, vagy megfigyelheti, hogy a szervezet felhasználója hogyan módosított egy erőforrást.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [A naplózás és a különböző naplózási típusok megismerése az Azure-ban](../azure-monitor/essentials/platform-logs-overview.md) 

Virtual Network NAT jelenleg nem hoz létre olyan további diagnosztikai naplókat, amelyeket az ügyfelek konfigurálnak.

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: Azure Security Center használata log Analytics munkaterülettel a biztonsági naplókban és eseményekben észlelt rendellenes tevékenységekkel kapcsolatos figyeléshez és riasztáshoz. Ehelyett engedélyezheti és elvégezheti a fedélzeti adatfeldolgozást az Azure Sentinel szolgáltatásban.

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md) 

- [Riasztások kezelése Azure Security Centerban](../security-center/security-center-managing-and-responding-alerts.md) 

- [Riasztás a log Analytics-naplófájlok adatkezeléséről](../azure-monitor/alerts/tutorial-response.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetéseken belüli összes erőforrást (például a számítási, tárolási, hálózati, portokat és protokollokat). 

Ellenőrizze a megfelelő (olvasási) engedélyeket a bérlőben, és sorolja fel az összes Azure-előfizetést és-erőforrást az előfizetések között.

Bár a klasszikus Azure-erőforrások felderítése az erőforrás-gráfon keresztül lehetséges, erősen ajánlott a Azure Resource Manager-alapú erőforrások létrehozása és használata.

- [Azure Resource Graph-lekérdezések](../governance/resource-graph/first-query-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkék alkalmazása az Azure-erőforrásokra,-csoportokra és-előfizetésekre, amelyek logikailag rendszerezik őket egy besorolásban. Minden címke egy nevet és egy érték párokat tartalmaz. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: címkék alkalmazása az Azure-erőforrások kezeléséhez.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md) 

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md) 

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: jelenleg nem érhető el

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az előfizetése (i) ban létrehozható erőforrások típusára.
Az Azure Resource Graph segítségével lekérdezheti vagy felderítheti az előfizetése (ke) n belüli erőforrásait. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 
- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozhatja, hogy mely szolgáltatásokat lehet kiépíteni a környezetében.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: nem alkalmazható; Virtual Network NAT-ban nincsenek biztonsági konfigurációk.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: az Azure DevOps használatával biztonságosan tárolhatók és kezelhetők a kódok, például az egyéni Azure Policy-definíciók, Azure Resource Manager sablonok és a kívánt állapotú konfigurációs parancsfájlok. Az Azure DevOps felügyelt erőforrásainak eléréséhez engedélyeket adhat meg vagy tagadhat meg bizonyos felhasználók, beépített biztonsági csoportok vagy Azure Active Directory (Azure AD) által meghatározott csoportok számára, ha az integrálva van az Azure DevOps, vagy Active Directory, ha a TFS integrálva van. 

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow) 

- [Engedélyek és csoportok az Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: nem alkalmazható; Virtual Network NAT-ban nincsenek biztonsági konfigurációk.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: az ajánlathoz létre kell hozni egy incidens-válasz eljárást, amely biztosítja, hogy a megfelelő incidens-reagálási folyamatok megtörténjenek, hogy az incidensek feloldása előtt megkapja a megfelelő szintű prioritást.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: az ajánlathoz létre kell hozni egy incidens-pontozási és rangsorolási eljárást, amely biztosítja a megfelelő kockázati vagy veszélyforrási pontozást, így az incidensek feloldása előtt a megfelelő szintű prioritást kapja.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: az ajánlathoz létre kell hozni és tesztelni kell az incidensek biztonsági válaszának eljárását, amely biztosítja a megfelelő mértékű elsőbbséget az incidensek által előrejelzett megoldásig.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: az ajánlathoz létre kell hozni és tesztelni kell az incidensek biztonsági válaszának eljárását, amely biztosítja a megfelelő mértékű elsőbbséget az incidensek által előrejelzett megoldásig.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az ajánlathoz létre kell hozni és tesztelni kell az incidensek biztonsági válaszának eljárását, amely biztosítja a megfelelő mértékű elsőbbséget az incidensek által előrejelzett megoldásig.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: az ajánlathoz létre kell hozni és tesztelni kell az incidensek biztonsági válaszának eljárását, amely biztosítja a megfelelő mértékű elsőbbséget az incidensek által előrejelzett megoldásig.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért tekintse meg az [Azure biztonsági teljesítményteszt: behatolási tesztek és a Red Team gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)című témakört.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft Cloud penetráció tesztelési szabályait, amelyekkel biztosíthatja, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét. 

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](../security/benchmarks/overview.md) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)