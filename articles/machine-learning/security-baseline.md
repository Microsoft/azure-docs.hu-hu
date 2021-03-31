---
title: Azure Machine Learning Azure biztonsági alapterve
description: A Azure Machine Learning biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: machine-learning
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f8bde45cfdf9cf1c9d50faee76161461d8b0aa0a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104607828"
---
# <a name="azure-security-baseline-for-azure-machine-learning"></a>Azure Machine Learning Azure biztonsági alapterve

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 1,0-es verziójáról](../security/benchmarks/overview-v1.md) Microsoft Azure Machine learningre vonatkozó útmutatást alkalmaz. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Azure Machine learning vonatkozó kapcsolódó útmutatás. A Azure Machine Learning nem alkalmazható **vezérlők** ki vannak zárva.

 
Ha szeretné megtekinteni, hogyan Azure Machine Learning teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Azure Machine learning biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: a Azure Machine learning a számítási erőforrások egyéb Azure-szolgáltatásaira támaszkodik. A számítási erőforrások (számítási célok) a modellek betanítására és üzembe helyezésére szolgálnak. Ezeket a számítási célokat virtuális hálózatban is létrehozhatja. Használhatja például az Azure Virtual Machine Learning számítási példányát egy modell betanításához, majd a modell üzembe helyezéséhez az Azure Kubernetes Service (ak) szolgáltatásban. A gépi tanulási életciklusokat a Azure Machine Learning képzések és az Azure-beli virtuális hálózatokon belüli feladatok elkülönítésével biztonságossá teheti.

A Azure Firewall a Azure Machine Learning-munkaterülethez és a nyilvános internethez való hozzáférés szabályozására használható.

- [Virtual Network elkülönítés és Adatvédelem – áttekintés](how-to-network-security-overview.md)

