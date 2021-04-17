---
title: Az Azure biztonsági alapkonfigurációja Event Hubs
description: A Event Hubs alapkonfigurációja eljárásokkal kapcsolatos útmutatást és forrásforrásokat biztosít az Azure biztonsági teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához.
author: msmbaldwin
ms.service: event-hubs
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 33417a9bda9ad4ce36dd6e14f74a53911f3c3473
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587153"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Az Azure biztonsági alapkonfigurációja Event Hubs

Ez a biztonsági alapkonfiguráció az Azure Biztonsági teljesítményteszt [1.0-s](../security/benchmarks/overview-v1.md) verziójának útmutatását alkalmazza a Event Hubs. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalmat az Azure  biztonsági teljesítményteszt által meghatározott biztonsági vezérlők és a kapcsolódó, az azure-beli biztonsági Event Hubs. **A** Event Hubs nem alkalmazható vezérlők ki vannak zárva.

 
Ha meg Event Hubs az Azure-biztonsági teljesítményteszt teljes körű leképezését, tekintse meg a teljes Event Hubs alapkonfiguráció [leképezési fájlját.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-erőforrások védelme virtuális hálózatokon belül

**Útmutató:** A Azure Event Hubs integrációja a virtuális hálózati szolgáltatásvégpontokkal biztonságos hozzáférést biztosít az üzenetkezelési képességekhez olyan számítási feladatokból, mint a virtuális hálózatokhoz kötött virtuális gépek, és mindkét végén biztonságossá válik a hálózati forgalom.

Ha legalább egy virtuális hálózati alhálózati szolgáltatásvégponthoz van kötve, a megfelelő Event Hubs névtér már nem fogad forgalmat bárhonnan, csak a virtuális hálózatok engedélyezett alhálózatairól. A virtuális hálózat szempontjából a Event Hubs-névtér szolgáltatásvégponthoz való kötése elkülönített hálózati alagutat konfigurál a virtuális hálózat alhálózata és az üzenetkezelési szolgáltatás között. 

Privát végpontot is létrehozhat, amely egy olyan hálózati adapter, amely privát módon és biztonságosan csatlakozik az Event Hubs szolgáltatáshoz a Azure Private Link szolgáltatással. A privát végpont a virtuális hálózatról származó magánhálózati IP-címet használ, így hatékonyan behozza a szolgáltatást a virtuális hálózatba. A szolgáltatás felé minden forgalom átirányítható a privát végponton keresztül, így nincs szükség átjárókra, NAT-eszközökre, ExpressRoute- vagy VPN-kapcsolatokra, illetve nyilvános IP-címekre. 

A hálózati névterét Azure Event Hubs tűzfalak használatával is biztosíthatja. Event Hubs támogatja az IP-alapú hozzáférés-vezérlést a bejövő tűzfalak támogatásához. A tűzfalszabályokat az azure-beli virtuális Azure Portal, Azure Resource Manager az Azure CLI vagy a Azure PowerShell.

- [Virtuális hálózati szolgáltatásvégpont használata Azure Event Hubs](event-hubs-service-endpoints.md)

- [Integráció Azure Event Hubs és Azure Private Link](private-link-service.md)

- [IP-tűzfalszabályok konfigurálása Azure Event Hubs névterek számára](event-hubs-ip-filtering.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.EventHub:**

[!INCLUDE [Resource Policy for Microsoft.EventHub 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** A Azure Security Center és kövesse a hálózatvédelmi javaslatokat az Azure-beli Event Hubs védelméhez. Ha Azure-beli virtuális gépeket használ az eseményközpontok eléréséhez, engedélyezze a hálózati biztonsági csoport forgalmi naplóit, és küldje el a naplókat egy tárfiókba a forgalom naplózásához.

- [NSG-forgalom naplóinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Az Azure Security Center](../security-center/security-center-network-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert kártékony IP-címekkel való kommunikáció megtagadása

**Útmutató:** Engedélyezze DDoS Protection Standard szabványt az eseményközpontokkal társított virtuális hálózatokon, hogy védelmet nyújtson az elosztott szolgáltatásmegtagadásos (DDoS-) támadások ellen. Az Azure Security Center intelligencia használatával megtagadhatja az ismert rosszindulatú vagy nem használt internetes IP-címekkel való kommunikációt.

- [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

- [További információ az integrált fenyegetés Azure Security Center intelligencia-megoldásról](../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="15-record-network-packets"></a>1.5: Hálózati csomagok rekordja

**Útmutató:** Ha Azure-beli virtuális gépeket használ az eseményközpontok eléréséhez, engedélyezze a hálózati biztonsági csoport forgalmi naplóit, és küldje el a naplókat egy tárfiókba a forgalom naplózásához. Emellett elküldheti a hálózati biztonsági csoport forgalmi naplóit egy Log Analytics-munkaterületre, és a Traffic Analytics segítségével betekintést nyerhet az Azure-felhőben lévő forgalomba. A hálózati Traffic Analytics a hálózati tevékenységek vizualizációja és a gyors helyek azonosítása, a biztonsági fenyegetések azonosítása, a forgalmi minták azonosítása és a hálózati konfigurációk észlelése.

Ha a rendellenes tevékenységek kivizsgálásához szükséges, engedélyezze a Network Watcher rögzítését.

- [NSG-forgalom naplóinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics](../network-watcher/traffic-analytics.md)

- [A Network Watcher](../network-watcher/network-watcher-create.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Hálózatalapú behatolásészlelési/behatolásvédelmi rendszerek (IDS/IPS) üzembe helyezése

**Útmutató:** Ha Azure-beli virtuális gépeket használ az eseményközpontok eléréséhez, válasszon egy ajánlatot a Azure Marketplace, amely támogatja az IDS/IPS funkciókat és a hasznos adatok vizsgálati képességeit. Ha a behatolásészlelés és/vagy a hasznos adatok vizsgálatán alapuló megelőzés nem szükséges a szervezet számára, használhatja Azure Event Hubs beépített tűzfal funkcióját. A tűzfalszabályok használatával korlátozhatja a Event Hubs ip-címtartományhoz vagy egy adott IP-címhez való hozzáférést a névtérhez.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Tűzfalszabály hozzáadása egy Event Hubs IP-címhez](event-hubs-ip-filtering.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációjának fenntartása

**Útmutató:** Szabványos biztonsági konfigurációkat határozhat meg és valósíthat meg a Azure Event Hubs-névterekkel társított hálózati erőforrásokhoz a Azure Policy. A **Azure Policy.EventHub-** és **Microsoft.Network-névterek** aliasai használatával egyéni szabályzatdefiníciókat hozhat létre a névterek hálózati konfigurációjának naplózásához Event Hubs kényszeríteni. A szabályzathoz kapcsolódó beépített szabályzatdefiníciókat is Azure Event Hubs, például:

- Az eseményközpontnak virtuális hálózati szolgáltatásvégpontot kell használnia.

További információk a hivatkozott hivatkozásokon érhetők el.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Az Azure beépített szabályzata Event Hubs névtérhez](../governance/policy/samples/built-in-policies.md#event-hub)

- [Azure Policy mintákat a hálózathoz](../governance/policy/samples/built-in-policies.md#network)

- [Azure Blueprints-terv létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutató:** Az eseményközpontokkal társított virtuális hálózatokhoz és más, a hálózati biztonsághoz és forgalomhoz kapcsolódó erőforrások címkéinek használata.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrások konfigurációjának figyelése és a változások észlelése érdekében

**Útmutató:** Az Azure-tevékenységnapló segítségével monitorozható a hálózati erőforrások konfigurációja, és észlelhetők a hálózati erőforrásokkal kapcsolatos Azure Event Hubs. Hozzon létre riasztásokat a Azure Monitor, amelyek akkor aktiválódnak, ha a kritikus hálózati erőforrások módosulnak.

- [Azure-tevékenységnapló eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Azure biztonsági teljesítményteszt: Naplózás és monitorozás.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Központi biztonsági naplókezelés konfigurálása

**Útmutató:** Az Azure Monitor-ben konfigurálja az eseményközpontokkal kapcsolatos naplókat a Tevékenységnapló és az Eseményközpont diagnosztikai beállításaiban, hogy a naplókat lekérdezni vagy egy tárfiókba küldjék a hosszú távú archiválás céljából.

- [Diagnosztikai beállítások konfigurálása a Azure Event Hubs](event-hubs-diagnostic-logs.md)

- [Az Azure-tevékenységnapló ismertetése](../azure-monitor/essentials/platform-logs-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure-erőforrások naplózásának engedélyezése

**Útmutató:** Diagnosztikai beállítások engedélyezése a Azure Event Hubs számára. A diagnosztikai beállítások három kategóriába sorolhatók a Azure Event Hubs: Archív naplók, működési naplók és automatikus skálázási naplók. Engedélyezze az operatív naplókat, hogy rögzítse az Event Hubs-műveletek során történteket, különösen a művelet típusát, beleértve az eseményközpont létrehozását, a használt erőforrásokat és a művelet állapotát.

Emellett engedélyezheti az Azure-tevékenységnapló diagnosztikai beállításait, és elküldheti őket egy Azure Storage-fiókba, egy eseményközpontba vagy egy Log Analytics-munkaterületre. A tevékenységnaplók betekintést nyújtanak a saját erőforrásán végrehajtott Azure Event Hubs és egyéb erőforrásokba. A tevékenységnaplók használatával meghatározhatja a névterek írási műveleteinek (PUT, POST, DELETE) "mit, ki és mikor" Azure Event Hubs esetén.

- [Diagnosztikai beállítások engedélyezése a Azure Event Hubs](event-hubs-diagnostic-logs.md)

- [Diagnosztikai beállítások engedélyezése az Azure-tevékenységnaplóhoz](../azure-monitor/essentials/activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.EventHub:**

[!INCLUDE [Resource Policy for Microsoft.EventHub 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: Biztonsági naplók tárolásának konfigurálása

**Útmutató:** Azure Monitor log Analytics-munkaterület megőrzési időszakát a szervezet megfelelőségi szabályozásának megfelelően állíthatja be az eseményközponttal kapcsolatos incidensek rögzítéséhez és áttekintéséhez.

- [Naplómegőrzési paraméterek beállítása Log Analytics-munkaterületeken](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és áttekintése

**Útmutató:** Naplók elemzése és figyelése a rendellenes viselkedés érdekében, valamint az eseményközpontokkal kapcsolatos eredmények rendszeres áttekintése. A Azure Monitor Log Analytics használatával áttekinthet naplókat, és lekérdezéseket hajthatja végre a naplóadatokon. Másik lehetőségként engedélyezheti és bevetheti az adatokat egy Azure Sentinel vagy egy külső rendszeradat- és eseménykezelési megoldásba.

- [További információ a Log Analytics-munkaterületről](../azure-monitor/logs/log-analytics-tutorial.md)

- [Egyéni lekérdezések végrehajtása a Azure Monitor](../azure-monitor/logs/get-started-queries.md)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató:** A Azure Monitor-ben konfigurálhatja a Azure Event Hubs-hoz kapcsolódó naplókat a tevékenységnaplóban Event Hubs, és diagnosztikai beállításokat is konfigurálhatja, hogy a naplókat lekérdezni vagy egy tárfiókba küldjék a hosszú távú archiválás céljából. A Log Analytics-munkaterület használatával riasztásokat hozhat létre a biztonsági naplókban és eseményekben található rendellenes tevékenységekhez.

Másik lehetőségként engedélyezheti és be is adatokat lehet Azure Sentinel. 

- [Az Azure-tevékenységnaplók](../azure-monitor/essentials/platform-logs-overview.md)

- [Diagnosztikai beállítások konfigurálása a Azure Event Hubs](event-hubs-diagnostic-logs.md)

- [Riasztás a Log Analytics-munkaterület naplóadatokkal kapcsolatos adatairól](../azure-monitor/alerts/tutorial-response.md)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ: [Azure Biztonsági teljesítményteszt: Identitás és Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: A rendszergazdai fiókok nyilvántartása

**Útmutató:** Azure Active Directory (Azure AD) beépített szerepkörökkel rendelkezik, amelyekhez explicit módon kell hozzárendelni és lekérdezhetőnek kell lennie. Az Azure AD PowerShell-modullal alkalmi lekérdezéseket hajt végre a rendszergazdai csoportok tagjainak felderítéséhez.

- [Címtárszerepkomó lekért szerepkör az Azure AD-ban a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrole)

- [Címtárbeli szerepkör tagjainak lekérte az Azure AD-ban a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Az alapértelmezett jelszavak módosítása, ha vannak

**Útmutató:** A vezérlősík hozzáférése Event Hubs vezérlése a Azure Active Directory (Azure AD) használatával történik. Az Azure AD nem rendelkezik az alapértelmezett jelszavak fogalmával.

Az adatsík hozzáférése Event Hubs felügyelt identitásokkal vagy identitásokkal, valamint közös hozzáférésű jogosultságokkal Alkalmazásregisztrációk Azure AD-val szabályozható. A közös hozzáférésű jogosultságokat az eseményközponthoz csatlakozó ügyfelek használják, és bármikor újragenerálhatóak.

- [A közös hozzáférésű jogosultságok Event Hubs](authenticate-shared-access-signature.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Dedikált rendszergazdai fiókok használata

**Útmutató:** Szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használatával kapcsolatban. Az Azure Security Center és hozzáférés-kezelés használatával figyelheti a rendszergazdai fiókok számát.

Emellett a dedikált rendszergazdai fiókok nyomon követéséhez használhatja a Azure Security Center vagy beépített Azure-szabályzatok javaslatait, például:

- Az előfizetéshez egynél több tulajdonosnak kell hozzárendelve lennie

- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből

- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

További információk a hivatkozott hivatkozásokon érhetők el.

- [Az Azure Security Center és hozzáférés figyelése (előzetes verzió)](../security-center/security-center-identity-access.md)

- [A Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Egyszeri Azure Active Directory (SSO) használata

**Útmutató:** Microsoft Azure hozzáférés-vezérlés integrált kezelését biztosítja az erőforrások és alkalmazások számára a Azure Active Directory (Azure AD) alapján. Az Azure AD és a Azure Event Hubs használatának egyik fő előnye, hogy többé nem kell a kódban tárolnia a hitelesítő adatait. Ehelyett OAuth 2.0 hozzáférési jogkivonatot kérhet a Microsoft Identity platformról. A jogkivonatot lekért erőforrás neve https: \/ /eventhubs.azure.net/. Az Azure AD hitelesíti az alkalmazást futtató rendszerbiztonsági szolgáltatásokat (felhasználót, csoportot vagy szolgáltatásnévket). Ha a hitelesítés sikeres, az Azure AD egy hozzáférési jogkivonatot ad vissza az alkalmazásnak, és az alkalmazás ezután a hozzáférési jogkivonattal engedélyezheti az erőforrások Azure Event Hubs kérését.

- [Alkalmazás hitelesítése az Azure AD-val a Event Hubs eléréséhez](authenticate-application.md)

- [Az SSO ismertetése az Azure AD-val](../active-directory/manage-apps/what-is-single-sign-on.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata minden Azure Active Directory-alapú hozzáféréshez

**Útmutató:** Engedélyezze Azure Active Directory (Azure AD) többtényezős hitelesítést, és kövesse Azure Security Center Identitás- és hozzáférés-kezelésre vonatkozó javaslatokat az Event Hub-kompatibilis erőforrások védelme érdekében.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése a Azure Security Center](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Dedikált gépek (Emelt szintű hozzáférésű munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató:** Emelt szintű hozzáférésű munkaállomások (PAW) használata többtényezős hitelesítéssel konfigurálva az Event Hub-kompatibilis erőforrásokba való bejelentkezéshez és konfiguráláshoz.

- [Tudnivalók az emelt szintű hozzáférésű munkaállomásokkal kapcsolatban](/security/compass/privileged-access-devices)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: A rendszergazdai fiókokból származó gyanús tevékenységek naplózása és riasztása

**Útmutató:** A Azure Active Directory (Azure AD) Privileged Identity Management naplók és riasztások generálása, ha gyanús vagy nem biztonságos tevékenység történik a környezetben. Az Azure AD kockázatészlelései segítségével megtekintheti a kockázatos felhasználói viselkedéssel kapcsolatos riasztásokat és jelentéseket. További naplózáshoz küldjön Azure Security Center riasztásokat a Azure Monitor és konfigurálja az egyéni riasztásokat/értesítéseket műveletcsoportok használatával.

- [Az Azure AD kockázatészlelései](../active-directory/identity-protection/overview-identity-protection.md)

- [Műveletcsoportok konfigurálása egyéni riasztáshoz és értesítéshez](../azure-monitor/alerts/action-groups.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató:** A feltételes hozzáférés elnevezett helyei segítségével csak az IP-címtartományok vagy országok/régiók meghatározott logikai csoportosításai számára engedélyezi a hozzáférést.

- [Elnevezett helyek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="39-use-azure-active-directory"></a>3.9: A Azure Active Directory

**Útmutató:** A Azure Active Directory (Azure AD) az Azure-erőforrások, például a Event Hubs. Ez lehetővé teszi az Azure szerepköralapú hozzáférés-vezérlését (Azure RBAC) a rendszergazdai szempontból érzékeny erőforrásokhoz.

- [ Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Erőforrások hozzáférésének Event Hubs Azure AD használatával](authorize-access-azure-active-directory.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: A felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató:** Azure Active Directory (Azure AD) naplókat biztosít az elavult fiókok felderítésének segítésekor. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férnek hozzá.

Emellett rendszeresen váltogatja a Event Hubs közös hozzáférésű jogosultságaikra.

- [Az Azure AD-jelentéskészítés](../active-directory/reports-monitoring/index.yml)

- [Az Azure Identity Hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

- [A közös hozzáférésű jogosultságok Event Hubs](authenticate-shared-access-signature.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Az inaktivált hitelesítő adatok elérésére tett kísérletek figyelése

**Útmutató:** Hozzáféréssel rendelkezik az Azure Active Directory (Azure AD) bejelentkezési tevékenységéhez, naplózási és kockázati eseménynapló-forrásaihoz, amelyek lehetővé teszik az integrációt bármely harmadik fél rendszerinformációs és eseménykezelési megoldásával vagy egy monitorozási eszközzel.

Ezt a folyamatot leegyszerűsítheti, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókjaihoz, és elküldi az auditnaplókat és a bejelentkezési naplókat egy Log Analytics-munkaterületre. A kívánt naplóriasztásokat a Log Analyticsben konfigurálhatja.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Erőforrások hozzáférésének Event Hubs Azure AD használatával](authorize-access-azure-active-directory.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltéréséről

**Útmutató:** Az Identity Protection és a kockázatészlelési funkciók az Azure Active Directory-ban (Azure AD) segítségével konfigurálhatja az automatikus válaszokat a felhőben engedélyezett Event Hubs gyanús műveletekre. A szervezet biztonsági válaszait Azure Sentinel automatikus válaszokat kell engedélyeznie.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati szabályzatok konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Hozzáférés biztosítanak a Microsoftnak a releváns ügyféladatokhoz a támogatási forgatókönyvek során

**Útmutató:** Jelenleg nem érhető el; Ügyfélszéf még nem támogatott a Event Hubs.

- [A Ügyfélszéf szolgáltatások listája](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Bizalmas adatok leltárának fenntartása

**Útmutató:** A bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások Event Hubs az erőforrások címkéinek használatával.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Bizalmas adatokat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** Implementálja a különálló előfizetéseket, igény szerint felügyeleti csoportokkal a fejlesztéshez, a teszteléshez és az éles környezethez. Event Hubs névtereket virtuális hálózattal kell elválasztani úgy, hogy a szolgáltatásvégpontokat engedélyezve és megfelelően címkézték meg.

A névteret tűzfalak Azure Event Hubs is biztosíthatja. Azure Event Hubs támogatja az IP-alapú hozzáférés-vezérlést a bejövő tűzfalak támogatásához. A tűzfalszabályokat az azure-beli virtuális Azure Portal, Azure Resource Manager vagy az Azure CLI vagy a Azure PowerShell.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [A Management Groups](../governance/management-groups/create-management-group-portal.md)

- [IP-tűzfalszabályok konfigurálása Azure Event Hubs névterek számára](event-hubs-ip-filtering.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Új Virtual Network](../virtual-network/quick-create-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Bizalmas adatok jogosulatlan átvitelének figyelése és blokkolása

**Útmutató:** Ha virtuális gépeket használ az eseményközpontok eléréséhez, használjon virtuális hálózatokat, szolgáltatásvégpontokat, Event Hubs tűzfalat, hálózati biztonsági csoportokat és szolgáltatáscímkéket az adatszivárgás esélyének csökkentése érdekében.

A Microsoft kezeli a mögöttes infrastruktúrát a Azure Event Hubs és szigorú vezérlőket valósított meg az ügyféladatok elvesztésének vagy kitettségének megakadályozására.

- [IP-tűzfalszabályok konfigurálása Azure Event Hubs névterek számára](event-hubs-ip-filtering.md)

- [A Virtual Network végpontok és a Azure Event Hubs](event-hubs-service-endpoints.md)

- [Integráció Azure Event Hubs és Azure Private Link](private-link-service.md)

- [A hálózati biztonsági csoportok és a szolgáltatáscímkék](../virtual-network/network-security-groups-overview.md)

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Aktív felderítési eszköz használata a bizalmas adatok azonosításához

**Útmutató:** Az adatazonosítási, besorolási és adatveszteség-megelőzési funkciók még nem érhetők el a Azure Event Hubs. Ha megfelelőségi célokból szükség van rá, implementálja a harmadik féltől származó megoldást.

A Microsoft által felügyelt mögöttes platform esetén a Microsoft minden ügyféltartalmat bizalmasként kezel, és nagy terjedelmüknek megfelelő védelmet biztosít az ügyfelek adatvesztése és -kitettsége ellen. Annak érdekében, hogy az Azure-beli ügyféladatok biztonságban maradnak, a Microsoft robusztus adatvédelmi vezérlőket és képességeket valósított meg és tart karban.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Az erőforrásokhoz való hozzáférés vezérlése szerepköralapú hozzáférés-vezérléssel

**Útmutató:** Azure Event Hubs támogatja a Azure Active Directory (Azure AD) az erőforrásokhoz Event Hubs kérelmeket. Az Azure AD-val azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC) adhat engedélyeket egy rendszerbiztonsági tagnak, amely lehet felhasználó vagy alkalmazás-szolgáltatásnév.

- [Az Azure RBAC és az elérhető szerepkörök Azure Event Hubs](authorize-access-azure-active-directory.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas adatok titkosítása

**Útmutató:** Azure Event Hubs támogatja az adattitkosítást a Microsoft által felügyelt vagy az ügyfél által kezelt kulcsokkal. Ezzel a funkcióval létrehozhatja, átforgathatja, letilthatja és visszavonhatja az ügyfél által felügyelt kulcsokhoz való hozzáférést, amelyek az Azure Event Hubs adatok titkosítására használhatók.

- [Ügyfél által kezelt kulcsok konfigurálása a titkosítási Azure Event Hubs](configure-customer-managed-key.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Kritikus fontosságú Azure-erőforrások változásainak naplózása és riasztása

**Útmutató:** Azure Monitor Azure-tevékenységnaplóval használva riasztásokat hozhat létre, amikor változásokra kerül sor az éles példányok Azure Event Hubs kritikus vagy kapcsolódó erőforrásokon.

- [Riasztások létrehozása az Azure-tevékenységnapló eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Azure biztonsági teljesítményteszt: Leltár és eszközkezelés.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Automatizált eszközfelderítési megoldás használata

**Útmutató:** A Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetések összes erőforrását (beleértve Azure Event Hubs névtereket is). Győződjön meg arról, hogy rendelkezik a megfelelő (olvasási) engedélyekkel a bérlőben, és képes enumerálni az összes Azure-előfizetést és az előfizetésen belüli erőforrásokat.

- [Lekérdezések létrehozása Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Az Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription)

- [Az Azure RBAC](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="62-maintain-asset-metadata"></a>6.2: Objektum metaadatainak karbantartása

**Útmutató:** Címkék alkalmazása Azure-erőforrásokra, amelyek metaadatokat adva logikailag rendszerezik őket egy taxonómiában.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Nem engedélyezett Azure-erőforrások törlése

**Útmutató:** A névterek és a kapcsolódó erőforrások rendszerezéséhez és nyomon követéséhez szükség szerint használjon címkézést, Azure Event Hubs felügyeleti csoportokat és különálló előfizetéseket. Rendszeresen összeegyezteti a leltárat, és gondoskodik arról, hogy a jogosulatlan erőforrások időben törlődjenek az előfizetésből.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [A Management Groups](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Nem jóváhagyott Azure-erőforrások figyelése

**Útmutató:** Azure Policy az alábbi beépített szabályzatdefiníciók használatával az ügyfél-előfizetésben létre lehet hozható erőforrások típusára vonatkozó korlátozásokat:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Emellett a Azure Resource Graph erőforrásokat is lekérdezhet/felderíthet az előfizetésen belül.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="69-use-only-approved-azure-services"></a>6.9: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató:** Azure Policy az alábbi beépített szabályzatdefiníciók használatával az ügyfél-előfizetésben létre lehet hozható erőforrások típusára vonatkozó korlátozásokat:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

További információk a hivatkozott hivatkozásokon érhetők el.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Korlátozza a felhasználók számára a Azure Resource Manager

**Útmutató:** Az Azure feltételes hozzáférés konfigurálásával korlátozhatja a felhasználók Azure Resource Manager-hozzáférését a "hozzáférés blokkolása" beállításával a "Microsoft Azure Management" alkalmazáshoz.

- [Feltételes hozzáférés konfigurálása a hozzáférés letilt Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információ: [Azure biztonsági teljesítményteszt: Biztonságos konfiguráció.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató:** Szabványos biztonsági konfigurációkat határozhat meg és valósíthat meg Azure Event Hubs üzemelő példányok számára. A Azure Policy a **Microsoft.EventHub-névtérben** található aliasokkal egyéni szabályzatokat hozhat létre a konfigurációk naplózása vagy kényszerítés érdekében. Emellett beépített szabályzatdefiníciókat is használhat a Azure Event Hubs például:

- Az eseményközpontban engedélyezni kell a diagnosztikai naplókat

- Az eseményközpontnak virtuális hálózati szolgáltatásvégpontot kell használnia

További információk a hivatkozott hivatkozásokon érhetők el.

- [Az Azure beépített szabályzata Event Hubs névtérhez](../governance/policy/samples/built-in-policies.md#event-hub)

- [Az elérhető aliasok Azure Policy megtekintése](/powershell/module/az.resources/get-azpolicyalias)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Biztonságos Azure-erőforráskonfigurációk fenntartása

**Útmutató:** A [deny] Azure Policy és a [deploy if not exist] ([Üzembe helyezés, ha nem létezik]) hatásokkal kényszerítheti a biztonságos beállításokat a Event Hubs engedélyezett erőforrásokon. 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

 
- [További információ a Azure Policy hatásairól](../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Konfigurációkezelési eszközök üzembe helyezése Azure-erőforrásokhoz

**Útmutató:** Azure Policy a "Microsoft.EventHub" névtérben található aliasok használatával létrehozhat egyéni szabályzatokat a rendszerkonfigurációk riasztására, naplózására és kényszerítésére. Emellett dolgozzon ki egy folyamatot és folyamatot a szabályzati kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Automatizált konfigurációfigyelés megvalósítása Azure-erőforrásokhoz

**Útmutató:** Azure Policy a "Microsoft.EventHub" névtérben található aliasok használatával létrehozhat egyéni szabályzatokat a rendszerkonfigurációk riasztására, naplózására és kényszerítésére. A Azure Policy [audit], [deny] és [deploy if not exist] hatásokkal automatikusan kényszerítheti a konfigurációkat a Azure Event Hubs és a kapcsolódó erőforrásokhoz.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-beli titkos kulcsok biztonságos kezelése

**Útmutató:** Az Azure App Service-on futó Azure-beli virtuális gépek vagy webalkalmazások eseményközpontok elérésére való használata esetén a Managed Service Identity és az Azure Key Vault együttes használatával egyszerűsítheti és biztosíthatja a közös hozzáférésű jogosultságok aláírásának kezelését a Azure Event Hubs üzemelő példányai számára. Győződjön Key Vault, hogy az engedélyezett soft-delete beállítással van konfigurálva.

- [Felügyelt identitás hitelesítése Azure Active Directory (Azure AD) használatával a Event Hubs eléréséhez](./authenticate-managed-identity.md?tabs=latest)

- [Felhasználó által kezelt kulcsok konfigurálása Event Hubs](configure-customer-managed-key.md)

- [Az Azure Managed Identities integrálása](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Új Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Felügyelt identitással Key Vault hitelesítés engedélyezése](../key-vault/general/assign-access-policy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identitások biztonságos és automatikus kezelése

**Útmutató:** Az Azure App Service-on futó Azure-beli virtuális gépek vagy webalkalmazások esetében a Managed Service Identity és az Azure Key Vault együttes használatával egyszerűsítheti és biztosíthatja a Azure Event Hubs. Győződjön Key Vault, hogy az engedélyezett soft-delete beállítással van konfigurálva.

A Managed Identities használatával automatikusan felügyelt identitást nyújthat az Azure-szolgáltatásoknak a Azure Active Directory (Azure AD) szolgáltatásban. A felügyelt identitások lehetővé teszik az Azure AD-hitelesítést támogató bármely szolgáltatásban történő hitelesítést, beleértve a Azure Key Vault szolgáltatást is, anélkül, hogy hitelesítő adatokat ad meg a kódban.

- [Felügyelt identitás hitelesítése az Azure AD-val a Event Hubs eléréséhez](./authenticate-managed-identity.md?tabs=latest)

- [Felhasználó által kezelt kulcsok konfigurálása Event Hubs](configure-customer-managed-key.md)

- [Felügyelt identitások konfigurálása](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Az Azure Managed Identities integrálása](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: A hitelesítő adatok nem szándékolt kitettségének megszüntetése

**Útmutató:** Hitelesítőadat-ellenőrző implementálja a hitelesítő adatokat a kódban. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

- [Hitelesítőadat-ellenőrző beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információ: [Azure biztonsági teljesítményteszt: Kártevők elleni védelem.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: A nem számítási Azure-erőforrásokba feltölteni szükséges fájlok előzetes beolvasása

**Útmutató:** A nem számítási Azure-erőforrásokra ( például Azure Event Hubs, App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL stb. feltöltött tartalmak előzetes beolvasása. A Microsoft ezekben az esetekben nem fér hozzá az Ön adataihoz.

A Microsoft kártevőirtó engedélyezve van a mögöttes gazdagépen, amely támogatja az Azure-szolgáltatásokat (például Azure Cache for Redis), de nem fut az ügyféltartalmakon.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Azure biztonsági teljesítményteszt: Adat-helyreállítás.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatikus biztonságimentek biztosítása

**Útmutató:** Geo-vészhelyreállítás konfigurálása Azure Event Hubs. Amikor teljes Azure-régiók vagy adatközpontok (ha nincsenek rendelkezésre állási zónák használatban) állásidőt tapasztalnak, kritikus fontosságú, hogy az adatfeldolgozás továbbra is egy másik régióban vagy adatközpontban működjon. Ezért a georeplikáció és a georeplikáció fontos szolgáltatás bármely vállalat számára. Azure Event Hubs a földrajzi vészhelyreállítást és a georeplikációt is támogatja a névtér szintjén. 

- [A vészhelyreállítás földrajzi vészhelyreállításának Azure Event Hubs](./event-hubs-geo-dr.md#availability-zones)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszer-biztonsági mentések és az ügyfelek által kezelt kulcsok biztonsági mentése

**Útmutató:** Azure Event Hubs az Azure Storage Service Encryption (Azure SSE) használatával titkosítja az Storage Service Encryption adatait. Event Hubs az Azure Storage-ban tárolja az adatokat, és alapértelmezés szerint az Azure Storage-ban tárolt összes adat a Microsoft által felügyelt kulcsokkal van titkosítva. Ha ügyfél által Azure Key Vault kulcsok tárolására használja, gondoskodjon a kulcsok rendszeres, automatikus biztonsági mentésének biztosításáról.

Gondoskodjon a titkos kulcsok rendszeres Key Vault biztonsági mentéséhez a következő PowerShell-paranccsal: Backup-AzKeyVaultSecret

- [Ügyfél által kezelt kulcsok konfigurálása az Azure Event Hubs adatok titkosításához](configure-customer-managed-key.md)

- [Titkos kulcsok Key Vault biztonsági mentése](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató:** Az ügyfél által kezelt biztonságimentett kulcsok visszaállításának tesztelése.

- [Kulcstartókulcsok visszaállítása az Azure-ban](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági másolatok és az ügyfél által kezelt kulcsok védelmének biztosítása

**Útmutató:** Engedélyezze a szoftverben a Key Vault, hogy megvédje a kulcsokat a véletlen vagy rosszindulatú törléstől. Azure Event Hubs felhasználó által kezelt kulcsokhoz a Soft Delete és a Do Not Purge (Nem végleges törlés) beállítás van konfigurálva.

Konfigurálja a szoftveres adatok rögzítéséhez használt Azure Storage-fiók Event Hubs törlését. Vegye figyelembe, hogy ez a funkció még nem támogatott Azure Data Lake Storage Gen 2-ben.

- [A soft delete engedélyezése a Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Kulcstartó beállítása](configure-customer-managed-key.md)

- [Az Azure Storage-blobok helyreállítható törlése](/azure/storage/blobs/soft-delete-blob-overview)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Incidensválasz-útmutató létrehozása

**Útmutató:** Győződjön meg arról, hogy vannak olyan írásos incidenskezelési tervek, amelyek meghatározzák a személyzet szerepköreit, valamint az incidenskezelés/-kezelés fázisát.

- [Munkafolyamat-automatizálások konfigurálása a Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és -rangsorolási eljárás létrehozása

**Útmutató:** Security Center súlyosságot rendel a riasztáshoz, hogy segítsen priorizálni az egyes riasztások kiosztásának sorrendjét, így ha egy erőforrás biztonsága sérül, azonnal elérhető lesz. A súlyosság azon alapul, hogy Security Center mennyire magabiztos a riasztás kiadásához használt eredményben vagy elemzésben, valamint a megbízhatósági szinten, hogy a riasztáshoz vezető tevékenység mögött rosszindulatú szándék volt-e.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="103-test-security-response-procedures"></a>10.3: Biztonsági reagálási eljárások tesztelése

**Útmutató:** Gyakorlatok a rendszerek incidensválasz-képességeinek rendszeres teszteléséhez. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [Tekintse meg az NIST kiadványát: Útmutató az IT-tervekhez és -képességekhez: Útmutató a teszteléshez, a betanításhoz és a gyakorlathoz programokhoz](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidens kapcsolattartási adatait, és konfigurálja a riasztási értesítéseket a biztonsági incidensek esetén

**Útmutató:** A Biztonsági incidensek kapcsolattartási adatait a Microsoft arra használja, hogy kapcsolatba lépjen Önnel, ha az Microsoft Biztonsági reagálási központ (MSRC) azt tapasztalja, hogy az ügyfél adatait illetéktelen vagy jogosulatlan fél férte hozzá.  Tekintse át az incidenseket a tény után, és győződjön meg arról, hogy a problémák megoldódnak. 

- [A biztonsági kapcsolattartó Azure Security Center beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidensválasz-rendszerbe

**Útmutató:** A Azure Security Center riasztások és javaslatok exportálása a Folyamatos exportálás funkcióval. A folyamatos exportálás lehetővé teszi, hogy manuálisan vagy folyamatosan exportálja a riasztásokat és javaslatokat. A riasztásokat a Azure Security Center adat-összekötővel streamelheti a Azure Sentinel.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: A biztonsági riasztások válaszának automatizálása

**Útmutató:** A Azure Security Center munkafolyamat-automatizálási funkcióját használhatja a biztonsági riasztások és javaslatok "Logic Apps" funkcióval történő automatikus aktiválásához.

- [A munkafolyamat-automatizálás és -Logic Apps](../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információ: [Azure biztonsági teljesítményteszt: Behatolási tesztek és Red Team-gyakorlatok.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Az Azure-erőforrások rendszeres behatolástesztelése és az összes kritikus fontosságú biztonsági eredmény szervizelésének biztosítása

**Útmutató:** Kövesse a Microsoft Cloud behatolástesztelési szabályait annak ellenőrzéséhez, hogy a behatolási tesztek nem sértik-e meg a Microsoft szabályzatát. A Microsoft red teaming és élő webhely-behatolási tesztek a Microsoft által felügyelt felhőinfra infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett stratégiájának és végrehajtásának használata. 

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](../security/benchmarks/overview.md) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)
