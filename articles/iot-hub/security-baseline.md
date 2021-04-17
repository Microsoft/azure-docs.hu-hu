---
title: Az Azure biztonsági alapkonfigurációja Azure IoT Hub
description: A Azure IoT Hub alapkonfigurációja eljárásokkal kapcsolatos útmutatást és forrásforrásokat biztosít az Azure biztonsági teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához.
author: msmbaldwin
ms.service: iot-hub
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 99243dbfac7fddb8a4fe9d64ed64ab706245ec3c
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587629"
---
# <a name="azure-security-baseline-for-azure-iot-hub"></a>Az Azure biztonsági alapkonfigurációja Azure IoT Hub

Ez a biztonsági alapkonfiguráció az Azure biztonsági teljesítményteszt [1.0-s](../security/benchmarks/overview-v1.md) verziójának útmutatását alkalmazza a Microsoft Azure IoT Hub. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalmat az Azure  biztonsági teljesítményteszt által meghatározott biztonsági vezérlők és a kapcsolódó, az azure-beli biztonsági Azure IoT Hub. **A** Azure IoT Hub nem alkalmazható vezérlők ki vannak zárva.

 
Ha meg Azure IoT Hub az Azure-biztonsági teljesítményteszt teljes leképezését, tekintse meg a teljes Azure IoT Hub alapkonfiguráció [leképezési fájlját.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-erőforrások védelme virtuális hálózatokon belül

**Útmutató:** IoT Hub egy több-bérlős szolgáltatásként nyújtott platform (PaaS), a különböző ügyfelek ugyanazt a számítási, hálózati és tárolási hardvererőforrás-készletet osztják meg. IoT Hub gazdagépnevei egy nyilvános végpontra vannak leképezve, amely nyilvánosan átirányítható IP-címmel van meg az interneten. A különböző ügyfelek ezt IoT Hub nyilvános végpontot, és a nagy kiterületű hálózatokon található IoT-eszközök és a helyszíni hálózatok mind hozzáférhetnek. A Microsoft úgy tervezte meg a szolgáltatást, hogy az teljes elkülönítést biztosítson az egyes bérlők adatai között, és folyamatosan dolgozik az eredmény biztosítása érdekében.

IoT Hub szolgáltatások, például az üzenetek útválasztása, a fájlfeltöltés és az eszközök tömeges importálása/exportálása szintén megköveteli a IoT Hub és egy ügyfél tulajdonában lévő Azure-erőforrás közötti kapcsolatot a nyilvános végponton keresztül. Ezek a kapcsolódási útvonalak együttesen teszik ki a bejövő forgalmat a IoT Hub az ügyfélerőforrások felé.

Javasolja, hogy korlátozza az Azure-erőforrásokhoz (beleértve a Azure IoT Hub-t) egy saját virtuális hálózaton keresztül, hogy csökkentse a kapcsolati kitettséget egy elkülönített hálózatban, és engedélyezze a helyszíni hálózati kapcsolatot közvetlenül az Azure gerinchálózatához. A Azure Private Link azure-beli privát végpont használatával, ahol ez lehetséges, lehetővé teszi a szolgáltatások privát hozzáférését más virtuális hálózatokról. 

A privát hozzáférés létrejötte után tiltsa le a nyilvános hálózati IoT Hub a biztonság érdekében. Ez a hálózati szintű vezérlés kényszerítve van egy adott IoT Hub-erőforráson, ami biztosítja az elkülönítést. Annak érdekében, hogy a szolgáltatás más ügyfélerőforrások számára is aktív maradjon a nyilvános elérési út használatával, a nyilvános végpont feloldható marad, az IP-címek felderíthetők, és a portok nyitva maradnak. Ez nem okoz problémát, mivel a Microsoft több biztonsági réteget integrál a bérlők közötti teljes elkülönítés biztosítása érdekében.

A nemkívánatos hozzáférés elkerülése érdekében minimálisan tartsa meg az eszközein a nyitott hardverportokat. Emellett építsen ki olyan mechanizmusokat, amelyek megakadályozzák vagy észlelik az eszköz fizikai illetéktelen módosítását.

- [IoT virtuális hálózatok támogatása](virtual-network-support.md)

- [Az IoT Hub nyilvános hálózati hozzáférésének kezelése](iot-hub-public-network-access.md)

- [Bérlők elkülönítése az Azure-ban](https://docs.microsoft.com/azure/security/fundamentals/isolation-choices#tenant-level-isolation)

- [Ajánlott hálózatépítési gyakorlat](https://docs.microsoft.com/azure/iot-fundamentals/security-recommendations#networking)

- [Azure Private Link áttekintés](../private-link/private-link-overview.md)

- [Azure hálózati biztonsági csoport](../virtual-network/network-security-groups-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Virtuális hálózatok, alhálózatok és hálózati hálózatok konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** A Azure Security Center kövesse a hálózatvédelmi javaslatokat az Azure-beli hálózati erőforrások biztonságának biztosítása érdekében. Engedélyezze a hálózati biztonsági csoport forgalomnaplóit, és küldje el a naplókat egy Azure Storage-fióknak naplózásra. A forgalmi naplókat elküldheti egy Log Analytics-munkaterületre is, majd a Traffic Analytics segítségével betekintést nyerhet az Azure-felhő forgalmi mintáiba. Az adatforgalmi Traffic Analytics a hálózati tevékenységek vizualizációja, a gyors helyek és a biztonsági fenyegetések azonosítása, a forgalmi minták azonosítása és a hálózati konfigurációk észlelése.
 
- [Hálózati biztonsági csoport forgalomnaplóinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics](../network-watcher/traffic-analytics.md)
 
- [A hálózati biztonság Azure Security Center](../security-center/security-center-network-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="13-protect-critical-web-applications"></a>1.3: Kritikus fontosságú webalkalmazások védelme

**Útmutató:** Nem alkalmazható; Ez a javaslat a számítási erőforrásokon futó webalkalmazások Azure App Service szánt.

**Felelősség:** Nem alkalmazható

**Azure Security Center:** Nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert kártékony IP-címekkel való kommunikáció megtagadása

**Útmutató:** Ismert kártékony IP-címek blokkolása IoT Hub IP-szűrési szabályokkal. A rosszindulatú kísérleteket az IoT-Azure Security Center is rögzítik, és riasztást küldenek.

Azure DDoS Protection Alapszintű szolgáltatás már engedélyezve van, és további költségek nélkül elérhető a IoT Hub. A folyamatos forgalomfigyelés és a gyakori hálózati szintű támadások valós idejű mérséklése ugyanazt a védelmet biztosítja, mint amelyet a Microsoft online szolgáltatások. Az Azure globális hálózatának teljes skálája felhasználható a támadások forgalmának régiók közötti elosztására és csökkentésére.

- [IoT Hub IP-szűrő](iot-hub-ip-filtering.md)

- [Azure Security Center IoT-gyanús IP-címek kommunikációja](/azure/asc-for-iot/concept-security-alerts)

- [Alapszintű Azure DDoS Protection kezelése](../ddos-protection/ddos-protection-overview.md)

- [Fenyegetésvédelem az Azure Security Centerben](../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="15-record-network-packets"></a>1.5: Hálózati csomagok rekordja

**Útmutató:** Nem alkalmazható; Ez a javaslat olyan ajánlatokhoz ajánlott, amelyek az ügyfelek által rögzíthet és megtekinthető hálózati csomagokat hoznak létre. IoT Hub nem hoz létre az ügyfelek számára elérhető hálózati csomagokat, és nem úgy tervezték, hogy közvetlenül az Azure-beli virtuális hálózatokon üzembe helyezhetők.

**Felelősség:** Nem alkalmazható

**Azure Security Center monitorozás:** Nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Hálózatalapú behatolásészlelési/behatolásvédelmi rendszerek (IDS/IPS) üzembe helyezése

**Útmutató:** Válasszon ki egy olyan ajánlatot a Azure Marketplace amely támogatja az IDS/IPS funkciót és a hasznos vizsgálatot.  Ha a hasznos információk vizsgálata nem követelmény, Azure Firewall fenyegetési intelligencia használható. Azure Firewall veszélyforrás-felderítésen alapuló szűrés az ismert kártékony IP-címek és tartományok be- és/vagy letiltásán alapul. Az IP-címek és -tartományok forrása a Microsoft veszélyforrás-felderítési hírcsatornája.

Telepítse a választott tűzfalmegoldást a szervezet minden hálózati határán a rosszindulatú forgalom észlelése és/vagy blokkolása érdekében. 

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [A virtuális gép Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Riasztások konfigurálása a Azure Firewall](../firewall/threat-intel.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Webalkalmazások forgalmának kezelése

**Útmutató:** Nem alkalmazható; Ez a javaslat a számítási erőforrásokon futó webalkalmazások Azure App Service ajánlott.

**Felelősség:** Nem alkalmazható

**Azure Security Center monitorozás:** Nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** A hálózati biztonsági csoportok vagy Azure IoT Hub hozzáféréssel Virtual Network erőforrásokhoz Virtual Network szolgáltatáscímkék használatával határozhatja meg a hálózati hozzáférés-vezérlés Azure Firewall t. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha megadja a szolgáltatáscímke nevét (például AzureIoTHub) egy szabály megfelelő forrás- vagy célmezőben, engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkét a címek változásával.

- [Szolgáltatáscímkék használata az Azure IoT-hez](iot-hub-understand-ip-address.md)
- [További információ a szolgáltatáscímkék használatával kapcsolatban](../virtual-network/service-tags-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációjának fenntartása

**Útmutató:** Szabványos biztonsági konfigurációkat határozhat meg és valósíthat meg a Azure IoT Hub-névterekkel társított hálózati erőforrásokhoz a Azure Policy. A Azure Policy a "Microsoft.Devices" és a "Microsoft.Network" névterek aliasai segítségével egyéni szabályzatokat hozhat létre a hálózati konfiguráció naplózásához Machine Learning kényszerítéséhez. 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutató:** Az üzembe helyezéshez társított hálózati erőforrások címkéinek Azure IoT Hub, hogy logikailag rendszerezze őket egy rendszerezésbe.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrások konfigurációjának figyelése és a változások észlelése érdekében

**Útmutató:** Az Azure-tevékenységnaplóval monitorozható a hálózati erőforrások konfigurációja, és észlelhetők a hálózati erőforrások változásai a Azure IoT Hub. Hozzon létre riasztásokat a Azure Monitor, amelyek akkor aktiválódnak, ha a kritikus hálózati erőforrások módosulnak.

- [Az Azure-tevékenységnapló eseményeinek megtekintése és lekérése](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Azure biztonsági teljesítményteszt: Naplózás és monitorozás.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Központi biztonsági naplókezelés konfigurálása

**Útmutató:** Naplók Azure Monitor a Azure IoT Hub által létrehozott biztonsági adatok Azure IoT Hub. A Azure Monitor Log Analytics-munkaterületek használatával végezhet lekérdezést és elemzést, valamint tárfiókokat használhat a hosszú távú/archiválási tárterülethez. Másik lehetőségként engedélyezheti és bevetheti az adatokat egy Azure Sentinel egy külső biztonsági incidens- és eseménykezelés (SIEM) számára.

- [Azure IoT-naplók beállítása](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub-reference#resource-logs)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure-erőforrások naplózásának engedélyezése

**Útmutató:** Az Azure IoT diagnosztikai beállításainak engedélyezése az Azure-erőforrásokon a naplózási, biztonsági és erőforrásnaplókhoz való hozzáféréshez. Az automatikusan elérhető tevékenységnaplók közé tartozik az eseményforrás, a dátum, a felhasználó, az időbélyeg, a forráscímek, a célcímek és egyéb hasznos elemek.

- [A naplók Azure IoT Hub beállítása](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub-reference#resource-logs)

- [Platformnaplók és -metrikák gyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [A naplózás és a különböző naplótípusok az Azure-ban](../azure-monitor/essentials/platform-logs-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Devices:**

[!INCLUDE [Resource Policy for Microsoft.Devices 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.devices-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Biztonsági naplók gyűjtése operációs rendszerekről

**Útmutató:** Nem alkalmazható; Ez a javaslat számítási erőforrásokhoz ajánlott.

**Felelősség:** Nem alkalmazható

**Azure Security Center monitorozás:** Nincs

### <a name="25-configure-security-log-storage-retention"></a>2.5: A biztonsági naplók tárolásának megőrzésének konfigurálása

**Útmutató:** A Azure Monitor a vállalat megfelelőségi szabályzatainak megfelelően állítsa be a naplómegőrzési megőrzési időszakot a Azure IoT Hub-példányokhoz társított Log Analytics-munkaterületeken.

- [Naplómegőrzési paraméterek beállítása](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és áttekintése

**Útmutató:** Elemezze és figyelje a rendellenes viselkedést a naplókban, és rendszeresen tekintse át a naplók Azure IoT Hub. A Azure Monitor és egy Log Analytics-munkaterület használatával áttekinthet naplókat, és lekérdezéseket hajthatja végre a naplóadatokon.

Másik lehetőségként engedélyezheti és be is használhatja az adatokat Azure Sentinel külső SIEM-hez. 

- [Az Azure IoT állapotának figyelése](monitor-iot-hub.md)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)
  
- [Ismerkedés a Log Analytics-lekérdezésekkel](../azure-monitor/logs/log-analytics-tutorial.md)
   
- [ Egyéni lekérdezések végrehajtása a Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató:** Az Azure Security Center for IoT használata Log Analytics-munkaterülettel a biztonsági naplókban és eseményekben található rendellenes tevékenységek monitorozásához és riasztásához. Másik lehetőségként engedélyezheti és be is használhatja az adatokat a Azure Sentinel. Az üzemeltetési riasztásokat olyan riasztásokkal is Azure Monitor amelyek biztonsági következményekkel járhatnak, például ha a forgalom váratlanul csökken.

- [A Azure IoT Hub állapotának figyelése](monitor-iot-hub.md)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Azure Security Center IoT-riasztások](/azure/asc-for-iot/concept-security-alerts)

- [Naplóelemzési naplóadatokra vonatkozó riasztások](../azure-monitor/alerts/tutorial-response.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="28-centralize-anti-malware-logging"></a>2.8: A kártevőirtó naplózás központosította

**Útmutató:** Nem alkalmazható; Azure IoT Hub nem készít kártevőirtó naplókat.

**Felelősség:** Nem alkalmazható

**Azure Security Center:** Nincs

### <a name="29-enable-dns-query-logging"></a>2.9: DNS-lekérdezésnaplózás engedélyezése

**Útmutató:** Nem alkalmazható; Azure IoT Hub nem készít DNS-sel kapcsolatos naplókat.

**Felelősség:** Nem alkalmazható

**Azure Security Center:** Nincs

### <a name="210-enable-command-line-audit-logging"></a>2.10: Parancssori naplózás engedélyezése

**Útmutató:** Nem alkalmazható; Ez a javaslat számítási erőforrásokhoz való.

**Felelősség:** Nem alkalmazható

**Azure Security Center:** Nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ: [Azure Biztonsági teljesítményteszt: Identitás és Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: A rendszergazdai fiókok nyilvántartása

**Útmutató:** Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) szerepkör-hozzárendelésekkel teszi lehetővé az Azure IoT Hubhoz való hozzáférés kezelését. Ezeket a szerepköröket felhasználókhoz, csoportokhoz, szolgáltatásnévhez és felügyelt identitásokhoz rendelheti hozzá. Bizonyos erőforrásokhoz előre definiált beépített szerepkörök is rendelkezésre állnak, és ezek a szerepkörök leltárba menthetőek vagy lekérdezhetőek olyan eszközökkel, mint az Azure CLI, Azure PowerShell vagy a Azure Portal.

- [Címtárszerepkomó le Azure Active Directory (Azure AD) a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrole)

- [Címtárbeli szerepkör tagjainak lekérte az Azure AD-ban a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Az alapértelmezett jelszavak módosítása, ha vannak

**Útmutató:** A Azure IoT Hub hozzáférés-vezérlése a Azure Active Directory (Azure AD) használatával történik. Az Azure AD nem rendelkezik az alapértelmezett jelszavak fogalmával.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Dedikált rendszergazdai fiókok használata

**Útmutató:** Szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használatával kapcsolatban.

A rendszergazdai fiókokhoz az azure-Azure Active Directory (Azure AD) használatával is Privileged Identity Management és Azure Resource Manager.

- [További információ a Privileged Identity Management](/azure/active-directory/privileged-identity-management/)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató:** Az alkalmazáshoz IoT Hub felhasználók Azure Active Directory (Azure AD) SSO-t. Használja Azure Security Center és hozzáférési javaslatokat.

- [Az SSO azure aD-val való használatának a](../active-directory/manage-apps/what-is-single-sign-on.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata minden Azure Active Directory hozzáféréshez

**Útmutató:** Engedélyezze Azure Active Directory (Azure AD) többtényezős hitelesítést a teljes Azure-bérlő védelme érdekében, így minden szolgáltatás kihasználható. IoT Hub szolgáltatás nem támogatja a többtényezős hitelesítést.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése a Azure Security Center](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Dedikált gépek (Emelt szintű hozzáférésű munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató:** Emelt szintű jogosultságot igénylő rendszergazdai feladatokhoz használjon biztonságos emelt szintű hozzáférési munkaállomást (PAW).

- [A biztonságos, emelt szintű hozzáférésű munkaállomások](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Többtényezős Azure Active Directory (Azure AD) engedélyezése](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: A rendszergazdai fiókokból származó gyanús tevékenységek naplózása és riasztása

**Útmutató:** Azure Active Directory (Azure AD) biztonsági jelentésekkel és monitorozással észlelheti, ha gyanús vagy nem biztonságos tevékenység történik a környezetben. A Azure Security Center identitás- és hozzáférési tevékenységek figyelése.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md)

- [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató:** A IoT Hub felhasználók számára a feltételes hozzáférés nem támogatott. Ennek mérséklése érdekében használjon Azure Active Directory (Azure AD) nevű helyeket, hogy csak az IP-címtartományok vagy országok/régiók meghatározott logikai csoportosításaiból engedélyezze a hozzáférést a teljes Azure-bérlő számára, így az összes szolgáltatás, köztük a IoT Hub.

- [Az Azure AD nevestűs helyek konfigurálása](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="39-use-azure-active-directory"></a>3.9: A Azure Active Directory

**Útmutató:** A felhasználói hozzáféréshez IoT Hub használja a Azure Active Directory (Azure AD) központi hitelesítési és engedélyezési rendszerként. Az Azure AD erős titkosítással védi az adatokat az átvitel alatt és az átvitel alatt. Az Azure AD emellett a felhasználói hitelesítő adatokat is megsóz, hashekkel és biztonságosan tárolja.

Eszköz- és szolgáltatás-hozzáférés esetén a IoT Hub biztonsági jogkivonatok és közös hozzáférésű jogosultsági jogosultságú (SAS-) jogkivonatok használatával hitelesíti az eszközöket és szolgáltatásokat, hogy elkerülje a kulcsok hálózaton való küldését. 

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)
- [IoT Hub biztonsági jogkivonatok](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-deployment#iot-hub-security-tokens)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: A felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató:** Azure Active Directory (Azure AD) naplókat biztosít az elavult fiókok felderítésének segítésekor. Emellett az Azure AD-identitás és -hozzáférési felülvizsgálatok használatával hatékonyan kezelheti a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férnek hozzá.

A Azure AD Privileged Identity Management (PIM) használatával naplókat és riasztásokat generáljon, ha gyanús vagy nem biztonságos tevékenység történik a környezetben.

- [Az Azure AD-jelentéskészítés](/azure/active-directory/reports-monitoring/)

- [Az Azure AD identitás- és hozzáférési felülvizsgálatainak használata](../active-directory/governance/access-reviews-overview.md)

- [Üzembe Azure AD Privileged Identity Management (PIM)](/azure/active-directory/privileged-identity-management/pim-deployment-plan)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Az inaktivált hitelesítő adatok elérésére tett kísérletek figyelése

**Útmutató:** Hozzáféréssel rendelkezik Azure Active Directory (Azure AD) bejelentkezési tevékenységéhez, naplózási és kockázati eseménynapló-forrásaihoz, amelyek lehetővé teszik az integrációt bármely SIEM-/monitorozási eszközzel.

Ezt a folyamatot leegyszerűsítheti, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókjaihoz, és elküldi az auditnaplókat és a bejelentkezési naplókat egy Log Analytics-munkaterületre. A kívánt riasztásokat a Log Analytics-munkaterületen konfigurálhatja.

A Azure Monitor erőforrásnaplókat a Kapcsolatok kategóriában a jogosulatlan kapcsolódási kísérletek figyelése során.

- [Azure-tevékenységnaplók integrálása Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Erőforrásnaplók konfigurálása az IoT Hubhoz](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub#collection-and-routing)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltéréséről

**Útmutató:** A Azure Active Directory (Azure AD) Identity Protection funkcióival konfigurálhatja a felhasználói identitásokkal kapcsolatos észlelt gyanús műveletekre adott automatikus válaszokat. Az adatokat be is Azure Sentinel vizsgálathoz.

- [ Kockázatos Azure AD-bejelentkezések megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [ Az Identity Protection kockázati szabályzatok konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [ A Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Hozzáférés biztosítanak a Microsoft számára a releváns ügyféladatokhoz a támogatási forgatókönyvek során

**Útmutató:** Olyan támogatási forgatókönyvekben, ahol a Microsoftnak hozzá kell férni az ügyféladatokhoz, azt közvetlenül az ügyféltől kell kérnie.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Bizalmas adatok leltárának fenntartása

**Útmutató:** A címkék segítségével nyomon lehet követni a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrásokat.
 
- [ Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Bizalmas adatokat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** Elkülönítés implementálás különálló előfizetések és felügyeleti csoportok használatával az egyes biztonsági tartományokhoz, például a környezet típusához és az adatok bizalmasságához. Korlátozhatja az alkalmazások és a vállalati környezetek számára az Azure-erőforrásokhoz való hozzáférés szintjét. Az Azure-erőforrásokhoz való hozzáférést az Azure RBAC-n keresztül szabályozhatja.
  
- [ További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [ Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)

- [ Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Bizalmas adatok jogosulatlan átvitelének figyelése és blokkolása

**Útmutató:** Külső megoldás használata a Azure Marketplace peremhálózatán a bizalmas adatok jogosulatlan átvitelének figyelése és az ilyen átvitelek blokkolása az információbiztonsági szakemberek riasztása során.

A Microsoft által felügyelt mögöttes platform esetén a Microsoft minden ügyféltartalmat bizalmasként kezel, és védelmet biztosít az ügyfelek adatvesztése és kitettsége ellen. Annak érdekében, hogy az Azure-beli ügyféladatok biztonságban maradnak, a Microsoft robusztus adatvédelmi vezérlőket és képességeket valósított meg és tart karban.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Az átvitel során bizalmas adatok titkosítása

**Útmutató:** IoT Hub iot-Transport Layer Security (TLS) segítségével biztosítja az IoT-eszközök és -szolgáltatások közötti kapcsolatokat. Jelenleg a TLS protokoll három verziója támogatott, ezek az 1.0-s, 1.1-es és 1.2-es verziók. Erősen ajánlott a TLS 1.2-t használni előnyben részesített TLS-verzióként a IoT Hub.

Kövesse Azure Security Center az átvitel közbeni, valamint az átvitel közbeni titkosításra vonatkozó javaslatokat.

- [TLS-támogatás a IoT Hub](iot-hub-tls-support.md)
- [Az átvitel során az Azure-ral történő titkosítás](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Aktív felderítési eszköz használata a bizalmas adatok azonosításához

**Útmutató:** Az adatazonosítási, besorolási és adatveszteség-megelőzési funkciók még nem érhetők el a Azure IoT Hub. Ha megfelelőségi célokból szükség van rá, implementálja a harmadik féltől származó megoldást.

A Microsoft által felügyelt mögöttes Azure-platform esetén a Microsoft bizalmasként kezeli az ügyfelek tartalmait, és nagy terjedelmüknek megfelelő védelmet biztosít az ügyfelek adatvesztése és -kitettsége ellen. Annak érdekében, hogy az Azure-beli ügyféladatok biztonságban maradnak, a Microsoft robusztus adatvédelmi vezérlőket és képességeket valósított meg és tart karban.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Az Erőforrásokhoz való hozzáférés kezelése az Azure RBAC használatával

**Útmutató:** A vezérlősík felhasználói hozzáférése IoT Hub azure RBAC használatával szabályozhatja a hozzáférést. Az adatsík hozzáféréséhez a IoT Hub használja a megosztott hozzáférési szabályzatokat a IoT Hub.

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

- [IoT Hub-hozzáférés szabályozása](iot-hub-devguide-security.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Kritikus fontosságú Azure-erőforrások változásainak naplózása és riasztása

**Útmutató:** Azure Monitor Azure-tevékenységnaplóval használva riasztásokat hozhat létre, amikor változásokra kerül sor az éles példányok Azure IoT Hub kritikus vagy kapcsolódó erőforrásokon.

- [Riasztások létrehozása az Azure-tevékenységnapló eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ: [Azure biztonsági teljesítményteszt: Biztonsági rések kezelése.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Automatikus javításkezelési megoldás üzembe helyezése külső szoftvercímek számára

**Útmutató:** Nem alkalmazható; Ez az útmutató a számítási erőforrásokhoz való.

**Felelősség:** Nem alkalmazható

**Azure Security Center:** Nincs

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: A biztonsági rések biztonsági rések biztonsági vizsgálatának összehasonlítása

**Útmutató:** Nem alkalmazható; Ez az útmutató a számítási erőforrásokhoz való.

**Felelősség:** Nem alkalmazható

**Azure Security Center:** Nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Kockázatminősítési folyamat használata a felderített biztonsági rések szervizelésének rangsorolására

**Útmutató:** Nem alkalmazható; Ez az útmutató a számítási erőforrásokhoz való.

**Felelősség:** Nem alkalmazható

**Azure Security Center:** Nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Azure biztonsági teljesítményteszt: Leltár és eszközkezelés.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Automatizált eszközfelderítési megoldás használata

**Útmutató:** Nem alkalmazható; Ez az útmutató a számítási erőforrásokhoz való.

**Felelősség:** Nem alkalmazható

**Azure Security Center monitorozás:** Nincs

### <a name="62-maintain-asset-metadata"></a>6.2: Objektum metaadatainak karbantartása

**Útmutató:** Címkék alkalmazása Azure-erőforrásokra (nem minden erőforrás támogatja a címkéket, de a legtöbb igen), hogy logikailag rendszerezze őket egy taxonómiában.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Nem engedélyezett Azure-erőforrások törlése

**Útmutató:** Az eszközök rendszerezéséhez és nyomon követéséhez használjon címkézést, felügyeleti csoportokat és különálló előfizetéseket, ha szükséges. Rendszeresen összeegyezteti a leltárat, és gondoskodik arról, hogy a jogosulatlan erőforrások időben törlődjenek az előfizetésből.
  
- [ További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Jóváhagyott Azure-erőforrások készletének meghatározása és karbantartása

**Útmutató:** Leltár létrehozása a jóváhagyott Azure-erőforrásokról és a számítási erőforrásokhoz jóváhagyott szoftverekről a szervezeti igényeknek megfelelően.

Minden IoT Hub rendelkezik egy identitásjegyzékkel, amely segítségével eszközönkénti erőforrások hozhatók létre a szolgáltatásban. Az eszköz identitások egyéni vagy csoportjai hozzáadhatók egy engedélyezési listához vagy egy tiltólistához, ami lehetővé teszi az eszköz hozzáférésének teljes vezérlését.

- [IoT Hub identity registry](iot-hub-devguide-identity-registry.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Nem jóváhagyott Azure-erőforrások figyelése

**Útmutató:** A Azure Policy az előfizetésben létrehozható erőforrások típusára vonatkozó korlátozásokat. 

Az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetésükön belüli erőforrásokat.  Győződjön meg arról, hogy a környezetben jelen van minden Azure-erőforrás jóvá van hagyva. 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató:** Nem alkalmazható; Ez a javaslat számítási erőforrásokhoz való.

**Felelősség:** Nem alkalmazható

**Azure Security Center:** Nincs

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Nem jóváhagyott Azure-erőforrások és -szoftveralkalmazások eltávolítása

**Útmutató:** Nem alkalmazható; Ez a javaslat számítási erőforrásokhoz való.

**Felelősség:** Nem alkalmazható

**Azure Security Center:** Nincs

### <a name="68-use-only-approved-applications"></a>6.8: Csak jóváhagyott alkalmazások használata

**Útmutató:** Nem alkalmazható; Ez a javaslat számítási erőforrásokhoz való.

**Felelősség:** Nem alkalmazható

**Azure Security Center:** Nincs

### <a name="69-use-only-approved-azure-services"></a>6.9: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató:** Azure Policy az alábbi beépített szabályzatdefiníciók használatával az ügyfél-előfizetésben létrehozható erőforrások típusára vonatkozó korlátozásokat a Azure Policy használatával:

* Nem engedélyezett erőforrástípusok
* Engedélyezett erőforrástípusok

Emellett a Azure Resource Graph az előfizetések erőforrásait is lekérdezheti/felderítheti.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)
- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: A jóváhagyott szoftvercímek leltárának fenntartása

**Útmutató:** Nem alkalmazható; Ez a javaslat számítási erőforrásokhoz ajánlott.

**Felelősség:** Nem alkalmazható

**Azure Security Center monitorozás:** Nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Korlátozza a felhasználók számára a Azure Resource Manager

**Útmutató:** A Azure Active Directory (Azure AD) feltételes hozzáféréssel korlátozhatja a felhasználók Azure Resource Manager-val való interakcióját a "Hozzáférés blokkolása" beállításával a "Microsoft Azure Management" alkalmazáshoz.

- [ Feltételes hozzáférés konfigurálása a hozzáférés letilt Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: A felhasználók szkriptek számítási erőforrásokban való végrehajtásának korlátozása

**Útmutató:** Nem alkalmazható; Ez a javaslat számítási erőforrásokhoz ajánlott.

**Felelősség:** Nem alkalmazható

**Azure Security Center monitorozás:** Nincs

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Magas kockázatú alkalmazások fizikai vagy logikai elkülönítése

**Útmutató:** Nem alkalmazható; ez a javaslat a számítási erőforrásokon futó webalkalmazások Azure App Service ajánlott.

**Felelősség:** Nem alkalmazható

**Azure Security Center monitorozás:** Nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információ: [Azure biztonsági teljesítményteszt: Biztonságos konfiguráció.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató:** Standard biztonsági konfigurációk meghatározása és megvalósítása az Azure Iot Hub szolgáltatáshoz a Azure Policy. A Azure Policy a "Microsoft.Devices" névtérben az aliasok használatával egyéni szabályzatokat hozhat létre, amelyek naplót végeznek vagy kikényszeríteni Azure IoT Hub konfigurációját.

Azure Resource Manager képes exportálni a sablont az JavaScript Object Notation -ban (JSON), amelyet át kell vizsgálni annak biztosítása érdekében, hogy a konfigurációk megfelelnek a szervezet biztonsági követelményeinek.

Az Azure-erőforrások biztonságos alapkonfigurációjaként Azure Security Center az azure-erőforrásokra vonatkozó javaslatokat is használhatja.

- [Az elérhető aliasok Azure Policy megtekintése](/powershell/module/az.resources/get-azpolicyalias)

- [Oktatóanyag: Szabályzatok létrehozása és kezelése a megfelelőség kényszerítéséhez](../governance/policy/tutorials/create-and-manage.md)

- [Egy- és többerőforrásos exportálás sablonba a Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Biztonsági javaslatok – gyorsútmutató](../security-center/recommendations-reference.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató:** Nem alkalmazható; Ez az útmutató a számítási erőforrásokhoz való.

**Felelősség:** Nem alkalmazható

**Azure Security Center:** Nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Biztonságos Azure-erőforráskonfigurációk fenntartása

**Útmutató:** A Azure Policy [deny] és a [deploy if not exist] használatával kényszerítheti a biztonságos beállításokat az Azure-erőforrásokon. Emellett a sablonsablonok Azure Resource Manager a szervezet által megkövetelt Azure-erőforrások biztonsági konfigurációjának fenntartásához.  
 
- [A Azure Policy hatásainak](../governance/policy/concepts/effects.md)
- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../governance/policy/tutorials/create-and-manage.md)
- [Azure Resource Manager sablonok áttekintése](../azure-resource-manager/templates/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató:** Nem alkalmazható; Ez az útmutató a számítási erőforrásokhoz való.

**Felelősség:** Nem alkalmazható

**Azure Security Center monitorozás:** Nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások konfigurációjának biztonságos tárolása

**Útmutató:** Ha egyéni Azure Policy definíciókat használ a Azure IoT Hub vagy a kapcsolódó erőforrásokhoz, az Azure Repos használatával biztonságosan tárolhatja és kezelheti a kódot.

- [Kód tárolása az Azure DevOpsban](/azure/devops/repos/git/gitworkflow)

- [Az Azure Repos dokumentációja](/azure/devops/repos)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató:** Nem alkalmazható; Ez az útmutató a számítási erőforrásokhoz való.

**Felelősség:** Nem alkalmazható

**Azure Security Center monitorozás:** Nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Konfigurációkezelési eszközök üzembe helyezése Azure-erőforrásokhoz

**Útmutató:** A Azure Policy a "Microsoft.Devices" névtérben aliasokkal hozhat létre egyéni szabályzatokat a rendszerkonfigurációk riasztásához, naplózásához és kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és folyamatot a szabályzati kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)
- [Aliasok használata](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Konfigurációkezelő eszközök központi telepítése operációs rendszerekhez

**Útmutató:** Nem alkalmazható; Ez az útmutató a számítási erőforrásokhoz való.

**Felelősség:** Nem alkalmazható

**Azure Security Center monitorozás:** Nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Automatizált konfigurációfigyelés megvalósítása Azure-erőforrásokhoz

**Útmutató:** A Azure Security Center az Azure-erőforrások alapkonfiguráció-vizsgálatának végrehajtásához. Emellett az Azure Azure Policy konfigurációk riasztása és naplózása is használható. 
 
- [ Javaslatok szervizlének Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Automatikus konfigurációfigyelés megvalósítása operációs rendszerekhez

**Útmutató:** Nem alkalmazható; Ez az útmutató a számítási erőforrásokhoz való.

**Felelősség:** Nem alkalmazható

**Azure Security Center:** Nincs

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-beli titkos kulcsok biztonságos kezelése

**Útmutató:** IoT Hub biztonsági jogkivonatok és közös hozzáférésű jogosultsági jogosultságú (SAS)-jogkivonatok használatával hitelesíti az eszközöket és szolgáltatásokat, hogy elkerülje a kulcsok hálózati küldését. 

A felügyelt identitások és a Azure Key Vault segítségével egyszerűsítheti a felhőalkalmazások titkos adatok kezelését.

- [IoT Hub biztonsági jogkivonatok](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-deployment#iot-hub-security-tokens)

- [Felügyelt identitások használata IoT Hub](https://docs.microsoft.com/azure/iot-hub/virtual-network-support#turn-on-managed-identity-for-iot-hub)

- [Kulcstartó létrehozása](../key-vault/general/quick-create-portal.md)

- [Felügyelt identitással Key Vault hitelesítésének engedélyezése](../key-vault/general/assign-access-policy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identitások biztonságos és automatikus kezelése

**Útmutató:** IoT Hub biztonsági jogkivonatokat és közös hozzáférésű jogosultsági (SAS) jogkivonatokat használ az eszközök és szolgáltatások hitelesítéséhez, hogy elkerülje a kulcsok hálózaton való küldését.

Felügyelt identitások használatával biztosítson egy automatikusan felügyelt identitást az Azure-szolgáltatások számára a Azure Active Directory (Azure AD) szolgáltatásban. A felügyelt identitások lehetővé teszik az Azure AD-hitelesítést támogató bármely szolgáltatásban történő hitelesítést, beleértve a Key Vault szolgáltatást is, anélkül, hogy a hitelesítő adatokat a kódban lenne.

- [IoT Hub biztonsági jogkivonatok](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-deployment#iot-hub-security-tokens)

- [Felügyelt identitások konfigurálása a IoT Hub](https://docs.microsoft.com/azure/iot-hub/virtual-network-support#turn-on-managed-identity-for-iot-hub)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: A hitelesítő adatok nem szándékolt kitettségének megszüntetése

**Útmutató:** Hitelesítőadat-ellenőrző implementálja a hitelesítő adatokat a kódban. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz. 
 
- [  Hitelesítőadat-ellenőrző beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információ: [Azure biztonsági teljesítményteszt: Kártevővédelem.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: A nem számítási Azure-erőforrásokba feltölteni szükséges fájlok előzetes beolvasása

**Útmutató:** A Microsoft kártevőirtó engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure IoT Hub), de nem fut az ügyfelek tartalmai között.

Az Ön felelőssége, hogy előzetesen megvizsgálja a nem számítási Azure-erőforrásokra feltöltött tartalmakat. A Microsoft nem fér hozzá az ügyféladatokhoz, ezért nem tudja az Ön nevében kártevőirtó vizsgálatot végezni az ügyféltartalmakban.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Azure biztonsági teljesítményteszt: Adat-helyreállítás.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatikus biztonságimentás-frissítések biztosítása

**Útmutató:** Azure IoT Hub szolgáltatás olyan módszereket és keretrendszereket biztosít, amelyek segítségével a IoT Hub-szolgáltatások magas rendelkezésre állásban és vészhelyreállításban állíthatók helyre adott üzleti célkitűzések alapján. 

- [IoT Hub magas rendelkezésre állása és vészhelyreállítása](iot-hub-ha-dr.md)

- [Klónozási IoT Hub](iot-hub-how-to-clone.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszer-biztonsági mentések és az ügyfelek által kezelt kulcsok biztonsági mentése

**Útmutató:** Azure IoT Hub azt javasolja, hogy a másodlagos IoT Hubnak tartalmaznia kell a megoldáshoz csatlakozni képes összes eszköz identitását. A megoldásnak meg kell tartania az eszköz identitásai georeplikált biztonsági másolatát, és fel kell töltenie őket a másodlagos IoT Hubra, mielőtt átváltja az eszközök aktív végpontját. A szolgáltatás eszközidentitás-exportálási IoT Hub hasznos ebben a környezetben.

- [IoT Hub magas rendelkezésre állása és vészhelyreállítása](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr#achieve-cross-region-ha)

- [IoT Hub identitás exportálása](iot-hub-bulk-identity-mgmt.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági mentés ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató:** Azure IoT Hub azt javasolja, hogy a másodlagos IoT Hubnak tartalmaznia kell a megoldáshoz csatlakozni képes összes eszköz identitását. A megoldásnak meg kell tartania az eszköz identitásai georeplikált biztonsági másolatát, és fel kell töltenie őket a másodlagos IoT Hubra, mielőtt átváltja az eszközök aktív végpontját. Ebben a környezetben hasznos IoT Hub eszközidentitás exportálási funkciója.

Rendszeres időközönként végezze el a biztonsági másolatban található tartalom visszaállítását. Győződjön meg arról, hogy vissza tudja állítani az ügyfél által kezelt biztonsági másolatok kulcsait.

- [IoT Hub magas rendelkezésre állása és vészhelyreállítása](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr#achieve-cross-region-ha)

- [IoT Hub eszközidentitás exportálása](iot-hub-bulk-identity-mgmt.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági másolatok és az ügyfél által kezelt kulcsok védelmének biztosítása

**Útmutató:** A kulcsok véletlen vagy rosszindulatú törléssel szembeni védelme érdekében engedélyezze a Key Vault és végleges törlés elleni védelmet. Ha az Azure Storage biztonsági másolatokat tárol, engedélyezze a helyreállítható törlést az adatok mentéséhez és helyreállításához blobok vagy blob-pillanatképek törlésekor.

 
- [Az Azure RBAC](../role-based-access-control/overview.md)

- [Az Azure Blob Storage soft delete parancsa](../storage/blobs/soft-delete-blob-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Incidensválasz-útmutató létrehozása

**Útmutató:** Incidensválasz-útmutató kidolgozása a szervezet számára. Győződjön meg arról, hogy vannak olyan írásos incidenskezelési tervek, amelyek meghatározzák a személyzet összes szerepkörét, valamint az incidenskezelés és -kezelés fázisát az észleléstől az incidens utáni felülvizsgálaton át az incidensek áttekintéséhez. 
  
- [ Útmutató saját biztonsági incidensek válaszadási folyamatának építéshez](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
  
- [ Microsoft Biztonsági reagálási központ az incidensek felépítése](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
 
- [  Az NIST számítógépes biztonsági incidenskezelési útmutatójának használata saját incidenskezelési terv létrehozásához](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és -rangsorolási eljárás létrehozása

**Útmutató:** Azure Security Center súlyosságot rendel az egyes riasztásokhoz, így fontossági sorrendbe adhatja, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy Security Center mennyire magabiztos a riasztás kiadásához használt eredmény vagy elemzési eredmény, valamint az, hogy a riasztáshoz vezető tevékenység mögött rosszindulatú szándék volt-e.

  
 Emellett címkék használatával jelölje meg az előfizetéseket, és hozzon létre egy elnevezési rendszert az Azure-erőforrások azonosításához és kategorizálásához, különösen a bizalmas adatokat feldolgozók számára. Az Ön felelőssége, hogy rangsorolja a riasztások szervizelését azon Azure-erőforrások és -környezet kritikussága alapján, ahol az incidens történt.
  
- [ Biztonsági riasztások a Azure Security Center](../security-center/security-center-alerts-overview.md)
  
- [ Címkék használata az Azure-erőforrások rendszerezése érdekében](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="103-test-security-response-procedures"></a>10.3: Biztonsági reagálási eljárások tesztelése

**Útmutató:** Gyakorlatok a rendszerek incidensválasz-képességeinek rendszeres teszteléséhez az Azure-erőforrások védelme érdekében. Azonosítsa a gyenge pontokat és hiányosságokat, majd szükség szerint módosítsa a választervet.
  
- [ Az NIST kiadványa – Útmutató az it-csomagokhoz és képességekhez szükséges tesztelési, képzési és gyakorlatprogramokhoz](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidens kapcsolattartási adatait, és konfigurálja a riasztási értesítéseket a biztonsági incidensek esetén

**Útmutató:** A Biztonsági incidensek kapcsolattartási adatait a Microsoft arra használja, hogy kapcsolatba lépjen Önnel, ha a Microsoft Biztonsági reagálási központ (MSRC) azt tapasztalja, hogy illetéktelen vagy jogosulatlan fél fért hozzá adataihoz. Tekintse át az incidenseket a tény után, és győződjön meg arról, hogy a problémák megoldódnak.
  
- [ A biztonsági kapcsolattartó Azure Security Center beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidensválasz-rendszerbe

**Útmutató:** A riasztások Azure Security Center és javaslatok exportálása a folyamatos exportálási funkcióval az Azure-erőforrásokat érintő kockázatok azonosítása érdekében. A folyamatos exportálás lehetővé teszi, hogy manuálisan vagy folyamatosan exportálja a riasztásokat és javaslatokat. A riasztásokat a Azure Security Center adat-összekötővel streamelheti a Azure Sentinel.
  
- [ Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)
 
- [ Riasztások streamelése Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: A biztonsági riasztások válaszának automatizálása

**Útmutató:** A munkafolyamat-automatizálási Azure Security Center az Azure-erőforrások védelmére vonatkozó biztonsági riasztásokhoz és javaslatokhoz adott válaszok automatikus aktiválásához.
  
- [ Munkafolyamat-automatizálás konfigurálása a Security Center](../security-center/workflow-automation.md)

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
