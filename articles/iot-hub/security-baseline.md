---
title: Azure biztonsági alapkonfiguráció az Azure IoT Hub
description: Az Azure IoT Hub biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: iot-hub
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 2947faa8c7b56fdbe4cf549529b63d506b810750
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570867"
---
# <a name="azure-security-baseline-for-azure-iot-hub"></a>Azure biztonsági alapkonfiguráció az Azure IoT Hub

A Microsoft Azure IoT Hub Azure biztonsági alapterve olyan javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági állapotát. A szolgáltatás alapterve az [Azure Security Benchmark 1,0-es verziójából](../security/benchmarks/overview.md)származik, amely javaslatokat tesz arra vonatkozóan, hogy miként védheti meg felhőalapú megoldásait az Azure-ban az ajánlott eljárásokat ismertető útmutató segítségével. További információ: [Azure Security](../security/benchmarks/security-baselines-overview.md)alapkonfigurációk áttekintése.

## <a name="network-security"></a>Hálózati biztonság

*További információt az [Azure biztonsági teljesítményteszt: hálózati biztonság](../security/benchmarks/security-control-network-security.md)című témakörben talál.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: alapértelmezés szerint a IoT hub állomásneve egy nyilvános végpontra mutat, amely az interneten keresztül nyilvánosan irányítható IP-címmel rendelkezik. A különböző ügyfelek megoszthatják ezt a IoT Hub nyilvános végpontot, és IoT a nagy területű hálózatokon és a helyszíni hálózatokon található eszközöket.

IoT Hub funkciók, például az üzenetek útválasztása, a fájlok feltöltése és a tömeges eszközök importálása/exportálása a nyilvános végponton keresztül a IoT Hub kapcsolatát is igényli a felhasználó által birtokolt Azure-erőforráshoz. Ezek a csatlakozási útvonalak együttesen alkotják a kimenő forgalmat IoT Hubról az ügyfelek erőforrásaira.

Ajánlott az Azure-erőforrásokhoz (beleértve az Azure IoT Hub-hoz) való kapcsolódás korlátozását egy olyan virtuális hálózaton keresztül, amelyet Ön birtokol, és amely a kapcsolati expozíció csökkentése egy elkülönített hálózatban, és a helyszíni hálózati kapcsolat közvetlen engedélyezése az Azure-beli gerinc hálózat számára. Az Azure Private link és az Azure Private-végpontja, ahol lehetséges, lehetővé teszi a szolgáltatásokhoz való privát hozzáférést más virtuális hálózatokból.

A nemkívánatos hozzáférés elkerülése érdekében tartsa meg az eszközökön a hardveres portokat a lehető legkevesebben. Emellett az eszköz fizikai illetéktelen módosításának megakadályozására vagy észlelésére szolgáló mechanizmusokat is létrehozhat.

