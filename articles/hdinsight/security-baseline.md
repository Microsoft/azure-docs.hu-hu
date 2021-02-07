---
title: A HDInsight Azure-os biztonsági alapkonfigurációja
description: A HDInsight Azure-os biztonsági alapkonfigurációja
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 5174c3bf2150c4671f1034eba6e661a10c222aec
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807682"
---
# <a name="azure-security-baseline-for-hdinsight"></a>A HDInsight Azure-os biztonsági alapkonfigurációja

A HDInsight Azure-beli biztonsági alapterve olyan javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági állapotát.

A szolgáltatás alapterve az [Azure Security Benchmark 1,0-es verziójából](../security/benchmarks/overview.md)származik, amely javaslatokat tesz arra vonatkozóan, hogy miként védheti meg felhőalapú megoldásait az Azure-ban az ajánlott eljárásokat ismertető útmutató segítségével.

További információ: [Azure Security](../security/benchmarks/security-baselines-overview.md)alapkonfigurációk áttekintése.

## <a name="network-security"></a>Hálózati biztonság

*További információkért lásd [: biztonsági ellenőrzés: hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: az erőforrások védelme hálózati biztonsági csoportokkal vagy Azure Firewall a Virtual Network

**Útmutató**: az Azure HDInsight-beli biztonsági rendszerek virtuális hálózatokon keresztül valósulnak meg. A vállalati rendszergazda létrehozhat egy fürtöt egy virtuális hálózaton belül, és hálózati biztonsági csoport (NSG) használatával korlátozhatja a virtuális hálózathoz való hozzáférést. Csak a bejövő hálózati biztonsági csoport szabályaiban engedélyezett IP-címek képesek kommunikálni az Azure HDInsight-fürttel. Ez a konfiguráció peremhálózati biztonságot nyújt. A virtuális hálózatban üzembe helyezett összes fürt egy privát végponttal is rendelkezik, amely a Virtual Network belső IP-címére lesz feloldva a fürt átjáróinak saját HTTP-eléréséhez.

A kiszűrése-on keresztüli adatvesztés kockázatának csökkentése érdekében az Azure Firewall használatával korlátozza az Azure HDInsight-fürtök kimenő hálózati forgalmát.

Azure-HDInsight üzembe helyezése Virtual Networkon belül és biztonságos hálózati biztonsági csoporttal: https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Az Azure HDInsight-fürtök kimenő forgalmának korlátozása Azure Firewall használatával: https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: az Azure HDInsight-fürt védelméhez használt virtuális hálózatra, alhálózatra és hálózati biztonsági csoportra vonatkozó hálózati védelmi javaslatok Azure Security Center használata és szervizelése. Engedélyezze a hálózati biztonsági csoport (NSG) folyamatának naplóit, és küldje el a naplókat egy Azure Storage-fiókba a forgalom naplózása érdekében. NSG-naplókat is küldhet egy Azure Log Analytics-munkaterületre, és az Azure Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. Az Azure Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati hibás konfigurációk meghatározását.

A NSG folyamat naplófájljainak engedélyezése:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Az Azure Traffic Analytics engedélyezése és használata:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

A Azure Security Center által biztosított hálózati biztonság ismertetése:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: nem alkalmazható; a teljesítményteszt az Azure apps szolgáltatáshoz vagy webalkalmazásokat üzemeltető számítási erőforrásokhoz készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: a DDOS-támadások elleni védelem érdekében engedélyezze az Azure DDoS standard Protectiont azon a virtuális hálózaton, amelyen az Azure-HDInsight üzembe lett helyezve. A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel.

A DDoS Protection konfigurálása:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Az Azure Security Center integrált veszélyforrások felderítésének ismertetése:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: hálózati csomagok és adatforgalmi naplók rögzítése

**Útmutató**: engedélyezze a hálózati biztonsági csoport (NSG) ostoroz naplóit az Azure HDInsight-fürt védelméhez használt alhálózathoz csatolt NSG. Jegyezze fel a NSG folyamat naplóit egy Azure Storage-fiókba a flow-rekordok létrehozásához. Ha a rendellenes tevékenységek kivizsgálásához szükséges, engedélyezze az Azure Network Watcher a csomagok rögzítését.

A NSG folyamat naplófájljainak engedélyezése:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

A Network Watcher engedélyezése:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati alapú behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: az Azure Security control id 1,1; követelménye teljesül. Üzembe helyezheti az Azure HDInsight-fürtöt egy virtuális hálózatban, és biztonságossá teheti a hálózati biztonsági csoporttal (NSG).

Az Azure-HDInsight több függősége is van, amelyek bejövő forgalmat igényelnek. A bejövő felügyeleti forgalmat nem lehet tűzfal-eszközön keresztül elküldeni. A szükséges felügyeleti forgalom forrás-címei ismertek és közzé vannak téve. Hozzon létre hálózati biztonsági csoportokra vonatkozó szabályokat ezekkel az adatokkal, hogy csak a megbízható helyek forgalmát engedélyezzék, így biztosítva a bejövő adatforgalom védelmét a fürtökön.

A kiszűrése-on keresztüli adatvesztés kockázatának csökkentése érdekében az Azure Firewall használatával korlátozza az Azure HDInsight-fürtök kimenő hálózati forgalmát.

HDInsight üzembe helyezése Virtual Networkon belül és biztonságos hálózati biztonsági csoporttal: https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

A HDInsight függőségeinek és a tűzfal használatának ismertetése: https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

HDInsight-felügyeleti IP-címek: https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: nem alkalmazható; a teljesítményteszt az Azure apps szolgáltatáshoz vagy webalkalmazásokat üzemeltető számítási erőforrásokhoz készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: virtuális hálózati szolgáltatás-címkék használata az Azure HDInsight-fürt üzembe helyezéséhez használt hálózati biztonsági csoportok (NSG) hálózati hozzáférés-vezérlésének definiálásához. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például ApiManagement), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

