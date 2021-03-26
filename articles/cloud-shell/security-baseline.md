---
title: Cloud Shell Azure biztonsági alapterve
description: A Cloud Shell biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b0045eacea7fdc13abdad35b5567eba3e994dcf9
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558873"
---
# <a name="azure-security-baseline-for-cloud-shell"></a>Cloud Shell Azure biztonsági alapterve

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 1,0-es verziójáról](../security/benchmarks/overview-v1.md) Cloud shellre vonatkozó útmutatást alkalmaz. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Cloud Shell vonatkozó kapcsolódó útmutatás. A Cloud Shell nem alkalmazható **vezérlők** ki vannak zárva.

 
Ha szeretné megtekinteni, hogyan Cloud Shell teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Cloud Shell biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: az ügyfelek Azure Cloud Shell üzembe helyezhetik az ügyfél tulajdonában lévő Virtual Network.

Ha Azure Cloud Shell telepít központilag egy ügyfél tulajdonában lévő Virtual Network, létre kell hoznia vagy használnia kell egy meglévő virtuális hálózatot. Győződjön meg arról, hogy a kiválasztott virtuális hálózat rendelkezik egy hálózati biztonsági csoporttal az alhálózatokra, valamint az alkalmazás megbízható portjaira és forrásaira konfigurált hálózati hozzáférés-vezérlőket.

- [Cloud Shell üzembe helyezése Azure-beli virtuális hálózaton](private-vnet.md)

