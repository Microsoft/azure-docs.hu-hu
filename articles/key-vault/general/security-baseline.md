---
title: Key Vault Azure biztonsági alapterve
description: A Key Vault biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 305fdd3a0b8e0c876924c6e1f090424e67571af0
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968705"
---
# <a name="azure-security-baseline-for-key-vault"></a>Key Vault Azure biztonsági alapterve

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 1,0-es verziójáról](../../security/benchmarks/overview-v1.md) Key Vaultre vonatkozó útmutatást alkalmaz. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Key Vault vonatkozó kapcsolódó útmutatás. Az Key Vaultre nem alkalmazható **vezérlők** , vagy amelyek esetében a felelősség a Microsoft által lett kizárva.

Ha szeretné megtekinteni, hogyan Key Vault teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Key Vault biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: Azure Key Vault integrálása az Azure Private-hivatkozással. Az Azure Private link Service lehetővé teszi az Azure-szolgáltatások (például Azure Key Vault) és az Azure által üzemeltetett ügyfél/partner szolgáltatások elérését a virtuális hálózat privát végpontján keresztül.

Az Azure Private-végpontok olyan hálózati adapterek, amelyek az Azure Private-kapcsolaton keresztül csatlakoznak a szolgáltatáshoz. A privát végpont egy magánhálózati IP-címet használ a VNet, és hatékonyan hozza a szolgáltatást a VNet. A szolgáltatás felé irányuló összes forgalom a privát végponton keresztül irányítható, így nincs szükség átjáróra, NAT-eszközre, ExpressRoute vagy VPN-kapcsolatra, vagy nyilvános IP-címekre. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Kapcsolódhat egy Azure-erőforrás egy példányához, amely a legmagasabb szintű részletességet nyújtja a hozzáférés-vezérlésben.

- [Key Vault integrálása az Azure Private-hivatkozással](/azure/key-vault/private-link-service)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/azure/governance/policy/samples/azure-security-benchmark) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/azure/security-center/security-center-recommendations)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/azure/security-center/azure-defender) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft.** kulcstartó:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: a Azure Security Center használata és a hálózati védelmi javaslatok követése a Key Vault által konfigurált erőforrások biztonságossá tételéhez az Azure-ban. 

- [További információ a Azure Security Center által biztosított hálózati biztonságról](../../security-center/security-center-network-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártevő IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: a Key Vault-példányokhoz társított Azure-beli virtuális hálózatok Azure DDoS Protection szabványának engedélyezése az elosztott szolgáltatásmegtagadási támadások elleni védelem érdekében. A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel.

 
- [Azure DDoS Protection standard kezelése a Azure Portal használatával](/azure/virtual-network/manage-ddos-protection)

- [Veszélyforrások észlelése az Azure szolgáltatási rétegében Azure Security Center](/azure/security-center/security-center-alerts-service-layer)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: ezt a követelményt a Azure Key Vault komplex veszélyforrások elleni védelem (ATP) konfigurálásával lehet kielégíteni. Az ATP további biztonsági intelligencia réteget biztosít. Ez az eszköz észleli a Azure Key Vault fiókok elérését vagy kiaknázását okozó potenciálisan káros kísérleteket.

Amikor Azure Security Center észleli a rendellenes tevékenységeket, riasztásokat jelenít meg. Emellett e-mailben elküldi az előfizetés-rendszergazdának a gyanús tevékenységek részleteit, és javaslatokat tesz az azonosított fenyegetések kivizsgálására és szervizelésére.

- [Az Advanced Threat Protection beállítása az Azure Key Vaulthoz](/azure/security-center/advanced-threat-protection-key-vault)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: a Azure Key Vault példányokhoz hozzáférést igénylő erőforrásokhoz használja az Azure-szolgáltatás címkéit a Azure Key Vault számára a hálózati biztonsági csoportokon vagy Azure Firewall a hálózati hozzáférés-vezérlés definiálásához. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például ApiManagement), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

