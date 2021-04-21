---
title: Az Azure biztonsági alapkonfigurációja Azure Machine Learning
description: A Azure Machine Learning alapkonfigurációja eljárásokkal kapcsolatos útmutatást és erőforrásokat biztosít az Azure biztonsági teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához.
author: msmbaldwin
ms.service: machine-learning
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6457624329d7bb5e367e9de5a1963884e11ad2e3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816998"
---
# <a name="azure-security-baseline-for-azure-machine-learning"></a>Az Azure biztonsági alapkonfigurációja Azure Machine Learning

Ez a biztonsági alapkonfiguráció az Azure Biztonsági teljesítményteszt [1.0-s](../security/benchmarks/overview-v1.md) verziójának útmutatását alkalmazza a Microsoft Azure Machine Learning. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalmat az Azure  biztonsági teljesítményteszt által meghatározott biztonsági vezérlők és a kapcsolódó, az azure-beli biztonsági Azure Machine Learning. **A** Azure Machine Learning nem alkalmazható vezérlők.

 
Ha meg Azure Machine Learning az Azure-biztonsági teljesítményteszt teljes körű leképezését, tekintse meg a teljes Azure Machine Learning alapkonfiguráció [leképezési fájlját.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-erőforrások védelme virtuális hálózatokon belül

**Útmutató:** Azure Machine Learning azure-szolgáltatásokra támaszkodik a számítási erőforrásokhoz. A számítási erőforrások (számítási célok) modellek betanítása és üzembe helyezése. Ezeket a számítási célokat létrehozhatja egy virtuális hálózaton. Például az Azure Virtual Machine Learning számítási példány használatával betaníthat egy modellt, majd üzembe helyezheti a modellt az Azure Kubernetes Service (AKS) szolgáltatásban. A gépi tanulási életciklusokat úgy biztosíthatja, hogy Azure Machine Learning betanítás és következtetési feladatok egy Azure-beli virtuális hálózaton belül vannak el különvetve.

Azure Firewall a munkaterülethez és a nyilvános internethez való Azure Machine Learning szabályozhatja.

- [Virtuális hálózatok elkülönítésének és adatvédelmének áttekintése](how-to-network-security-overview.md)

- [Munkaterület használata Azure Firewall Azure Machine Learning](how-to-access-azureml-behind-firewall.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Virtuális hálózatok, alhálózatok és hálózati hálózatok konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** Azure Machine Learning azure-szolgáltatásokra támaszkodik a számítási erőforrásokhoz. Rendeljen hálózati biztonsági csoportokat a központi telepítés során létrehozott Machine Learning hálózatokhoz. 

Engedélyezze a hálózati biztonsági csoport forgalomnaplóit, és küldje el a naplókat egy Azure Storage-fióknak naplózásra. A forgalmi naplókat egy Log Analytics-munkaterületre is elküldheti, majd a Traffic Analytics segítségével betekintést nyerhet az Azure-felhő forgalmi mintáiba. A hálózati Traffic Analytics néhány előnye a hálózati tevékenység vizualizációja, a gyors helyek és a biztonsági fenyegetések azonosítása, a forgalmi minták azonosítása és a hálózati helytelen konfigurációk észlelése.

- [Hálózati biztonsági csoport forgalomnaplóinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Az alkalmazás engedélyezése és Traffic Analytics](../network-watcher/traffic-analytics.md)

- [A hálózati biztonság Azure Security Center](../security-center/security-center-network-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="13-protect-critical-web-applications"></a>1.3: Kritikus fontosságú webalkalmazások védelme

**Útmutató:** Engedélyezheti a HTTPS-t az által üzembe helyezett webszolgáltatásokkal való kommunikáció Azure Machine Learning. A webszolgáltatások az Azure Kubernetes Servicesben (AKS) vagy a Azure Container Instances (ACI) vannak üzembe helyezni, és az ügyfelek által beküldött adatok biztonságossá teve. Magánhálózati IP-címet is használhat az AKS-sel a pontozás korlátozására, így csak a virtuális hálózat mögötti ügyfelek férhetnek hozzá a webszolgáltatáshoz.

- [TLS használata webszolgáltatás védelméhez az Azure Machine Learning szolgáltatás segítségével](how-to-secure-web-service.md)

- [Virtuális hálózatok elkülönítése és adatvédelme – áttekintés](how-to-network-security-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert kártékony IP-címekkel való kommunikáció megtagadása

**Útmutató:** Engedélyezze DDoS Protection Standard szabványt a Machine Learning-példányhoz társított virtuális hálózatokon az elosztott szolgáltatásmegtagadásos (DDoS-) támadások ellen. Az Azure Security Center fenyegetésészlelés használatával észlelheti az ismert rosszindulatú vagy nem használt internetes IP-címekkel való kommunikációt.

A Azure Firewall a szervezet minden hálózati határán üzembe helyezheti a fenyegetések felderítésére épülő szűrést engedélyező és a "Riasztás és megtagadás" beállítással a kártékony hálózati forgalomhoz.

- [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

- [Munkaterület használata Azure Firewall Azure Machine Learning](how-to-access-azureml-behind-firewall.md)

- [További információ az Azure Security Center fenyegetésészlelésről](../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="15-record-network-packets"></a>1.5: Hálózati csomagok rekordja

**Útmutató:** Minden olyan virtuális géphez, amely megfelelő bővítményt telepített a Azure Machine Learning-szolgáltatásokban, engedélyezheti a Network Watcher csomagrögzítést a rendellenes tevékenységek kivizsgálásához. 

- [Új Network Watcher létrehozása](../network-watcher/network-watcher-create.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Hálózatalapú behatolásészlelési/behatolásvédelmi rendszerek (IDS/IPS) üzembe helyezése

**Útmutató:** Telepítse a választott tűzfalmegoldást a szervezet minden hálózati határán a rosszindulatú forgalom észlelése és/vagy blokkolása érdekében.

Válasszon ki egy olyan ajánlatot a Azure Marketplace, amely támogatja az IDS/IPS funkciót és a hasznos vizsgálatot.  Ha a hasznos információk vizsgálata nem követelmény, Azure Firewall fenyegetési intelligencia használható. Azure Firewall veszélyforrás-felderítésen alapuló szűrés az ismert kártékony IP-címek és tartományok be- és/vagy letiltásán alapul. Az IP-címek és -tartományok forrása a Microsoft veszélyforrás-felderítési hírcsatornája.

- [A Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Riasztások konfigurálása Azure Firewall](../firewall/threat-intel.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Webalkalmazások forgalmának kezelése

**Útmutató:** Nem alkalmazható; Ez a javaslat a számítási erőforrásokon futó webalkalmazások Azure App Service szánt.

**Felelősség:** Nem alkalmazható

**Azure Security Center:** Nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** A Azure Machine Learning-fiókhoz hozzáféréssel bíró erőforrások esetében Virtual Network szolgáltatáscímkékkel határozhatja meg a hálózati biztonsági csoportok vagy hálózati Azure Firewall. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha megadja a szolgáltatáscímke nevét (például AzureMachineLearning) egy szabály megfelelő forrás- vagy célmezőjében, engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkét a címek változásával.

Azure Machine Learning service a számítási célok szolgáltatási címkéinek listáját egy virtuális hálózaton belül, amely segít a bonyolultság minimalizálásában, használhatja útmutatásként a hálózatkezelésben.

- [További információ a szolgáltatáscímkék használatával kapcsolatban](../virtual-network/service-tags-overview.md)

- [Virtuális hálózatok elkülönítése és adatvédelme – áttekintés](how-to-network-security-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációjának fenntartása

**Útmutató:** Szabványos biztonsági konfigurációkat határozhat meg és valósíthat meg a Azure Machine Learning-névterekkel társított hálózati erőforrásokhoz a Azure Policy. A "Microsoft.MachineLearning" és a "Microsoft.Network" névterek Azure Policy-aliasai használatával egyéni szabályzatokat hozhat létre az Machine Learning hálózati konfigurációjának naplózásához vagy kikényszerításához. 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutató:** Az üzembe helyezéshez társított hálózati erőforrásokhoz Azure Machine Learning címkéket használhat, hogy logikailag rendszerezze őket egy taxonómia alapján.

A virtuális hálózatban lévő Azure Machine Learning, amely támogatja a Leírás mezőt, a használatával dokumentálja a hálózatra/hálózatról való forgalmat engedélyező szabályokat.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrások konfigurációjának figyelése és a változások észlelése érdekében

**Útmutató:** Az Azure-tevékenységnapló segítségével monitorozható a hálózati erőforrások konfigurációja, és észlelhetők az erőforrásokkal kapcsolatos Azure Machine Learning. Hozzon létre riasztásokat a Azure Monitor, amelyek akkor aktiválódnak, ha a kritikus hálózati erőforrások módosulnak.

- [Azure-tevékenységnapló eseményeinek megtekintése és lekérése](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Azure biztonsági teljesítményteszt: Naplózás és monitorozás.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Központi biztonsági naplókezelés konfigurálása

**Útmutató:** Naplók Azure Monitor a naplókba a naplók által létrehozott biztonsági adatok Azure Machine Learning. A Azure Monitor Log Analytics-munkaterületek használatával lekérdezheti és elvégezheti az elemzéseket, valamint Azure Storage-fiókokat használhat hosszú távú és archiválási tárterülethez. Másik lehetőségként engedélyezheti és bevetheti az adatokat egy Azure Sentinel egy külső biztonsági incidens és eseménykezelés (SIEM) számára.

- [Diagnosztikai naplók konfigurálása a Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#configuration)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure-erőforrások naplózásának engedélyezése

**Útmutató:** Diagnosztikai beállítások engedélyezése az Azure-erőforrásokon a naplózási, biztonsági és diagnosztikai naplókhoz való hozzáféréshez. Az automatikusan elérhető tevékenységnaplók közé tartozik az eseményforrás, a dátum, a felhasználó, az időbélyeg, a forráscímek, a célcímek és egyéb hasznos elemek.

Biztonsági és megfelelőségi okokból Machine Learning szolgáltatásműveleti naplókat is megfeleltethet.

- [Platformnaplók és -metrikák gyűjtése Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)

- [A naplózás és a különböző naplótípusok az Azure-ban](/azure/azure-monitor/platform/platform-logs-overview)

- [Bejelentkezés engedélyezése Azure Machine Learning](how-to-log-view-metrics.md)

- [Monitorozási Azure Machine Learning](monitor-azure-machine-learning.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Biztonsági naplók gyűjtése operációs rendszerekről

**Útmutató:** Ha a számítási erőforrás a Microsoft tulajdonában van, akkor a Microsoft felelős azok gyűjtéséért és monitorozásáért. 

Azure Machine Learning különböző számítási erőforrásokat, sőt saját számítási erőforrásokat is támogat. A szervezet tulajdonában lévő számítási erőforrásokhoz használja a Azure Security Center az operációs rendszer figyelését. 

- [Azure-beli virtuális gép belső gazdagépnaplóinak gyűjtése a Azure Monitor](/azure/azure-monitor/learn/quick-collect-azurevm)

- [Az Azure Security Center-adatgyűjtés](../security-center/security-center-enable-data-collection.md)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

### <a name="25-configure-security-log-storage-retention"></a>2.5: A biztonsági naplók tárolásának megőrzésének konfigurálása

**Útmutató:** A Azure Monitor vállalat megfelelőségi szabályozásainak megfelelően állítsa be a naplómegőrzési megőrzési megőrzési időszakot a Azure Machine Learning-példányokhoz társított Log Analytics-munkaterületeken.

- [Naplómegőrzési paraméterek beállítása](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és áttekintése

**Útmutató:** Elemezze és figyelje a rendellenes viselkedést a naplókban, és rendszeresen tekintse át a Azure Machine Learning. A Azure Monitor és egy Log Analytics-munkaterület használatával áttekinthet naplókat, és lekérdezéseket hajthatja végre a naplóadatokon.

Másik lehetőségként engedélyezheti és be is használhatja az adatokat Azure Sentinel külső SIEM-hez. 

- [Lekérdezések végrehajtása Azure Machine Learning Log Analytics-munkaterületeken](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#analyzing-log-data)

- [Naplózás engedélyezése a Azure Machine Learning](how-to-log-view-metrics.md)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Ismerkedés a Log Analytics-lekérdezésekkel](../azure-monitor/logs/log-analytics-tutorial.md)

- [Egyéni lekérdezések végrehajtása a Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató:** A Azure Monitor-ban konfigurálja a Azure Machine Learning-hoz kapcsolódó naplókat a tevékenységnaplóban Machine Learning, és diagnosztikai beállításokat a naplók log Analytics-munkaterületre való elküldése céljából, hogy lekérdezhetőek vagy egy tárfiókba küldjenek hosszú távú archiválás céljából. A Log Analytics-munkaterület használatával riasztásokat hozhat létre a biztonsági naplókban és eseményekben található rendellenes tevékenységekhez.

Azt is teheti, hogy engedélyezi és beveszi az adatokat a Azure Sentinel.

- [További információ a riasztások Azure Machine Learning kapcsolatban](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#alerts)

- [Riasztás a Log Analytics-munkaterület naplóadatokkal kapcsolatos adatairól](/azure/azure-monitor/learn/tutorial-response)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="28-centralize-anti-malware-logging"></a>2.8: A kártevőirtó naplózás központosította

**Útmutató:** Ha a számítási erőforrás a Microsoft tulajdonában van, akkor a Microsoft felelős a kártevőirtó alkalmazások Azure Machine Learning service.

Azure Machine Learning különböző számítási erőforrásokat, sőt saját számítási erőforrásokat is támogat. A szervezet tulajdonában lévő számítási erőforrásokhoz engedélyezze a kártevőirtó események gyűjtését a Microsoft Antimalware és Azure Cloud Services Virtual Machines.

- [A felhőszolgáltatásokhoz Microsoft Antimalware bővítmény konfigurálása](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension)

- [A Microsoft Antimalware](../security/fundamentals/antimalware.md)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

### <a name="29-enable-dns-query-logging"></a>2.9: DNS-lekérdezésnaplózás engedélyezése

**Útmutató:** Nem alkalmazható; Azure Machine Learning nem készít DNS-sel kapcsolatos naplókat.

**Felelősség:** Nem alkalmazható

**Azure Security Center:** Nincs

### <a name="210-enable-command-line-audit-logging"></a>2.10: Parancssori naplózás engedélyezése

**Útmutató:** Azure Machine Learning különböző számítási erőforrásokat és akár saját számítási erőforrásokat is támogat. A szervezet tulajdonában lévő számítási erőforrások esetén a Azure Security Center az Azure-beli virtuális gépek biztonsági eseménynapló-monitorozásának engedélyezéséhez. Azure Security Center Log Analytics-ügynököt az összes támogatott Azure-beli virtuális gépre kiépíti, valamint minden olyan újra, amely az automatikus kiépítés engedélyezése esetén jön létre. Az ügynököt manuálisan is telepítheti. Az ügynök engedélyezi a 4688-as folyamat-létrehozási eseményt és a parancssori mezőt a 4688-as eseményen belül. A virtuális gépen létrehozott új folyamatokat az eseménynapló rögzíti, Security Center az észlelési szolgáltatások figyelik.

- [Adatgyűjtés az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ: [Azure Biztonsági teljesítményteszt: Identitás és Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: A rendszergazdai fiókok leltárának fenntartása

**Útmutató:** A Azure Portal-erőforrás identitás- és hozzáférés-kezelés lapján konfigurálhatja az Azure szerepköralapú hozzáférés-vezérlését (Azure RBAC), és leltárt tarthat Azure Machine Learning erőforrásairól. A szerepköröket a rendszer a felhasználókra, csoportokra, szolgáltatásnévre és felügyelt identitásra alkalmazza a Active Directory. Használhatja a beépített szerepköröket vagy egyéni szerepköröket egyéni felhasználók és csoportok számára.

Azure Machine Learning beépített szerepköröket biztosít a gyakori felügyeleti forgatókönyvekhez a Azure Machine Learning. A Azure Active Directory -ban (Azure AD) profillal rendelkező személy hozzárendelheti ezeket a szerepköröket felhasználókhoz, csoportokhoz, szolgáltatásnévhez vagy felügyelt identitásokhoz, hogy hozzáférést biztosítson vagy megtagadjon a Azure Machine Learning erőforrásokhoz és műveletekhez.

Az Azure AD PowerShell-modullal adhoc-lekérdezéseket is végrehajthat a rendszergazdai csoportok tagjainak felderítéséhez.

- [Az Azure szerepköralapú hozzáférés-vezérlésének Azure Machine Learning](how-to-assign-roles.md)

- [Címtárszerepkepkreta az Azure AD-ban a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrole)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Az alapértelmezett jelszavak módosítása, ha vannak

**Útmutató:** A Machine Learning hozzáférés-vezérlése a Azure Active Directory (Azure AD) használatával történik. Az Azure AD nem rendelkezik az alapértelmezett jelszavak fogalmával.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Dedikált rendszergazdai fiókok használata

**Útmutató:** Azure Machine Learning új munkaterület létrehozásakor három alapértelmezett szerepkört kap, amelyek szabványos üzemeltetési eljárásokat hoznak létre a tulajdonosi fiókok használatával kapcsolatban.

A rendszergazdai fiókokhoz az azure-Azure Active Directory (Azure AD) használatával is Privileged Identity Management és Azure Resource Manager.

- [További információ az Machine Learning szerepkörökről](https://docs.microsoft.com/azure/machine-learning/how-to-assign-roles#default-roles)

- [További információ a Privileged Identity Management](/azure/active-directory/privileged-identity-management/index)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató:** Machine Learning integrálva van a Azure Active Directory (Azure AD) szolgáltatással, használjon Azure AD SSO-t az önálló hitelesítő adatok szolgáltatásonkénti konfigurálása helyett. Használja Azure Security Center és hozzáférési javaslatokat.

- [Az SSO és az Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata minden Azure Active Directory hozzáféréshez

**Útmutató:** Engedélyezze a Azure Active Directory (Azure AD) többtényezős hitelesítést, és kövesse Azure Security Center identitással és hozzáféréssel kapcsolatos javaslatokat.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése a Azure Security Center](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Dedikált gépek (Emelt szintű hozzáférésű munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató:** Használjon biztonságos, Azure által felügyelt munkaállomást (más néven emelt szintű hozzáférési szintű munkaállomást vagy emelt hozzáférési szintű munkaállomást) az emelt szintű jogosultságot igénylő felügyeleti feladatokhoz.

- [A biztonságos, Azure által felügyelt munkaállomások](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Többtényezős Azure Active Directory (Azure AD) engedélyezése](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: A rendszergazdai fiókokból származó gyanús tevékenységek naplózása és riasztása

**Útmutató:** Azure Active Directory (Azure AD) biztonsági jelentésekkel és monitorozással észlelheti, ha gyanús vagy nem biztonságos tevékenység történik a környezetben. A Azure Security Center identitás- és hozzáférési tevékenységek figyelése.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md)

- [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató:** A Azure Active Directory (Azure AD) nevű helyekkel csak az IP-címtartományok vagy országok/régiók meghatározott logikai csoportosításaiból engedélyezi a hozzáférést.

- [Az Azure AD nevestű helyek konfigurálása](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="39-use-azure-active-directory"></a>3.9: A Azure Active Directory

**Útmutató:** A Azure Active Directory (Azure AD) használata központi hitelesítési és engedélyezési rendszerként. Az Azure AD erős titkosítással védi az adatokat az átvitel alatt és az átvitel alatt. Az Azure AD emellett a felhasználói hitelesítő adatokat is megsokossa, hashes és biztonságosan tárolja.
 
A szerepkör-hozzáférés több szintre is kiterjedhet az Azure-ban. A Machine Learning a szerepkörök a munkaterület szintjén kezelhetők, például ha tulajdonosi hozzáféréssel rendelkezik egy munkaterülethez, előfordulhat, hogy nem rendelkezik tulajdonosi hozzáféréssel a munkaterületet tartalmazó erőforráscsoporthoz. Ez részletesebb hozzáférés-vezérlést biztosít az ugyanazon erőforráscsoporton belüli szerepkörök elválasztása érdekében. 

- [Azure Machine Learning-munkaterülethez való hozzáférés kezelése](how-to-assign-roles.md) 
 
- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: A felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató:** Azure Active Directory (Azure AD) naplókat biztosít az elavult fiókok felderítésének segítésekor. Emellett az Azure AD-identitás és -hozzáférési felülvizsgálatok használatával hatékonyan kezelheti a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férnek hozzá.

A Azure AD Privileged Identity Management (PIM) használatával naplókat és riasztásokat generál, ha gyanús vagy nem biztonságos tevékenység történik a környezetben.

- [Az Azure AD-jelentéskészítés](/azure/active-directory/reports-monitoring)

- [Az Azure AD identitás- és hozzáférési felülvizsgálatainak használata](../active-directory/governance/access-reviews-overview.md)

- [Üzembe Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Az inaktivált hitelesítő adatok elérésére tett kísérletek figyelése

**Útmutató:** Hozzáféréssel rendelkezik Azure Active Directory (Azure AD) bejelentkezési tevékenységéhez, naplózási és kockázati eseménynapló-forrásaihoz, amelyek lehetővé teszik az integrációt bármely SIEM-/monitorozási eszközzel.

Leegyszerűsítheti ezt a folyamatot, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókokhoz, és elküldi az auditnaplókat és a bejelentkezési naplókat egy Log Analytics-munkaterületre. A kívánt riasztásokat a Log Analytics-munkaterületen konfigurálhatja.

- [Azure-tevékenységnaplók integrálása Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltéréséről

**Útmutató:** A Azure Active Directory (Azure AD) Identity Protection funkcióival konfigurálhatja a felhasználói identitásokkal kapcsolatos észlelt gyanús műveletek automatikus válaszait. Az adatokat be is Azure Sentinel vizsgálathoz.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati szabályzatok konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Hozzáférés biztosítanak a Microsoftnak a releváns ügyféladatokhoz a támogatási forgatókönyvek során

**Útmutató:** Nem alkalmazható; Azure Machine Learning service nem támogatja az ügyfelek zárolását.

**Felelősség:** Nem alkalmazható

**Azure Security Center monitorozás:** Nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Bizalmas adatok leltárának fenntartása

**Útmutató:** A címkék segítségével nyomon lehet követni a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrásokat.
 
- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Bizalmas adatokat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** Elkülönítés implementálás különálló előfizetések és felügyeleti csoportok használatával az egyes biztonsági tartományokhoz, például a környezet típusához és az adatok bizalmasságához. Korlátozhatja az alkalmazások és a vállalati környezetek azure-erőforrásokhoz való hozzáférésének szintjét. Az Azure-erőforrásokhoz való hozzáférést az Azure RBAC-n keresztül szabályozhatja.
 
- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)

 
- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Bizalmas adatok jogosulatlan átvitelének figyelése és blokkolása

**Útmutató:** A peremhálózaton található Azure Marketplace harmadik féltől származó megoldással figyelheti a bizalmas adatok jogosulatlan átvitelét, és letilthatja az ilyen átviteleket, miközben riasztja az információbiztonsági szakembereket. 

A Microsoft által felügyelt mögöttes platformon a Microsoft az összes ügyféltartalmat bizalmasként kezeli, és védelmet nyújt az ügyfelek adatainak elvesztése és mások általi hozzáférése ellen. Annak érdekében, hogy az Azure-beli ügyféladatok biztonságban maradnak, a Microsoft robusztus adatvédelmi vezérlőket és képességeket valósított meg és tart karban. 

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Az átvitel során bizalmas adatok titkosítása

**Útmutató:** A Azure Machine Learning webszolgáltatások csak az 1.2-es TLS-verziót támogatják, amely kényszeríti az adattitkosítást az átvitel során.

- [TLS használata webszolgáltatás védelméhez az Azure Machine Learning szolgáltatás segítségével](how-to-secure-web-service.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: A bizalmas adatok azonosítása aktív felderítési eszközzel

**Útmutató:** Az adatazonosítási, besorolási és adatveszteség-megelőzési funkciók még nem érhetők el a Azure Machine Learning. Implementálja a külső megoldásokat, ha megfelelőségi célokból szükség van rá.

A Microsoft által kezelt mögöttes platform esetén a Microsoft bizalmasként kezeli az összes ügyféltartalmat, és nagy terjedelmüknek megfelelő védelmet biztosít az ügyfelek adatvesztése és kitettsége ellen. Annak érdekében, hogy az Azure-beli ügyféladatok biztonságban maradnak, a Microsoft robusztus adatvédelmi vezérlőket és képességeket valósított meg és tart karban.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Az erőforrásokhoz való hozzáférés kezelése az Azure RBAC használatával

**Útmutató:** Azure Machine Learning támogatja a Azure Active Directory (Azure AD) az erőforrásokhoz való kérések Machine Learning érdekében. Az Azure AD-val azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC) adhat engedélyeket egy rendszerbiztonsági tagnak, amely lehet felhasználó vagy alkalmazás-szolgáltatásnév.

- [Azure Machine Learning-munkaterülethez való hozzáférés kezelése](how-to-assign-roles.md)

- [Az Azure RBAC használata Kubernetes-hitelesítéshez](../aks/manage-azure-rbac.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas adatok titkosítása

**Útmutató:** Azure Machine Learning pillanatképeket, kimeneteket és naplókat tárol az Azure Blob Storage-fiókban, amely az Azure Machine Learning munkaterülethez és az előfizetéséhez van kötve. Az Azure Blob Storage-ban tárolt összes adat titkosítva van a Microsoft által felügyelt kulcsokkal. Az Azure Blob Storage-ban tárolt adatokat saját kulcsokkal is titkosíthatja a Machine Learning szolgáltatásban. 

- [Azure Machine Learning adattitkosítás használata](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#encryption-at-rest)

- [Inaktív adatok Azure-ban való titkosításának ismertetése](../security/fundamentals/encryption-atrest.md)

- [Ügyfél által felügyelt titkosítási kulcsok konfigurálása](../storage/common/customer-managed-keys-configure-key-vault.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Kritikus fontosságú Azure-erőforrások változásainak naplózása és riasztása

**Útmutató:** A Azure Monitor az Azure-tevékenységnaplóval használva riasztásokat hozhat létre arra vonatkozóan, hogy mikor történik változás a Azure Machine Learning és más kritikus vagy kapcsolódó erőforrások éles példányán.

- [Riasztások létrehozása az Azure-tevékenységnapló eseményeihez](/azure/azure-monitor/platform/alerts-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ: [Azure biztonsági teljesítményteszt: Biztonsági rések kezelése.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Automatizált sebezhetőség-ellenőrzési eszközök futtatása

**Útmutató:** Ha a számítási erőforrás a Microsoft tulajdonában van, akkor a Microsoft felelős a biztonsági rések kezeléséért a Azure Machine Learning service. 

Azure Machine Learning eltérő támogatást nyújt a különböző számítási erőforrások, sőt a saját számítási erőforrásai is. A szervezet tulajdonában lévő számítási erőforrások esetén kövesse az Azure Security Center azure-beli virtuális gépek, tároló-rendszerképek és SQL-kiszolgálók sebezhetőségi felmérésének elvégzésére vonatkozó javaslatait.

- [Biztonsági rések felmérésére Azure Security Center javaslatok megvalósítása](../security-center/deploy-vulnerability-assessment-vm.md)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Az operációs rendszer automatikus javításkezelési megoldásának üzembe helyezése

**Útmutató:** Ha a számítási erőforrás a Microsoft tulajdonában van, akkor a Microsoft felel a javítások kezeléséért Azure Machine Learning service. 

Azure Machine Learning eltérő támogatást nyújt a különböző számítási erőforrások, sőt a saját számítási erőforrásai is. A szervezet tulajdonában lévő számítási erőforrások esetén a Azure Automation Update Management használatával győződjön meg arról, hogy a legújabb biztonsági frissítések telepítve vannak a Windows és Linux rendszerű virtuális gépeken. Windows rendszerű virtuális gépek esetén győződjön meg arról, Windows Update engedélyezve van, és automatikus frissítésre van beállítva.

- [Virtuális gépek Update Management konfigurálása az Azure-ban](../automation/update-management/overview.md)

- [Az azure-beli biztonsági szabályzatok Security Center](../security-center/policy-reference.md)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Automatikus javításkezelési megoldás üzembe helyezése külső szoftvercímek számára

**Útmutató:** Azure Machine Learning különböző számítási erőforrások és akár saját számítási erőforrások támogatása is eltérő. A szervezet tulajdonában lévő számítási erőforrásokhoz használjon külső javításkezelő megoldást. A környezetben már Konfigurációkezelő ügyfelek is használhatnak System Center Updates Publisher, így egyéni frissítéseket tehetnek közzé a Windows Server Update Service-be. Ez lehetővé Update Management, hogy a Konfigurációkezelő külső szoftverekkel frissítő adattárként használják a gépeket.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: A biztonsági rések biztonsági rések biztonsági vizsgálatának összehasonlítása

**Útmutató:** Azure Machine Learning különböző számítási erőforrásokat és akár saját számítási erőforrásokat is támogat. A szervezet tulajdonában lévő számítási erőforrások esetén kövesse a Azure Security Center biztonsági rések felmérésére vonatkozó javaslatait az Azure-beli virtuális gépeken, tárolói rendszerképeken és SQL-kiszolgálókon. A vizsgálati eredményeket konzisztens időközönként exportálja, és hasonlítsa össze az eredményeket a korábbi vizsgálatokkal annak ellenőrzéséhez, hogy a biztonsági rések ki lett-e orvosolva. Ha a biztonsági rések kezelésére vonatkozó javaslatokat Azure Security Center, a kiválasztott megoldás portálján kimutatással megtekintheti az előzményvizsgálati adatokat.

- [Biztonsági rések felmérésére Azure Security Center javaslatok megvalósítása](../security-center/deploy-vulnerability-assessment-vm.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Kockázatminősítési folyamat használata a felderített biztonsági rések szervizelésének rangsorolására

**Útmutató:** Nem alkalmazható; Ez az útmutató a számítási erőforrásokhoz való.

**Felelősség:** Nem alkalmazható

**Azure Security Center:** Nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Azure biztonsági teljesítményteszt: Leltár és eszközkezelés.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Automatizált eszközfelderítési megoldás használata

**Útmutató:** A Azure Resource Graph az előfizetések erőforrásait (például számítási, tárolási, hálózati, portok és protokollok stb.) lekérdezésére és felderítésére használhatja. Győződjön meg arról, hogy megfelelő (olvasási) engedélyeket használ a bérlőben, és számba veszi az előfizetések összes Azure-előfizetését és erőforrásait.

Bár a klasszikus Azure-erőforrások a Azure Resource Graph Explorerrel felderíthetőek, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

- [Az Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription)

- [Az Azure RBAC](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="62-maintain-asset-metadata"></a>6.2: Objektum metaadatainak karbantartása

**Útmutató:** Címkék alkalmazása Azure-erőforrásokra, metaadatok hozzáadásával, hogy logikailag rendszerezze őket egy taxonóniában.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Nem engedélyezett Azure-erőforrások törlése

**Útmutató:** Az eszközök rendszerezéséhez és nyomon követéséhez használjon címkézést, felügyeleti csoportokat és különálló előfizetéseket, ha szükséges. Rendszeresen összeegyezteti a leltárat, és gondoskodik arról, hogy a jogosulatlan erőforrások időben törlődjenek az előfizetésből.

- [ További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [ Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)
 
- [ Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Jóváhagyott Azure-erőforrások készletének meghatározása és karbantartása

**Útmutató:** Leltár létrehozása a jóváhagyott Azure-erőforrásokról és a számítási erőforrásokhoz jóváhagyott szoftverekről a szervezeti igényeknek megfelelően.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Nem jóváhagyott Azure-erőforrások figyelése

**Útmutató:** Azure Policy az alábbi beépített szabályzatdefiníciók használatával az ügyfél-előfizetésben létre lehet hozható erőforrások típusára vonatkozó korlátozásokat:

* Nem engedélyezett erőforrástípusok
* Engedélyezett erőforrástípusok

Emellett a Azure Resource Graph az előfizetések erőforrásainak lekérdezéséhez/felderítéséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató:** Azure Machine Learning különböző számítási erőforrásokat és akár saját számítási erőforrásokat is támogat. A szervezet tulajdonában lévő számítási erőforrások esetén a Azure Automation változáskövetés és leltározás automatizálhatja a leltárinformációk gyűjtését a Windows és Linux rendszerű virtuális gépekről. A szoftver neve, verziója, közzétevője és frissítési ideje a Azure Portal. A szoftver telepítési dátumának és egyéb információinak lekértéhez engedélyezze a vendégszintű diagnosztikát, és irányazza a Windows-eseménynaplókat a Log Analytics-munkaterületre.

- [Virtuális gép Azure Automation leltározásának engedélyezése](../automation/automation-tutorial-installed-software.md)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Nem jóváhagyott Azure-erőforrások és -szoftveralkalmazások eltávolítása

**Útmutató:** Azure Machine Learning különböző számítási erőforrásokat és akár saját számítási erőforrásokat is támogat. A szervezet tulajdonában lévő számítási erőforrásokhoz használja a Azure Security Center Fájlintegritás-figyelés (FIM) eszközét a virtuális gépekre telepített összes szoftver azonosításához. Egy másik lehetőség, amely a FIM helyett vagy azzal együtt használható, Azure Automation változáskövetés és leltározás a leltárt a Linux és Windows rendszerű virtuális gépekről. 

A jogosulatlan szoftverek eltávolítására saját folyamatot is megvalósíthat. Harmadik féltől származó megoldással is azonosíthatja a jóvá nem hagyott szoftvereket.

Távolítsa el az Azure-erőforrásokat, ha már nincs rájuk szükség.

- [A fájlintegritás monitorozásának használata](../security-center/security-center-file-integrity-monitoring.md)

- [A Azure Automation változáskövetés és leltározás](../automation/change-tracking/overview.md)

- [Az Azure-beli virtuális gépek leltárának engedélyezése](../automation/automation-tutorial-installed-software.md)

- [Azure-erőforráscsoport és -erőforrás törlése](../azure-resource-manager/management/delete-resource-group.md)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

### <a name="68-use-only-approved-applications"></a>6.8: Csak jóváhagyott alkalmazások használata

**Útmutató:** Azure Machine Learning különböző számítási erőforrások és akár saját számítási erőforrások támogatása is eltérő. A szervezet tulajdonában lévő számítási erőforrások esetén adaptív alkalmazásvezérlőkkel biztosíthatja Azure Security Center, hogy csak az engedélyezett szoftverek végrehajtása és az összes jogosulatlan szoftver végrehajtása legyen blokkolva az Azure Virtual Machines.

- [Az adaptív Azure Security Center használata](../security-center/security-center-adaptive-application.md)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

### <a name="69-use-only-approved-azure-services"></a>6.9: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató:** Azure Policy az alábbi beépített szabályzatdefiníciók használatával az ügyfél-előfizetésben létre lehet hozható erőforrások típusára vonatkozó korlátozásokat:

* Nem engedélyezett erőforrástípusok
* Engedélyezett erőforrástípusok

Emellett a Azure Resource Graph lekérdezheti és felderítheti az előfizetések erőforrásait.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: A jóváhagyott szoftvercímek leltárának fenntartása

**Útmutató:** Azure Machine Learning különböző számítási erőforrások és akár saját számítási erőforrások támogatása is eltérő. A szervezet Azure Security Center tulajdonában lévő számítási erőforrások esetében adaptív alkalmazásvezérlők használatával adhatja meg, hogy egy szabály mely fájltípusokra vonatkozhat vagy melyek nem.

Harmadik féltől származó megoldás implementálható, ha az adaptív alkalmazásvezérlők nem hajtják végre a követelményt.

- [Az adaptív Azure Security Center használata](../security-center/security-center-adaptive-application.md)

**Felelősség:** Nem alkalmazható

**Azure Security Center monitorozás:** Nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Korlátozza a felhasználók számára a Azure Resource Manager

**Útmutató:** A Azure Active Directory (Azure AD) feltételes hozzáféréssel korlátozhatja a felhasználók Azure Resources Managerrel való interakcióját a "Hozzáférés blokkolása" beállításával a "Microsoft Azure Management" alkalmazáshoz.

- [Feltételes hozzáférés konfigurálása az Azure Resources Managerhez való hozzáférés letiltása érdekében](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: Korlátozza a felhasználók szkriptek végrehajtására való képességét a számítási erőforrásokban

**Útmutató:** Azure Machine Learning különböző számítási erőforrásokat és akár saját számítási erőforrásokat is támogat. A szervezet tulajdonában lévő számítási erőforrások esetén a szkriptek típusától függően az operációs rendszerre jellemző konfigurációk vagy külső erőforrások használatával korlátozhatja a felhasználók szkriptek végrehajtására való képességét az Azure számítási erőforrásaiban. Adaptív alkalmazásvezérlő Azure Security Center ket is használhat annak biztosítására, hogy csak az engedélyezett szoftverek végrehajtása és az összes jogosulatlan szoftver végrehajtása legyen letiltva az Azure Virtual Machines.

- [PowerShell-szkriptek végrehajtásának vezérlése Windows-környezetekben](/powershell/module/microsoft.powershell.security/set-executionpolicy)

- [Az adaptív Azure Security Center használata](../security-center/security-center-adaptive-application.md)

**Felelősség:** Nem alkalmazható

**Azure Security Center:** Nincs

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: A magas kockázatú alkalmazások fizikai vagy logikai elkülönítése

**Útmutató:** Nem alkalmazható; Ez a javaslat a számítási erőforrásokon futó webalkalmazások Azure App Service szánt.

**Felelősség:** Nem alkalmazható

**Azure Security Center:** Nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információ: [Azure biztonsági teljesítményteszt: Biztonságos konfiguráció.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató:** Standard biztonsági konfigurációk definiálja és implementálja a Azure Machine Learning service a Azure Policy. Használjon Azure Policy aliasokat a "Microsoft.MachineLearning" névtérben egyéni szabályzatok létrehozásához, amelyek naplózják vagy kikényszeríteni a Azure Machine Learning konfigurációját.

Azure Resource Manager képes exportálni a sablont az JavaScript Object Notation -ban (JSON), amelyet át kell vizsgálni annak biztosítása érdekében, hogy a konfigurációk megfelelnek a szervezet biztonsági követelményeinek.

Az Azure-erőforrások biztonságos alapkonfigurációjaként Azure Security Center az azure-erőforrásokra vonatkozó javaslatokat is használhatja.

Azure Machine Learning teljes mértékben támogatja a Git-adattárakat a munka nyomon követéséhez; Az adattárakat közvetlenül a megosztott munkaterület fájlrendszerére klónozhatja, a Gitet használhatja a helyi munkaállomáson, és biztosíthatja, hogy a biztonságos konfigurációk a kóderőforrásokra is vonatkoznak a saját Machine Learning részeként.

- [Az elérhető aliasok Azure Policy megtekintése](/powershell/module/az.resources/get-azpolicyalias)

- [Oktatóanyag: Szabályzatok létrehozása és kezelése a megfelelőség kikényszeríteni érdekében](../governance/policy/tutorials/create-and-manage.md)

- [Egy- és többerőforrásos exportálás sablonba a Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Biztonsági javaslatok – gyorsútmutató](../security-center/recommendations-reference.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató:** Ha a számítási erőforrás a Microsoft tulajdonában van, akkor a Microsoft felelős az operációs rendszer biztonságos konfigurációiért a Azure Machine Learning service.

Azure Machine Learning eltérő támogatást nyújt a különböző számítási erőforrások, sőt a saját számítási erőforrásai is. A szervezet tulajdonában lévő számítási erőforrásokhoz ajánlott Azure Security Center a biztonsági konfigurációkat az összes számítási erőforráson.  Emellett egyéni operációsrendszer-lemezképeket vagy a Azure Automation állapotkonfigurációt is használhat a szervezet által megkövetelt operációs rendszer biztonsági konfigurációjának létrehozására.

- [A javaslatok Azure Security Center figyelése](../security-center/security-center-recommendations.md)

- [Biztonsági javaslatok – gyorsútmutató](../security-center/recommendations-reference.md)

- [Azure Automation State Configuration áttekintés](../automation/automation-dsc-overview.md)

- [Virtuális merevlemez feltöltése és használata új Windows rendszerű virtuális gépek létrehozásához az Azure-ban](../virtual-machines/windows/upload-generalized-managed.md)

- [Linux rendszerű virtuális gép létrehozása egyéni lemezről az Azure CLI használatával](../virtual-machines/linux/upload-vhd.md)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Biztonságos Azure-erőforrás-konfigurációk fenntartása

**Útmutató:** A Azure Policy [deny] és a [deploy if not exist] használatával kényszerítheti a biztonságos beállításokat az Azure-erőforrásokon. Emellett a sablonsablonok Azure Resource Manager a szervezet által megkövetelt Azure-erőforrások biztonsági konfigurációjának fenntartásához. 
 
- [A Azure Policy hatásainak](../governance/policy/concepts/effects.md)
 
- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../governance/policy/tutorials/create-and-manage.md)
 
- [ Azure Resource Manager sablonok áttekintése](../azure-resource-manager/templates/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató:** Ha a számítási erőforrás a Microsoft tulajdonában van, akkor a Microsoft felelős az operációs rendszer biztonságos konfigurációiért a Azure Machine Learning service.

Azure Machine Learning különböző számítási erőforrásokat, sőt saját számítási erőforrásokat is támogat. A szervezet tulajdonában lévő számítási erőforrásokhoz kövesse a biztonsági rések felmérésére vonatkozó Azure Security Center azure-beli számítási erőforrásokra vonatkozó ajánlásokban található javaslatokat.  Emellett sablonokat, Azure Resource Manager operációsrendszer-lemezképeket vagy Azure Automation State Configuration is használhatja a szervezet által megkövetelt operációs rendszer biztonsági konfigurációjának fenntartásához.   A Microsoft virtuálisgép-sablonjai és a Azure Automation State Configuration segíthetnek a biztonsági követelmények követelményeknek való megfelelésben és fenntartásában. 

Vegye figyelembe, Azure Marketplace a Microsoft által közzétett rendszerképeket a Microsoft kezeli és tartja fenn. 

- [Biztonsági rések felmérésére Azure Security Center javaslatok megvalósítása](../security-center/deploy-vulnerability-assessment-vm.md)

- [Azure-beli virtuális gép létrehozása ARM-sablonból](../virtual-machines/windows/ps-template.md)

- [Azure Automation State Configuration áttekintés](../automation/automation-dsc-overview.md)

- [Windows rendszerű virtuális gép létrehozása a Azure Portal](../virtual-machines/windows/quick-create-portal.md)

- [Információk a virtuálisgép-sablon letöltésével kapcsolatban](/previous-versions/azure/virtual-machines/windows/download-template)

- [Példaszkript egy VHD Azure-ba történő feltöltéséhez és új virtuális gép létrehozásához](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások konfigurációjának biztonságos tárolása

**Útmutató:** Ha egyéni Azure Policy definíciókat használ a Machine Learning vagy a kapcsolódó erőforrásokhoz, az Azure Repos használatával biztonságosan tárolhatja és kezelheti a kódot.

Azure Machine Learning teljes mértékben támogatja a Git-adattárakat a munka nyomon követéséhez; Az adattárakat közvetlenül a megosztott munkaterület fájlrendszerére klónozhatja, a Gitet használhatja a helyi munkaállomáson, és biztosíthatja, hogy a biztonságos konfigurációk a kóderőforrásokra is vonatkoznak a saját Machine Learning részeként.

- [Kód tárolása az Azure DevOpsban](/azure/devops/repos/git/gitworkflow)

- [Az Azure Repos dokumentációja](/azure/devops/repos/)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató:** Azure Machine Learning különböző számítási erőforrások és akár saját számítási erőforrások támogatása is eltérő. A szervezet tulajdonában lévő számítási erőforrásokhoz használja az Azure szerepköralapú hozzáférés-vezérlését (Azure RBAC), hogy csak a jogosult felhasználók férnek hozzá az egyéni rendszerképekhez. Azure-Shared Image Gallery a rendszerképeket megoszthatja a szervezet különböző felhasználóival, szolgáltatásnévvel vagy Azure Active Directory (Azure AD) csoportokkal. Tároló rendszerképeket tárol a Azure Container Registry és az Azure RBAC használatával biztosíthatja, hogy csak a jogosult felhasználók férnek hozzá.

- [Az Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Az Azure RBAC for Container Registry](../container-registry/container-registry-roles.md)

- [Az Azure RBAC konfigurálása](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Shared Image Gallery áttekintése](../virtual-machines/shared-image-galleries.md)

- [Az Azure RBAC használata Kubernetes-hitelesítéshez](../aks/manage-azure-rbac.md)

**Felelősség:** Nem alkalmazható

**Azure Security Center monitorozás:** Nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Konfigurációkezelési eszközök üzembe helyezése Azure-erőforrásokhoz

**Útmutató:** A Azure Policy a "Microsoft.MachineLearning" névtérben található aliasok használatával egyéni szabályzatokat hozhat létre a rendszerkonfigurációk riasztására, naplózására és kényszerítésére. Emellett dolgozzon ki egy folyamatot és folyamatot a szabályzati kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Aliasok használata](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Konfigurációkezelő eszközök központi telepítése operációs rendszerekhez

**Útmutató:** Ha a számítási erőforrás a Microsoft tulajdonában van, akkor a Microsoft felelős az erőforrások biztonságos Azure Machine Learning service.

Azure Machine Learning különböző számítási erőforrásokat, sőt saját számítási erőforrásokat is támogat. A szervezet tulajdonában lévő számítási erőforrásokhoz használja a Azure Automation State Configuration (DSC) Desired State Configuration csomópontokhoz bármely felhőalapú vagy helyszíni adatközpontban. Könnyedén felvehet gépeket, deklaratív konfigurációkat rendelhet hozzájuk, és megtekintheti a jelentéseket, amelyek megmutatják, hogy az egyes gépek megfelelnek-e a megadott célállapotnak. 

- [A Azure Automation State Configuration](../automation/automation-dsc-onboarding.md)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Automatizált konfigurációfigyelés megvalósítása Azure-erőforrásokhoz

**Útmutató:** A Azure Security Center az Azure-erőforrások alapkonfiguráció-vizsgálatának végrehajtásához. Emellett az Azure Azure Policy-konfigurációk riasztását és naplózását is használhatja.
 
 
 
- [ Javaslatok szervizlének Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Automatikus konfigurációfigyelés megvalósítása operációs rendszerekhez

**Útmutató:** Ha a számítási erőforrás a Microsoft tulajdonában van, akkor a Microsoft felelős az erőforrások automatikus biztonságos konfigurációfigyeléséért Azure Machine Learning service.

Azure Machine Learning különböző számítási erőforrásokat, sőt saját számítási erőforrásokat is támogat. A szervezet tulajdonában lévő számítási erőforrások esetében használja a Azure Security Center Compute Apps szolgáltatást, és kövesse a virtuális gépekre, kiszolgálókra és tárolókra vonatkozó &amp; javaslatokat.

- [Az Azure Security Center tárolókra vonatkozó ajánlásainak értelmezése](../security-center/container-security.md)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-beli titkos kulcsok biztonságos kezelése

**Útmutató:** A felügyelt identitások és a Azure Key Vault leegyszerűsítheti a felhőalkalmazások titkos adatok kezelését.

Azure Machine Learning támogatja az ügyfél által kezelt kulcsokkal való adattitkosítást, a kulcsforgatás és -visszavonás a szervezet biztonsági és megfelelőségi követelményeinek megfelelően szükséges. 

A Azure Key Vault a titkos kódokat biztonságosan, a betanító szkriptek tiszta szövege helyett a távoli futtatásba használhatja.

- [Azure Machine Learning által kezelt kulcsok használata](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-blob-storage)

- [Hitelesítő adatok titkos kulcsának használata Azure Machine Learning betanítás futtatásakor](how-to-use-secrets-in-runs.md)

- [Felügyelt identitások használata Azure-erőforrásokhoz](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Új Key Vault](../key-vault/general/quick-create-portal.md)

- [Hitelesítés a Key Vault](../key-vault/general/authentication.md)

- [Hozzáférés-Key Vault hozzárendelése](../key-vault/general/assign-access-policy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identitások biztonságos és automatikus kezelése

**Útmutató:** Azure Machine Learning támogatja a beépített szerepköröket és az egyéni szerepkörök létrehozására való képességet. Felügyelt identitások használatával biztosítson egy automatikusan felügyelt identitást az Azure-szolgáltatások számára a Azure Active Directory (Azure AD) szolgáltatásban. A felügyelt identitások lehetővé teszik az Azure AD-hitelesítést támogató bármely szolgáltatásban történő hitelesítést, beleértve a Key Vault szolgáltatást is, anélkül, hogy a hitelesítő adatokat a kódban lenne.

- [Azure Machine Learning-munkaterülethez való hozzáférés kezelése](how-to-assign-roles.md)

- [Felügyelt identitások konfigurálása Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: A hitelesítő adatok nem szándékolt kitettségének megszüntetése

**Útmutató:** Hitelesítőadat-ellenőrző implementálja a hitelesítő adatokat a kódban. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz. 

- [Hitelesítőadat-ellenőrző beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információ: [Azure biztonsági teljesítményteszt: Kártevők elleni védelem.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: Központilag felügyelt kártevőirtó szoftver használata

**Útmutató:** A Microsoft kártevőirtó engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure Machine Learning), de nem fut az ügyféltartalmakon.

Azure Machine Learning különböző számítási erőforrásokat, sőt saját számítási erőforrásokat is támogat. A szervezet tulajdonában lévő számítási erőforrásokhoz használja az Azure Microsoft Antimalware az erőforrások folyamatos figyelése és megvédése érdekében. Linux rendszeren használjon külső gyártótól származó kártevőirtó megoldást. A tárfiókok Azure Security Center az adatszolgáltatások fenyegetésészlelési funkcióját is használhatja a tárfiókba feltöltött kártevők észleléséhez.

- [Az Azure-Microsoft Antimalware konfigurálása](../security/fundamentals/antimalware.md)

- [Fenyegetésvédelem az Azure Security Centerben](../security-center/azure-defender.md)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: A nem számítási Azure-erőforrásokba feltölteni szükséges fájlok előzetes beolvasása

**Útmutató:** A Microsoft kártevőirtó engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure Machine Learning), de nem fut az ügyfelek tartalmai között.

Az Ön felelőssége, hogy előzetesen megvizsgálja a nem számítási Azure-erőforrásokra feltöltött tartalmakat. A Microsoft nem fér hozzá az ügyféladatokhoz, ezért az Ön nevében nem tud kártevőirtó vizsgálatot végezni az ügyféltartalmakban.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3: Kártevőirtó szoftverek és aláírások frissítésének biztosítása

**Útmutató:** A Microsoft kártevőirtó engedélyezve és karbantartva van az Azure-szolgáltatásokat (például az Azure Machine Learninget) támogató mögöttes gazdagépen, azonban nem fut az ügyfelek tartalmai között.

Azure Machine Learning különböző számítási erőforrásokat, sőt saját számítási erőforrásokat is támogat. A szervezet tulajdonában lévő számítási erőforrások esetében kövesse a Azure Security Center Compute Apps (Számítási alkalmazások) Azure Security Center, hogy az összes végpont naprakész legyen a legújabb &amp; aláírásokkal. Linux rendszeren használjon külső gyártótól származó kártevőirtó megoldást.

- [Azure-Microsoft Antimalware üzembe helyezése](../security/fundamentals/antimalware.md)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Azure biztonsági teljesítményteszt: Adat-helyreállítás.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatikus biztonságimentás-frissítések biztosítása

**Útmutató:** A Machine Learning szolgáltatásban az adat-helyreállítás kezelése a csatlakoztatott adattárak adatkezelési folyamatán keresztül megy keresztül. Kövesse a csatlakoztatott tárolókra vonatkozó adat-helyreállítási irányelveket az adatok ügyfélszervezeti szabályzatonkénti biztonságimentéséhez.

- [Fájlok helyreállítása az Azure-beli virtuális gépek biztonsági másolatából](../backup/backup-azure-restore-files-from-vm.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszer-biztonsági mentések és az ügyfelek által kezelt kulcsok biztonsági mentése

**Útmutató:** A Machine Learning szolgáltatásban az adatok biztonsági mentése a csatlakoztatott adattárak adatkezelési szolgáltatásán keresztül megy keresztül. Engedélyezze Azure Backup virtuális gépek számára, és konfigurálja a kívánt gyakoriságot és megőrzési időszakokat. Az ügyfél által felügyelt kulcsok biztonsági Azure Key Vault.

- [Fájlok helyreállítása az Azure-beli virtuális gépek biztonsági másolatából](../backup/backup-azure-restore-files-from-vm.md)

- [A kulcskulcsok Key Vault visszaállítása az Azure-ban](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató:** Az adatok biztonsági mentésének ellenőrzése Machine Learning szolgáltatásban a csatlakoztatott adattárak adatkezelési szolgáltatásán keresztül megy keresztül. Rendszeresen végezze el az adatok helyreállítását a Azure Backup. Győződjön meg arról, hogy vissza tudja állítani az ügyfél által kezelt biztonsági másolatban található kulcsokat.

- [Fájlok helyreállítása Azure-beli virtuális gépek biztonsági másolatából](../backup/backup-azure-restore-files-from-vm.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági másolatok és az ügyfél által kezelt kulcsok védelmének biztosítása

**Útmutató:** Helyszíni biztonsági mentés esetén az Azure-ba való biztonsági mentéskor megadott jelszóval történik az adattitkosítás. Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával megvédheti a biztonsági másolatokat és az ügyfelek által kezelt kulcsokat. 

Engedélyezze a kulcstörlést és a végleges törlés elleni védelmet Key Vault a kulcsok véletlen vagy rosszindulatú törléssel szembeni védelme érdekében. Ha az Azure Storage biztonsági másolatokat tárol, engedélyezze a helyreállítható törlést az adatok mentéséhez és helyreállításához blobok vagy blob-pillanatképek törlésekor.

- [Az Azure RBAC](../role-based-access-control/overview.md)

- [Helyreállítható törlés és a végleges törléssel szembeni védelem engedélyezése kulcstartóban](../storage/blobs/soft-delete-blob-overview.md)

- [Az Azure Blob Storage soft delete parancsa](../storage/blobs/soft-delete-blob-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Incidensválasz-útmutató létrehozása

**Útmutató:** Incidensválasz-útmutató kidolgozása a szervezet számára. Győződjön meg arról, hogy vannak olyan írásos incidenskezelési tervek, amelyek meghatározzák a személyzet összes szerepkörét, valamint az incidenskezelés és -kezelés fázisát az észleléstől az incidens utáni felülvizsgálaton át az incidensek áttekintéséhez. 

- [Útmutató saját biztonsági incidensek válaszadási folyamatának építéshez](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Biztonsági reagálási központ az incidensek felépítése](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [A NIST számítógépes biztonsági incidenskezelési útmutatójának használata saját incidenskezelési terv létrehozásához](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és -rangsorolási eljárás létrehozása

**Útmutató:** Azure Security Center súlyosságot rendel az egyes riasztásokhoz, így priorizálhatja, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy Security Center mennyire magabiztos a riasztás kiadásához használt eredményben vagy analitikus módon, valamint a megbízhatósági szinten, hogy a riasztáshoz vezető tevékenység mögött rosszindulatú szándék volt-e.

Emellett jelölje meg az előfizetéseket címkék használatával, és hozzon létre egy elnevezési rendszert az Azure-erőforrások azonosításához és kategorizálásához, különösen a bizalmas adatok feldolgozásakor. Az Ön felelőssége, hogy rangsorolja a riasztások szervizelését azon Azure-erőforrások és -környezet kritikussága alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="103-test-security-response-procedures"></a>10.3: Biztonsági reagálási eljárások tesztelése

**Útmutató:** Gyakorlatok a rendszerek incidensválasz-képességeinek rendszeres teszteléséhez az Azure-erőforrások védelme érdekében. Azonosítsa a gyenge pontokat és hiányosságokat, majd szükség szerint módosítsa a választervet.

- [Az NIST kiadványa – Útmutató az it-csomagokhoz és képességekhez szükséges tesztelési, képzési és gyakorlatprogramokhoz](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidens kapcsolattartási adatait, és konfigurálja a riasztási értesítéseket a biztonsági incidensek esetén

**Útmutató:** A Biztonsági incidens kapcsolattartási adatait a Microsoft arra használja, hogy kapcsolatba lépjen Önnel, ha a Microsoft Biztonsági reagálási központ (MSRC) azt tapasztalja, hogy az Ön adatait illetéktelen vagy illetéktelen fél férte hozzá. Tekintse át az incidenseket a probléma megoldása érdekében.

- [Az Azure Security Center biztonsági kapcsolattartójának beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidensválasz-rendszerbe

**Útmutató:** A Azure Security Center riasztások és javaslatok exportálása a folyamatos exportálási funkcióval az Azure-erőforrásokat érintő kockázatok azonosítása érdekében. A folyamatos exportálás lehetővé teszi, hogy manuálisan vagy folyamatosan exportálja a riasztásokat és javaslatokat. A riasztásokat a Azure Security Center adat-összekötővel streamelheti a Azure Sentinel.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: A biztonsági riasztások válaszának automatizálása

**Útmutató:** A munkafolyamat-automatizálási Azure Security Center a biztonsági riasztások és javaslatok automatikus aktiválására az Azure-erőforrások védelme érdekében.

- [Munkafolyamat-automatizálás konfigurálása a Security Center](../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információ: [Azure biztonsági teljesítményteszt: Behatolási tesztek és Red Team-gyakorlatok.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Az Azure-erőforrások rendszeres behatolástesztelése és az összes kritikus fontosságú biztonsági eredmény szervizelésének biztosítása

**Útmutató:** Kövesse a Microsoft Cloud behatolástesztelési szabályait annak ellenőrzéséhez, hogy a behatolási tesztek nem sértik-e meg a Microsoft szabályzatát. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét.

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