- [Hálózati biztonsági csoport létrehozása biztonsági szabályokkal](../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Firewall üzembe helyezése és konfigurálása](../firewall/tutorial-firewall-deploy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: a Azure Cloud shell egy böngészőalapú parancssori felület, amely ugyanazt az engedélyt használja, mint amely a Azure Portal eléréséhez használatos, ebben az esetben a Azure Portal egy egyszeri bejelentkezéssel is hitelesítve lesz Cloud shell.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: a Azure Cloud shell egy böngészőalapú parancssori felület, amely ugyanazt az engedélyt használja, mint amely a Azure Portal elérésére szolgál, ebben az esetben a Azure Portalhoz való kapcsolódáshoz szükséges többtényezős hitelesítésre is szükség lesz Cloud shell.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információért lásd az [Azure biztonsági teljesítményteszt: biztonsági rés kezelése](../security/benchmarks/security-control-vulnerability-management.md)című témakört.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: a Azure Cloud shell egy böngészőalapú parancssori felület, amely a felhőalapú erőforrások interaktív kezelésére szolgál.  Minden egyes ügyfél-tároló elmúlóan új tárolót használ az egyes munkamenetekhez.  A tároló lemezképeit a Cloud Shell csapata figyeli és frissíti.

Azure Cloud Shell lehetővé teszi, hogy az ügyfelek saját rendszerképbe telepítsék saját eszközeiket vagy szoftvereket szervezeti igényeik szerint.

Az ügyfelek feladata, hogy az automatikus biztonsági rések vizsgálatára szolgáló eszközöket futtasson a környezetben futó szoftvereken.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: automatikus javítási megoldás üzembe helyezése harmadik féltől származó szoftverek címeihez

**Útmutató**: nem alkalmazható; A Azure Cloud Shell egy böngészőalapú parancssori felület, amely a felhőalapú erőforrások interaktív kezelésére szolgál.  Minden egyes ügyfél-tároló elmúlóan új tárolót használ az egyes munkamenetekhez.  A tároló lemezképeit a Cloud Shell csapata figyeli és frissíti.

Azure Cloud Shell lehetővé teszi, hogy az ügyfelek saját rendszerképbe telepítsék saját eszközeiket vagy szoftvereket szervezeti igényeik szerint.

A környezetében futó szoftverfrissítési felügyeletért az ügyfelek felelősek.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: a Azure Cloud shell egy böngészőalapú parancssori felület, amely a felhőalapú erőforrások interaktív kezelésére szolgál.  Minden egyes ügyfél-tároló elmúlóan új tárolót használ az egyes munkamenetekhez.  A tároló lemezképeit a Cloud Shell csapata figyeli és frissíti.

Azure Cloud Shell lehetővé teszi, hogy az ügyfelek saját rendszerképbe telepítsék saját eszközeiket vagy szoftvereket szervezeti igényeik szerint.

Az ügyfelek feladata a szoftveres sebezhetőségi vizsgálatok során felderített biztonsági rések szervizelése. A vizsgálati eredményeket konzisztens időközönként exportálja, és összehasonlítja az eredményeket a korábbi vizsgálatokkal annak ellenőrzéséhez, hogy a biztonsági rések szervizelése megtörtént-e. A Azure Security Center által javasolt sebezhetőség-kezelési javaslatok használatakor a kiválasztott megoldás portálján megtekintheti a korábbi vizsgálati adataikat.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: a Azure Cloud shell egy böngészőalapú parancssori felület, amely a felhőalapú erőforrások interaktív kezelésére szolgál.  Minden egyes ügyfél-tároló elmúlóan új tárolót használ az egyes munkamenetekhez.  A tároló lemezképeit a Cloud Shell csapata figyeli és frissíti.

Azure Cloud Shell lehetővé teszi, hogy az ügyfelek saját rendszerképbe telepítsék saját eszközeiket vagy szoftvereket szervezeti igényeik szerint.

Az ügyfelek feladata a szoftveres sebezhetőségi vizsgálatok során felderített biztonsági rések szervizelése.  Használjon egy közös kockázati pontozási programot (például gyakori sebezhetőségi pontozási rendszer) vagy a harmadik féltől származó ellenőrzési eszköz által biztosított alapértelmezett kockázati minősítéseket. 

- [NIST-kiadvány – gyakori sebezhetőségi pontozási rendszerek](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: a Azure Cloud shell egy böngészőalapú parancssori felület, amely a felhőalapú erőforrások interaktív kezelésére szolgál.  Minden egyes ügyfél-tároló elmúlóan új tárolót használ az egyes munkamenetekhez.  A tároló lemezképeit és eszközeit a Cloud Shell csapata figyeli és frissíti.  Az ügyfél a saját rendszerképében saját szervezeti igényei szerint telepítheti saját eszközeit, és a telepítés során az eszközökhöz nem szükséges `sudo` engedély.

Javasoljuk, hogy hozzon létre egy leltárt a jóváhagyott szoftverekről, amelyeket Azure Cloud Shell a szervezeti igényeinek megfelelően telepít.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: a Azure Cloud shell egy ingyenes szolgáltatás, amely nem rendelkezik felhasználói tulajdonú eszközökkel.  A tároló lemezképeit és eszközeit a Cloud Shell csapata figyeli és frissíti. 

Azure Cloud Shell lehetővé teszi, hogy az ügyfelek saját rendszerképbe telepítsék saját eszközeiket vagy szoftvereket szervezeti igényeik szerint.

Az ügyfelek feladata a környezetben futó szoftveralkalmazások figyelése, hogy azok a szervezet házirendjében legyenek jóváhagyva.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: a Azure Cloud shell egy ingyenes szolgáltatás, amely nem rendelkezik felhasználói tulajdonú eszközökkel.  A tároló lemezképeit és eszközeit a Cloud Shell csapata figyeli és frissíti. 

Azure Cloud Shell lehetővé teszi, hogy az ügyfelek saját rendszerképbe telepítsék saját eszközeiket vagy szoftvereket szervezeti igényeik szerint.

Az ügyfelek feladata a környezetben futó szoftveralkalmazások figyelése, hogy a rendszer a nem jóváhagyott szoftvereket szervezeti házirend szerint felügyelje.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: a Azure Cloud shell egy ingyenes szolgáltatás, amely nem rendelkezik felhasználói tulajdonú eszközökkel.  A tároló lemezképeit és eszközeit a Cloud Shell csapata figyeli és frissíti.  Előfordulhat, hogy az ügyfél nem távolít el bizonyos eszközöket.

Azure Cloud Shell lehetővé teszi, hogy az ügyfelek szervezeti igényeik szerint telepítsék saját eszközeiket vagy alkalmazásaikat a saját rendszerképében.

Az ügyfelek feladata a környezetben futó alkalmazások figyelése, hogy azok a szervezet házirendjében legyenek jóváhagyva.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: a jóváhagyott szoftverek leltárának fenntartása

**Útmutató**: nem alkalmazható; A Azure Cloud Shell egy böngészőalapú parancssori felület, amely a felhőalapú erőforrások interaktív kezelésére szolgál.  Minden egyes ügyfél-tároló elmúlóan új tárolót használ az egyes munkamenetekhez.  A tároló lemezképeit a Cloud Shell csapata figyeli és frissíti.

Azure Cloud Shell lehetővé teszi, hogy az ügyfelek saját rendszerképbe telepítsék saját eszközeiket vagy szoftvereket szervezeti igényeik szerint.

Az ügyfelek feladata, hogy fenntartsák a környezetben futó jóváhagyott szoftverek leltárát annak biztosítására, hogy a szoftvereket a szervezeti házirend szerint jóváhagyták.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokban

**Útmutató**: a Azure Cloud shell egy böngészőalapú parancssori felület, amely a felhőalapú erőforrások interaktív kezelésére szolgál.  Cloud Shell függvényen belül végrehajtott műveletek ugyanazok, mint az azonos eszközökről vagy nyelvekről végrehajtott műveletek helyi környezetben futnak.  Az egyes eszközökről és nyelvekről származó műveleteket korlátozni kell, az ügyfelek nem korlátozhatják a hozzáférést a Cloud Shellhoz, és nem korlátozhatják a felhasználók számára elérhetővé tételét.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: a Azure Cloud Shell el lehet különíteni egy ügyfél virtuális hálózatában.

- [Cloud Shell üzembe helyezése Azure-beli virtuális hálózaton](private-vnet.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: a Cloud Shell lehetővé teszi a parancsfájlok futtatását, létrehozását és a Cloud Shell-környezetbe való feltöltését.  A hitelesítő adatok Azure Key Vaultba való áthelyezése a Javaslatunk.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információt az [Azure biztonsági teljesítményteszt: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md)című témakörben talál.*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: központilag felügyelt kártevő szoftver használata

**Útmutató**: a Azure Cloud shell egy böngészőalapú parancssori felület, amely a felhőalapú erőforrások interaktív kezelésére szolgál.  Minden egyes ügyfél-tároló elmúlóan új tárolót használ az egyes munkamenetekhez.  A tároló lemezképeit és eszközeit a Cloud Shell csapata figyeli és frissíti.  Az ügyfél a saját rendszerképében saját szervezeti igényei szerint telepítheti saját eszközeit, és a telepítés során az eszközökhöz nem szükséges `sudo` engedély.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: a Azure Cloud shell egy böngészőalapú parancssori felület, amely a felhőalapú erőforrások interaktív kezelésére szolgál.  Minden egyes ügyfél-tároló elmúlóan új tárolót használ az egyes munkamenetekhez.  A tároló lemezképeit és eszközeit a Cloud Shell csapata figyeli és frissíti.  Az ügyfél a saját rendszerképében saját szervezeti igényei szerint telepítheti saját eszközeit, és a telepítés során az eszközökhöz nem szükséges `sudo` engedély.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

- [Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül](../security-center/security-center-planning-and-operations-guide.md) 

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásával kapcsolatos támogatáshoz](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy a megbízhatósági Security Center hol van a megállapításban, illetve a riasztás kibocsátására használt analitikai módszer, valamint a riasztást eredményező tevékenység mögött rosszindulatú szándékkal rendelkező megbízhatósági szint.

Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben nem termelt), és hozzon létre egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md) 

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és edzésprogramjának kidolgozásához](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az ügyfél adatait egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után.

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és-javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md) 

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a válaszokat a "Logic apps" használatával a biztonsági riasztások és javaslatok esetében.

- [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért tekintse meg az [Azure biztonsági teljesítményteszt: behatolási tesztek és a Red Team gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)című témakört.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft Cloud penetráció tesztelési szabályait, amelyekkel biztosíthatja, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét.

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](../security/benchmarks/overview.md) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)