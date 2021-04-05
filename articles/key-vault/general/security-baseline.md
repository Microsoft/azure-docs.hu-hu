---
title: A Key Vault Azure-os biztonsági alapkonfigurációja
description: A Key Vault Azure-os biztonsági alapkonfigurációja
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ad47ac7d51de6ab497d7061961c14b5324fab931
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98202453"
---
# <a name="azure-security-baseline-for-key-vault"></a>A Key Vault Azure-os biztonsági alapkonfigurációja

Az Key Vault Azure biztonsági alapkonfigurációja olyan javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági állapotát.

A szolgáltatás alapterve az [Azure Security Benchmark 1,0-es verziójából](../../security/benchmarks/overview.md)származik, amely javaslatokat tesz arra vonatkozóan, hogy miként védheti meg felhőalapú megoldásait az Azure-ban az ajánlott eljárásokat ismertető útmutató segítségével.

További információ: [Azure Security](../../security/benchmarks/security-baselines-overview.md)alapkonfigurációk áttekintése.

## <a name="network-security"></a>Hálózati biztonság

*További információkért lásd [: biztonsági ellenőrzés: hálózati biztonság](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: az erőforrások védelme hálózati biztonsági csoportokkal vagy Azure Firewall a Virtual Network

**Útmutató**: Azure Key Vault integrálása az Azure Private-hivatkozással. 

Az Azure Private link Service lehetővé teszi az Azure-szolgáltatások (például Azure Key Vault) és az Azure által üzemeltetett ügyfél/partner szolgáltatások elérését a virtuális hálózat privát végpontján keresztül.

Az Azure Private-végpontok olyan hálózati adapterek, amelyek az Azure Private-kapcsolaton keresztül csatlakoznak a szolgáltatáshoz. A privát végpont egy magánhálózati IP-címet használ a VNet, és hatékonyan hozza a szolgáltatást a VNet. A szolgáltatás felé irányuló összes forgalom a privát végponton keresztül irányítható, így nincs szükség átjáróra, NAT-eszközre, ExpressRoute vagy VPN-kapcsolatra, vagy nyilvános IP-címekre. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Kapcsolódhat egy Azure-erőforrás egy példányához, amely a legmagasabb szintű részletességet nyújtja a hozzáférés-vezérlésben.

Key Vault integrálása az Azure Private-hivatkozással:

https://docs.microsoft.com/azure/key-vault/private-link-service


**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: a Azure Security Center használata és a hálózati védelmi javaslatok követése a Key Vault által konfigurált erőforrások biztonságossá tételéhez az Azure-ban. 

További információ a Azure Security Center által biztosított hálózati biztonságról: 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: a Key Vault-példányokhoz társított Azure-beli virtuális hálózatok Azure DDoS Protection szabványának engedélyezése az elosztott szolgáltatásmegtagadási támadások elleni védelem érdekében. A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel.

 
Azure DDoS Protection standard kezelése a Azure Portal használatával: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Veszélyforrások észlelése a Azure Security Center Azure szolgáltatási rétegében: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: hálózati csomagok és adatforgalmi naplók rögzítése

**Útmutató**: a Azure Key Vault nem használ hálózati biztonsági csoportokat (NSG), és a flow-naplókat nem rögzíti Azure Key Vault. Ehelyett használja az Azure privát hivatkozását Azure Key Vault példányai biztonságossá tételéhez és a diagnosztikai beállítások engedélyezéséhez a mérőszámok és a naplózási események rögzítéséhez.

Key Vault integrálása az Azure Private-hivatkozással:

https://docs.microsoft.com/azure/key-vault/private-link-service

Azure Key Vault naplózás: https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati alapú behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: ezt a követelményt a Azure Key Vault komplex veszélyforrások elleni védelem (ATP) konfigurálásával lehet kielégíteni. Az ATP további biztonsági intelligencia réteget biztosít. Ez az eszköz észleli a Azure Key Vault fiókok elérését vagy kiaknázását okozó potenciálisan káros kísérleteket.

Amikor Azure Security Center észleli a rendellenes tevékenységeket, riasztásokat jelenít meg. Emellett e-mailben elküldi az előfizetés-rendszergazdának a gyanús tevékenységek részleteit, és javaslatokat tesz az azonosított fenyegetések kivizsgálására és szervizelésére.

Komplex veszélyforrások elleni védelem beállítása Azure Key Vaulthoz:

https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault



**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: a Azure Key Vault példányokhoz hozzáférést igénylő erőforrásokhoz használja az Azure-szolgáltatás címkéit a Azure Key Vault számára a hálózati biztonsági csoportokon vagy Azure Firewall a hálózati hozzáférés-vezérlés definiálásához. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például ApiManagement), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