- [Azure Firewall mögötti munkaterület használata Azure Machine Learning](how-to-access-azureml-behind-firewall.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: a Azure Machine learning a számítási erőforrások egyéb Azure-szolgáltatásaira támaszkodik. Rendeljen hálózati biztonsági csoportokat a Machine Learning üzemelő példányként létrehozott hálózatokhoz. 

Engedélyezze a hálózati biztonsági csoport adatfolyam-naplóit, és küldje el a naplókat egy Azure Storage-fiókba a naplózáshoz. A flow-naplókat Log Analytics munkaterületre is elküldheti, majd a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalmi mintákba. A Traffic Analytics néhány előnye, hogy képes megjeleníteni a hálózati tevékenységeket, azonosíthatja a gyakori helyeket és a biztonsági fenyegetéseket, megismerheti a forgalmi folyamatok mintáit, és meghatározhatja a hálózati helytelen konfigurációkat.

- [Hálózati biztonsági csoport folyamatábráinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: engedélyezheti a HTTPS-t a Azure Machine learning által központilag telepített webszolgáltatásokkal folytatott kommunikáció biztonságossá tételéhez. A webszolgáltatások üzembe helyezése az Azure Kubernetes Services (ak) szolgáltatásban vagy Azure Container Instances (ACI) történik, és az ügyfelek által beküldött adatvédelmek biztonságossá tétele. A saját IP-címmel is használhatja a pontozást, így csak a virtuális hálózat mögött található ügyfelek férhetnek hozzá a webszolgáltatáshoz.

- [TLS használata webszolgáltatás védelméhez az Azure Machine Learning szolgáltatás segítségével](how-to-secure-web-service.md)

- [Virtual Network elkülönítés és Adatvédelem – áttekintés](how-to-network-security-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: a Machine learning-példányhoz társított virtuális hálózatok DDoS Protection szabványának engedélyezése az elosztott szolgáltatásmegtagadási (DDoS) támadások elleni védelemhez. Azure Security Center integrált veszélyforrások észlelésével észlelheti a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel.

Azure Firewall üzembe helyezése minden szervezet hálózati határain, és a fenyegetésekkel kapcsolatos intelligencia-alapú Szűrés engedélyezve van, és úgy van beállítva, hogy a "riasztás és megtagadás" a kártékony hálózati forgalmat.

- [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall mögötti munkaterület használata Azure Machine Learning](how-to-access-azureml-behind-firewall.md)

- [További információ a Azure Security Center fenyegetések észleléséről](../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: a Azure Machine learning-szolgáltatásokban telepített megfelelő bővítménnyel rendelkező virtuális gépek esetében engedélyezheti Network Watcher csomagok rögzítését a rendellenes tevékenységek kivizsgálásához. 

- [Network Watcher példány létrehozása](../network-watcher/network-watcher-create.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: az Ön által választott tűzfal-megoldás üzembe helyezése minden szervezet hálózati határain a kártékony forgalom észlelése és/vagy blokkolása céljából.

Válasszon ki egy olyan ajánlatot az Azure piactéren, amely támogatja az AZONOSÍTÓk/IP-címek funkciót a hasznos adatok vizsgálatával.  Ha a hasznos adatok ellenőrzése nem követelmény, Azure Firewall a veszélyforrások felderítése is használható. Azure Firewall veszélyforrások felderítésére szolgáló szűrés a riasztások és/vagy az ismert kártékony IP-címek és tartományok felé irányuló, illetve azokból való adatforgalom letiltására szolgál. Az IP-címek és -tartományok forrása a Microsoft veszélyforrás-felderítési hírcsatornája.

- [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

- [Riasztások konfigurálása Azure Firewall](../firewall/threat-intel.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: a Azure Machine learning-fiókhoz hozzáférést igénylő erőforrásokhoz használja a Virtual Network szolgáltatás címkéit a hálózati biztonsági csoportok vagy a Azure Firewall hálózati hozzáférés-vezérlésének definiálásához. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatási címke nevének (például AzureMachineLearning) megadásával a szabály megfelelő forrás vagy cél mezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

Azure Machine Learning a szolgáltatás dokumentálja a számítási célokhoz tartozó szolgáltatási címkék listáját egy virtuális hálózaton belül, amely megkönnyíti a bonyolultság minimalizálását, és útmutatásként használhatja azt a hálózatkezelésben.

- [További információ a szolgáltatási címkék használatáról](../virtual-network/service-tags-overview.md)

- [Virtual Network elkülönítés és Adatvédelem – áttekintés](how-to-network-security-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: a Azure Machine learning névterekhez társított hálózati erőforrásokra vonatkozó szabványos biztonsági konfigurációk definiálása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. MachineLearning" és a "Microsoft. Network" névterekben, hogy egyéni szabályzatokat hozzon létre a Machine Learning névterek hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: a Azure Machine learning üzembe helyezéséhez társított hálózati erőforrásokhoz használjon címkéket, hogy a besorolások alapján logikailag szervezze őket.

A Description (Leírás) mezőt támogató Azure Machine Learning virtuális hálózatban lévő erőforrások számára a használatával dokumentálhatja azokat a szabályokat, amelyek engedélyezik a hálózatra irányuló adatforgalmat.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata a hálózati erőforrás-konfigurációk figyelésére és a Azure Machine learning kapcsolódó hálózati erőforrások változásainak észlelésére. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitorban](/azure/azure-monitor/platform/alerts-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: naplók beolvasása Azure monitor segítségével a Azure Machine learning által generált biztonsági adatokat összesítve. Azure Monitor a Log Analytics-munkaterületek használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat hosszú távú és archiválási tároláshoz. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó biztonsági incidensek és események felügyeletét (SIEM).

- [Diagnosztikai naplók konfigurálása Azure Machine Learninghoz](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#configuration)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: az Azure-erőforrások diagnosztikai beállításainak engedélyezése a naplózási, biztonsági és diagnosztikai naplókhoz való hozzáféréshez. A automatikusan elérhető tevékenység-naplók közé tartozik az eseményforrás, a dátum, a felhasználó, az időbélyeg, a forráscím, a célcím és más hasznos elemek.

Biztonsági és megfelelőségi célokból is korrelálhat Machine Learning szolgáltatási műveletek naplóit.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)

- [A naplózás és a különböző naplózási típusok megismerése az Azure-ban](/azure/azure-monitor/platform/platform-logs-overview)

- [Bejelentkezés engedélyezése Azure Machine Learning](how-to-track-experiments.md)

- [Figyelés Azure Machine Learning](monitor-azure-machine-learning.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: Ha a számítási erőforrás a Microsoft tulajdonában van, akkor a Microsoft feladata a begyűjtése és monitorozása. 

A Azure Machine Learning különböző számítási erőforrásokon és saját számítási erőforrásokon is eltérő támogatást nyújt. A szervezete tulajdonában lévő számítási erőforrások esetében az operációs rendszer figyeléséhez Azure Security Center kell használnia. 

- [Az Azure-beli virtuális gépek belső gazdagép-naplóinak összegyűjtése Azure Monitor](/azure/azure-monitor/learn/quick-collect-azurevm)

- [Az Azure Security Center adatgyűjtés ismertetése](../security-center/security-center-enable-data-collection.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: a Azure monitor a szervezet megfelelőségi előírásai alapján állítsa be a Azure Machine learning-példányokhoz társított log Analytics-munkaterületek naplózásának megőrzési időtartamát.

- [Napló-megőrzési paraméterek beállítása](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: elemezze és figyelje a rendellenes viselkedés naplóit, és rendszeresen tekintse át a Azure Machine learning eredményeit. A naplók áttekintéséhez és a naplózási adatok lekérdezéséhez használja a Azure Monitor és egy Log Analytics munkaterületet.

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et. 

- [Azure Machine Learning lekérdezések végrehajtása Log Analytics-munkaterületeken](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#analyzing-log-data)

- [Bejelentkezés engedélyezése Azure Machine Learning](how-to-track-experiments.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

- [Log Analytics lekérdezések első lépései](../azure-monitor/logs/log-analytics-tutorial.md)

- [Egyéni lekérdezések végrehajtása a Azure Monitorban](/azure/azure-monitor/log-query/get-started-queries)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: Azure monitorban konfigurálja a tevékenység naplójában Azure Machine Learninghoz kapcsolódó naplókat, és Machine learning a diagnosztikai beállításokat, hogy a naplókat egy log Analytics munkaterületre küldje, vagy egy Storage-fiókba a hosszú távú archiválási tároláshoz. Log Analytics munkaterület használatával riasztásokat hozhat létre a biztonsági naplókban és eseményekben található rendellenes tevékenységekhez.

Alternatív megoldásként engedélyezheti és elvégezheti a fedélzeti adatfeldolgozást az Azure Sentinel szolgáltatásban.

- [További információ a Azure Machine Learning riasztásokról](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#alerts)

- [Riasztás Log Analytics-munkaterület naplófájljainak adatvédelme](/azure/azure-monitor/learn/tutorial-response)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: Ha a számítási erőforrás a Microsoft tulajdonában van, akkor a Microsoft felelős a Azure Machine learning szolgáltatás antimalware-környezetének telepítéséhez.

A Azure Machine Learning különböző számítási erőforrásokon és saját számítási erőforrásokon is eltérő támogatást nyújt. A szervezete tulajdonában lévő számítási erőforrások esetében engedélyezze az Azure-Cloud Services és Virtual Machines a Microsoft antimalware-hez készült események gyűjtését.

- [A Microsoft antimalware-bővítmény konfigurálása a Cloud Serviceshez](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension)

- [A Microsoft antimalware megismerése](../security/fundamentals/antimalware.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: nem alkalmazható; A Azure Machine Learning nem dolgozza fel a DNS-sel kapcsolatos naplókat, és nem hoz létre.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: a Azure Machine learning különböző számítási erőforrásokon és akár saját számítási erőforrásoknál is különböző támogatást nyújt. A számítási erőforrások a szervezet tulajdonában vannak, Azure Security Center használatával engedélyezheti az Azure-beli virtuális gépek biztonsági eseménynaplóinak figyelését. Azure Security Center kiépíti az Log Analytics ügynököt az összes támogatott Azure-beli virtuális gépen, valamint az automatikus kiépítés engedélyezése esetén létrehozott újakat. Vagy manuálisan is telepítheti az ügynököt. Az ügynök engedélyezi a 4688-es folyamat-létrehozási eseményt és a 4688-es eseményen belüli parancssori mezőt. A virtuális gépen létrehozott új folyamatokat az eseménynapló rögzíti, és a Security Center észlelési szolgáltatásai figyelik.

- [Adatgyűjtés az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: a Azure Portal erőforrásának identitás-és hozzáférés-kezelési lapján konfigurálhatja az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC), és megtarthatja a leltárt Azure Machine learning erőforrásokon. A szerepköröket a rendszer a felhasználókra, csoportokra, egyszerű szolgáltatásokra és felügyelt identitásokra alkalmazza Active Directoryban. A felhasználók és csoportok számára beépített szerepköröket vagy egyéni szerepköröket is használhat.

A Azure Machine Learning beépített szerepköröket biztosít a Azure Machine Learning gyakori felügyeleti eseteihez. A Azure Active Directory (Azure AD) profillal rendelkező személy hozzárendelheti ezeket a szerepköröket a felhasználókhoz, csoportokhoz, egyszerű szolgáltatásokhoz vagy felügyelt identitásokhoz, így biztosíthatja vagy megtagadhatja a hozzáférést az erőforrásokhoz és műveletekhez Azure Machine Learning erőforrásokon.

Az Azure AD PowerShell-modullal is elvégezheti az alkalmi lekérdezéseket a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

- [Az Azure szerepköralapú hozzáférés-vezérlésének megismerése Azure Machine Learning](how-to-assign-roles.md)

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: Machine learning erőforrásokhoz való hozzáférés-kezelést Azure Active Directory (Azure ad) vezérli. Az Azure AD nem rendelkezik az alapértelmezett jelszavak fogalmával.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: a Azure Machine learning három alapértelmezett szerepkört tartalmaz, amikor új munkaterületet hoz létre, valamint szabványos üzemeltetési eljárásokat hoz létre a tulajdonosi fiókok használata körül.

Az Azure Active Directory (Azure AD) Privileged Identity Management és Azure Resource Manager használatával bármikor engedélyezheti a rendszergazdai fiókokhoz való hozzáférést.

- [További Machine Learning alapértelmezett szerepkörök](https://docs.microsoft.com/azure/machine-learning/how-to-assign-roles#default-roles)

- [További információ a Privileged Identity Management](/azure/active-directory/privileged-identity-management/index)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: a Machine learning integrálva van Azure Active Directory (Azure ad) szolgáltatással, az Azure ad SSO-t az egyes önálló hitelesítő adatok konfigurálása helyett használhatja. Azure Security Center identitás és hozzáférési javaslatok használata.

- [Az egyszeri bejelentkezés ismertetése az Azure AD-vel](../active-directory/manage-apps/what-is-single-sign-on.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: engedélyezze az Azure Active Directory (Azure ad) többtényezős hitelesítését, és kövesse Azure Security Center identitás-és hozzáférési javaslatait.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: emelt szintű jogosultságokat igénylő felügyeleti feladatokhoz használjon biztonságos, Azure által felügyelt munkaállomás (más néven privilegizált hozzáférési munkaállomás vagy Paw) használatát.

- [A biztonságos, Azure által felügyelt munkaállomások ismertetése](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [A Azure Active Directory (Azure AD) többtényezős hitelesítésének engedélyezése](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: a Azure Active Directory (Azure ad) biztonsági jelentéseinek és figyelésének használata annak észlelésére, hogy a környezetben gyanús vagy nem biztonságos tevékenységek történnek-e. A Azure Security Center használatával figyelheti az identitás-és hozzáférési tevékenységeket.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md)

- [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: csak a jóváhagyott helyekről származó Azure-erőforrások kezelése

**Útmutató**: az Azure Active Directory (Azure ad) nevű hely használata, amely lehetővé teszi, hogy csak az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz férhessenek hozzá.

- [Az Azure AD nevesített helyeinek konfigurálása](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerrel. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.
 
A szerepkör-hozzáférés az Azure több szintjére is kiterjed. Machine Learning a szerepkörök a munkaterület szintjén kezelhetők, például a munkaterület tulajdonosi hozzáférése nem jogosult a munkaterületet tartalmazó erőforráscsoporthoz való tulajdonosi hozzáférésre. Ez részletesebb hozzáférés-vezérlést biztosít az azonos erőforráscsoporthoz tartozó szerepkörök elkülönítéséhez. 

- [Azure Machine Learning-munkaterülethez való hozzáférés kezelése](how-to-assign-roles.md) 
 
- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (Azure ad) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure AD identitás-és hozzáférési felülvizsgálatok segítségével hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá.

A naplók és a riasztások generálásához használjon Azure AD Privileged Identity Management (PIM), ha a környezetben gyanús vagy nem biztonságos tevékenység történik.

- [Az Azure AD jelentéskészítés ismertetése](/azure/active-directory/reports-monitoring)

- [Az Azure AD identitás- és hozzáférési felülvizsgálatainak használata](../active-directory/governance/access-reviews-overview.md)

- [Azure AD Privileged Identity Management (PIM) üzembe helyezése](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: hozzáférhet Azure Active Directory (Azure ad) bejelentkezési tevékenységhez, naplózáshoz és kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik bármely Siem/monitoring eszköz integrálását.

Ezt a folyamatot leegyszerűsítheti, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókjaihoz, és elküldi a naplókat és a bejelentkezési naplókat egy Log Analytics munkaterületre. Log Analytics munkaterületen belül konfigurálhatja a kívánt riasztásokat.

- [Azure-beli Tevékenységnaplók integrálása a Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: az Azure Active Directory (Azure ad) Identity Protection-funkciókkal konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. További vizsgálat céljából az Azure Sentinelbe is betöltheti az adatmennyiséget.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: nem alkalmazható; Azure Machine Learning szolgáltatás nem támogatja az ügyfél kulcstárolóját.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.
 
- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: az elkülönítés megvalósítása különálló előfizetések és felügyeleti csoportok használatával az egyes biztonsági tartományokhoz, például a környezeti típusokhoz és az adatérzékeny szintekhez. Korlátozhatja az alkalmazásaihoz és a vállalati környezetekhez igénybe veheti az Azure-erőforrásokhoz való hozzáférés szintjét. Az Azure-erőforrásokhoz való hozzáférést az Azure RBAC segítségével szabályozhatja.
 
- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)

 
- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: külső féltől származó megoldás használata az Azure Marketplace-en a hálózati kerületekben a bizalmas információk jogosulatlan átvitelének figyelése és az ilyen átvitelek blokkolása az adatbiztonsági szakemberek értesítése mellett. 

A Microsoft által felügyelt mögöttes platformon a Microsoft az összes ügyféltartalmat bizalmasként kezeli, és védelmet nyújt az ügyfelek adatainak elvesztése és mások általi hozzáférése ellen. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket. 

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: a Azure Machine learning használatával üzembe helyezett webszolgáltatások csak a 1,2-es TLS-verziót támogatják, amely az adattitkosítást kényszeríti az átvitel során.

- [TLS használata webszolgáltatás védelméhez az Azure Machine Learning szolgáltatás segítségével](how-to-secure-web-service.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el Azure Machine learning számára. Szükség esetén hozzon létre egy külső gyártótól származó megoldást.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés kezelése az Azure RBAC

**Útmutató**: a Azure Machine Learning az Azure Active Directory (Azure ad) használatával támogatja a kérelmek Machine learning erőforrásokhoz való engedélyezését. Az Azure AD-vel az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) segítségével engedélyeket biztosíthat egy rendszerbiztonsági tag számára, amely lehet egy felhasználó vagy egy egyszerű alkalmazás.

- [Azure Machine Learning-munkaterülethez való hozzáférés kezelése](how-to-assign-roles.md)

- [Az Azure RBAC használata az Kubernetes-hitelesítéshez](../aks/manage-azure-rbac.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a Azure Machine learning a pillanatképeket, a kimeneteket és a naplókat a Azure Machine learning munkaterülethez és az előfizetéséhez kötött Azure Blob Storage-fiókban tárolja. Az Azure Blob Storage-ban tárolt összes adatok titkosítva vannak a Microsoft által felügyelt kulcsokkal. Az Azure Blob Storage-ban tárolt adatait a Machine Learning szolgáltatás saját kulcsaival is titkosíthatja. 

- [Inaktív adatok titkosításának Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#encryption-at-rest)

- [Inaktív adatok Azure-ban való titkosításának ismertetése](../security/fundamentals/encryption-atrest.md)

- [Az ügyfél által felügyelt titkosítási kulcsok konfigurálása](../storage/common/customer-managed-keys-configure-key-vault.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások a Azure Machine learning és más kritikus vagy kapcsolódó erőforrások éles példányain lépnek életbe.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](/azure/azure-monitor/platform/alerts-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információért lásd az [Azure biztonsági teljesítményteszt: biztonsági rés kezelése](../security/benchmarks/security-control-vulnerability-management.md)című témakört.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: Ha a számítási erőforrás a Microsoft tulajdonában van, akkor a Microsoft feladata a Azure Machine learning szolgáltatás biztonsági réseinak kezelése. 

A Azure Machine Learning különböző számítási erőforrásokon és saját számítási erőforrásokon is eltérő támogatást nyújt. A szervezete tulajdonában lévő számítási erőforrások esetében kövesse Azure Security Center a sebezhetőségi felmérések Azure-beli virtuális gépeken, tároló-lemezképeken és SQL-kiszolgálókon történő végrehajtásához szükséges ajánlásokat.

- [Azure Security Center sebezhetőségi felméréssel kapcsolatos javaslatok megvalósítása](../security-center/deploy-vulnerability-assessment-vm.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: Ha a számítási erőforrás a Microsoft tulajdonában van, akkor a Microsoft feladata a Azure Machine learning szolgáltatás javításának kezelése. 

A Azure Machine Learning különböző számítási erőforrásokon és saját számítási erőforrásokon is eltérő támogatást nyújt. A szervezete tulajdonában lévő számítási erőforrások esetében Azure Automation Update Management segítségével győződjön meg arról, hogy a legújabb biztonsági frissítések telepítve vannak a Windows-és Linux-alapú virtuális gépeken. Windows rendszerű virtuális gépek esetén győződjön meg arról, hogy Windows Update engedélyezve van, és automatikus frissítésre van beállítva.

- [Virtuális gépek Update Management konfigurálása az Azure-ban](../automation/update-management/overview.md)

- [A Security Center által figyelt Azure biztonsági házirendek ismertetése](../security-center/policy-reference.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: automatikus javítási megoldás üzembe helyezése harmadik féltől származó szoftverek címeihez

**Útmutató**: a Azure Machine learning különböző számítási erőforrásokon és akár saját számítási erőforrásoknál is különböző támogatást nyújt. A szervezete tulajdonában lévő számítási erőforrások esetében használjon harmadik féltől származó javítási felügyeleti megoldást. Azok az ügyfelek, akik már használnak Configuration Manager a környezetben, System Center Updates Publisher is használhatják, így egyéni frissítéseket tehetnek közzé a Windows Server Update szolgáltatásban. Ez lehetővé teszi, hogy Update Management a harmadik féltől származó szoftverrel rendelkező frissítési adattárként Configuration Manager használó gépeket.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: a Azure Machine learning különböző számítási erőforrásokon és akár saját számítási erőforrásoknál is különböző támogatást nyújt. A szervezete tulajdonában lévő számítási erőforrások esetében az Azure Security Center ajánlásainak követése az Azure-beli virtuális gépeken, a tároló-lemezképeken és az SQL-kiszolgálókon végzett sebezhetőségi felmérések végrehajtásához. A vizsgálati eredményeket konzisztens időközönként exportálja, és összehasonlítja az eredményeket a korábbi vizsgálatokkal annak ellenőrzéséhez, hogy a biztonsági rések szervizelése megtörtént-e. A Azure Security Center által javasolt sebezhetőség-kezelési javaslatok használatakor a kiválasztott megoldás portálján megtekintheti a korábbi vizsgálati adataikat.

- [Azure Security Center sebezhetőségi felméréssel kapcsolatos javaslatok megvalósítása](../security-center/deploy-vulnerability-assessment-vm.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetésekben lévő erőforrásokat (például a számítási, tárolási, hálózati, portok és protokollok stb.). Győződjön meg arról, hogy a bérlőben a megfelelő (olvasási) engedélyek szerepelnek, valamint az összes Azure-előfizetés enumerálása, valamint az előfizetések erőforrásai.

Bár a klasszikus Azure-erőforrások az Azure Resource Graph Explorerben is felderíthető, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription)

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkék alkalmazása az Azure-erőforrásokra, metaadatok hozzáadása a logikai rendszerezéshez a besorolás szerint.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és az elkülönített előfizetések használata, ha szükséges, az eszközök rendszerezéséhez és nyomon követéséhez. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

- [ További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [ Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)
 
- [ Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: a számítási erőforrásokhoz jóváhagyott Azure-erőforrások és jóváhagyott szoftverek leltárának létrehozása a szervezeti igényeknek megfelelően.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

* Nem engedélyezett erőforrástípusok
* Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetéseken belüli erőforrásokat.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: a Azure Machine learning különböző számítási erőforrásokon és akár saját számítási erőforrásoknál is különböző támogatást nyújt. A szervezete tulajdonában lévő számítási erőforrások esetében Azure Automation Change Tracking és leltár segítségével automatizálhatja a leltári információk gyűjtését Windows és Linux rendszerű virtuális gépekről. A szoftver neve, verziója, közzétevője és frissítési ideje elérhető a Azure Portal. A szoftver telepítési dátumának és egyéb információinak beszerzéséhez engedélyezze a vendég szintű diagnosztikát, és irányítsa a Windows-eseménynaplókat Log Analytics munkaterületre.

- [Azure Automation leltározási gyűjtemény engedélyezése virtuális géphez](../automation/automation-tutorial-installed-software.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: a Azure Machine learning különböző számítási erőforrásokon és akár saját számítási erőforrásoknál is különböző támogatást nyújt. A szervezete tulajdonában lévő számítási erőforrások esetében a virtuális gépekre telepített összes szoftver azonosításához használja a Azure Security Center fájl integritásának figyelését (FIM). Egy másik lehetőség, amely a FIM használatával vagy együtt használható, Azure Automation a Change Tracking és a leltárt, hogy leltárt gyűjtsön a Linux és a Windows rendszerű virtuális gépekről. 

A jogosulatlan szoftverek eltávolításához saját eljárást is alkalmazhat. Harmadik féltől származó megoldást is használhat a nem jóváhagyott szoftverek azonosítására.

Távolítsa el az Azure-erőforrásokat, ha már nincs rájuk szükség.

- [A fájl integritás-figyelésének használata](../security-center/security-center-file-integrity-monitoring.md)

- [A Azure Automation Change Tracking és a leltár ismertetése](../automation/change-tracking/overview.md)

- [Az Azure-beli virtuális gépek leltározásának engedélyezése](../automation/automation-tutorial-installed-software.md)

- [Azure-erőforráscsoport és erőforrás-törlés](../azure-resource-manager/management/delete-resource-group.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: a Azure Machine learning különböző számítási erőforrásokon és akár saját számítási erőforrásoknál is különböző támogatást nyújt. A szervezete tulajdonában lévő számítási erőforrások esetében Azure Security Center adaptív alkalmazás-vezérlőelemekkel biztosíthatja, hogy csak a hitelesített szoftverek fussanak, és az összes jogosulatlan szoftver le legyen tiltva az Azure Virtual Machines.

- [Azure Security Center adaptív alkalmazás-vezérlők használata](../security-center/security-center-adaptive-application.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

* Nem engedélyezett erőforrástípusok
* Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph használatával kérdezheti le és derítheti fel az előfizetések erőforrásait.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: a jóváhagyott szoftverek leltárának fenntartása

**Útmutató**: a Azure Machine learning különböző számítási erőforrásokon és akár saját számítási erőforrásoknál is különböző támogatást nyújt. A szervezete tulajdonában lévő számítási erőforrások esetében Azure Security Center adaptív alkalmazás-vezérlőelemekkel megadhatja, hogy mely fájltípusok esetében lehet egy szabály, vagy nem alkalmazható.

Harmadik féltől származó megoldás implementálása, ha az adaptív alkalmazások vezérlői nem felelnek meg a követelménynek.

- [Azure Security Center adaptív alkalmazás-vezérlők használata](../security-center/security-center-adaptive-application.md)

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure Active Directory (Azure ad) feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek az Azure Resources Managerrel együttműködni az "Microsoft Azure felügyelet" alkalmazás "hozzáférés letiltása" beállításával.

- [Feltételes hozzáférés konfigurálása az Azure Resources Manager elérésének blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokban

**Útmutató**: a Azure Machine learning különböző számítási erőforrásokon és akár saját számítási erőforrásoknál is különböző támogatást nyújt. A szervezete tulajdonában lévő számítási erőforrások esetében a parancsfájlok típusától függően az operációs rendszerre jellemző konfigurációk vagy harmadik féltől származó erőforrások segítségével korlátozható, hogy a felhasználók képesek-e parancsfájlokat végrehajtani az Azure számítási erőforrásaiban. Azure Security Center adaptív alkalmazás-vezérlők használatával biztosíthatja, hogy csak a hitelesített szoftverek legyenek végrehajtva, és az összes jogosulatlan szoftver le legyen tiltva az Azure-Virtual Machines.

- [A PowerShell-parancsfájlok végrehajtásának szabályozása Windows-környezetekben](/powershell/module/microsoft.powershell.security/set-executionpolicy)

- [Azure Security Center adaptív alkalmazás-vezérlők használata](../security-center/security-center-adaptive-application.md)

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: a Azure Machine learning szolgáltatás szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. MachineLearning" névtérben egyéni szabályzatok létrehozásához a Azure Machine Learning-szolgáltatások konfigurációjának naplózásához vagy érvénybe léptetéséhez.

A Azure Resource Manager lehetővé teszi a sablon exportálását JavaScript Object Notation (JSON), amelyet át kell tekinteni annak érdekében, hogy a konfigurációk megfeleljenek a szervezete biztonsági követelményeinek.

A Azure Security Center javaslatai az Azure-erőforrások biztonságos alapkonfigurációja is használhatók.

Azure Machine Learning teljes mértékben támogatja a git-Tárházak használatát a nyomon követéshez; a Tárházak klónozása közvetlenül a megosztott munkaterület fájlrendszerén, a git használata a helyi munkaállomáson, és gondoskodjon arról, hogy a biztonságos konfigurációk a kód erőforrásaira vonatkozzanak a Machine Learning környezet részeként.

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias)

- [Oktatóanyag: szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében](../governance/policy/tutorials/create-and-manage.md)

- [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)

- [Biztonsági javaslatok – gyorsútmutató](../security-center/recommendations-reference.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: Ha a számítási erőforrás a Microsoft tulajdonában van, akkor a Microsoft felelős a Azure Machine learning szolgáltatás operációs rendszerének biztonságos konfigurációjában.

A Azure Machine Learning különböző számítási erőforrásokon és saját számítási erőforrásokon is eltérő támogatást nyújt. A szervezete tulajdonában lévő számítási erőforrások esetében Azure Security Center javaslatokkal kezelheti a biztonsági konfigurációkat az összes számítási erőforráson.  Emellett egyéni operációsrendszer-lemezképeket vagy Azure Automation állapot-konfigurációt is használhat a szervezete által igényelt operációs rendszer biztonsági konfigurációjának létrehozásához.

- [Azure Security Center javaslatok figyelése](../security-center/security-center-recommendations.md)

- [Biztonsági javaslatok – gyorsútmutató](../security-center/recommendations-reference.md)

- [Azure Automation állapot konfigurációjának áttekintése](../automation/automation-dsc-overview.md)

- [Virtuális merevlemez feltöltése és használata új Windows rendszerű virtuális gépek létrehozásához az Azure-ban](../virtual-machines/windows/upload-generalized-managed.md)

- [Linuxos virtuális gép létrehozása egyéni lemezről az Azure CLI-vel](../virtual-machines/linux/upload-vhd.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure-erőforrások biztonságos beállításainak betartatásához használja a Azure Policy [deny] és a [telepítés ha nem létezik] lehetőséget. Emellett Azure Resource Manager-sablonokkal is megőrizheti a szervezete által igényelt Azure-erőforrások biztonsági konfigurációját. 
 
- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)
 
- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../governance/policy/tutorials/create-and-manage.md)
 
- [ Azure Resource Manager sablonok áttekintése](../azure-resource-manager/templates/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: Ha a számítási erőforrás a Microsoft tulajdonában van, akkor a Microsoft felelős a Azure Machine learning szolgáltatás operációs rendszerének biztonságos konfigurációjában.

A Azure Machine Learning különböző számítási erőforrásokon és saját számítási erőforrásokon is eltérő támogatást nyújt. A szervezete tulajdonában lévő számítási erőforrások esetében Azure Security Center a sebezhetőségi felméréseknek az Azure-beli számítási erőforrásokon való végrehajtásával kapcsolatos ajánlásokat követve.  Emellett a szervezete által igényelt operációs rendszer biztonsági konfigurációjának fenntartása érdekében Azure Resource Manager sablonokat, egyéni operációsrendszer-lemezképeket vagy Azure Automation állapot-konfigurációt is használhat.   A Azure Automation állapot-konfigurációval együtt a Microsoft virtuálisgép-sablonjai segíthetnek a biztonsági követelmények teljesítésében és fenntartásában. 

Vegye figyelembe, hogy a Microsoft által közzétett Azure Marketplace virtuálisgép-rendszerképeket a Microsoft felügyeli és tartja karban. 

- [Azure Security Center sebezhetőségi felméréssel kapcsolatos javaslatok megvalósítása](../security-center/deploy-vulnerability-assessment-vm.md)

- [Azure-beli virtuális gép létrehozása ARM-sablonból](../virtual-machines/windows/ps-template.md)

- [Azure Automation állapot konfigurációjának áttekintése](../automation/automation-dsc-overview.md)

- [Windows rendszerű virtuális gép létrehozása a Azure Portalban](../virtual-machines/windows/quick-create-portal.md)

- [A virtuálisgép-sablon letöltésével kapcsolatos információk](/previous-versions/azure/virtual-machines/windows/download-template)

- [Példaszkript egy VHD Azure-ba történő feltöltéséhez és új virtuális gép létrehozásához](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: ha a Machine Learninghoz vagy a kapcsolódó erőforrásokhoz egyéni Azure Policy definíciókat használ, az Azure Repos használatával biztonságosan tárolhatja és kezelheti a kódot.

Azure Machine Learning teljes mértékben támogatja a git-Tárházak használatát a nyomon követéshez; a Tárházak klónozása közvetlenül a megosztott munkaterület fájlrendszerén, a git használata a helyi munkaállomáson, és gondoskodjon arról, hogy a biztonságos konfigurációk a kód erőforrásaira vonatkozzanak a Machine Learning környezet részeként.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Az Azure Repos dokumentációja](/azure/devops/repos/)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: a Azure Machine learning különböző számítási erőforrásokon és akár saját számítási erőforrásoknál is különböző támogatást nyújt. A szervezete tulajdonában lévő számítási erőforrások esetében az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával biztosíthatja, hogy csak a jogosult felhasználók férhessenek hozzá az egyéni lemezképekhez. Az Azure megosztott képtárat használva a képeket megoszthatja a szervezeten belüli különböző felhasználókhoz, egyszerű szolgáltatásokhoz vagy Azure Active Directory (Azure AD) csoportokhoz is. Tárolja a Azure Container Registry tároló lemezképeit, és az Azure RBAC használatával győződjön meg arról, hogy csak a jogosult felhasználók férhetnek hozzá.

- [Az Azure RBAC ismertetése](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Az Azure-RBAC megismerése Container Registry](../container-registry/container-registry-roles.md)

- [Az Azure RBAC konfigurálása](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Megosztott képgyűjtemény – áttekintés](../virtual-machines/shared-image-galleries.md)

- [Az Azure RBAC használata az Kubernetes-hitelesítéshez](../aks/manage-azure-rbac.md)

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: Azure Policy aliasok használata a "Microsoft. MachineLearning" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszer-konfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Aliasok használata](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: konfigurációs felügyeleti eszközök telepítése operációs rendszerekhez

**Útmutató**: Ha a számítási erőforrás a Microsoft tulajdonában van, akkor a Microsoft feladata a Azure Machine learning szolgáltatás biztonságos konfigurálásának biztosítása.

A Azure Machine Learning különböző számítási erőforrásokon és saját számítási erőforrásokon is eltérő támogatást nyújt. A szervezete tulajdonában lévő számítási erőforrások esetében használja Azure Automation állapot konfigurációját a kívánt állapot-konfiguráció (DSC) csomópontjaihoz bármilyen Felhőbeli vagy helyszíni adatközpontban. Könnyedén beépítheti a gépeket, hozzárendelheti a deklaratív konfigurációkat, és megtekintheti azokat a jelentéseket, amelyek az egyes gépek megfelelőségét a megadott kívánt állapotnak megfelelően mutatják. 

- [Azure Automation állapot konfigurációjának engedélyezése](../automation/automation-dsc-onboarding.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: a Azure Security Center használata az Azure-erőforrások alapkonfigurációjának vizsgálatához. Emellett az Azure-erőforrások konfigurációjának riasztására és naplózására Azure Policy is használhatja.
 
 
 
- [ Javaslatok szervizelése Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: Ha a számítási erőforrás a Microsoft tulajdonában van, akkor a Microsoft feladata a Azure Machine learning szolgáltatás automatikus biztonságos konfigurációjának figyelése.

A Azure Machine Learning különböző számítási erőforrásokon és saját számítási erőforrásokon is eltérő támogatást nyújt. A szervezete tulajdonában lévő számítási erőforrások esetében Azure Security Center számítási alkalmazásokat kell használnia, &amp; és követnie kell a virtuális gépek és a kiszolgálók, valamint a tárolók javaslatait.

- [Az Azure Security Center tárolókra vonatkozó ajánlásainak értelmezése](../security-center/container-security.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: felügyelt identitások használata a Azure Key Vaultkel együtt a Felhőbeli alkalmazások titkos felügyeletének egyszerűbbé tételéhez.

Azure Machine Learning támogatja az adattárolási titkosítást az ügyfél által felügyelt kulcsokkal, a kulcs elforgatását és visszavonását a szervezet biztonsági és megfelelőségi követelményeinek megfelelően kell kezelni. 

A betanítási szkriptek nem titkosított szöveg helyett a Azure Key Vault használatával továbbíthatja a titkos kulcsokat a távoli futtatásokhoz.

- [Ügyfél által felügyelt kulcsok Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-blob-storage)

- [Hitelesítő adatokkal kapcsolatos titkos kódok használata Azure Machine Learning betanítási futtatásokban](how-to-use-secrets-in-runs.md)

- [Felügyelt identitások használata Azure-erőforrásokhoz](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Key Vault létrehozása](../key-vault/general/quick-create-portal.md)

- [Hitelesítés Key Vault](../key-vault/general/authentication.md)

- [Key Vault hozzáférési szabályzatok kiosztása](../key-vault/general/assign-access-policy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: a Azure Machine learning támogatja a beépített szerepköröket és az egyéni szerepkörök létrehozását is. Felügyelt identitások használatával biztosíthatja az Azure-szolgáltatások automatikus felügyelt identitását Azure Active Directoryban (Azure AD). A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

- [Azure Machine Learning-munkaterülethez való hozzáférés kezelése](how-to-assign-roles.md)

- [Felügyelt identitások konfigurálása az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz. 

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információt az [Azure biztonsági teljesítményteszt: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md)című témakörben talál.*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: központilag felügyelt kártevő szoftver használata

**Útmutató**: a Microsoft kártevő szoftverrel való ellátása engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure Machine learning), azonban nem az ügyfél tartalmán fut.

A Azure Machine Learning különböző számítási erőforrásokon és saját számítási erőforrásokon is eltérő támogatást nyújt. A szervezete tulajdonában lévő számítási erőforrások esetében az Azure-hoz készült Microsoft antimalware használatával folyamatosan figyelheti és védheti az erőforrásait. Linux esetén használjon harmadik féltől származó antimalware-megoldást. Emellett a Azure Security Center veszélyforrások észlelését is használhatja az adatszolgáltatásokhoz a Storage-fiókokba feltöltött kártevők észlelése érdekében.

- [Az Azure-hoz készült Microsoft antimalware konfigurálása](../security/fundamentals/antimalware.md)

- [Fenyegetésvédelem az Azure Security Centerben](../security-center/azure-defender.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft kártevő szoftverrel való ellátása engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure Machine learning), azonban nem az ügyfél tartalmán fut.

Az Ön felelőssége, hogy előzetesen beszkennelje a nem számítási Azure-erőforrásokra feltöltött tartalmakat. A Microsoft nem fér hozzá az ügyféladatok eléréséhez, így az Ön nevében nem végezhet kártevő-ellenőrzéseket az ügyfél-tartalmakon.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: az Azure-szolgáltatásokat (például az Azure Machine learninget) támogató mögöttes gazdagép számára engedélyezett és karbantartott Microsoft kártevő-elhárító szolgáltatás, amely azonban nem az ügyfél-tartalmakon fut.

A Azure Machine Learning különböző számítási erőforrásokon és saját számítási erőforrásokon is eltérő támogatást nyújt. A szervezete tulajdonában lévő számítási erőforrások esetében kövesse az Azure Security Center, a számítási alkalmazások című témakörben foglalt javaslatokat, amelyekkel &amp; biztosítható, hogy az összes végpont naprakész legyen a legújabb aláírásokkal. Linux esetén használjon harmadik féltől származó antimalware-megoldást.

- [Az Azure-hoz készült Microsoft antimalware üzembe helyezése](../security/fundamentals/antimalware.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a Machine learning szolgáltatás adathelyreállítási felügyelete a csatlakoztatott adattárakon lévő adatkezelésen keresztül történik. Győződjön meg arról, hogy a csatlakoztatott áruházakra vonatkozó adathelyreállítási irányelvek nyomon követésével biztonsági másolatot készít az összes ügyfél-szervezeti szabályzatról.

- [Fájlok helyreállítása az Azure-beli virtuális gépek biztonsági másolatából](../backup/backup-azure-restore-files-from-vm.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: a Machine learning szolgáltatásban tárolt adatbiztonsági mentés a csatlakoztatott adattárakon lévő adatkezelésen keresztül történik. Engedélyezze a virtuális gépek Azure Backupét, és konfigurálja a kívánt gyakoriságot és megőrzési időt. Az ügyfél által felügyelt kulcsok biztonsági mentése Azure Key Vault-ben.

- [Fájlok helyreállítása az Azure-beli virtuális gépek biztonsági másolatából](../backup/backup-azure-restore-files-from-vm.md)

- [Key Vault kulcsok visszaállítása az Azure-ban](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: a Machine learning szolgáltatás adatbiztonsági mentési ellenőrzése a csatlakoztatott adattárakon lévő adatkezelésen keresztül történik. Rendszeresen végrehajtja a tartalom Azure Backupban történő helyreállítását. Győződjön meg arról, hogy visszaállíthatja az ügyfél által felügyelt kulcsok biztonsági mentését.

- [Fájlok helyreállítása egy Azure-beli virtuális gép biztonsági másolatából](../backup/backup-azure-restore-files-from-vm.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: a helyszíni biztonsági mentéshez a rendszer a inaktív adatok titkosítását az Azure-ba történő biztonsági mentéshez megadott hozzáférési kód használatával biztosítja. Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával gondoskodhat a biztonsági másolatok és az ügyfél által felügyelt kulcsok biztonságáról. 

A kulcsok véletlen vagy rosszindulatú törléssel szembeni védelme érdekében engedélyezze a Key Vault a védelem lágy törlését és kiürítését. Ha az Azure Storage-t használja a biztonsági másolatok tárolásához, a Soft delete lehetővé teszi az adatok mentését és helyreállítását a Blobok vagy blob-Pillanatképek törlésekor.

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

- [Helyreállítható törlés és a végleges törléssel szembeni védelem engedélyezése kulcstartóban](../storage/blobs/soft-delete-blob-overview.md)

- [Soft DELETE az Azure Blob Storage-hoz](../storage/blobs/soft-delete-blob-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: incidensek kifejlesztése útmutató a szervezet számára. Győződjön meg arról, hogy van olyan írásos incidens-válasz, amely meghatározza a személyzet összes szerepkörét, valamint az incidensek kezelésének és kezelésének fázisait az incidensek vizsgálatát követően. 

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [A NIST számítógépes biztonsági incidensek kezelésével kapcsolatos útmutató a saját incidens-válasz tervének létrehozásához](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Azure Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy a megbízhatósági Security Center hol van a megállapításban, illetve a riasztás kibocsátására használt analitikai módszer, valamint a riasztást eredményező tevékenység mögött rosszindulatú szándékkal rendelkező megbízhatósági szint.

Emellett megadhatja a címkéket használó előfizetéseket, és létrehozhat egy elnevezési rendszert az Azure-erőforrások azonosításához és kategorizálásához, különösen a bizalmas adatok feldolgozásához. Az Ön felelőssége, hogy rangsorolja a riasztások szervizelését az Azure-erőforrások és-környezet kritikus jellemzői alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: az Azure-erőforrások védelmének biztosítása érdekében a rendszer az incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatokat hajt végre. Azonosítsa a gyenge pontokat és a hézagokat, majd szükség szerint módosítsa a választ.

- [A NIST kiadványa – útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és gyakorlatához](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után.

- [Az Azure Security Center biztonsági kapcsolattartójának beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és javaslatok exportálása a folyamatos exportálás funkcióval az Azure-erőforrásokkal kapcsolatos kockázatok azonosítása érdekében. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a munkafolyamat-automatizálási szolgáltatás használata Azure Security Center a biztonsági riasztásokra és az Azure-erőforrások védelmére vonatkozó ajánlásokra adott válaszok automatikus elindítására.

- [A Munkafolyamat-automatizálás konfigurálása a Security Centerban](../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért tekintse meg az [Azure biztonsági teljesítményteszt: behatolási tesztek és a Red Team gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)című témakört.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft Cloud penetráció tesztelési szabályait, amelyekkel biztosíthatja, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét.

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