- [Azure-szolgáltatás címkék – áttekintés](../../virtual-network/service-tags-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: a Azure Key Vault-példányokhoz társított hálózati erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. kulcstartó" és a "Microsoft. Network" névterekben, hogy egyéni szabályzatokat hozzon létre a Azure Key Vault példányok hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Használhatja a Azure Key Vaulthoz kapcsolódó beépített szabályzat-definíciókat is, például a következőket:
- Key Vault virtuális hálózati szolgáltatás végpontját kell használnia

További információkat az alábbi hivatkozásokon találhat:

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy-minták](/azure/governance/policy/samples)

- [Terv meghatározása és hozzárendelése a portálon](../../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: a metaadatok és a logikai szervezet számára a Azure Key Vault-példányok hálózati biztonságával és forgalmával kapcsolatos erőforrásokhoz használható címkék használata.

A címkézéshez kapcsolódó beépített Azure Policy definíciók, például a "címke és a hozzá tartozó érték megkövetelése" kifejezés használatával biztosíthatja, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a címkézetlen erőforrásokról.

A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon.

- [Címkék használata az Azure-erőforrások rendszerezéséhez](/azure/azure-resource-manager/resource-group-using-tags)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata a hálózati erőforrás-konfigurációk figyelésére és a Azure Key Vault-példányokhoz kapcsolódó hálózati erőforrások változásainak észlelésére. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](/azure/azure-monitor/platform/activity-log-view)