Az Azure-szolgáltatás címkék áttekintése: https://docs.microsoft.com/azure/virtual-network/service-tags-overview


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: a Azure Key Vault-példányokhoz társított hálózati erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. kulcstartó" és a "Microsoft. Network" névterekben, hogy egyéni szabályzatokat hozzon létre a Azure Key Vault példányok hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Használhatja a Azure Key Vaulthoz kapcsolódó beépített szabályzat-definíciókat is, például a következőket:

Key Vault virtuális hálózati szolgáltatás végpontját kell használnia

Oktatóanyag: szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy minták:

https://docs.microsoft.com/azure/governance/policy/samples/#networ

Rövid útmutató: terv meghatározása és hozzárendelése a portálon:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: a metaadatok és a logikai szervezet számára a Azure Key Vault-példányok hálózati biztonságával és forgalmával kapcsolatos erőforrásokhoz használható címkék használata.

A címkézéshez kapcsolódó beépített Azure Policy definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.

A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon.

Címkék használata az Azure-erőforrások rendszerezéséhez:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata a hálózati erőforrás-konfigurációk figyelésére és a Azure Key Vault-példányokhoz kapcsolódó hálózati erőforrások változásainak észlelésére. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

Azure-Tevékenységnaplók eseményeinek megtekintése és beolvasása:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Műveletnapló-riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ [: Security Control: naplózás és figyelés](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: nem alkalmazható; A Microsoft fenntartja az Azure-erőforrásokhoz használt időforrást, például a Azure Key Vault a naplókban lévő időbélyegek esetében.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: naplók beolvasása Azure monitor segítségével a Azure Key Vault által generált biztonsági adatokat összesítve. A Azure Monitoron belül az Azure Log Analytics-munkaterülettel kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat a hosszú távú/archiválási tároláshoz. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et. 

Azure Key Vault naplózás:

https://docs.microsoft.com/azure/key-vault/key-vault-logging

Rövid útmutató: a fedélzeti Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a diagnosztikai beállítások engedélyezése a Azure Key Vault példányokon a naplózási, biztonsági és diagnosztikai naplókhoz való hozzáféréshez. A automatikusan elérhető tevékenység-naplók közé tartozik az eseményforrás, a dátum, a felhasználó, az időbélyeg, a forráscím, a célcím és más hasznos elemek.

Azure Key Vault naplózás:

https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: a Azure monitoron belül a Azure Key Vault-naplók tárolására szolgáló log Analytics munkaterülethez a szervezet megfelelőségi szabályainak megfelelően állítsa be a megőrzési időszakot. Használja az Azure Storage-fiókokat hosszú távú/archiválási tároláshoz.

Az adatok megőrzési idejének módosítása: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: a rendellenes viselkedésre vonatkozó naplók elemzése és figyelése, valamint a Azure Key Vault által védett erőforrások eredményeinek rendszeres áttekintése. Az Azure Monitor Log Analytics munkaterülete segítségével tekintheti át a naplókat, és lekérdezéseket végezhet a naplózási adatokon. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik fél SIEM-nek. 

Gyors útmutató: Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Ismerkedjen meg a Azure Monitor Log Analyticsával:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Ismerkedés a Azure Monitor-naplózási lekérdezésekkel:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: riasztások engedélyezése rendellenes tevékenységhez

**Útmutató**: a Azure Security Center a komplex veszélyforrások elleni védelem (ATP) engedélyezése a Key Vaulthoz. A Azure Key Vault diagnosztikai beállításainak engedélyezése és a naplók elküldése egy Log Analytics munkaterületre. A Log Analytics-munkaterületet az Azure Sentinelbe irányíthatja, mivel ez egy biztonsági előkészítési automatizált választ (felszárnyaló) megoldást biztosít. Ez lehetővé teszi a forgatókönyvek (automatizált megoldások) létrehozását és a biztonsági problémák megoldására való felhasználását.

Gyors útmutató: Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Biztonsági riasztások kezelése és válaszadás a Azure Security Centerban:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Válaszadás az Azure Monitor riasztásokkal rendelkező eseményekre:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: nem alkalmazható; Azure Key Vault nem dolgoz fel kártevő szoftverrel kapcsolatos naplókat, illetve nem hoz létre.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: nem alkalmazható; A Azure Key Vault nem dolgozza fel a DNS-sel kapcsolatos naplókat, és nem hoz létre.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ [: Security Control (identitás és Access Control](../../security/benchmarks/security-control-identity-access-control.md)).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: a Azure Active Directory által regisztrált alkalmazások leltárának fenntartása, valamint a Azure Key Vault kulcsok, titkos kódok és tanúsítványok hozzáférését biztosító felhasználói fiókok. A Key Vault-hozzáférés lekérdezéséhez és egyeztetéséhez használhatja a Azure Portal vagy a PowerShellt is. A PowerShell elérésének megtekintéséhez használja a következő parancsot:

(Get-AzResource-ResourceId [KeyVaultResourceID]). Properties. AccessPolicies

Alkalmazás regisztrálása a Azure Active Directory:

https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

Biztonságos hozzáférés a kulcstartóhoz:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: nem alkalmazható; Azure Key Vault nem rendelkezik az alapértelmezett jelszavakkal, mivel a hitelesítés Active Directory és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) által biztosított.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a Azure Key Vault példányokhoz hozzáféréssel rendelkező dedikált rendszergazdai fiókok használatával. Az aktív rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelési (jelenleg előzetes verzióban elérhető) használatát.