Az Azure HDInsight szolgáltatáshoz tartozó címkék megismerése és használata:

https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: szabványos biztonsági konfigurációk definiálása és implementálása az Azure HDInsight-fürthöz kapcsolódó hálózati erőforrásokhoz. Használjon Azure Policy aliasokat a "Microsoft. HDInsight" és a "Microsoft. Network" névterekben egyéni szabályzatok létrehozásához az Azure HDInsight-fürt hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez.

Az Azure-tervezetek segítségével leegyszerűsítheti a nagyméretű Azure-környezetek nagy léptékű üzembe helyezését, például a Azure Resource Manager sablonokat, az Azure RBAC-vezérlőket és a házirendeket egyetlen terv definíciójában. Egyszerűen alkalmazhatja a tervrajzot új előfizetésekre és környezetekre, és részletesen beállíthatja a vezérlést és a felügyeletet a verziószámozással.

Az elérhető Azure Policy aliasok megtekintése:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias Azure Policy konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Blueprint létrehozása:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: a hálózati biztonsági csoport (NSG) és az Azure HDInsight-fürthöz társított hálózati biztonsággal és adatforgalommal kapcsolatos egyéb erőforrásokhoz tartozó címkék használata. Az egyes NSG-szabályok esetében a "Leírás" mezővel adhatja meg az üzleti igényeket és/vagy időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatra irányuló adatforgalmat.

A címkézéshez kapcsolódó beépített Azure Policy definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.

A Azure PowerShell vagy az Azure parancssori felülete (CLI) használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon.

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Virtuális hálózat létrehozása:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

NSG létrehozása biztonsági konfigurációval:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-Tevékenységnaplók használatával figyelheti a hálózati erőforrás-konfigurációkat, és felderítheti az Azure HDInsight üzemelő példányokhoz kapcsolódó hálózati erőforrások változásait. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

