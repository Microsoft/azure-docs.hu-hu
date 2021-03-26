---
title: Azure biztonsági alapkonfiguráció a HDInsight-hez
description: A HDInsight biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c5ffdecf768be0962950bb3691dbb11fb0e70120
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565010"
---
# <a name="azure-security-baseline-for-hdinsight"></a>Azure biztonsági alapkonfiguráció a HDInsight-hez

Ez a biztonsági alapterv az [Azure biztonsági teljesítményteszt 1,0-es verziójáról](../security/benchmarks/overview-v1.md) a HDInsight-re vonatkozó útmutatást alkalmazza. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** és a HDInsight vonatkozó kapcsolódó útmutatás szerint van csoportosítva. A HDInsight nem alkalmazható **vezérlők** ki vannak zárva.

 
Ha szeretné megtudni, hogy a HDInsight hogyan teljes mértékben leképezi az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes HDInsight biztonsági alapkonfiguráció-hozzárendelési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: az Azure HDInsight-beli biztonsági rendszerek virtuális hálózatokon keresztül valósulnak meg. A vállalati rendszergazda létrehozhat egy fürtöt egy virtuális hálózaton belül, és hálózati biztonsági csoport (NSG) használatával korlátozhatja a virtuális hálózathoz való hozzáférést. Csak a bejövő hálózati biztonsági csoport szabályaiban engedélyezett IP-címek képesek kommunikálni az Azure HDInsight-fürttel. Ez a konfiguráció peremhálózati biztonságot nyújt. A virtuális hálózatban üzembe helyezett összes fürt egy privát végponttal is rendelkezik, amely a Virtual Network belső IP-címére lesz feloldva a fürt átjáróinak saját HTTP-eléréséhez.

A kiszűrése-on keresztüli adatvesztés kockázatának csökkentése érdekében az Azure Firewall használatával korlátozza az Azure HDInsight-fürtök kimenő hálózati forgalmát.

- [Azure-HDInsight üzembe helyezése Virtual Networkon belül és biztonságos hálózati biztonsági csoporttal](hdinsight-create-virtual-network.md)