Identitás és hozzáférés figyelése (előzetes verzió):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: az alkalmazás zökkenőmentes hitelesítéséhez és a Azure Key Vault titkos kódok eléréséhez használni kívánt jogkivonat lekéréséhez használja az Azure egyszerű szolgáltatását a AppID, a TenantID és a ClientSecret együtt.

Szolgáltatások közötti hitelesítés Azure Key Vault .NET használatával:

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: engedélyezze az Azure Active Directory multi-Factor Authentication, és kövesse Azure Security Center identitás-és hozzáférés-kezelési (jelenleg előzetes verzióban elérhető) javaslatokat az Event hub-kompatibilis erőforrások védelméhez.

Felhőalapú Azure AD-Multi-Factor Authentication üzembe helyezésének megtervezése:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Identitás és hozzáférés figyelése (előzetes verzió):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: az Key Vault engedélyezett erőforrások bejelentkezéséhez és konfigurálásához KONFIGURÁLT Azure AD-multi-Factor Authentication (MFA) rendelkező, rendszerjogosultságú hozzáférési munkaállomás (Paw) használata. 

Emelt szintű hozzáférésű munkaállomások: https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/ 

Felhőalapú Azure AD-Multi-Factor Authentication üzembe helyezésének megtervezése: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutatás**: Azure Active Directory (Azure AD) PRIVILEGED Identity Management (PIM) használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik. Az Azure AD-kockázati észlelések használatával a kockázatos felhasználói viselkedésre vonatkozó riasztásokat és jelentéseket tekinthet meg. További naplózáshoz küldje el Azure Security Center kockázatkezelési riasztásokat a Azure Monitorba, és konfigurálja az egyéni riasztásokat/értesítéseket a műveleti csoportok használatával.

A komplex veszélyforrások elleni védelem (ATP) engedélyezése a Azure Key Vault számára a gyanús tevékenységekre vonatkozó riasztások létrehozásához.

Azure AD Privileged Identity Management (PIM) üzembe helyezése: https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Összetett veszélyforrások elleni védelem beállítása a Azure Key Vault (előzetes verzió): https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault

Azure Key Vault (előzetes verzió) riasztásai: https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv

Azure Active Directory kockázati észlelések: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Műveleti csoportok létrehozása és kezelése a Azure Portalban: https://docs.microsoft.com/azure/azure-monitor/platform/action-groups



**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférési szabályzat tárolási állapotának konfigurálása és az elnevezett helyek kezelése. Az elnevezett helyszínekkel logikai csoportosításokat hozhat létre az IP-címtartományok vagy országok és régiók számára. A konfigurált elnevezett helyekre korlátozhatja a bizalmas erőforrásokhoz, például a Key Vault titkos kódokhoz való hozzáférést.

Mi a hely feltétele Azure Active Directory feltételes hozzáférésben?: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations



**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerként az Azure-erőforrások, például a Key Vault számára. Ez lehetővé teszi az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) számára a bizalmas erőforrások felügyeletét.

 