Az Azure Activity log eseményeinek megtekintése és beolvasása:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Riasztások létrehozása a Azure Monitorban: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ [: Security Control: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: a Microsoft fenntartja az Azure HDInsight-fürt összetevőinek időforrásait, a számítási környezetek időszinkronizálását is frissítheti.

Az Azure számítási erőforrások időszinkronizálásának konfigurálása:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

Útmutató: az Azure HDInsight-fürt **Bevezetésével** Azure monitor a fürt által generált biztonsági adatokat összesítve. Az egyéni lekérdezések használatával észlelheti és reagálhat a környezetbeli fenyegetésekre. 

Azure HDInsight-fürt előkészítése Azure Monitorre:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Egyéni lekérdezések létrehozása Azure HDInsight-fürthöz:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: engedélyezze a Azure monitort a HDInsight-fürthöz, és irányítsa azt egy log Analytics-munkaterületre. Ez naplózza a kapcsolódó fürt adatait és az operációsrendszer-metrikákat az összes Azure HDInsight-fürt csomópontjaihoz.

A HDInsight-fürt naplózásának engedélyezése:

 https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

HDInsight-naplók lekérdezése:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: az Azure HDInsight-fürt előkészítése Azure monitor. Győződjön meg arról, hogy a használt Log Analytics munkaterületen a szervezet megfelelőségi előírásai szerint beállított napló-megőrzési időtartam szerepel.

Azure HDInsight-fürt előkészítése Azure Monitorre:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Log Analytics munkaterület megőrzési időtartamának konfigurálása:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: az Azure HDInsight-fürt előkészítése Azure monitor. Győződjön meg arról, hogy a használt Azure Log Analytics munkaterület rendelkezik a szervezet megfelelőségi előírásai szerint beállított napló-megőrzési időtartammal.

Azure HDInsight-fürt előkészítése Azure Monitorre:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Log Analytics munkaterület megőrzési időtartamának konfigurálása:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: azure-log Analytics munkaterület-lekérdezések használata az Azure HDInsight-naplók lekérdezéséhez:

Egyéni lekérdezések létrehozása az Azure HDInsight-fürtökhöz:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: riasztások engedélyezése rendellenes tevékenységhez

**Útmutató**: az Azure log Analytics munkaterületének használata az Azure HDInsight-fürttel kapcsolatos rendellenes tevékenységekre vonatkozó figyeléshez és riasztáshoz.

Riasztások kezelése Azure Security Centerban:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Riasztás a log Analytics-naplófájlok adatkezeléséről:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: az Azure HDInsight előzetesen telepített és engedélyezett ClamScan tartalmaz, azonban felügyelni kell a szoftvereket, és manuálisan kell összesíteni/figyelnie a naplók ClamScan.

A ClamScan ismertetése:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: harmadik féltől származó megoldás implementálása a DNS-naplózáshoz.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: a konzol naplózásának manuális konfigurálása a csomópontok alapján.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ [: Security Control (identitás és Access Control](../security/benchmarks/security-control-identity-access-control.md)).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: az Azure HDInsight-fürt kiépítés során létrehozott helyi rendszergazdai fiók, valamint a létrehozott egyéb fiókok nyilvántartásának fenntartása. Emellett, ha az Azure AD-integrációt használja, az Azure AD olyan beépített szerepkörökkel rendelkezik, amelyeket explicit módon kell hozzárendelni, és ezért lekérdezhető. Az Azure AD PowerShell-modul használatával ad hoc lekérdezéseket a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

Emellett Azure Security Center identitás-és hozzáférés-kezelési javaslatokat is használhat.

Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole

Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember

Identitás és hozzáférés figyelése Azure Security Centerekkel:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: fürt kiépítés esetén az Azure-ban új jelszavakat kell létrehoznia a webes portálhoz és a Secure Shell-(SSH-) hozzáféréshez. Nincsenek módosítható alapértelmezett jelszavak, azonban más jelszavakat is megadhat az SSH és a webes portál eléréséhez.

Jelszavak beállítása Azure HDInsight-fürt kiépítés esetén:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: az Azure HDInsight-fürt hitelesítésének integrálása Azure Active Directory használatával. Szabályzatokat és eljárásokat hozhat létre a dedikált rendszergazdai fiókok használata körül.

Emellett Azure Security Center identitás-és hozzáférés-kezelési javaslatokat is használhat.

Az Azure HDInsight-hitelesítés integrálása Azure Active Directory használatával:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Identitás és hozzáférés figyelése Azure Security Centerekkel:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: az Azure HDInsight-ügynök használata az Enterprise Security Package (ESP) fürtökbe való bejelentkezéshez multi-Factor Authentication használatával, jelszavak megadása nélkül. Ha már bejelentkezett más Azure-szolgáltatásokba, például a Azure Portalba, bejelentkezhet az Azure HDInsight-fürtbe egyszeri bejelentkezéses (SSO) felhasználói élményben.

Az Azure HDInsight ID Broker engedélyezése:

https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker#enable-hdinsight-id-broker

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: az Azure ad MFA engedélyezése és a Azure Security Center identitás-és hozzáférés-kezelési javaslatok követése. Az Enterprise Security Package konfigurált Azure HDInsight-fürtök csatlakozni tudnak egy tartományhoz, így a tartományi felhasználók a fürt hitelesítő adataikkal hitelesíthetők, és big data feladatokat futtathatnak. Ha a többtényezős hitelesítés (MFA) engedélyezve van, a rendszer a felhasználókat a második hitelesítési tényező megadására fogja felvenni.

Az MFA engedélyezése az Azure-ban:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Identitás és hozzáférés figyelése Azure Security Centeron belül:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: az Azure HDInsight-fürtök és a kapcsolódó erőforrások bevezetésére és konfigurálására konfigurált többtényezős hitelesítés (MFA) használata a mancsok (emelt szintű hozzáférési munkaállomások) használatával.

További tudnivalók az emelt szintű hozzáférésű munkaállomásokról:

https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

Az MFA engedélyezése az Azure-ban:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: az Azure HDInsight-fürtöket a konfigurált Enterprise Security Package lehet csatlakoztatni egy tartományhoz, hogy a tartományi felhasználók a hitelesítéshez a tartományi hitelesítő adataikat használják. A naplók és a riasztások generálásához Azure Active Directory (HRE) biztonsági jelentéseket használhat, ha a HRE-környezetben gyanús vagy nem biztonságos tevékenység történik. A Azure Security Center használatával figyelheti az identitás-és hozzáférési tevékenységeket.

A kockázatos tevékenységre megjelölt HRE-felhasználók azonosítása:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

A felhasználók identitás-és hozzáférési tevékenységének figyelése Azure Security Centerban:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: az Azure HDInsight-fürtöket a konfigurált Enterprise Security Package lehet csatlakoztatni egy tartományhoz, hogy a tartományi felhasználók a hitelesítéshez a tartományi hitelesítő adataikat használják. A feltételes hozzáférés elnevezett helyeivel engedélyezheti a hozzáférést az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz.

Elnevezett helyszínek konfigurálása az Azure-ban:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (HRE) használata központi hitelesítési és engedélyezési rendszerrel. A HRE erős titkosítással védi az adatok védelmét a nyugalmi és átviteli állapotban lévő adatokhoz. A HRE a felhasználó hitelesítő adatait is sók, kivonatok és biztonságosan tárolja.

A konfigurált Enterprise Security Package (ESP) rendszerű Azure HDInsight-fürtök csatlakozni tudnak egy tartományhoz, így a tartományi felhasználók a fürthöz való hitelesítéshez használhatják a tartományi hitelesítő adataikat.

HRE-példány létrehozása és konfigurálása:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Enterprise Security Package konfigurálása a Azure Active Directory Domain Services Azure HDInsight-ben:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: Azure Active Directory (HRE) hitelesítés használata az Azure HDInsight-fürttel. A HRE olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználó hozzáférését rendszeresen felül lehet vizsgálni, hogy csak a megfelelő felhasználók férhessenek hozzájuk. 

Az Azure Identity hozzáférési felülvizsgálatok használata:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: az inaktivált fiókok elérésére irányuló kísérletek figyelése

**Útmutató**: a deaktivált fiókok elérésére irányuló kísérletek figyeléséhez használja a Azure Active Directory (HRE) bejelentkezést és a naplókat. Ezek a naplók bármely harmadik féltől származó SIEM/monitoring eszközbe integrálhatók.

Ezt a folyamatot leegyszerűsítheti a HRE felhasználói fiókjainak diagnosztikai beállításainak létrehozásával, a naplók és a bejelentkezési naplók Azure Log Analytics-munkaterületre való elküldésével. A kívánt riasztások konfigurálása az Azure Log Analytics-munkaterületen belül.

Azure-beli tevékenység-naplók integrálása a Azure Monitorba:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a konfigurált Enterprise Security Package (ESP)-vel rendelkező Azure HDInsight-fürtök csatlakozni tudnak egy tartományhoz, így a tartományi felhasználók a fürthöz való hitelesítéshez használhatják a tartományi hitelesítő adataikat.  A Azure Active Directory (HRE) kockázati Észlelésekkel és az Identity Protection szolgáltatással konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. Emellett további vizsgálatot is betöltheti az Azure Sentinelbe.

HRE kockázatos bejelentkezések megtekintése:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: nem érhető el; Ügyfélszéf az Azure HDInsight még nem támogatott.

Ügyfélszéf támogatott szolgáltatások listája: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információkért lásd [: biztonsági ellenőrzés:](../security/benchmarks/security-control-data-protection.md)adatvédelem.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: az Azure HDInsight üzemelő példányokhoz kapcsolódó erőforrásokon használható címkék használata a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követéséhez.

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. Az Azure HDInsight-fürtöket és a kapcsolódó Storage-fiókokat a megfelelő címkével elválasztva, a hálózati biztonsági csoporton (NSG) vagy Azure Firewallon belül kell megadni. A fürt adatainak biztonságos Azure Storage-fiókban vagy Azure Data Lake Storageban (Gen1 vagy Gen2) belül kell szerepelniük.

Adja meg az Azure HDInsight-fürt tárolási beállításait:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

A Azure Data Lake Storage biztonságossá tétele:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Azure Storage-fiókok biztonságossá tétele:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: a bizalmas adatok tárolására vagy feldolgozására szolgáló Azure HDInsight-fürtök esetén a fürt és a kapcsolódó erőforrások bizalmasként való megjelölése címkék használatával. A kiszűrése-on keresztüli adatvesztés kockázatának csökkentése érdekében az Azure Firewall használatával korlátozza az Azure HDInsight-fürtök kimenő hálózati forgalmát.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

Az Azure HDInsight-fürtök kimenő forgalmának korlátozása Azure Firewall használatával:

https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

Az ügyfelek adatvédelem az Azure-ban:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: az összes bizalmas adat titkosítása az átvitel során. Győződjön meg arról, hogy az Azure HDInsight-fürthöz vagy a fürt adattárához (Azure Storage-fiókokhoz vagy Azure Data Lake Storage Gen1/Gen2) csatlakozó ügyfelek képesek a TLS 1,2 vagy újabb egyeztetésére. A Microsoft Azure-erőforrások alapértelmezés szerint a TLS 1,2-et fogják egyeztetni. 

Az Azure Data Lake Storage-titkosítás ismertetése az átvitel során:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Az Azure Storage-fiók titkosításának megismerése az átvitel során:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el az Azure Storage vagy a számítási erőforrások számára. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

Az ügyfelek adatvédelem az Azure-ban:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC

**Útmutató**: az Azure HDInsight Enterprise Security Package (ESP) segítségével az Apache Ranger segítségével részletes hozzáférés-vezérlési és adattitkosítási szabályzatokat hozhat létre és kezelhet a fájlokban, mappákban, adatbázisokban, táblázatokban és sorokban/oszlopokban tárolt adataihoz. A Hadoop-rendszergazda konfigurálhatja a szerepköralapú hozzáférés-vezérlést (RBAC), hogy az Apache Rangerben lévő beépülő modulokkal biztonságossá tegye a Apache Hive, a HBase, a Kafka és a Spark használatát.

A RBAC szabályzatok Apache Ranger használatával történő konfigurálása lehetővé teszi, hogy a szervezet egyik szerepkörével társítsa az engedélyeket. Ez az absztrakciós réteg megkönnyíti annak biztosítását, hogy a felhasználók csak a munkahelyi feladataik elvégzéséhez szükséges engedélyekkel rendelkezzenek.

Enterprise Security Package konfigurációk a HDInsight Azure Active Directory Domain Servicesával:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

A vállalati biztonság áttekintése az Azure HDInsight-ben:

https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview



**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: a bizalmas adatok tárolására vagy feldolgozására szolgáló Azure HDInsight-fürtök esetén a fürt és a kapcsolódó erőforrások bizalmasként való megjelölése címkék használatával. Az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el az Azure Storage vagy a számítási erőforrások számára. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

Az ügyfelek adatvédelem az Azure-ban:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutatás**: ha a Azure SQL Database használatával tárolja a Apache Hive és az Apache Oozie-metaadatokat, az SQL-adatok mindig titkosítva maradnak. Az Azure Storage-fiókok és a Data Lake Storage (Gen1 vagy Gen2) esetében ajánlott engedélyezni a Microsoft számára a titkosítási kulcsok kezelését, azonban lehetősége van a saját kulcsainak kezelésére.

Az Azure Storage-fiókok titkosítási kulcsainak kezelése:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

Azure Data Lake Storage létrehozása az ügyfél által felügyelt titkosítási kulcsok használatával:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal

A Azure SQL Database titkosításának megismerése:

https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview#data-encryption

A SQL Database transzparens adattitkosítás konfigurálása az ügyfél által felügyelt kulcsok használatával:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: az Azure HDInsight-fürtökhöz társított Azure Storage-fiókok diagnosztikai beállításainak konfigurálása a fürt adatain végzett összes szifilisz-művelet figyeléséhez és naplózásához. Az Azure HDInsight-fürthöz társított összes Storage-fiók vagy Data Lake-tároló naplózásának engedélyezése.

További naplózás/naplózás engedélyezése egy Azure Storage-fiókhoz:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

További naplózási/naplózási Azure Data Lake Storage engedélyezése:

https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ [: Security Control: sebezhetőségi kezelés](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: harmadik féltől származó sebezhetőségi felügyeleti megoldás implementálása.

Ha Rapid7, Qualys vagy bármely más sebezhetőség-kezelési platformra vonatkozó előfizetéssel rendelkezik, parancsfájl-műveletek használatával telepítheti az Azure HDInsight-fürtcsomópontokon a sebezhetőségi felmérési ügynököket, és a megfelelő portálon keresztül felügyelheti a csomópontokat.

A Rapid7-ügynök manuális telepítése:

https://insightvm.help.rapid7.com/docs/install

A Qualys-ügynök manuális telepítése:

https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf

Parancsfájl-műveletek használata:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: az automatikus rendszerfrissítések engedélyezve lettek a fürtcsomópontok rendszerképeihez, azonban a frissítések alkalmazásának biztosításához rendszeresen újra kell indítani a fürtcsomópontok szolgáltatást.

A Microsoft az alap Azure HDInsight-csomópontok rendszerképeinek karbantartásához és frissítéséhez.

Az operációsrendszer-javítási ütemterv konfigurálása a HDInsight-fürtökhöz:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: a harmadik féltől származó szoftveres javításokat kezelő megoldás telepítése

**Útmutató**: parancsfájl-műveletek vagy más mechanizmusok használata az Azure HDInsight-fürtök javításához. Az újonnan létrehozott fürtök mindig rendelkeznek a legújabb elérhető frissítésekkel, beleértve a legfrissebb biztonsági javításokat is.

A Linux-alapú Azure HDInsight-fürtök operációsrendszer-javítási ütemtervének konfigurálása:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

Parancsfájl-műveletek használata:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: külső féltől származó biztonsági rések kezelésére szolgáló megoldás implementálása, amely lehetővé teszi a sebezhetőségi vizsgálatok időbeli összevetését. Ha Rapid7 vagy Qualys-előfizetéssel rendelkezik, használhatja a gyártó portálját a biztonsági rések vizsgálatának megtekintésére és összehasonlítására.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: általános kockázati pontozási programot (például gyakori sebezhetőségi pontozási rendszer) vagy a harmadik féltől származó ellenőrzési eszköz által biztosított alapértelmezett kockázati minősítéseket használjon.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információkért lásd [: biztonsági vezérlés: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: az Azure Asset Discovery használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az összes erőforrást (például a számítási, tárolási, hálózati, portok és protokollok stb.), beleértve az Azure HDInsight-fürtöket is az előfizetésében.  Győződjön meg arról, hogy megfelelő (olvasási) engedélyekkel rendelkezik a bérlőben, és képes felsorolni az összes Azure-előfizetést, valamint az előfizetésében lévő erőforrásokat.

Bár a klasszikus Azure-erőforrások felderítése az erőforrás-gráfon keresztül lehetséges, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

Lekérdezések létrehozása az Azure Resource Graph használatával:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Az Azure-előfizetések megtekintése:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription

Az Azure RBAC ismertetése:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, így a metaadatok logikailag rendszerezve lesznek a besorolásban.

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és az elkülönített előfizetések használata, ha szükséges, az eszközök rendszerezéséhez és nyomon követéséhez. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

További Azure-előfizetések létrehozása:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Management Groups létrehozása:

https://docs.microsoft.com/azure/governance/management-groups/create

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: a jóváhagyott Azure-erőforrások és-szoftverek leltárának fenntartása

**Útmutató**: a számítási erőforrásokhoz jóváhagyott Azure-erőforrások és jóváhagyott szoftverek listájának meghatározása

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetése (ke) n belüli erőforrásait. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Lekérdezések létrehozása az Azure Graph használatával: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: harmadik féltől származó megoldás implementálása a fürt csomópontjainak a nem jóváhagyott szoftveralkalmazások számára történő figyeléséhez.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az összes erőforrást (például a számítási, tárolási, hálózati, portok és protokollok stb.), beleértve az Azure HDInsight-fürtöket is az előfizetésében.  Távolítsa el a felderített nem jóváhagyott Azure-erőforrásokat. Az Azure HDInsight-fürtcsomópontok esetében hozzon létre egy harmadik féltől származó megoldást a nem jóváhagyott szoftverek eltávolítására vagy riasztására.

Lekérdezések létrehozása az Azure Graph használatával:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: az Azure HDInsight-fürtcsomópontok esetében hozzon létre egy harmadik féltől származó megoldást, amely megakadályozza a jogosulatlan szoftverek futtatását.


**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Adott erőforrástípus megtagadása a következővel: Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="610-implement-approved-application-list"></a>6,10: jóváhagyott alkalmazások listájának implementálása

**Útmutató**: az Azure HDInsight-fürtcsomópontok esetében hozzon létre egy harmadik féltől származó megoldást, amely megakadályozza a nem engedélyezett fájltípusok végrehajtását.


**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: a felhasználók az Azure Resources Managerrel való interakcióra való képességének korlátozása parancsfájlok használatával

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók a Azure Resource Manager interakcióját a "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával.

A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez nem alkalmazható az Azure-HDInsight, mert a fürt felhasználóinak (nem rendszergazdáknak) nem kell hozzáférést adni az egyes csomópontokhoz a feladatok futtatásához. A fürt rendszergazdája rendszergazdai hozzáféréssel rendelkezik az összes fürtcsomóponton.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: nem alkalmazható

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: nem alkalmazható; a teljesítményteszt az Azure apps szolgáltatáshoz vagy webalkalmazásokat üzemeltető számítási erőforrásokhoz készült.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: nem alkalmazható

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információkért lásd [: biztonság-vezérlés: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: Azure Policy aliasok használata a "Microsoft. HDInsight" névtérben egyéni szabályzatok létrehozásához a HDInsight-fürt hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez.

Az elérhető Azure Policy aliasok megtekintése:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias

Azure Policy konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: a Microsoft által kezelt és karbantartott Azure HDInsight operációsrendszer-lemezképek. A fürtcsomópontok operációs rendszerének biztonságos konfigurációjának végrehajtásáért felelős ügyfél. 


**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure HDInsight-fürtök és a kapcsolódó erőforrások biztonságos beállításainak érvénybe léptetéséhez használja a Azure Policy [deny] és a [telepítés ha nem létezik] lehetőséget.

Azure Policy konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy effektusok ismertetése:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: a Microsoft által kezelt és karbantartott Azure HDInsight operációsrendszer-lemezképek. Az operációs rendszer szintű állapot konfigurációjának végrehajtásáért felelős ügyfél.


**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: ha egyéni Azure Policy-definíciókat használ, az Azure DevOps vagy az Azure Repos segítségével biztonságosan tárolhatja és kezelheti a kódot.

[Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

[Az Azure Repos dokumentációja](/azure/devops/repos/index?view=azure-devops&preserve-view=true)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: nem alkalmazható; Az Azure HDInsight nem alkalmazható Egyéni rendszerképek.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: nem alkalmazható

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: rendszerkonfiguráció-felügyeleti eszközök telepítése

**Útmutató**: Azure Policy aliasok használata a "Microsoft. HDInsight" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszer-konfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

Azure Policy konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: rendszerkonfiguráció-felügyeleti eszközök központi telepítése operációs rendszerekhez

**Útmutató**: egy külső gyártótól származó megoldás implementálása a fürt csomópontjaihoz tartozó operációs rendszerek kívánt állapotának fenntartásához.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: az Azure-szolgáltatások automatizált konfigurációs figyelésének megvalósítása

**Útmutató**: Azure Policy aliasok használata a "Microsoft. HDInsight" névtérben egyéni szabályzatok létrehozásához a HDInsight-fürt konfigurációjának naplózásához vagy érvényesítéséhez.

[Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias)

[Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: külső gyártótól származó megoldás implementálása a fürtcsomópont operációs rendszerei állapotának figyelésére.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: az Azure HDInsight a Apache Kafka BRING Your Own Key (BYOK) támogatását tartalmazza. Ez a funkció lehetővé teszi az inaktív adatok titkosításához használt kulcsok megépítését és kezelését.

Az Azure HDInsight összes felügyelt lemeze Azure Storage Service Encryption (SSE) védelemmel van ellátva. Alapértelmezés szerint ezeknek a lemezeknek az adatai a Microsoft által felügyelt kulcsokkal vannak titkosítva. Ha engedélyezi a BYOK-t, az Azure HDInsight titkosítási kulcsát a Azure Key Vault használatával használhatja és felügyelheti.

A Key Vault az Azure HDInsight üzemelő példányokkal is használható a fürtözött tárolók (Azure Storage-fiókok és Azure Data Lake Storage) kulcsainak kezeléséhez

Saját kulcs használata az Azure HDInsight Apache Kafkaához:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Az Azure Storage-fiókok titkosítási kulcsainak kezelése:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: a felügyelt identitások használhatók az Azure HDInsight, hogy lehetővé tegyék a fürtök számára a Azure Active Directory tartományi szolgáltatások elérését, a Azure Key Vault elérését vagy a fájlok elérését Azure Data Lake Storage Gen2.

Felügyelt identitások megismerése az Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: Ha az Azure HDInsight-telepítéshez kapcsolódó kódokat használ, a hitelesítő adatoknak a kódban való azonosításához a hitelesítőadat-olvasót is megvalósíthatja. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz. 

A hitelesítőadat-olvasó beállítása:

https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információkért lásd [: biztonsági ellenőrzés: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: az Azure HDInsight előzetesen telepített és engedélyezett ClamScan tartalmaz, azonban felügyelni kell a szoftvereket, és manuálisan kell összesíteni/figyelnie a naplók ClamScan.

Az Azure HDInsight ClamScan megismerése:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft antimalware engedélyezve van a mögöttes gazdagépen, amely támogatja az Azure-szolgáltatásokat, de nem az ügyfél tartalmán fut.

Az Azure HDInsight-fürt üzembe helyezéséhez (például Data Lake Storage, Blob Storage stb.) kapcsolódó Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata. A Microsoft nem fér hozzá az ügyféladatokat ezekben a példányokban.

Ismerje meg a Microsoft antimalware az Azure Cloud Services és Virtual Machines:

 https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: az Azure HDInsight előre telepített és engedélyezve van a ClamScan lemezképekhez. A ClamScan automatikusan végrehajtja a motor-és a definíciók frissítéseit, azonban a naplók összesítését és kezelését manuálisan kell elvégezni.

Az Azure Azure HDInsight ClamScan ismertetése:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="data-recovery"></a>Adat-helyreállítás

*További információkért lásd [: biztonsági ellenőrzés: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: Ha Azure Storage-fiókot használ a HDInsight-fürt adattárához, válassza ki a megfelelő redundancia-beállítást (LRS, ZRS, GRS, ra-GRS).  Ha Azure SQL Database használ az Azure HDInsight-fürt adattárához, konfigurálja az aktív földrajzi replikálást.

Tárolási redundancia konfigurálása az Azure Storage-fiókokhoz:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Redundancia konfigurálása Azure SQL Database esetén:

https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és biztonsági másolat készítése bármely ügyfél által felügyelt kulcsról

**Útmutató**: Ha Azure Storage-fiókot használ az Azure HDInsight-fürt adattárához, válassza ki a megfelelő redundancia-beállítást (LRS, ZRS, GRS, ra-GRS). Ha az Azure HDInsight üzemelő példányának bármely részén Azure Key Vault használ, győződjön meg róla, hogy a kulcsok biztonsági mentése megtörténik.

Adja meg az Azure HDInsight-fürt tárolási beállításait:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

Tárolási redundancia konfigurálása az Azure Storage-fiókokhoz:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Key Vault kulcsok biztonsági mentése az Azure-ban:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: Ha Azure Key Vault használja az Azure HDInsight üzemelő példányát, tesztelje a biztonsági másolatok biztonsági mentését.

Saját kulcs használata az Azure HDInsight Apache Kafkaához:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Key Vault-kulcsok visszaállítása az Azure-ban:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: Ha Azure Key Vault az Azure HDInsight üzemelő példányához van használatban, engedélyezze a Soft-Delete a Key Vault a kulcsok véletlen vagy rosszindulatú Törlés elleni megvédéséhez.

Soft-Delete engedélyezése a Azure Key Vaultban:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ [: Security Control: incidens válasza](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Ellenőrizze, hogy vannak-e írásos incidensekre vonatkozó válaszok, amelyek meghatározzák a személyzet szerepköreit, valamint az incidensek kezelésének és kezelésének fázisait.

Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel a riasztásokhoz, így könnyebben rangsorolhatja az egyes riasztásokban való részvétel sorrendjét, hogy az erőforrások biztonsága esetén azonnal elérhető legyen. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és a réseket, és szükség szerint módosítsa a tervet. Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és gyakorlatának megtervezéséhez:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte.

A Azure Security Center biztonsági kapcsolattartó beállítása:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és-javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

Folyamatos exportálás konfigurálása:

https://docs.microsoft.com/azure/security-center/continuous-export

Riasztások továbbítása az Azure Sentinelbe:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a válaszokat a "Logic apps" használatával a biztonsági riasztások és javaslatok esetében.

A Munkafolyamat-automatizálás és a Logic Apps konfigurálása:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért lásd [: biztonsági ellenőrzés: behatolási tesztek és Red Team-gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: rendszeres penetrációs tesztelést végez az Azure-erőforrásokon, és gondoskodik az összes kritikus biztonsági vizsgálat 60 napon belüli szervizeléséről

**Útmutató**: kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék meg a Microsoft-házirendeket:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

A Microsoft által felügyelt felhő-infrastruktúrával,-szolgáltatásokkal és-alkalmazásokkal kapcsolatos további információkért tekintse meg a következő témakört: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure biztonsági teljesítménytesztét](../security/benchmarks/overview.md)
- További információ az [Azure biztonsági Alaptervekről](../security/benchmarks/security-baselines-overview.md)