- [IoT virtuális hálózatok támogatása](virtual-network-support.md)
- [a loT hálózatkezelésének ajánlott gyakorlata](../iot-fundamentals/security-recommendations.md?context=azure%2fiot-hub%2frc%2frc#networking)
- [Az Azure Private link áttekintése](../private-link/private-link-overview.md)
- [Azure-beli hálózati biztonsági csoport](../virtual-network/network-security-groups-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: a Azure Security Center használata és a hálózatvédelmi javaslatok követése az Azure-hálózati erőforrások biztonságossá tételéhez. Engedélyezze a hálózati biztonsági csoport adatfolyam-naplóit, és küldje el a naplókat egy Azure Storage-fiókba a naplózáshoz. A flow-naplókat Log Analytics munkaterületre is elküldheti, majd a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalmi mintákba. A Traffic Analytics néhány előnye, hogy képes megjeleníteni a hálózati tevékenységeket, azonosíthatja a gyakori helyeket és a biztonsági fenyegetéseket, megismerheti a forgalmi folyamatok mintáit, és meghatározhatja a hálózati helytelen konfigurációkat.
 
- [Hálózati biztonsági csoport folyamatábráinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)
 
- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: blokkolja az ismert kártékony IP-címeket IoT hub IP-szűrési szabályokkal. A rosszindulatú kísérleteket a rendszer a IoT Azure Security Center használatával is rögzíti és figyelmezteti.

Az alapszintű Azure DDoS Protection már engedélyezve van, és a IoT Hub részeként nem vehető igénybe további díj. A forgalom folyamatos monitorozása és a gyakori hálózati szintű támadások valós idejű enyhítése biztosítja a Microsoft online szolgáltatások által használt védelmi adatokat. Az Azure globális hálózatának teljes skálája felhasználható a különböző régiók közötti támadási forgalom elosztására és enyhítésére.

- [IoT Hub IP-szűrő](iot-hub-ip-filtering.md)

- [Azure Security Center IoT gyanús IP-címekkel való kommunikációhoz](../defender-for-iot/concept-security-alerts.md)

- [Alapszintű Azure DDoS Protection kezelése](../ddos-protection/ddos-protection-overview.md)

- [Fenyegetésvédelem az Azure Security Centerben](../security-center/azure-defender.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: nem alkalmazható; Ez a javaslat olyan ajánlatok számára készült, amelyek olyan hálózati csomagokat hoznak létre, amelyeket az ügyfelek rögzíthetnek és megtekinthetnek. A IoT Hub nem hoz létre az ügyfelek felé irányuló hálózati csomagokat, és nem úgy tervezték, hogy közvetlenül az Azure Virtual Networks szolgáltatásba telepítse őket.

**Azure Security Center figyelés**: nem

**Felelősség**: nem alkalmazható

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: válasszon ki egy olyan ajánlatot az Azure piactéren, amely támogatja az azonosítók/IP-címek funkciót a hasznos adatok vizsgálatával.  Ha a hasznos adatok ellenőrzése nem követelmény, Azure Firewall a veszélyforrások felderítése is használható. Azure Firewall veszélyforrások felderítésére szolgáló szűrés a riasztások és/vagy az ismert kártékony IP-címek és tartományok felé irányuló, illetve azokból való adatforgalom letiltására szolgál. Az IP-címek és -tartományok forrása a Microsoft veszélyforrás-felderítési hírcsatornája.

Az Ön által választott tűzfal-megoldás üzembe helyezésével azonosíthatja és/vagy blokkolhatja a kártékony forgalmat. 

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

- [Riasztások konfigurálása Azure Firewall](../firewall/threat-intel.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: az Azure-IoT hub elérését igénylő erőforrások esetében Virtual Network szolgáltatás-címkék használatával határozhatja meg a hálózati biztonsági csoportokon vagy a Azure Firewallokon a hálózati hozzáférés-vezérlést. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatási címke nevének (például AzureIoTHub) megadásával a szabály megfelelő forrás vagy cél mezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

- [Szolgáltatás-címkék használata az Azure IoT](iot-hub-understand-ip-address.md)
- [További információ a szolgáltatási címkék használatáról](../virtual-network/service-tags-overview.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: az Azure IoT hub névterekhez társított hálózati erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. Devices" és a "Microsoft. Network" névterekben, hogy egyéni szabályzatokat hozzon létre a Machine Learning névterek hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: az Azure IoT hub üzembe helyezéséhez társított hálózati erőforrásokhoz használjon címkéket, hogy logikailag szervezze őket egy besorolásba.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata a hálózati erőforrás-konfigurációk figyelésére és az Azure IoT hub-hoz kapcsolódó hálózati erőforrások változásainak észlelésére. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitorban](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: a Microsoft fenntartja az Azure-erőforrásokhoz, például az azure-IoT hubhoz használt időforrást a naplók időbélyegzői esetében.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: naplók beolvasása Azure monitor segítségével az Azure-IoT hub által generált biztonsági adatokat összesítve. Azure Monitor a Log Analytics-munkaterületek használatával kérdezheti le és végezheti el az elemzéseket, és használhat tárolási fiókokat a hosszú távú/archiválási tároláshoz. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó biztonsági incidensek és események felügyeletét (SIEM).

- [Azure IoT-naplók beállítása](monitor-iot-hub-reference.md#resource-logs)
- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: az Azure IoT diagnosztikai beállításainak engedélyezése az Azure-erőforrásokon a naplózási, biztonsági és erőforrás-naplókhoz való hozzáféréshez. A automatikusan elérhető tevékenység-naplók közé tartozik az eseményforrás, a dátum, a felhasználó, az időbélyeg, a forráscím, a célcím és más hasznos elemek.

- [Azure IoT Hub-naplók beállítása](monitor-iot-hub-reference.md#resource-logs)

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [A naplózás és a különböző naplózási típusok megismerése az Azure-ban](../azure-monitor/essentials/platform-logs-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: a Azure monitor a szervezet megfelelőségi előírásai alapján állítsa be az Azure-IoT hub példányaihoz társított log Analytics-munkaterületek naplózásának megőrzési időtartamát.

- [Napló-megőrzési paraméterek beállítása](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: elemezze és figyelje a rendellenes viselkedésre vonatkozó naplókat, és rendszeresen tekintse át az Azure-IoT hub eredményeit. A naplók áttekintéséhez és a naplózási adatok lekérdezéséhez használja a Azure Monitor és egy Log Analytics munkaterületet.

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et. 

- [Az Azure IoT állapotának figyelése](monitor-iot-hub.md)
- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)
  
- [Log Analytics lekérdezések első lépései](../azure-monitor/logs/log-analytics-tutorial.md)
   
- [ Egyéni lekérdezések végrehajtása a Azure Monitorban](../azure-monitor/logs/get-started-queries.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: a IoT Azure Security Center használata a biztonsági naplókban és eseményekben található rendellenes tevékenységek figyelésére és riasztására log Analytics munkaterülettel. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel szolgáltatásban való ellátását. Olyan Azure Monitor is meghatározhat működési riasztásokat, amelyek biztonsági következményekkel járhatnak, például ha a forgalom váratlanul csökken.

- [Az Azure IoT Hub állapotának figyelése](monitor-iot-hub.md)
- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)
- [IoT-riasztások Azure Security Center](../defender-for-iot/concept-security-alerts.md)

- [Riasztás a log Analytics-naplófájlok adatkezeléséről](../azure-monitor/alerts/tutorial-response.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: nem alkalmazható; Az Azure IoT Hub nem dolgoz fel kártevő szoftverrel kapcsolatos naplókat, illetve nem hoz létre.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: nem alkalmazható; Az Azure IoT Hub a DNS-sel kapcsolatos naplókat nem dolgozza fel és nem hozza létre.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: identitás-és hozzáférés-vezérlés](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) lehetővé teszi az Azure IoT hub elérésének kezelését a szerepkör-hozzárendeléseken keresztül. Ezeket a szerepköröket hozzárendelheti a felhasználókhoz, a csoportok egyszerű szolgáltatásaihoz és a felügyelt identitásokhoz. Bizonyos erőforrásokhoz előre definiált beépített szerepkörök tartoznak, és ezeket a szerepköröket leltározott vagy lekérdezheti az olyan eszközökkel, mint például az Azure CLI vagy a Azure PowerShell, vagy a Azure Portal. 

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: az Azure IoT hub-erőforrásokhoz való hozzáférés-kezelést Azure Active Directory (Azure ad) szabályozza. Az Azure AD nem rendelkezik az alapértelmezett jelszavak fogalmával.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül.

Azure AD Privileged Identity Management és Azure Resource Manager használatával is engedélyezheti az igény szerinti hozzáférést a rendszergazdai fiókokhoz.

- [További információ a Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: a IoT hubhoz hozzáférő felhasználók Azure Active Directory SSO-t használják. Azure Security Center identitás és hozzáférési javaslatok használata. 

- [Az egyszeri bejelentkezés ismertetése az Azure AD-vel](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: engedélyezze az Azure ad MFA-t az általános Azure-bérlők számára az összes szolgáltatás igénybevétele érdekében. IoT Hub szolgáltatás nem rendelkezik MFA-támogatással.  

- [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: emelt szintű jogosultságokat igénylő felügyeleti feladatokhoz használjon biztonságos, Azure által felügyelt munkaállomás (más néven privilegizált hozzáférési munkaállomás vagy Paw) használatát.

- [A biztonságos, Azure által felügyelt munkaállomások ismertetése](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Az Azure AD MFA engedélyezése](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: Azure Active Directory biztonsági jelentések és figyelés használata a környezetben előforduló gyanús vagy nem biztonságos tevékenységek észlelésére. A Azure Security Center használatával figyelheti az identitás-és hozzáférési tevékenységeket.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md)
- [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: csak a jóváhagyott helyekről származó Azure-erőforrások kezelése

**Útmutató**: IoT hubhoz hozzáférő felhasználók esetén a feltételes hozzáférés nem támogatott. Ennek enyhítése érdekében az Azure AD nevesített helyeivel csak a teljes Azure-bérlőhöz tartozó IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz férhet hozzá, így az összes szolgáltatás, többek között a IoT Hub is elérhetővé válik. 

- [Az Azure AD nevesített helyeinek konfigurálása](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: IoT hubhoz való felhasználói hozzáféréshez használja a Azure Active Directory (Azure ad) központi hitelesítési és engedélyezési rendszerként. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

Eszköz-és szolgáltatás-hozzáférés esetén a IoT Hub biztonsági jogkivonatokat és közös hozzáférésű aláírási (SAS-) jogkivonatokat használ az eszközök és szolgáltatások hitelesítéséhez, hogy elkerülje a kulcsok küldését a hálózaton. 

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)
- [Biztonsági jogkivonatok IoT Hub](../iot-fundamentals/iot-security-deployment.md#iot-hub-security-tokens)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: az Azure ad olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure AD identitás-és hozzáférési felülvizsgálatok segítségével hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá. 

A naplók és a riasztások generálásához használjon Azure AD Privileged Identity Management (PIM), ha a környezetben gyanús vagy nem biztonságos tevékenység történik.

- [Az Azure AD jelentéskészítés ismertetése](../active-directory/reports-monitoring/index.yml)
- [Az Azure AD identitás- és hozzáférési felülvizsgálatainak használata](../active-directory/governance/access-reviews-overview.md)
- [Azure AD Privileged Identity Management (PIM) üzembe helyezése](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: hozzáférhet az Azure ad bejelentkezési tevékenységekhez, a naplózáshoz és a kockázatos Eseménynapló-forrásokhoz, amelyek lehetővé teszik bármely Siem/monitoring eszköz integrálását. 

Ezt a folyamatot leegyszerűsítheti, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókjaihoz, és elküldi a naplókat és a bejelentkezési naplókat egy Log Analytics munkaterületre. Log Analytics munkaterületen belül konfigurálhatja a kívánt riasztásokat. 

A felhasználó Azure Monitor erőforrás-naplókat a kapcsolatok kategóriában a jogosulatlan kapcsolódási kísérletek figyelésére.

- [Azure-beli Tevékenységnaplók integrálása a Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Az IoT hub erőforrás-naplófájljainak konfigurálása](monitor-iot-hub.md#collection-and-routing)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a Azure ad Identity Protection funkcióinak használatával konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. További vizsgálat céljából az Azure Sentinelbe is betöltheti az adatmennyiséget.
  
- [ Az Azure AD kockázatos bejelentkezések megtekintése](../active-directory/identity-protection/overview-identity-protection.md)
  
- [ Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)
  
- [ Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: a támogatási forgatókönyvekben, ahol a Microsoftnak hozzá kell férnie az ügyféladatok eléréséhez, közvetlenül az ügyféltől kérhető. 

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információt az [Azure biztonsági teljesítményteszt: Adatvédelem](../security/benchmarks/security-control-data-protection.md)című témakörben talál.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.
 
- [ Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: az elkülönítés megvalósítása különálló előfizetések és felügyeleti csoportok használatával az egyes biztonsági tartományokhoz, például a környezeti típusokhoz és az adatérzékeny szintekhez. Korlátozhatja az alkalmazásaihoz és a vállalati környezetekhez igénybe veheti az Azure-erőforrásokhoz való hozzáférés szintjét. Az Azure-erőforrásokhoz való hozzáférést az Azure RBAC segítségével szabályozhatja.
  
- [ További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)
- [ Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)
- [ Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: külső féltől származó megoldás használata az Azure Marketplace-en a hálózati kerületekben a bizalmas információk jogosulatlan átvitelének figyelése és az ilyen átvitelek blokkolása az adatbiztonsági szakemberek értesítése mellett.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és védelmet nyújt az ügyfelek adatvesztése és a kitettség ellen. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: a IoT hub TRANSPORT Layer Security (TLS) protokollt használ a IoT-eszközök és-szolgáltatások közötti kapcsolatok biztonságossá tételéhez. A TLS protokoll három verziója jelenleg támogatott, azaz a 1,0, 1,1 és 1,2 verziókat. Javasoljuk, hogy a TLS 1,2-et használja elsődleges TLS-verzióként IoT Hubhoz való csatlakozáskor.

Kövesse Azure Security Center a inaktív adatok titkosítására és az átvitel közbeni titkosításra vonatkozó ajánlásokat, ahol lehetséges.

- [TLS-támogatás a IoT Hub](iot-hub-tls-support.md)
- [A titkosítás ismertetése az Azure-ban](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el az Azure IoT hub számára. Harmadik féltől származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.

A Microsoft által felügyelt mögöttes Azure platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésének és a kitettségének védelme érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés kezelése az Azure RBAC

**Útmutató**: a IoT hubhoz való felhasználói hozzáférés vezérléséhez használja az Azure RBAC a hozzáférés szabályozásához. Az adatsíkok IoT Hubhoz való hozzáféréséhez használja a IoT Hubhoz tartozó megosztott hozzáférési házirendeket.

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

- [IoT Hub-hozzáférés szabályozása](iot-hub-devguide-security.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

A Microsoft felügyeli az Azure IoT Hub mögöttes infrastruktúráját, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a IoT hub támogatja az inaktív adatok titkosítását az ügyfél által felügyelt kulcsokkal (CMK), más néven a "saját kulcs használata" (BYOK).

Az Azure IoT Hub inaktív és átvitel alatt álló adatok titkosítását teszi lehetővé az adatközpontokban, és a szolgáltatáshoz való hozzáféréskor visszafejti azt. Alapértelmezés szerint a IoT Hub a Microsoft által felügyelt kulcsokat használja az adatok titkosításához a nyugalmi állapotban.

- [Inaktív adatok titkosítása az ügyfél által felügyelt kulcsokkal IoT Hub](iot-hub-customer-managed-keys.md)

- [Inaktív adatok Azure-ban való titkosításának ismertetése](../security/fundamentals/encryption-atrest.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használatával riasztásokat hozhat létre, amelyekkel az Azure IoT hub és más kritikus vagy kapcsolódó erőforrások éles példányain végezheti el a módosításokat.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információért lásd az [Azure biztonsági teljesítményteszt: biztonsági rés kezelése](../security/benchmarks/security-control-vulnerability-management.md)című témakört.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: nem alkalmazható; A Microsoft a biztonsági rések kezelését az Azure IoT Hubt támogató mögöttes rendszereken végzi.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: nem alkalmazható; A Microsoft az Azure IoT Hubt támogató mögöttes rendszereken végzi a javítások kezelését. 

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Microsoft

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: automatikus javítási megoldás üzembe helyezése harmadik féltől származó szoftverek címeihez

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

## <a name="inventory-and-asset-management"></a>Leltár- és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkék alkalmazása az Azure-erőforrásokra (nem minden erőforrás támogatja a címkéket, de a legtöbb esetben), hogy logikailag szervezze őket egy besorolásba.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és az elkülönített előfizetések használata, ha szükséges, az eszközök rendszerezéséhez és nyomon követéséhez. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.
  
- [ További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)
  
- [ Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)
  
- [ Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: a számítási erőforrásokhoz jóváhagyott Azure-erőforrások és jóváhagyott szoftverek leltárának létrehozása a szervezeti igényeknek megfelelően.

Minden IoT Hub rendelkezik egy azonosító beállításjegyzékgel, amely a szolgáltatásban eszközönkénti erőforrások létrehozására használható. Az eszközök hozzáférésének teljes körű szabályozása lehetővé teszi, hogy egy engedélyezési listához vagy egy adott csoporthoz tartozó identitásokat lehessen hozzáadni.

- [IoT Hub Identity Registry](iot-hub-devguide-identity-registry.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az előfizetésekben létrehozható erőforrásokra vonatkozóan. 

Az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetésükön belüli erőforrásokat.  Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva. 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

* Nem engedélyezett erőforrástípusok
* Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetéseken belüli erőforrásokat.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)
- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: a jóváhagyott szoftverek leltárának fenntartása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure ad feltételes hozzáférés használatával korlátozhatja a felhasználók interakcióját a Azure Resource Manager az "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával.
  
- [ A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokban

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: az Azure IOT hub szolgáltatás szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. A "Microsoft. Devices" névtérben Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre az Azure IoT Hub-szolgáltatások konfigurációjának naplózásához vagy érvényesítéséhez.

A Azure Resource Manager lehetővé teszi a sablon exportálását JavaScript Object Notation (JSON), amelyet át kell tekinteni annak érdekében, hogy a konfigurációk megfeleljenek a szervezete biztonsági követelményeinek.

A Azure Security Center javaslatai az Azure-erőforrások biztonságos alapkonfigurációja is használhatók.

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Oktatóanyag: szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében](../governance/policy/tutorials/create-and-manage.md)

- [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)

- [Biztonsági javaslatok – gyorsútmutató](../security-center/recommendations-reference.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure-erőforrások biztonságos beállításainak betartatásához használja a Azure Policy [deny] és a [telepítés ha nem létezik] lehetőséget. Emellett Azure Resource Manager-sablonokkal is megőrizheti a szervezete által igényelt Azure-erőforrások biztonsági konfigurációját.  
 
- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)
- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../governance/policy/tutorials/create-and-manage.md)
- [Azure Resource Manager sablonok áttekintése](../azure-resource-manager/templates/overview.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: Ha az azure-IoT hub vagy a kapcsolódó erőforrásokhoz egyéni Azure Policy-definíciókat használ, az Azure Repos segítségével biztonságosan tárolhatja és kezelheti a kódot.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)
- [Az Azure Repos dokumentációja](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: a "Microsoft. Devices" névtérben található Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)
- [Aliasok használata](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: konfigurációs felügyeleti eszközök telepítése operációs rendszerekhez

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: a Azure Security Center használata az Azure-erőforrások alapkonfigurációjának vizsgálatához. Emellett az Azure-erőforrások konfigurációjának riasztására és naplózására Azure Policy is használhatja. 
 
- [ Javaslatok szervizelése Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: a IoT hub biztonsági jogkivonatokat és közös hozzáférésű aláírási (SAS) jogkivonatokat használ az eszközök és szolgáltatások hitelesítéséhez, hogy elkerülje a kulcsok küldését a hálózaton. 

A felügyelt identitásokat a Azure Key Vaultekkel együtt használva egyszerűsítheti a Felhőbeli alkalmazások titkos kezelését.

- [Biztonsági jogkivonatok IoT Hub](../iot-fundamentals/iot-security-deployment.md#iot-hub-security-tokens)
- [Felügyelt identitások használata IoT Hubhoz](virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

- [Kulcstartó létrehozása](../key-vault/general/quick-create-portal.md)
- [Key Vault hitelesítés biztosítása felügyelt identitással](../key-vault/general/assign-access-policy-portal.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: a IoT hub biztonsági jogkivonatokat és közös hozzáférésű aláírási (SAS) jogkivonatokat használ az eszközök és szolgáltatások hitelesítéséhez, hogy elkerülje a kulcsok küldését a hálózaton. 

Felügyelt identitások használatával biztosíthatja az Azure-szolgáltatások automatikus felügyelt identitását az Azure AD-ben. A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

- [Biztonsági jogkivonatok IoT Hub](../iot-fundamentals/iot-security-deployment.md#iot-hub-security-tokens)
- [Felügyelt identitások konfigurálása a IoT Hubhoz](virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz. 
 
- [  A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információt az [Azure biztonsági teljesítményteszt: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md)című témakörben talál.*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: központilag felügyelt kártevő szoftver használata

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

A Microsoft kártevő szoftveres verziója engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure App Service), azonban az nem az ügyfél tartalmán fut.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Microsoft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft kártevő szoftverrel való ellátása engedélyezve van az Azure-szolgáltatásokat (például Azure IoT hub) támogató mögöttes gazdagépen, azonban az nem az ügyfél tartalmán fut.

Az Ön felelőssége, hogy előzetesen beszkennelje a nem számítási Azure-erőforrásokra feltöltött tartalmakat. A Microsoft nem fér hozzá az ügyféladatok eléréséhez, így az Ön nevében nem végezhet kártevő-ellenőrzéseket az ügyfél-tartalmakon.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: nem alkalmazható; Ez a teljesítményteszt számítási erőforrások számára készült. A Microsoft antimalware engedélyezve van a mögöttes gazdagépen, amely támogatja az Azure-szolgáltatásokat, de nem fut az ügyfél tartalmán.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Microsoft

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: az Azure IoT hub Service olyan módszereket és keretrendszert biztosít, amelyekkel a IoT hub szolgáltatások elérhetővé válnak, és a katasztrófák a meghatározott üzleti célkitűzések alapján állíthatók helyre. 

- [IoT Hub magas rendelkezésre állása és vészhelyreállítása](iot-hub-ha-dr.md)

- [A IoT Hub klónozása](iot-hub-how-to-clone.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: az Azure IoT hub javasolja, hogy a másodlagos IoT hub minden olyan eszköz-identitást tartalmazzon, amely képes csatlakozni a megoldáshoz. A megoldásnak meg kell őriznie az eszköz-identitások földrajzilag replikált biztonsági mentését, és fel kell töltenie őket a másodlagos IoT hubhoz, mielőtt az aktív végpontot átváltja az eszközökre. A IoT Hub eszköz-identitás exportálási funkciója hasznos ebben a környezetben.

- [IoT Hub magas rendelkezésre állása és vészhelyreállítása](iot-hub-ha-dr.md#achieve-cross-region-ha)

- [IoT Hub eszköz identitásának exportálása](iot-hub-bulk-identity-mgmt.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: az Azure IoT hub javasolja, hogy a másodlagos IoT hub minden olyan eszköz-identitást tartalmazzon, amely képes csatlakozni a megoldáshoz. A megoldásnak meg kell őriznie az eszköz-identitások földrajzilag replikált biztonsági mentését, és fel kell töltenie őket a másodlagos IoT hubhoz, mielőtt az aktív végpontot átváltja az eszközökre. A IoT Hub eszköz-identitás exportálási funkciója hasznos ebben a környezetben.

A tartalom biztonsági mentésének rendszeres időközönkénti helyreállítása. Győződjön meg arról, hogy visszaállíthatja az ügyfél által felügyelt kulcsok biztonsági mentését.

- [IoT Hub magas rendelkezésre állása és vészhelyreállítása](iot-hub-ha-dr.md#achieve-cross-region-ha)

- [IoT Hub eszköz identitásának exportálása](iot-hub-bulk-identity-mgmt.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: a kulcsok véletlen vagy rosszindulatú törléssel szembeni védelme érdekében engedélyezze a Key Vault a védelem törlését és kiürítését. Ha az Azure Storage-t használja a biztonsági másolatok tárolásához, a Soft delete lehetővé teszi az adatok mentését és helyreállítását a Blobok vagy blob-Pillanatképek törlésekor.
 
 
- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)
- [Soft DELETE az Azure Blob Storage-hoz](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információt az [Azure biztonsági teljesítményteszt: incidens válasza](../security/benchmarks/security-control-incident-response.md)című témakörben talál.*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: incidensek kifejlesztése útmutató a szervezet számára. Győződjön meg arról, hogy van olyan írásos incidens-válasz, amely meghatározza a személyzet összes szerepkörét, valamint az incidensek kezelésének és kezelésének fázisait az incidensek vizsgálatát követően. 
  
- [ Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
  
- [ Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
 
- [  A NIST számítógépes biztonsági incidensek kezelésével kapcsolatos útmutató a saját incidens-válasz tervének létrehozásához](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Azure Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy a megbízhatósági Security Center hol van a megállapításban, illetve a riasztás kibocsátására használt analitikai módszer, valamint a riasztást eredményező tevékenység mögött rosszindulatú szándékkal rendelkező megbízhatósági szint.

  
 Emellett megadhatja a címkéket használó előfizetéseket, és létrehozhat egy elnevezési rendszert az Azure-erőforrások azonosításához és kategorizálásához, különösen a bizalmas adatok feldolgozásához. Az Ön felelőssége, hogy rangsorolja a riasztások szervizelését az Azure-erőforrások és-környezet kritikus jellemzői alapján, ahol az incidens történt.
  
- [ Biztonsági riasztások Azure Security Center](../security-center/security-center-alerts-overview.md)
  
- [ Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: az Azure-erőforrások védelmének biztosítása érdekében a rendszer az incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatokat hajt végre. Azonosítsa a gyenge pontokat és a hézagokat, majd szükség szerint módosítsa a választ.
  
- [ A NIST kiadványa – útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és gyakorlatához](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után.
  
- [ A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és javaslatok exportálása a folyamatos exportálás funkcióval az Azure-erőforrásokkal kapcsolatos kockázatok azonosítása érdekében. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.
  
- [ Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)
 
- [ Riasztások továbbítása az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a munkafolyamat-automatizálási szolgáltatás használata Azure Security Center a biztonsági riasztásokra és az Azure-erőforrások védelmére vonatkozó ajánlásokra adott válaszok automatikus elindítására.
  
- [ A Munkafolyamat-automatizálás konfigurálása a Security Centerban](../security-center/workflow-automation.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért tekintse meg az [Azure biztonsági teljesítményteszt: behatolási tesztek és a Red Team gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)című témakört.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft Cloud penetráció tesztelési szabályait, amelyekkel biztosíthatja, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét.

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Megosztott

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure biztonsági teljesítménytesztét](../security/benchmarks/overview.md)
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)