Rövid útmutató: új bérlő létrehozása Azure Active Directoryban:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: Tekintse át Azure Active Directory (Azure ad) naplókat, amelyek segítenek az elavult fiókok Azure Key Vault rendszergazdai szerepkörökkel való felderítésében. Emellett az Azure AD hozzáférési felülvizsgálatok segítségével hatékonyan kezelheti a csoporttagságok kezelését, hozzáférhet a Azure Key Vaulthoz és a szerepkör-hozzárendelésekhez használható vállalati alkalmazásokhoz. A felhasználók hozzáférését rendszeresen felül kell vizsgálni, például 90 naponta, hogy csak a megfelelő felhasználók férhessenek hozzájuk.

Azure Active Directory jelentések és figyelési dokumentáció:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Mik az Azure AD hozzáférési felülvizsgálatai?:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: az inaktivált fiókok elérésére irányuló kísérletek figyelése

**Útmutató**: a Azure Key Vault és Azure Active Directory diagnosztikai beállításainak engedélyezése, az összes napló küldése egy log Analytics munkaterületre. A kívánt riasztások (például a letiltott titkok elérésére tett kísérletek) konfigurálása Log Analyticson belül.

Azure AD-naplók integrálása Azure Monitor naplókkal: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Áttelepítés a régi Key Vault megoldásról: https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution



**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a Azure Active Directory Identity Protection és kockázati észlelési funkciói segítségével konfigurálhatja az Azure Key Vault védett erőforrásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. A szervezet biztonsági válaszainak megvalósításához engedélyeznie kell az automatikus válaszokat az Azure Sentinel használatával. 

Kockázatos bejelentkezési jelentések a Azure Active Directory portálon: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

Útmutató: kockázatkezelési házirendek konfigurálása és engedélyezése: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Az Azure Sentinel előkészítése:  https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: nem alkalmazható; A Ügyfélszéf Azure Key Vault esetében nem támogatott.

Támogatott szolgáltatások és forgatókönyvek általánosan elérhetővé: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="data-protection"></a>Adatvédelem

*További információkért lásd [: biztonsági ellenőrzés:](../../security/benchmarks/security-control-data-protection.md)adatvédelem.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: az olyan Azure-erőforrások nyomon követéséhez, amelyek bizalmas információkat tárolnak vagy dolgoznak fel Azure Key Vault engedélyezett erőforrásokon, használja a címkéket. 

Címkék használata az Azure-erőforrások rendszerezéséhez: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: az Azure Key Vaulthoz való hozzáférés biztosítása érdekében a virtuális hálózati szolgáltatás végpontjai úgy vannak konfigurálva, hogy korlátozzák az egyes alhálózatokhoz való hozzáférést.

A tűzfalszabályok érvénybe léptetése után csak akkor hajthat végre Azure Key Vault adatsík-műveleteket, ha a kérelme engedélyezett alhálózatokból vagy IP-címtartományokból származik. Ez a Azure Portal Azure Key Vault hozzáférésére is vonatkozik. Bár a Azure Portal is tallózhat a kulcstartóban, előfordulhat, hogy nem tudja listázni a kulcsokat, titkokat vagy tanúsítványokat, ha az ügyfélszámítógép nem szerepel az engedélyezett listán. Ez hatással van a Azure Key Vault választóra és az egyéb Azure-szolgáltatásokra is. Előfordulhat, hogy látni fogja a kulcstartók listáját, de nem jeleníti meg a kulcsokat, ha a tűzfalszabályok megakadályozzák az ügyfélszoftvert.

Azure Key Vault tűzfalak és virtuális hálózatok konfigurálása: https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Azure Key Vault virtuális hálózati szolgáltatási végpontok: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints



**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: a Azure Key Vaulton belül tárolt összes adat bizalmasnak tekintendő. A Azure Key Vault titkokhoz való hozzáférés szabályozásához használja Azure Key Vault adatsík-hozzáférés-vezérlést. A hálózati réteg elérésének vezérléséhez Key Vault beépített tűzfallal is használhatja. A Azure Key Vaulthoz való hozzáférés figyeléséhez engedélyezze Key Vault diagnosztikai beállításokat, és küldje el a naplókat egy Azure Storage-fiókba vagy Log Analytics munkaterületre.

Biztonságos hozzáférés a kulcstartóhoz: https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