- [Műveletnapló-riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával](/azure/azure-monitor/platform/alerts-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: naplók beolvasása Azure monitor segítségével a Azure Key Vault által generált biztonsági adatokat összesítve. A Azure Monitoron belül az Azure Log Analytics-munkaterülettel kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat a hosszú távú/archiválási tároláshoz. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et. 

- [Az Azure Key Vault naplózása](/azure/key-vault/key-vault-logging)

- [Rövid útmutató: a fedélzeti Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a diagnosztikai beállítások engedélyezése a Azure Key Vault példányokon a naplózási, biztonsági és diagnosztikai naplókhoz való hozzáféréshez. A automatikusan elérhető tevékenység-naplók közé tartozik az eseményforrás, a dátum, a felhasználó, az időbélyeg, a forráscím, a célcím és más hasznos elemek.

- [Az Azure Key Vault naplózása](/azure/key-vault/key-vault-logging)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/azure/governance/policy/samples/azure-security-benchmark) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/azure/security-center/security-center-recommendations)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/azure/security-center/azure-defender) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft.** kulcstartó:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 2.3](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: a Azure monitoron belül a Azure Key Vault-naplók tárolására szolgáló log Analytics munkaterülethez a szervezet megfelelőségi szabályainak megfelelően állítsa be a megőrzési időszakot. Használja az Azure Storage-fiókokat hosszú távú/archiválási tároláshoz.

- [Az adatmegőrzési időtartam módosítása](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: a rendellenes viselkedésre vonatkozó naplók elemzése és figyelése, valamint a Azure Key Vault által védett erőforrások eredményeinek rendszeres áttekintése. Az Azure Monitor Log Analytics munkaterülete segítségével tekintheti át a naplókat, és lekérdezéseket végezhet a naplózási adatokon. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et. 

- [A fedélzeti Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Ismerkedés a Log Analyticsával Azure Monitor](/azure/azure-monitor/log-query/get-started-portal)

- [Ismerkedés az Azure Monitor-naplólekérdezésekkel](/azure/azure-monitor/log-query/get-started-queries)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: a Azure Security Center a komplex veszélyforrások elleni védelem (ATP) engedélyezése a Key Vaulthoz. A Azure Key Vault diagnosztikai beállításainak engedélyezése és a naplók elküldése egy Log Analytics munkaterületre. A Log Analytics-munkaterületet az Azure Sentinelbe irányíthatja, mivel ez egy biztonsági előkészítési automatizált választ (felszárnyaló) megoldást biztosít. Ez lehetővé teszi a forgatókönyvek (automatizált megoldások) létrehozását és a biztonsági problémák megoldására való felhasználását.

- [Gyorsútmutató: Az Azure Sentinel előkészítése](../../sentinel/quickstart-onboard.md)

- [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](../../security-center/security-center-managing-and-responding-alerts.md)

- [Eseményekre való válaszadás Azure Monitor-riasztásokkal](/azure/azure-monitor/learn/tutorial-response)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: a Azure Active Directory (Azure ad) által regisztrált alkalmazások leltárának fenntartása, valamint a Azure Key Vault kulcsok, titkos kódok és tanúsítványok hozzáférését biztosító felhasználói fiókok. A Key Vault-hozzáférés lekérdezéséhez és egyeztetéséhez használhatja a Azure Portal vagy a PowerShellt is. A PowerShell elérésének megtekintéséhez használja a következő parancsot:

(Get-AzResource-ResourceId [KeyVaultResourceID]). Properties. AccessPolicies

- [Alkalmazás regisztrálása az Azure AD-ben](/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory)

- [Biztonságos hozzáférés egy kulcstartóhoz](secure-your-key-vault.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a Azure Key Vault példányokhoz hozzáféréssel rendelkező dedikált rendszergazdai fiókok használatával. Az aktív rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelési (jelenleg előzetes verzióban elérhető) használatát.

- [Identitás és hozzáférés figyelése (előzetes verzió)](../../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory egyszeri bejelentkezés (SSO) használata

**Útmutató**: az alkalmazás zökkenőmentes hitelesítéséhez és a Azure Key Vault titkos kódok eléréséhez használni kívánt jogkivonat lekéréséhez használja az Azure egyszerű szolgáltatását a AppID, a TenantID és a ClientSecret együtt.

- [Szolgáltatások közötti hitelesítés Azure Key Vault .NET használatával](/azure/key-vault/service-to-service-authentication)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: engedélyezze az Azure Active Directory (Azure ad) többtényezős hitelesítését, és kövesse a Azure Security Center identitás-és hozzáférés-kezelési (jelenleg előzetes verzióban elérhető) javaslatokat az Event hub-kompatibilis erőforrások védelméhez.

- [Azure AD Multi-Factor Authentication üzemelő példány megtervezése](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése (előzetes verzió)](../../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: biztonságos, Azure által felügyelt munkaállomások használata felügyeleti feladatokhoz

**Útmutató**: a rendszerjogosultságú hozzáférési munkaállomás (Paw) használata az Azure ad többtényezős hitelesítéssel, amely a Key Vault engedélyezett erőforrások bejelentkezésére és konfigurálására van konfigurálva.

- [Emelt hozzáférési szintű munkaállomások](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Felhőalapú Azure AD többtényezős hitelesítés üzembe helyezésének megtervezése](../../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutatás**: Azure Active Directory (Azure AD) PRIVILEGED Identity Management (PIM) használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik. Az Azure AD-kockázati észlelések használatával a kockázatos felhasználói viselkedésre vonatkozó riasztásokat és jelentéseket tekinthet meg. További naplózáshoz küldje el Azure Security Center kockázatkezelési riasztásokat a Azure Monitorba, és konfigurálja az egyéni riasztásokat/értesítéseket a műveleti csoportok használatával.

A komplex veszélyforrások elleni védelem (ATP) engedélyezése a Azure Key Vault számára a gyanús tevékenységekre vonatkozó riasztások létrehozásához.

- [Azure AD Privileged Identity Management (PIM) üzembe helyezése](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Komplex veszélyforrások elleni védelem beállítása a Azure Key Vaulthoz (előzetes verzió)](/azure/security-center/advanced-threat-protection-key-vault)

- [Riasztások Azure Key Vault (előzetes verzió)](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv)

- [Azure AD-kockázati észlelések](/azure/active-directory/reports-monitoring/concept-risk-events)

- [Műveletcsoportok létrehozása és felügyelete az Azure Portalon](/azure/azure-monitor/platform/action-groups)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférési szabályzat tárolási állapotának konfigurálása és az elnevezett helyek kezelése. Az elnevezett helyszínekkel logikai csoportosításokat hozhat létre az IP-címtartományok vagy országok és régiók számára. A konfigurált elnevezett helyekre korlátozhatja a bizalmas erőforrásokhoz, például a Key Vault titkos kódokhoz való hozzáférést.

- [Mi a hely feltétele Azure Active Directory (Azure AD) feltételes hozzáférésben?](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerként az Azure-erőforrások, például a Key Vault számára. Ez lehetővé teszi az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) számára a bizalmas erőforrások felügyeletét.

- [Új bérlő létrehozása az Azure AD-ben](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: Tekintse át Azure Active Directory (Azure ad) naplókat, amelyek segítenek az elavult fiókok Azure Key Vault rendszergazdai szerepkörökkel való felderítésében. Emellett az Azure AD hozzáférési felülvizsgálatok segítségével hatékonyan kezelheti a csoporttagságok kezelését, hozzáférhet a Azure Key Vaulthoz és a szerepkör-hozzárendelésekhez használható vállalati alkalmazásokhoz. A felhasználók hozzáférését rendszeresen felül kell vizsgálni, például 90 naponta, hogy csak a megfelelő felhasználók férhessenek hozzájuk.

- [Azure AD-jelentések és-figyelési dokumentáció](/azure/active-directory/reports-monitoring/)

- [Mik azok az Azure AD-hozzáférési felülvizsgálatok?](../../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: a Azure Key Vault és Azure Active Directory (Azure ad) diagnosztikai beállításainak engedélyezése, az összes napló küldése egy log Analytics munkaterületre. A kívánt riasztások (például a letiltott titkok elérésére tett kísérletek) konfigurálása Log Analyticson belül.

- [Azure AD-naplók integrálása Azure Monitor naplókkal](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Áttelepítés a régi Key Vault megoldásból](/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: az Azure Active Directory (Azure ad) identitás-és kockázati észlelési funkcióinak használatával konfigurálhatja az Azure Key Vault védett erőforrásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. A szervezet biztonsági válaszainak megvalósításához engedélyeznie kell az automatikus válaszokat az Azure Sentinel használatával.

- [Kockázatos bejelentkezések jelentés az Azure AD-portálon](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Útmutató: kockázatkezelési szabályzatok konfigurálása és engedélyezése](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: az olyan Azure-erőforrások nyomon követéséhez, amelyek bizalmas információkat tárolnak vagy dolgoznak fel Azure Key Vault engedélyezett erőforrásokon, használja a címkéket. 

- [Címkék használata az Azure-erőforrások rendszerezéséhez](/azure/azure-resource-manager/resource-group-using-tags)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: az Azure Key Vaulthoz való hozzáférés biztosítása érdekében a virtuális hálózati szolgáltatás végpontjai úgy vannak konfigurálva, hogy korlátozzák az egyes alhálózatokhoz való hozzáférést.

A tűzfalszabályok érvénybe léptetése után csak akkor hajthat végre Azure Key Vault adatsík-műveleteket, ha a kérelme engedélyezett alhálózatokból vagy IP-címtartományokból származik. Ez a Azure Portal Azure Key Vault hozzáférésére is vonatkozik. Bár a Azure Portal is tallózhat a kulcstartóban, előfordulhat, hogy nem tudja listázni a kulcsokat, titkokat vagy tanúsítványokat, ha az ügyfélszámítógép nem szerepel az engedélyezett listán. Ez hatással van a Azure Key Vault választóra és az egyéb Azure-szolgáltatásokra is. Előfordulhat, hogy látni fogja a kulcstartók listáját, de nem jeleníti meg a kulcsokat, ha a tűzfalszabályok megakadályozzák az ügyfélszoftvert.

- [Azure Key Vault tűzfalak és virtuális hálózatok konfigurálása](/azure/key-vault/key-vault-network-security)

- [Virtuális hálózati szolgáltatásvégpontok az Azure Key Vaulthoz](/azure/key-vault/key-vault-overview-vnet-service-endpoints)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: a Azure Key Vaulton belül tárolt összes adat bizalmasnak tekintendő. A Azure Key Vault titkokhoz való hozzáférés szabályozásához használja Azure Key Vault adatsík-hozzáférés-vezérlést. A hálózati réteg elérésének vezérléséhez Key Vault beépített tűzfallal is használhatja. A Azure Key Vaulthoz való hozzáférés figyeléséhez engedélyezze Key Vault diagnosztikai beállításokat, és küldje el a naplókat egy Azure Storage-fiókba vagy Log Analytics munkaterületre.

- [Biztonságos hozzáférés egy kulcstartóhoz](secure-your-key-vault.md)

- [Azure Key Vault tűzfalak és virtuális hálózatok konfigurálása](/azure/key-vault/key-vault-network-security)

- [Az Azure Key Vault naplózása](/azure/key-vault/key-vault-logging)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés kezelése az Azure RBAC

**Útmutató**: biztonságos hozzáférés a Azure Key Vault példányok felügyeleti és adatsíkjával.

- [Biztonságos hozzáférés egy kulcstartóhoz](secure-your-key-vault.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor Azure Key Vault elemzési megoldásával áttekintheti Azure Key Vault naplózási eseménynaplóit.

- [Azure Key Vault elemzési megoldás a Azure Monitor](/azure/azure-monitor/insights/azure-key-vault)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információért lásd az [Azure biztonsági teljesítményteszt: biztonsági rés kezelése](../../security/benchmarks/security-control-vulnerability-management.md)című témakört.*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: a Azure Security Center által biztosított alapértelmezett kockázati minősítések (biztonságos pontszám) használata.

- [Javítsa a biztonságos pontszámot Azure Security Center](/azure/security-center/security-center-secure-score)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetésen belüli összes erőforrást (beleértve Azure Key Vault példányokat is). Győződjön meg arról, hogy megfelelő (olvasási) engedélyekkel rendelkezik a bérlőben, és képes felsorolni az összes Azure-előfizetést, valamint az előfizetésében lévő erőforrásokat.

- [Az első Resource Graph-lekérdezés futtatása az Azure Resource Graph Explorerrel](../../governance/resource-graph/first-query-portal.md)

- [Az aktuális fiók által elérhető előfizetések beolvasása](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkék alkalmazása olyan Azure Key Vault erőforrásokhoz, amelyekkel a metaadatok logikailag rendezhetők a besorolásba.

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és a különálló előfizetések használata, ahol szükséges, Azure Key Vault példányok és kapcsolódó erőforrások rendszerezése és nyomon követése. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

- [További Azure-előfizetés létrehozása](/azure/billing/billing-create-subscription)

- [Felügyeleti csoportok létrehozása az erőforrások rendszerezéséhez és kezeléséhez](/azure/governance/management-groups/create)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](/azure/azure-resource-manager/resource-group-using-tags)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: az Azure-szabályzatok használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetésben (k) belüli erőforrásokat.

- [Oktatóanyag: szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében](../../governance/policy/tutorials/create-and-manage.md)

- [Rövid útmutató: az első Resource Graph-lekérdezés futtatása az Azure Resource Graph Explorerrel](../../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: az Azure-szabályzatok használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

További információkat az alábbi hivatkozásokon találhat:

- [Oktatóanyag: szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy-minták](/azure/governance/policy/samples/built-in-policies#general)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek a Azure Resource Manager interakcióra az "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával. Ez megakadályozhatja az erőforrások létrehozását és módosítását a magas biztonsági környezetben, például a Key Vault konfigurációval rendelkezők számára.

- [Az Azure-felügyelethez való hozzáférés kezelése feltételes hozzáféréssel](../../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: Azure Policy aliasok használata a "Microsoft. kulcstartó" névtérben egyéni szabályzatok létrehozásához a Azure Key Vault példányok konfigurációjának naplózásához vagy érvényesítéséhez. Használhat beépített Azure Policy-definíciókat is Azure Key Vault például:

- Key Vault objektumoknak helyreállítható kell lenniük

- Key Vault diagnosztikai beállításainak központi telepítése Log Analytics munkaterületre

- A Key Vault lévő diagnosztikai naplókat engedélyezni kell

- Key Vault virtuális hálózati szolgáltatás végpontját kell használnia

- Key Vault diagnosztikai beállításainak üzembe helyezése az Event hub-ban

- A Azure Security Center javaslatainak használata biztonságos alapkonfigurációként a Azure Key Vault példányok számára.

További információkat az alábbi hivatkozásokon találhat:

- [Az elérhető Azure Policy aliasok megtekintése](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Oktatóanyag: szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében](../../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: használja a Azure Policy [megtagadás] és a [telepítés, ha nem létezik] lehetőséget a biztonságos beállítások betartatásához a Azure Key Vault-kompatibilis erőforrások között. 

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../../governance/policy/tutorials/create-and-manage.md)

  
- [Azure Policy effektusok ismertetése](../../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: ha az Azure Key Vault engedélyezett erőforrásokhoz egyéni Azure Policy definíciókat használ, az Azure Repos használatával biztonságosan tárolhatja és kezelheti a kódot.

- [Kód tárolása az Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Az Azure Repos dokumentációja](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: a "Microsoft. kulcstartó" névtérben található Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: a Azure Security Center használatával elvégezheti a Azure Key Vault által védett erőforrások alapkonfigurációjának vizsgálatát.

- [Javaslatok szervizelése Azure Security Center](../../security-center/security-center-remediate-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: a Managed Service Identity együttes használata a Azure Key Vault a Felhőbeli alkalmazások titkok kezelésének egyszerűsítése és biztonságossá tétele érdekében. Győződjön meg arról, hogy a Azure Key Vault-törlés engedélyezve van.

- [Integrálás az Azure felügyelt identitásokkal](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Key Vault létrehozása](quick-create-portal.md)

- [Hitelesítés a Key Vaultban](authentication.md)

- [Key Vault hozzáférési szabályzat kiosztása](assign-access-policy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/azure/governance/policy/samples/azure-security-benchmark) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/azure/security-center/security-center-recommendations)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/azure/security-center/azure-defender) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft.** kulcstartó:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 7.11](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-7-11.md)]

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: a Managed Service Identity együttes használata a Azure Key Vault a Felhőbeli alkalmazások titkos felügyeletének egyszerűsítése és biztonságossá tétele érdekében.

  

- [Integráció az Azure felügyelt identitásokkal](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Key Vault létrehozása](quick-create-portal.md)    

- [Hitelesítés Key Vault](authentication.md)

- [Key Vault hozzáférési szabályzat kiosztása](assign-access-policy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.  
  
- [ A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információt az [Azure biztonsági teljesítményteszt: kártevők elleni védelem](../../security/benchmarks/security-control-malware-defense.md)című témakörben talál.*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft kártevő szoftverrel való ellátása engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure Key Vault), azonban nem az ügyfél tartalmán fut.

A nem számítási Azure-erőforrásokra, például a Azure Key Vaultra feltöltött vagy azokon továbbított tartalmak előzetes vizsgálata. A Microsoft nem fér hozzá az adataihoz ezekben a példányokban.

- [Megismerheti a Microsoft antimalware-t az Azure Cloud Services és Virtual Machines](../../security/fundamentals/antimalware.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a következő PowerShell-parancsokkal biztosíthatja a Key Vault tanúsítványok, kulcsok, felügyelt tárolási fiókok és titkok rendszeres automatikus biztonsági mentését:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Szükség esetén a Key Vault biztonsági másolatait Azure Backupon belül is tárolhatja.

- [Key Vault tanúsítványok biztonsági mentése](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Key Vault kulcsok biztonsági mentése](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Key Vault felügyelt Storage-fiókok biztonsági mentése](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Key Vault titkos kódok biztonsági mentése](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

- [A Azure Backup engedélyezése](/azure/backup)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: biztonsági másolatokat készíthet a Key Vault tanúsítványokról, kulcsokról, felügyelt tárolási fiókokról és titkokról a következő PowerShell-parancsokkal:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Szükség esetén a Key Vault biztonsági másolatait Azure Backupon belül is tárolhatja.

- [Key Vault tanúsítványok biztonsági mentése](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Key Vault kulcsok biztonsági mentése](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Key Vault felügyelt Storage-fiókok biztonsági mentése](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Key Vault titkos kódok biztonsági mentése](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

- [A Azure Backup engedélyezése](/azure/backup)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: a Key Vault tanúsítványok, kulcsok, felügyelt tárolási fiókok és titkos kódok adatvisszaállításának rendszeres időközönkénti végrehajtása a következő PowerShell-parancsokkal:

- Restore-AzKeyVaultCertificate

- Restore-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Restore-AzKeyVaultSecret

További információkat az alábbi hivatkozásokon találhat:

- [Key Vault tanúsítványok visszaállítása](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Key Vault kulcsok visszaállítása](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Key Vault felügyelt Storage-fiókok visszaállítása](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Key Vault titkos kódok visszaállítása](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: Ellenőrizze, hogy engedélyezve van-e a soft delete Azure Key Vault. A Soft-delete lehetővé teszi a törölt kulcstartók és tároló objektumok, például kulcsok, titkos kódok és tanúsítványok helyreállítását. 

- [A Azure Key Vault Soft-delete használata](/azure/key-vault/key-vault-soft-delete-powershell)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/azure/governance/policy/samples/azure-security-benchmark) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/azure/security-center/security-center-recommendations)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/azure/security-center/azure-defender) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft.** kulcstartó:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 9.4](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-9-4.md)]

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig. Ezeknek a folyamatoknak összpontosítaniuk kell a bizalmas rendszerek védelmére, például a Key Vault titkokat használó rendszerekre.

- [Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül](../../security-center/security-center-planning-and-operations-guide.md)   

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)   

- [Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásával kapcsolatos támogatáshoz](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztások kijavításához használt mérőszámban, illetve a riasztást eredményező tevékenységen belül rosszindulatú szándékkal. Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben, nem gyártva), és létrehoz egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához, különösen a bizalmas adatok, például a Azure Key Vault titkok feldolgozásához.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensekre adott válaszait rendszeres ütemben tesztelheti az Azure Key Vault-példányok és a kapcsolódó erőforrások védelmének elősegítése érdekében. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és edzésprogramjának kidolgozásához](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte.  A problémák megoldása érdekében tekintse át az incidenseket a tény után.

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: a Azure Security Center riasztások és javaslatok exportálása a folyamatos exportálás funkcióval a Azure Key Vault-kompatibilis erőforrások kockázatainak azonosítása érdekében. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását.  Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba. 

 

- [Folyamatos exportálás konfigurálása](../../security-center/continuous-export.md) 

  

- [Riasztások streamelése az Azure Sentinelbe](../../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan elindíthatja a válaszokat az "Logic apps" használatával a biztonsági riasztások és a Azure Key Vault által védett erőforrások védelme érdekében javasolt javaslatok alapján. 

 

- [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért tekintse meg az [Azure biztonsági teljesítményteszt: behatolási tesztek és a Red Team gyakorlatok](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)című témakört.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft Cloud penetráció tesztelési szabályait, amelyekkel biztosíthatja, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét.

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