- [Az Azure HDInsight-fürtök kimenő forgalmának korlátozása Azure Firewall](hdinsight-restrict-outbound-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: az Azure HDInsight-fürt védelméhez használt virtuális hálózatra, alhálózatra és hálózati biztonsági csoportra vonatkozó hálózati védelmi javaslatok Azure Security Center használata és szervizelése. Engedélyezze a hálózati biztonsági csoport (NSG) folyamatának naplóit, és küldje el a naplókat egy Azure Storage-fiókba a forgalom naplózása érdekében. NSG-naplókat is küldhet egy Azure Log Analytics-munkaterületre, és az Azure Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. Az Azure Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati hibás konfigurációk meghatározását.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Az Azure Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártevő IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: a DDOS-támadások elleni védelem érdekében engedélyezze az Azure DDoS standard Protectiont azon a virtuális hálózaton, amelyen az Azure-HDInsight üzembe lett helyezve. A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel.

- [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

- [Az Azure Security Center integrált veszélyforrások felderítésének megismerése](../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: engedélyezze a hálózati biztonsági csoport (NSG) számára az Azure HDInsight-fürt védelméhez használt alhálózathoz csatolt NSG. Jegyezze fel a NSG folyamat naplóit egy Azure Storage-fiókba a flow-rekordok létrehozásához. Ha a rendellenes tevékenységek kivizsgálásához szükséges, engedélyezze az Azure Network Watcher a csomagok rögzítését.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Network Watcher engedélyezése](../network-watcher/network-watcher-create.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: az Azure Security control id 1,1; követelménye teljesül. Üzembe helyezheti az Azure HDInsight-fürtöt egy virtuális hálózatban, és biztonságossá teheti a hálózati biztonsági csoporttal (NSG).

Az Azure-HDInsight több függősége is van, amelyek bejövő forgalmat igényelnek. A bejövő felügyeleti forgalmat nem lehet tűzfal-eszközön keresztül elküldeni. A szükséges felügyeleti forgalom forrás-címei ismertek és közzé vannak téve. Hozzon létre hálózati biztonsági csoportokra vonatkozó szabályokat ezekkel az adatokkal, hogy csak a megbízható helyek forgalmát engedélyezzék, így biztosítva a bejövő adatforgalom védelmét a fürtökön.

A kiszűrése-on keresztüli adatvesztés kockázatának csökkentése érdekében az Azure Firewall használatával korlátozza az Azure HDInsight-fürtök kimenő hálózati forgalmát.

- [HDInsight üzembe helyezése Virtual Networkon belül és biztonságos hálózati biztonsági csoporttal](hdinsight-create-virtual-network.md)

- [A HDInsight függőségeinek és a tűzfal használatának ismertetése](hdinsight-restrict-outbound-traffic.md)

- [HDInsight kezelése – IP-címek](hdinsight-management-ip-addresses.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: virtuális hálózati szolgáltatás-címkék használata az Azure HDInsight-fürt üzembe helyezéséhez használt hálózati biztonsági csoportok (NSG) hálózati hozzáférés-vezérlésének definiálásához. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például ApiManagement), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

- [Az Azure HDInsight szolgáltatásbeli címkék megismerése és használata](../virtual-network/network-security-groups-overview.md#service-tags)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: szabványos biztonsági konfigurációk definiálása és implementálása az Azure HDInsight-fürthöz kapcsolódó hálózati erőforrásokhoz. Használjon Azure Policy aliasokat a "Microsoft. HDInsight" és a "Microsoft. Network" névterekben egyéni szabályzatok létrehozásához az Azure HDInsight-fürt hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez.

Az Azure-tervrajzok segítségével leegyszerűsítheti a nagyméretű Azure-alapú üzembe helyezéseket, például a Azure Resource Manager sablonokat, az Azure RBAC-vezérlőket és a házirendeket egyetlen terv definíciójában. Egyszerűen alkalmazhatja a tervrajzot új előfizetésekre és környezetekre, és részletesen beállíthatja a vezérlést és a felügyeletet a verziószámozással.

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: a hálózati biztonsági csoport (NSG) és az Azure HDInsight-fürthöz társított hálózati biztonsággal és adatforgalommal kapcsolatos egyéb erőforrásokhoz tartozó címkék használata. Az egyes NSG-szabályok esetében a "Leírás" mezővel adhatja meg az üzleti igényeket és/vagy időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatra irányuló adatforgalmat.

A címkézéshez kapcsolódó beépített Azure Policy definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.

A Azure PowerShell vagy az Azure parancssori felülete (CLI) használatával felkeresheti vagy végrehajthatja az erőforrásokon végzett műveleteket a címkék alapján.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-Tevékenységnaplók használatával figyelheti a hálózati erőforrás-konfigurációkat, és felderítheti az Azure HDInsight üzemelő példányokhoz kapcsolódó hálózati erőforrások változásait. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitorban](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

Útmutató: az Azure HDInsight-fürt **Bevezetésével** Azure monitor a fürt által generált biztonsági adatokat összesítve. Az egyéni lekérdezések használatával észlelheti és reagálhat a környezetbeli fenyegetésekre. 

- [Azure HDInsight-fürt előkészítése Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Egyéni lekérdezések létrehozása Azure HDInsight-fürthöz](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: engedélyezze a Azure monitort a HDInsight-fürthöz, és irányítsa azt egy log Analytics-munkaterületre. Ez naplózza a kapcsolódó fürt adatait és az operációsrendszer-metrikákat az összes Azure HDInsight-fürt csomópontjaihoz.

- [A HDInsight-fürt naplózásának engedélyezése](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [HDInsight-naplók lekérdezése](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: az Azure HDInsight-fürt előkészítése Azure monitor. Győződjön meg arról, hogy a használt Log Analytics munkaterületen a szervezet megfelelőségi előírásai szerint beállított napló-megőrzési időtartam szerepel.

- [Azure HDInsight-fürt előkészítése Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Log Analytics munkaterület megőrzési időtartamának konfigurálása](../azure-monitor/logs/manage-cost-storage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: az Azure HDInsight-fürt előkészítése Azure monitor. Győződjön meg arról, hogy a használt Azure Log Analytics munkaterület rendelkezik a szervezet megfelelőségi előírásai szerint beállított napló-megőrzési időtartammal.

- [Azure HDInsight-fürt előkészítése Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Log Analytics munkaterület megőrzési időtartamának konfigurálása](../azure-monitor/logs/manage-cost-storage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: azure-log Analytics munkaterület-lekérdezések használata az Azure HDInsight-naplók lekérdezéséhez:

- [Egyéni lekérdezések létrehozása az Azure HDInsight-fürtökhöz](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: az Azure log Analytics munkaterületének használata az Azure HDInsight-fürttel kapcsolatos rendellenes tevékenységekre vonatkozó figyeléshez és riasztáshoz.

- [Riasztások kezelése Azure Security Centerban](../security-center/security-center-managing-and-responding-alerts.md)

- [Riasztás a log Analytics-naplófájlok adatkezeléséről](../azure-monitor/alerts/tutorial-response.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: az Azure HDInsight előzetesen telepített és engedélyezett ClamScan tartalmaz, azonban felügyelni kell a szoftvereket, és manuálisan kell összesíteni/figyelnie a naplók ClamScan.

- [A ClamScan ismertetése](./hdinsight-faq.md#security-and-certificates)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: harmadik féltől származó megoldás implementálása a DNS-naplózáshoz.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: a konzol naplózásának manuális konfigurálása a csomópontok alapján.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: az Azure HDInsight-fürt kiépítés során létrehozott helyi rendszergazdai fiók, valamint a létrehozott egyéb fiókok nyilvántartásának fenntartása. Továbbá, ha Azure Active Directory (Azure AD) integrációt használ, az Azure AD olyan beépített szerepköröket tartalmaz, amelyeket explicit módon kell hozzárendelni, és ezért lekérdezhető. Az Azure AD PowerShell-modul használatával ad hoc lekérdezéseket a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

Emellett Azure Security Center identitás-és hozzáférés-kezelési javaslatokat is használhat.

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Identitás és hozzáférés figyelése Azure Security Center](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: fürt kiépítés esetén az Azure-ban új jelszavakat kell létrehoznia a webes portálhoz és a Secure Shell-(SSH-) hozzáféréshez. Nincsenek módosítható alapértelmezett jelszavak, azonban más jelszavakat is megadhat az SSH és a webes portál eléréséhez.

- [Jelszavak beállítása Azure HDInsight-fürt kiépítés esetén](hdinsight-hadoop-linux-use-ssh-unix.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: az Azure HDInsight-fürt hitelesítésének integrálása Azure Active Directory (Azure ad) használatával. Szabályzatokat és eljárásokat hozhat létre a dedikált rendszergazdai fiókok használata körül.

Emellett Azure Security Center identitás-és hozzáférés-kezelési javaslatokat is használhat.

- [Az Azure HDInsight-hitelesítés integrálása az Azure AD-vel](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- [Identitás és hozzáférés figyelése Azure Security Center](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory egyszeri bejelentkezés (SSO) használata

**Útmutató**: az Azure HDInsight ID Broker használatával többtényezős hitelesítéssel jelentkezhet be Enterprise Security Package (ESP) fürtökbe anélkül, hogy jelszót kellene biztosítania. Ha már bejelentkezett más Azure-szolgáltatásokba, például a Azure Portalba, bejelentkezhet az Azure HDInsight-fürtbe egyszeri bejelentkezéses (SSO) felhasználói élményben.

- [Az Azure HDInsight ID Broker engedélyezése](./domain-joined/identity-broker.md#enable-hdinsight-id-broker)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: engedélyezze Azure Active Directory (Azure ad) többtényezős hitelesítését, és kövesse Azure Security Center identitás-és hozzáférés-kezelési javaslatait. Az Enterprise Security Package konfigurált Azure HDInsight-fürtök csatlakozni tudnak egy tartományhoz, így a tartományi felhasználók a fürt hitelesítő adataikkal hitelesíthetők, és big data feladatokat futtathatnak. Ha engedélyezve van a többtényezős hitelesítés, a rendszer a felhasználókat a második hitelesítési tényező megadására fogja felvenni.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: a mancsok (emelt szintű hozzáférésű munkaállomások) használata többtényezős hitelesítéssel, amely az Azure HDInsight-fürtök és a kapcsolódó erőforrások bejelentkezésére és konfigurálására van konfigurálva.

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: az Azure HDInsight-fürtöket a konfigurált Enterprise Security Package lehet csatlakoztatni egy tartományhoz, hogy a tartományi felhasználók a hitelesítéshez a tartományi hitelesítő adataikat használják. A naplók és a riasztások generálásához Azure Active Directory (Azure AD) biztonsági jelentéseket használhat, ha az Azure AD-környezetben gyanús vagy nem biztonságos tevékenység történik. A Azure Security Center használatával figyelheti az identitás-és hozzáférési tevékenységeket.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md)

- [A felhasználók identitás-és hozzáférési tevékenységének figyelése Azure Security Center](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: az Azure HDInsight-fürtöket a konfigurált Enterprise Security Package lehet csatlakoztatni egy tartományhoz, hogy a tartományi felhasználók a hitelesítéshez a tartományi hitelesítő adataikat használják. A feltételes hozzáférés elnevezett helyeivel engedélyezheti a hozzáférést az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz.

- [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerrel. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

A konfigurált Enterprise Security Package (ESP) rendszerű Azure HDInsight-fürtök csatlakozni tudnak egy tartományhoz, így a tartományi felhasználók a fürthöz való hitelesítéshez használhatják a tartományi hitelesítő adataikat.

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Enterprise Security Package konfigurálása a Azure AD Domain Services az Azure HDInsight](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: Azure Active Directory (Azure ad) hitelesítés használata az Azure HDInsight-fürttel. Az Azure AD olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználó hozzáférését rendszeresen felül lehet vizsgálni, hogy csak a megfelelő felhasználók férhessenek hozzájuk.

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: a inaktivált fiókok elérésére irányuló kísérletek figyeléséhez használja a Azure Active Directory (Azure ad) bejelentkezési és naplózási naplókat. Ezek a naplók bármely harmadik féltől származó SIEM/monitoring eszközbe integrálhatók.

Ezt a folyamatot leegyszerűsítheti az Azure AD felhasználói fiókok diagnosztikai beállításainak létrehozásával, a naplók és a bejelentkezési naplók Azure Log Analytics-munkaterületre való elküldésével. A kívánt riasztások konfigurálása az Azure Log Analytics-munkaterületen belül.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a konfigurált Enterprise Security Package (ESP)-vel rendelkező Azure HDInsight-fürtök csatlakozni tudnak egy tartományhoz, így a tartományi felhasználók a fürthöz való hitelesítéshez használhatják a tartományi hitelesítő adataikat. Használja a Azure Active Directory (Azure AD) kockázati Észleléseit és az Identity Protection szolgáltatást a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatikus válaszok konfigurálásához. Emellett további vizsgálatot is betöltheti az Azure Sentinelbe.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: nem érhető el; Ügyfélszéf az Azure HDInsight még nem támogatott.

- [Ügyfélszéf támogatott szolgáltatások listája](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: az Azure HDInsight üzemelő példányokhoz kapcsolódó erőforrásokon használható címkék használata a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követéséhez.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. Az Azure HDInsight-fürtöket és a kapcsolódó Storage-fiókokat a megfelelő címkével elválasztva, a hálózati biztonsági csoporton (NSG) és a Azure Firewallon belül biztonságossá kell tennie. A fürt adatainak biztonságos Azure Storage-fiókban vagy Azure Data Lake Storageban (Gen1 vagy Gen2) belül kell szerepelniük.

- [Tárolási beállítások kiválasztása az Azure HDInsight-fürthöz](hdinsight-hadoop-compare-storage-options.md)

- [A Azure Data Lake Storage biztonságossá tétele](../data-lake-store/data-lake-store-security-overview.md)

- [Azure Storage-fiókok biztonságossá tétele](../storage/blobs/security-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: a bizalmas adatok tárolására vagy feldolgozására szolgáló Azure HDInsight-fürtök esetén a fürt és a kapcsolódó erőforrások bizalmasként való megjelölése címkék használatával. A kiszűrése-on keresztüli adatvesztés kockázatának csökkentése érdekében az Azure Firewall használatával korlátozza az Azure HDInsight-fürtök kimenő hálózati forgalmát.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az Azure HDInsight-fürtök kimenő forgalmának korlátozása Azure Firewall](hdinsight-restrict-outbound-traffic.md)

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: az összes bizalmas adat titkosítása az átvitel során. Győződjön meg arról, hogy az Azure HDInsight-fürthöz vagy a fürt adattárához (Azure Storage-fiókokhoz vagy Azure Data Lake Storage Gen1/Gen2) csatlakozó ügyfelek képesek a TLS 1,2 vagy újabb egyeztetésére. A Microsoft Azure-erőforrások alapértelmezés szerint a TLS 1,2-et fogják egyeztetni. 

- [Azure Data Lake Storage titkosítás ismertetése az átvitel során](../data-lake-store/data-lake-store-security-overview.md)

- [Az Azure Storage-fiók titkosításának megismerése az átvitel során](../storage/blobs/security-recommendations.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el az Azure Storage vagy a számítási erőforrások számára. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC 

**Útmutató**: az Azure HDInsight Enterprise Security Package (ESP) segítségével az Apache Ranger segítségével részletes hozzáférés-vezérlési és adattitkosítási szabályzatokat hozhat létre és kezelhet a fájlokban, mappákban, adatbázisokban, táblázatokban és sorokban/oszlopokban tárolt adataihoz. A Hadoop-rendszergazda konfigurálhatja a szerepköralapú hozzáférés-vezérlést (RBAC), hogy az Apache Rangerben lévő beépülő modulokkal biztonságossá tegye a Apache Hive, a HBase, a Kafka és a Spark használatát.

A RBAC szabályzatok Apache Ranger használatával történő konfigurálása lehetővé teszi, hogy a szervezet egyik szerepkörével társítsa az engedélyeket. Ez az absztrakciós réteg megkönnyíti annak biztosítását, hogy a felhasználók csak a munkahelyi feladataik elvégzéséhez szükséges engedélyekkel rendelkezzenek.

- [Enterprise Security Package konfigurációk Azure Active Directory (Azure AD) tartományi szolgáltatásokkal a HDInsight-ben](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- [A vállalati biztonság áttekintése az Azure HDInsight](domain-joined/hdinsight-security-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: a bizalmas adatok tárolására vagy feldolgozására szolgáló Azure HDInsight-fürtök esetén a fürt és a kapcsolódó erőforrások bizalmasként való megjelölése címkék használatával. Az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el az Azure Storage vagy a számítási erőforrások számára. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutatás**: ha a Azure SQL Database használatával tárolja a Apache Hive és az Apache Oozie-metaadatokat, az SQL-adatok mindig titkosítva maradnak. Az Azure Storage-fiókok és a Data Lake Storage (Gen1 vagy Gen2) esetében ajánlott engedélyezni a Microsoft számára a titkosítási kulcsok kezelését, azonban lehetősége van a saját kulcsainak kezelésére.

- [Az Azure Storage-fiókok titkosítási kulcsainak kezelése](../storage/common/customer-managed-keys-configure-key-vault.md)

- [Azure Data Lake Storage létrehozása az ügyfél által felügyelt titkosítási kulcsok használatával](../data-lake-store/data-lake-store-get-started-portal.md)

- [A Azure SQL Database titkosításának megismerése](../azure-sql/database/sql-database-paas-overview.md#data-encryption)

- [A SQL Database transzparens adattitkosítás konfigurálása az ügyfél által felügyelt kulcsok használatával](../azure-sql/database/transparent-data-encryption-tde-overview.md?tabs=azure-portal)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: az Azure HDInsight-fürtökhöz társított Azure Storage-fiókok diagnosztikai beállításainak konfigurálása a fürt adatain végzett összes szifilisz-művelet figyeléséhez és naplózásához. Az Azure HDInsight-fürthöz társított összes Storage-fiók vagy Data Lake-tároló naplózásának engedélyezése.

- [További naplózás/naplózás engedélyezése egy Azure Storage-fiókhoz](../storage/common/manage-storage-analytics-logs.md)

- [További naplózás/naplózás engedélyezése Azure Data Lake Storage](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információért lásd az [Azure biztonsági teljesítményteszt: biztonsági rés kezelése](../security/benchmarks/security-control-vulnerability-management.md)című témakört.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: harmadik féltől származó sebezhetőségi felügyeleti megoldás implementálása.

Ha Rapid7, Qualys vagy bármely más sebezhetőség-kezelési platformra vonatkozó előfizetéssel rendelkezik, parancsfájl-műveletek használatával telepítheti az Azure HDInsight-fürtcsomópontokon a sebezhetőségi felmérési ügynököket, és a megfelelő portálon keresztül felügyelheti a csomópontokat.

- [A Rapid7-ügynök manuális telepítése](https://insightvm.help.rapid7.com/docs/install)

- [A Qualys-ügynök manuális telepítése](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

- [Parancsfájl-műveletek használata](hdinsight-hadoop-customize-cluster-linux.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: az automatikus rendszerfrissítések engedélyezve lettek a fürtcsomópontok rendszerképeihez, azonban a frissítések alkalmazásának biztosításához rendszeresen újra kell indítani a fürtcsomópontok szolgáltatást.

A Microsoft az alap Azure HDInsight-csomópontok rendszerképeinek karbantartásához és frissítéséhez.

- [Az operációsrendszer-javítási ütemterv konfigurálása a HDInsight-fürtökhöz](hdinsight-os-patching.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: a harmadik féltől származó szoftverek címeihez készült automatizált javítási megoldás üzembe helyezése

**Útmutató**: parancsfájl-műveletek vagy más mechanizmusok használata az Azure HDInsight-fürtök javításához. Az újonnan létrehozott fürtök mindig rendelkeznek a legújabb elérhető frissítésekkel, beleértve a legfrissebb biztonsági javításokat is.

- [A Linux-alapú Azure HDInsight-fürtök operációsrendszer-javítási ütemtervének konfigurálása](hdinsight-os-patching.md)

- [Parancsfájl-műveletek használata](hdinsight-hadoop-customize-cluster-linux.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: külső féltől származó biztonsági rések kezelésére szolgáló megoldás implementálása, amely lehetővé teszi a sebezhetőségi vizsgálatok időbeli összevetését. Ha Rapid7 vagy Qualys-előfizetéssel rendelkezik, használhatja a gyártó portálját a biztonsági rések vizsgálatának megtekintésére és összehasonlítására.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: általános kockázati pontozási programot (például gyakori sebezhetőségi pontozási rendszer) vagy a harmadik féltől származó ellenőrzési eszköz által biztosított alapértelmezett kockázati minősítéseket használjon.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az összes erőforrást (például számítási, tárolási, hálózati, portok és protokollok stb.), beleértve az Azure HDInsight-fürtöket is az előfizetéseken belül. Győződjön meg arról, hogy megfelelő (olvasási) engedélyekkel rendelkezik a bérlőben, és képes felsorolni az összes Azure-előfizetést, valamint az előfizetésében lévő erőforrásokat.

Bár a klasszikus Azure-erőforrások felderíthető az Azure Resource Graph használatával, erősen ajánlott Azure Resource Manager erőforrások létrehozása és használata.

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription)

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, így a metaadatok logikailag rendszerezve lesznek a besorolásban.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és az elkülönített előfizetések használata, ha szükséges, az eszközök rendszerezéséhez és nyomon követéséhez. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: a jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: a számítási erőforrásokhoz jóváhagyott Azure-erőforrások és jóváhagyott szoftverek listájának meghatározása

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Az Azure Resource Graph segítségével lekérdezheti és felderítheti az előfizetésében lévő erőforrásokat. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: harmadik féltől származó megoldás implementálása a fürt csomópontjainak a nem jóváhagyott szoftveralkalmazások számára történő figyeléséhez.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az összes erőforrást (például számítási, tárolási, hálózati, portok és protokollok stb.), beleértve az Azure HDInsight-fürtöket is az előfizetéseken belül.  Távolítsa el a felderített nem jóváhagyott Azure-erőforrásokat. Az Azure HDInsight-fürtcsomópontok esetében hozzon létre egy harmadik féltől származó megoldást a nem jóváhagyott szoftverek eltávolítására vagy riasztására.

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: az Azure HDInsight-fürtcsomópontok esetében hozzon létre egy harmadik féltől származó megoldást, amely megakadályozza a jogosulatlan szoftverek futtatását.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

További információkat az alábbi hivatkozásokon találhat:

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: a jóváhagyott szoftverek leltárának fenntartása

**Útmutató**: az Azure HDInsight-fürtcsomópontok esetében hozzon létre egy harmadik féltől származó megoldást, amely megakadályozza a nem engedélyezett fájltípusok végrehajtását.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók a Azure Resource Manager interakcióját a "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával.

- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: Azure Policy aliasok használata a "Microsoft. HDInsight" névtérben egyéni szabályzatok létrehozásához a HDInsight-fürt hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez.

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: a Microsoft által kezelt és karbantartott Azure HDInsight operációsrendszer-lemezképek. A fürtcsomópontok operációs rendszerének biztonságos konfigurációjának végrehajtásáért felelős ügyfél.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure HDInsight-fürtök és a kapcsolódó erőforrások biztonságos beállításainak érvénybe léptetéséhez használja a Azure Policy [deny] és a [telepítés ha nem létezik] lehetőséget.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: a Microsoft által kezelt és karbantartott Azure HDInsight operációsrendszer-lemezképek. Az operációs rendszer szintű állapot konfigurációjának végrehajtásáért felelős ügyfél.

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: ha egyéni Azure Policy-definíciókat használ, az Azure DevOps vagy az Azure Repos segítségével biztonságosan tárolhatja és kezelheti a kódot.

- [Az Azure Repos git oktatóanyaga](/azure/devops/repos/git/gitworkflow)

- [Az Azure Repos dokumentációja](/azure/devops/repos/index)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: Azure Policy aliasok használata a "Microsoft. HDInsight" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszer-konfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: konfigurációs felügyeleti eszközök telepítése operációs rendszerekhez

**Útmutató**: egy külső gyártótól származó megoldás implementálása a fürt csomópontjaihoz tartozó operációs rendszerek kívánt állapotának fenntartásához.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: Azure Policy aliasok használata a "Microsoft. HDInsight" névtérben egyéni szabályzatok létrehozásához a HDInsight-fürt konfigurációjának naplózásához vagy érvényesítéséhez.

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: külső gyártótól származó megoldás implementálása a fürtcsomópont operációs rendszerei állapotának figyelésére.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: az Azure HDInsight a Apache Kafka BRING Your Own Key (BYOK) támogatását tartalmazza. Ez a funkció lehetővé teszi az inaktív adatok titkosításához használt kulcsok megépítését és kezelését.

Az Azure HDInsight összes felügyelt lemeze Azure Storage Service Encryption (SSE) védelemmel van ellátva. Alapértelmezés szerint ezeknek a lemezeknek az adatai a Microsoft által felügyelt kulcsokkal vannak titkosítva. Ha engedélyezi a BYOK-t, az Azure HDInsight titkosítási kulcsát a Azure Key Vault használatával használhatja és felügyelheti.

A Key Vault az Azure HDInsight üzemelő példányokkal is használható a fürtözött tárolók (Azure Storage-fiókok és Azure Data Lake Storage) kulcsainak kezeléséhez

- [Saját kulcs használata az Azure HDInsight Apache Kafka](./disk-encryption.md)

- [Az Azure Storage-fiókok titkosítási kulcsainak kezelése](../storage/common/customer-managed-keys-configure-key-vault.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: a felügyelt identitások használhatók az Azure HDInsight, hogy a fürtök hozzáférhessenek Azure Active Directory (Azure ad) tartományi szolgáltatásokhoz, a hozzáférési Azure Key Vaultekhez vagy a fájlok eléréséhez Azure Data Lake Storage Gen2.

- [Felügyelt identitások megismerése az Azure HDInsight](hdinsight-managed-identities.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: Ha az Azure HDInsight-telepítéshez kapcsolódó kódokat használ, a hitelesítő adatoknak a kódban való azonosításához a hitelesítőadat-olvasót is megvalósíthatja. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz. 

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információt az [Azure biztonsági teljesítményteszt: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md)című témakörben talál.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: az Azure HDInsight előzetesen telepített és engedélyezett ClamScan tartalmaz, azonban felügyelni kell a szoftvereket, és manuálisan kell összesíteni/figyelnie a naplók ClamScan.

- [Az Azure HDInsight ClamScan ismertetése](./hdinsight-faq.md#security-and-certificates)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft antimalware engedélyezve van a mögöttes gazdagépen, amely támogatja az Azure-szolgáltatásokat, de nem az ügyfél tartalmán fut.

Az Azure HDInsight-fürt üzembe helyezéséhez (például Data Lake Storage, Blob Storage stb.) kapcsolódó Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata. A Microsoft nem fér hozzá az ügyféladatokat ezekben a példányokban.

- [Megismerheti a Microsoft antimalware-t az Azure Cloud Services és Virtual Machines](../security/fundamentals/antimalware.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: az Azure HDInsight előre telepített és engedélyezve van a ClamScan lemezképekhez. A ClamScan automatikusan végrehajtja a motor-és a definíciók frissítéseit, azonban a naplók összesítését és kezelését manuálisan kell elvégezni.

- [Az Azure Azure HDInsight ClamScan ismertetése](./hdinsight-faq.md#security-and-certificates)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: Ha Azure Storage-fiókot használ a HDInsight-fürt adattárához, válassza ki a megfelelő redundancia-beállítást (LRS, ZRS, GRS, ra-GRS).  Ha Azure SQL Database használ az Azure HDInsight-fürt adattárához, konfigurálja az aktív földrajzi replikálást.

- [Tárolási redundancia konfigurálása Azure Storage-fiókokhoz](../storage/common/storage-redundancy.md)

- [Redundancia konfigurálása Azure SQL Databasehoz](../azure-sql/database/active-geo-replication-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: Ha Azure Storage-fiókot használ az Azure HDInsight-fürt adattárához, válassza ki a megfelelő redundancia-beállítást (LRS, ZRS, GRS, ra-GRS). Ha az Azure HDInsight üzemelő példányának bármely részén Azure Key Vault használ, győződjön meg róla, hogy a kulcsok biztonsági mentése megtörténik.

- [Tárolási beállítások kiválasztása az Azure HDInsight-fürthöz](hdinsight-hadoop-compare-storage-options.md)

- [Tárolási redundancia konfigurálása Azure Storage-fiókokhoz](../storage/common/storage-redundancy.md)

- [Key Vault kulcsok biztonsági mentése az Azure-ban](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: Ha Azure Key Vault használja az Azure HDInsight üzemelő példányát, tesztelje az ügyfél által felügyelt kulcsok biztonsági másolatának visszaállítását.

- [Saját kulcs használata az Azure HDInsight Apache Kafka](./disk-encryption.md)

- [Key Vault-kulcsok visszaállítása az Azure-ban](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: Ha Azure Key Vault használja az Azure HDInsight üzemelő példányát, engedélyezze a helyreállítható törlést Key Vault a kulcsok véletlen vagy rosszindulatú Törlés elleni védelemmel való ellátásához.

- [A Soft delete Azure Key Vault engedélyezése](../key-vault/general/soft-delete-overview.md)

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

**Útmutató**: a Security Center súlyosságot rendel a riasztásokhoz, így könnyebben rangsorolhatja az egyes riasztásokban való részvétel sorrendjét, hogy az erőforrások biztonsága esetén azonnal elérhető legyen. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint a riasztást eredményező tevékenység mögötti rosszindulatú szándékkal.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és edzésprogramjának kidolgozásához](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte.

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

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](../security/benchmarks/overview.md) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)