Azure Key Vault tűzfalak és virtuális hálózatok konfigurálása: https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Azure Key Vault naplózás: https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: a hitelesítéshez, a felügyelethez és az adatsíkon való hozzáféréshez Azure Key Vault összes forgalom titkosítva van, és HTTPS-en keresztül megy át: 443-as port. (A CRL esetében azonban esetenként a HTTP [port 80] forgalom lesz látható.) Azure Key Vault továbbra is engedélyezi a TLS 1,1-és TLS 1,0-adatmennyiséget. Az adat a TLS 1,2-ra korlátozódik az ügyféloldali konfiguráción keresztül.



Hozzáférés Azure Key Vault tűzfal mögött: https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: nem alkalmazható; a Azure Key Vaulton (titkok, kulcsok és tanúsítványok) belüli összes adat bizalmasnak számít.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC

**Útmutató**: biztonságos hozzáférés a Azure Key Vault példányok felügyeleti és adatsíkjával.

Biztonságos hozzáférés a kulcstartóhoz:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: a Microsoft kezeli a Azure Key Vault alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

Mi az Azure Key Vault?

https://docs.microsoft.com/azure/key-vault/key-vault-overview

Azure Customer-adatvédelem:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: az összes felügyelt objektum (kulcs, tanúsítvány és titok) titkosítva van Azure Key Vaultban.

Támogató dokumentáció:

- [Titkosítási modell és kulcskezelő tábla](../../security/fundamentals/encryption-atrest.md)


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor Azure Key Vault elemzési megoldásával áttekintheti Azure Key Vault naplózási eseménynaplóit.

Azure Key Vault elemzési megoldás a Azure Monitorban:

https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ [: Security Control: sebezhetőségi kezelés](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: a Microsoft a sebezhetőségek kezelését a Azure Key Vault támogató mögöttes rendszereken hajtja végre.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: N/A; A Microsoft a Key Vaultt támogató mögöttes rendszereken végzi a javítások kezelését.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: a harmadik féltől származó szoftveres javításokat kezelő megoldás telepítése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: a Microsoft a sebezhetőségek kezelését a Key Vault támogató mögöttes rendszereken hajtja végre.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: a Azure Security Center által biztosított alapértelmezett kockázati minősítések (biztonságos pontszám) használata.

Javítsa biztonságos pontszámát Azure Security Centerban:

https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információkért lásd [: biztonsági vezérlés: leltár és eszközkezelés](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: az Azure Asset Discovery használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetésen belüli összes erőforrást (beleértve Azure Key Vault példányokat is). Győződjön meg arról, hogy megfelelő (olvasási) engedélyekkel rendelkezik a bérlőben, és képes felsorolni az összes Azure-előfizetést, valamint az előfizetésében lévő erőforrásokat.

Rövid útmutató: az első Resource Graph-lekérdezés futtatása az Azure Resource Graph Explorerrel:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Az aktuális fiók által elérhető előfizetések beolvasása.:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?

https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkék alkalmazása olyan Azure Key Vault erőforrásokhoz, amelyekkel a metaadatok logikailag rendezhetők a besorolásba.

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és a különálló előfizetések használata, ahol szükséges, Azure Key Vault példányok és kapcsolódó erőforrások rendszerezése és nyomon követése. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

Hozzon létre egy további Azure-előfizetést:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Felügyeleti csoportok létrehozása az erőforrás-szervezethez és-kezeléshez:

https://docs.microsoft.com/azure/governance/management-groups/create

Címkék használata az Azure-erőforrások rendszerezéséhez: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: a jóváhagyott Azure-erőforrások és-szoftverek leltárának fenntartása

**Útmutató**: a számítási erőforrásokhoz jóváhagyott Azure-erőforrások és jóváhagyott szoftverek listájának meghatározása

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: az Azure-szabályzatok használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetésben (k) belüli erőforrásokat.

Oktatóanyag: szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Rövid útmutató: az első Resource Graph-lekérdezés futtatása az Azure Resource Graph Explorerrel: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: nem alkalmazható; Ez a javaslat az Azure egészére és számítási erőforrásaira szolgál.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: az Azure-szabályzatok használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Oktatóanyag: szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy minták: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="610-implement-approved-application-list"></a>6,10: jóváhagyott alkalmazások listájának implementálása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6,11: korlátozza a felhasználók képességét a AzureResources-kezelővel való interakcióra parancsfájlok használatával

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek a Azure Resource Manager (ARM) használatára a "Microsoft Azure felügyelet" alkalmazás "hozzáférés letiltása" beállításával. Ez megakadályozhatja az erőforrások létrehozását és módosítását a magas biztonsági környezetben, például a Key Vault konfigurációval rendelkezők számára.

Az Azure-felügyelethez való hozzáférés kezelése feltételes hozzáféréssel:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információkért lásd [: biztonság-vezérlés: biztonságos konfiguráció](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: Azure Policy aliasok használata a "Microsoft. kulcstartó" névtérben egyéni szabályzatok létrehozásához a Azure Key Vault példányok konfigurációjának naplózásához vagy érvényesítéséhez. Használhat beépített Azure Policy-definíciókat is Azure Key Vault például:

Key Vault objektumoknak helyreállítható kell lenniük

Key Vault diagnosztikai beállításainak központi telepítése Log Analytics munkaterületre

A Key Vault lévő diagnosztikai naplókat engedélyezni kell

Key Vault virtuális hálózati szolgáltatás végpontját kell használnia

Key Vault diagnosztikai beállításainak üzembe helyezése az Event hub-ban

A Azure Security Center javaslatainak használata biztonságos alapkonfigurációként a Azure Key Vault példányok számára.

Az elérhető Azure Policy aliasok megtekintése:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Oktatóanyag: szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: használja a Azure Policy [megtagadás] és a [telepítés, ha nem létezik] lehetőséget a biztonságos beállítások betartatásához a Azure Key Vault-kompatibilis erőforrások között. 

Oktatóanyag: szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage 

  
Azure Policy effektusok ismertetése: 

https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: ha az Azure Key Vault engedélyezett erőforrásokhoz egyéni Azure Policy definíciókat használ, az Azure Repos használatával biztonságosan tárolhatja és kezelheti a kódot.

Kód tárolása az Azure DevOps: 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops 

Az Azure Repos dokumentációja: 

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: rendszerkonfiguráció-felügyeleti eszközök telepítése

**Útmutató**: a "Microsoft. kulcstartó" névtérben található Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

Azure Policy konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: rendszerkonfiguráció-felügyeleti eszközök központi telepítése operációs rendszerekhez

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: az Azure-szolgáltatások automatizált konfigurációs figyelésének megvalósítása

**Útmutató**: a Azure Security Center használata a Azure Key Vault által védett erőforrások alapkonfigurációjának vizsgálatához 

  

Javaslatok szervizelése Azure Security Centerban: 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: nem alkalmazható; Ez a teljesítményteszt számítási erőforrások számára készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: a Managed Service Identity együttes használata a Azure Key Vault a Felhőbeli alkalmazások titkos felügyeletének egyszerűsítése és biztonságossá tétele érdekében. Győződjön meg arról, hogy a Azure Key Vault-törlés engedélyezve van.

Integráció az Azure felügyelt identitásokkal:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Key Vault létrehozása:

https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

A Key Vault hitelesítése:

https://docs.microsoft.com/azure/key-vault/general/authentication

Key Vault hozzáférési szabályzatok kiosztása:

https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: a Managed Service Identity együttes használata a Azure Key Vault a Felhőbeli alkalmazások titkos felügyeletének egyszerűsítése és biztonságossá tétele érdekében. 

  

* [Integráció az Azure felügyelt identitásokkal](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Key Vault létrehozása](quick-create-portal.md)

* [Hitelesítés Key Vault](authentication.md)

* [Key Vault hozzáférési szabályzatok kiosztása](assign-access-policy-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.  
  
 A hitelesítőadat-olvasó beállítása: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információkért lásd [: biztonsági ellenőrzés: kártevők elleni védelem](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült. A Microsoft kezeli a kártevő szoftvereket a mögöttes platformon.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft kártevő szoftverrel való ellátása engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure Key Vault), azonban nem az ügyfél tartalmán fut.

A nem számítási Azure-erőforrásokra, például a Azure Key Vaultra feltöltött vagy azokon továbbított tartalmak előzetes vizsgálata. A Microsoft nem fér hozzá az adataihoz ezekben a példányokban.

Ismerje meg a Microsoft antimalware az Azure Cloud Services és Virtual Machines: https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült. A Microsoft kezeli a kártevő szoftvereket a mögöttes platformon.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="data-recovery"></a>Adat-helyreállítás

*További információkért lásd [: biztonsági ellenőrzés: adat-helyreállítás](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a következő PowerShell-parancsokkal biztosíthatja a Key Vault tanúsítványok, kulcsok, felügyelt tárolási fiókok és titkok rendszeres automatikus biztonsági mentését:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Szükség esetén a Key Vault biztonsági másolatait Azure Backupon belül is tárolhatja.

Key Vault tanúsítványok biztonsági mentése: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Key Vault kulcsok biztonsági mentése: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Key Vault felügyelt Storage-fiókok biztonsági mentése: https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Key Vault titkos kódok biztonsági mentése: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

A Azure Backup engedélyezése: https://docs.microsoft.com/azure/backup



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és biztonsági másolat készítése bármely ügyfél által felügyelt kulcsról

**Útmutató**: biztonsági másolatokat készíthet a Key Vault tanúsítványokról, kulcsokról, felügyelt tárolási fiókokról és titkokról a következő PowerShell-parancsokkal:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Szükség esetén a Key Vault biztonsági másolatait Azure Backupon belül is tárolhatja.

Key Vault tanúsítványok biztonsági mentése: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Key Vault kulcsok biztonsági mentése: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Key Vault felügyelt Storage-fiókok biztonsági mentése: https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Key Vault titkos kódok biztonsági mentése: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

A Azure Backup engedélyezése: https://docs.microsoft.com/azure/backup



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: a Key Vault tanúsítványok, kulcsok, felügyelt tárolási fiókok és titkos kódok adatvisszaállításának rendszeres időközönkénti végrehajtása a következő PowerShell-parancsokkal:

- Restore-AzKeyVaultCertificate

- Restore-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Restore-AzKeyVaultSecret

Key Vault tanúsítványok visszaállítása:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

Key Vault kulcsok visszaállítása: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

Key Vault felügyelt Storage-fiókok visszaállítása: https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

Key Vault titkos kódok visszaállítása: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: Ellenőrizze, hogy engedélyezve van-e a Azure Key Vault a Soft delete. A Soft-delete lehetővé teszi a törölt kulcstartók és tároló objektumok, például kulcsok, titkos kódok és tanúsítványok helyreállítását. 

A Azure Key Vault Soft delete használata: 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ [: Security Control: incidens válasza](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig. Ezeknek a folyamatoknak összpontosítaniuk kell a bizalmas rendszerek védelmére, például a Key Vault titkokat használó rendszerekre.

Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül: 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   

Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához:  

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

A Microsoft Security Response Center egy Incidensének anatómiája:   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   

Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásához: 

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt. Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben, nem gyártva), és létrehoz egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához, különösen a bizalmas adatok, például a Azure Key Vault titkok feldolgozásához.


**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensekre adott válaszait rendszeres ütemben tesztelheti az Azure Key Vault-példányok és a kapcsolódó erőforrások védelmének elősegítése érdekében. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és gyakorlatának megtervezéséhez: 

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte.  A problémák megoldása érdekében tekintse át az incidenseket a tény után.

A Azure Security Center biztonsági kapcsolattartó beállítása:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: a Azure Security Center riasztások és javaslatok exportálása a folyamatos exportálás funkcióval a Azure Key Vault-kompatibilis erőforrások kockázatainak azonosítása érdekében. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását.  Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba. 

 

Folyamatos exportálás konfigurálása: 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

Riasztások továbbítása az Azure Sentinelbe: 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan elindíthatja a válaszokat az "Logic apps" használatával a biztonsági riasztások és a Azure Key Vault által védett erőforrások védelme érdekében javasolt javaslatok alapján. 

 

A Munkafolyamat-automatizálás és a Logic Apps konfigurálása: 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért lásd [: biztonsági ellenőrzés: behatolási tesztek és Red Team-gyakorlatok](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: rendszeres penetrációs tesztelést végez az Azure-erőforrásokon, és gondoskodik az összes kritikus biztonsági vizsgálat 60 napon belüli szervizeléséről

**Útmutató**: nem kell közvetlenül a Azure Key Vault szolgáltatáson elvégezni a toll-tesztelést, azonban javasoljuk, hogy tesztelje az Azure-erőforrásokat, amelyek Key Vault használnak a titkok biztonságának biztosításához.

Követnie kell a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

A Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatos további információkért tekintse meg a következő témakört: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure biztonsági teljesítménytesztét](../../security/benchmarks/overview.md)
- További információ az [Azure biztonsági Alaptervekről](../../security/benchmarks/security-baselines-overview.md)