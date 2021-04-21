---
title: Az Azure biztonsági alapkonfigurációja Key Vault
description: A Key Vault alapkonfiguráció eljárásokkal kapcsolatos útmutatást és forrásforrásokat biztosít az Azure biztonsági teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához.
author: msmbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1f56de94df4fd5d4dd154ae8485edb9eed88364c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753343"
---
# <a name="azure-security-baseline-for-key-vault"></a>Az Azure biztonsági alapkonfigurációja Key Vault

Ez a biztonsági alapkonfiguráció az Azure Biztonsági teljesítményteszt [1.0-s](../../security/benchmarks/overview-v1.md) verziójának útmutatását alkalmazza a Key Vault. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalmat az Azure  biztonsági teljesítményteszt által meghatározott biztonsági vezérlők és a kapcsolódó, az alkalmazásra vonatkozó Key Vault. **A** nem alkalmazható Key Vault, vagy amelyekért a felelősség a Microsoft felelős, ki vannak zárva.

Ha meg Key Vault, hogyan lehet teljes mértékben leképezni az Azure-biztonsági teljesítménytesztre, tekintse meg a teljes Key Vault alapkonfiguráció [leképezési fájlját.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-erőforrások védelme virtuális hálózatokon belül

**Útmutató:** Integráció Azure Key Vault a Azure Private Link. Azure Private Link Szolgáltatás lehetővé teszi az Azure-szolgáltatások (például Azure Key Vault) és az Azure által üzemeltetett ügyfél-/partnerszolgáltatások privát végponton keresztüli hozzáférését a virtuális hálózatban.

Az Azure privát végpontok olyan hálózati adapterek, amelyek privát módon és biztonságosan csatlakoztatják a Azure Private Link. A privát végpont a virtuális hálózat egyik magánhálózati IP-címét használja, így hatékonyan behozza a szolgáltatást a virtuális hálózatba. A szolgáltatás felé minden forgalom átirányítható a privát végponton keresztül, így nincs szükség átjárókra, NAT-eszközökre, ExpressRoute- vagy VPN-kapcsolatokra vagy nyilvános IP-címekre. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Csatlakozhat egy Azure-erőforráspéldányhoz, így a legmagasabb szintű részletességet biztosít a hozzáférés-vezérlésben.

- [A Key Vault integrálása Azure Private Link](/azure/key-vault/private-link-service)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.KeyVault:**

[!INCLUDE [Resource Policy for Microsoft.KeyVault 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** A Azure Security Center és kövesse a hálózatvédelmi javaslatokat az Azure-ban Key Vault erőforrások biztonságának biztosítása érdekében. 

- [További információ a hálózati biztonságról, amelyet a Azure Security Center](../../security-center/security-center-network-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert kártékony IP-címekkel való kommunikáció megtagadása

**Útmutató:** Engedélyezze Azure DDoS Protection Standard szabványt az Key Vault-példányokhoz társított Azure-beli virtuális hálózatokon az elosztott szolgáltatásmegtagadásos támadások elleni védelem érdekében. Az Azure Security Center intelligencia használatával megtagadhatja az ismert rosszindulatú vagy nem használt internetes IP-címekkel való kommunikációt.

 
- [A Azure DDoS Protection Standard kezelése a Azure Portal](/azure/virtual-network/manage-ddos-protection)

- [Fenyegetésészlelés az Azure szolgáltatási rétegében a Azure Security Center](/azure/security-center/security-center-alerts-service-layer)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Hálózatalapú behatolásészlelési/behatolásvédelmi rendszerek (IDS/IPS) üzembe helyezése

**Útmutató:** Ez a követelmény a komplex veszélyforrások elleni védelem (Advanced Threat Protection, ATP) konfigurálásával Azure Key Vault. Az ATP a biztonsági intelligencia egy további rétegét biztosítja. Ez az eszköz észleli a fiókok hozzáférésére vagy kihasználására irányuló potenciálisan Azure Key Vault kísérleteket.

Ha Azure Security Center tevékenységet észlel, riasztásokat jelenít meg. Emellett e-mailt küld az előfizetés rendszergazdájának a gyanús tevékenység részleteivel, valamint az azonosított fenyegetések kivizsgálására és orvoslására vonatkozó javaslatokkal.

- [Az Advanced Threat Protection beállítása az Azure Key Vaulthoz](/azure/security-center/advanced-threat-protection-key-vault)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** A Azure Key Vault-példányokhoz hozzáférést igényelő erőforrások esetében a Azure Key Vault Azure-szolgáltatáscímkéivel határozhatja meg a hálózati biztonsági csoportok vagy erőforrások Azure Firewall. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha megadja a szolgáltatáscímke nevét (például ApiManagement) egy szabály megfelelő forrás- vagy célmezőben, engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkét a címek változásával.

- [Az Azure-szolgáltatáscímkék áttekintése](../../virtual-network/service-tags-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációjának fenntartása

**Útmutató:** Standard biztonsági konfigurációkat határozhat meg és valósíthat meg a Azure Key Vault-példányokhoz társított hálózati Azure Policy. A Azure Policy a "Microsoft.KeyVault" és a "Microsoft.Network" névterek aliasainak használatával egyéni szabályzatokat hozhat létre a hálózati konfiguráció naplózásához vagy kényszerítés Azure Key Vault példányokhoz. A szabályzathoz kapcsolódó beépített szabályzatdefiníciókat is Azure Key Vault, például:
- Key Vault hálózati szolgáltatásvégpontot kell használnia

További információkat az alábbi hivatkozásokon találhat:

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy-minták](/azure/governance/policy/samples)

- [Terv meghatározása és hozzárendelése a portálon](../../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutató:** Címkék használata a hálózati biztonsághoz és a forgalomfolyamhoz kapcsolódó erőforrásokhoz a Azure Key Vault a metaadatok és a logikai rendszer szervezés érdekében.

A címkézéshez Azure Policy beépített címkedefiníciók (például "Címke és érték megkövetelése" ) használatával biztosíthatja, hogy az összes erőforrás címkékkel legyen létrehozva, és értesítést küld a címkézetlen erőforrásokról.

A címkék alapján Azure PowerShell azure cli használatával erőforrásokat kereshet vagy hajthat végre műveleteken.

- [Címkék használata az Azure-erőforrások rendszerezéséhez](/azure/azure-resource-manager/resource-group-using-tags)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrások konfigurációjának figyelése és a változások észlelése érdekében

**Útmutató:** Az Azure-tevékenységnapló segítségével monitorozható a hálózati erőforrások konfigurációja, és észlelhetők a virtuális Azure Key Vault kapcsolatos hálózati erőforrások változásai. Hozzon létre riasztásokat a Azure Monitor, amelyek akkor aktiválódnak, ha a kritikus hálózati erőforrások módosulnak.

- [Azure-tevékenységnapló eseményeinek megtekintése és lekérése](/azure/azure-monitor/platform/activity-log-view)

- [Tevékenységnapló-riasztások létrehozása, megtekintése és kezelése a Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Azure biztonsági teljesítményteszt: Naplózás és monitorozás.](../../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Központi biztonsági naplókezelés konfigurálása

**Útmutató:** Naplók Azure Monitor a Azure Key Vault. A Azure Monitor Azure Log Analytics-munkaterület használatával végezhet lekérdezést és elemzést, valamint használhatja az Azure Storage-fiókokat a hosszú távú/archiválási tárterülethez. Másik lehetőségként engedélyezheti és bevetheti az adatokat Azure Sentinel külső SIEM-hez. 

- [Azure Key Vault naplózás](/azure/key-vault/key-vault-logging)

- [Rövid útmutató: Útmutató Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure-erőforrások naplózásának engedélyezése

**Útmutató:** Diagnosztikai beállítások engedélyezése a Azure Key Vault a naplózási, biztonsági és diagnosztikai naplókhoz való hozzáféréshez. Az automatikusan elérhető tevékenységnaplók közé tartozik az eseményforrás, a dátum, a felhasználó, az időbélyeg, a forráscímek, a célcímek és egyéb hasznos elemek.

- [Azure Key Vault naplózás](/azure/key-vault/key-vault-logging)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.KeyVault:**

[!INCLUDE [Resource Policy for Microsoft.KeyVault 2.3](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: A biztonsági naplók tárolásának megőrzésének konfigurálása

**Útmutató:** Azure Monitor log Analytics-munkaterületen belül, amely a Azure Key Vault-naplókat fogja használni, a szervezet megfelelőségi szabályozásának megfelelően állítsa be a megőrzési megőrzési időszakot. Az Azure Storage-fiókokat hosszú távú/archiválási tárterülethez használhatja.

- [Az adatmegőrzési időtartam módosítása](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és áttekintése

**Útmutató:** Naplók elemzése és figyelése rendellenes viselkedés észlelése érdekében, valamint az Azure Key Vault erőforrások eredményeinek rendszeres áttekintése. A Azure Monitor Log Analytics-munkaterületén áttekinthet naplókat, és lekérdezéseket hajthatja végre a naplóadatokon. Másik lehetőségként engedélyezheti és bevetheti az adatokat Azure Sentinel külső SIEM-hez. 

- [On-board Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [A Log Analytics első lépések a Azure Monitor](/azure/azure-monitor/log-query/get-started-portal)

- [Ismerkedés az Azure Monitor-naplólekérdezésekkel](/azure/azure-monitor/log-query/get-started-queries)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató:** A Azure Security Center komplex veszélyforrások elleni védelem (ATP) engedélyezése a Key Vault. Engedélyezze a diagnosztikai beállításokat a Azure Key Vault és küldje el a naplókat egy Log Analytics-munkaterületre. A Log Analytics-munkaterületet a Azure Sentinel, mivel biztonsági vezénylési automatizált választ (SOAR) kínál. Ez lehetővé teszi, hogy forgatókönyveket (automatizált megoldásokat) hoznak létre és használjanak a biztonsági problémák megoldásához.

- [Gyorsútmutató: Az Azure Sentinel előkészítése](../../sentinel/quickstart-onboard.md)

- [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](../../security-center/security-center-managing-and-responding-alerts.md)

- [Eseményekre való válaszadás Azure Monitor-riasztásokkal](/azure/azure-monitor/learn/tutorial-response)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ: [Azure Biztonsági teljesítményteszt: Identitás és Access Control.](../../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: A rendszergazdai fiókok leltárának fenntartása

**Útmutató:** Leltározás a Azure Active Directory (Azure AD) által regisztrált alkalmazásokról, valamint az összes olyan felhasználói fiókról, amely hozzáféréssel rendelkezik a Azure Key Vault kulcsokhoz, titkos kulcsokhoz és tanúsítványokhoz. A hozzáférés lekérdezéséhez és egyeztetéséhez használhatja Azure Portal vagy a PowerShell Key Vault t. A hozzáférés PowerShellben való megtekintéséhez használja a következő parancsot:

(Get-AzResource -ResourceId [KeyVaultResourceID]). Properties.AccessPolicies

- [Alkalmazás regisztrálása az Azure AD-ban](/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory)

- [Biztonságos hozzáférés egy kulcstartóhoz](security-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Dedikált rendszergazdai fiókok használata

**Útmutató:** Szabványos üzemeltetési eljárásokat hozhat létre a dedikált rendszergazdai fiókok használatával kapcsolatban, amelyek hozzáféréssel Azure Key Vault példányokhoz. Az Azure Security Center és hozzáférés-kezelés (jelenleg előzetes verzióban) használatával figyelheti az aktív rendszergazdai fiókok számát.

- [Identitás és hozzáférés figyelése (előzetes verzió)](../../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Egyszeri Azure Active Directory (SSO) használata

**Útmutató:** Az AppId, a TenantID és a ClientSecret együttes használatával zökkenőmentesen hitelesítheti az alkalmazást, és lekérheti a titkos kulcsok eléréséhez használt Azure Key Vault jogkivonatot.

- [Szolgáltatások között való hitelesítés a .NET Azure Key Vault használatával](/azure/key-vault/service-to-service-authentication)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata minden Azure Active Directory-alapú hozzáféréshez

**Útmutató:** Engedélyezze Azure Active Directory (Azure AD) többtényezős hitelesítést, és kövesse Azure Security Center (jelenleg előzetes verzióban elérhető) identitás- és hozzáférés-kezelésre vonatkozó javaslatokat az Event Hub-kompatibilis erőforrások védelme érdekében.

- [Az Azure AD Multi-Factor Authentication üzembe helyezésének megterve](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése (előzetes verzió)](../../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Biztonságos, Azure által felügyelt munkaállomások használata felügyeleti feladatokhoz

**Útmutató:** Emelt szintű hozzáférésű munkaállomás (PAW) használata többtényezős Azure AD-hitelesítéssel, amely az engedélyezett erőforrásokba való bejelentkezésre és Key Vault konfigurálható.

- [Emelt hozzáférési szintű munkaállomások](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Többtényezős Azure AD-hitelesítés üzembe helyezésének megtervezése](../../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: A rendszergazdai fiókokból származó gyanús tevékenységek naplózása és riasztása

**Útmutató:** A Azure Active Directory (Azure AD) Privileged Identity Management (PIM) használatával naplókat és riasztásokat generál, ha gyanús vagy nem biztonságos tevékenység történik a környezetben. Az Azure AD kockázatészlelései segítségével riasztásokat és jelentéseket lehet megtekinteni a kockázatos felhasználói viselkedésről. További naplózáshoz küldjön Azure Security Center riasztásokat a Azure Monitor és konfigurálja az egyéni riasztásokat/értesítéseket a műveletcsoportok használatával.

Engedélyezze az Advanced Threat Protection (ATP) Azure Key Vault a gyanús tevékenységekre vonatkozó riasztások létrehozásához.

- [Üzembe Azure AD Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Komplex veszélyforrások elleni védelem beállítása Azure Key Vault (előzetes verzió)](/azure/security-center/advanced-threat-protection-key-vault)

- [Riasztások Azure Key Vault (előzetes verzió)](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv)

- [Az Azure AD kockázatészlelései](/azure/active-directory/reports-monitoring/concept-risk-events)

- [Műveletcsoportok létrehozása és felügyelete az Azure Portalon](/azure/azure-monitor/platform/action-groups)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató:** Feltételes hozzáférési szabályzat hely feltételének konfigurálása és a nevestűs helyek kezelése. Elnevezett helyekkel IP-címtartományok, országok és régiók logikai csoportosítását hozhatja létre. A bizalmas erőforrásokhoz( például a titkos kulcsokhoz Key Vault a konfigurált elnevezett helyekhez korlátozhatja a hozzáférést.

- [Mi a hely feltétele a Azure Active Directory (Azure AD) feltételes hozzáférésében?](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="39-use-azure-active-directory"></a>3.9: A Azure Active Directory

**Útmutató:** A Azure Active Directory (Azure AD) használata központi hitelesítési és engedélyezési rendszerként olyan Azure-erőforrásokhoz, mint Key Vault. Ez lehetővé teszi az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) számára a bizalmas erőforrások felügyeletét.

- [Új bérlő létrehozása az Azure AD-ban](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: A felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató:** Tekintse Azure Active Directory (Azure AD) naplóit, hogy segítsen a rendszergazdai szerepkörökkel rendelkező elavult Azure Key Vault felderítésében. Emellett az Azure AD hozzáférési felülvizsgálatok használatával hatékonyan kezelheti a csoporttagságokat, a hozzáféréseket a vállalati alkalmazásokhoz, amelyek hozzáféréssel Azure Key Vault és szerepkör-hozzárendelések. A felhasználói hozzáférést rendszeresen, például 90 naponta kell felülvizsgálni, hogy csak a megfelelő felhasználók férnek hozzá.

- [Az Azure AD-jelentések és -monitorozás dokumentációja](/azure/active-directory/reports-monitoring/)

- [Mik azok az Azure AD hozzáférési felülvizsgálatok?](../../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Az inaktivált hitelesítő adatok elérésére tett kísérletek figyelése

**Útmutató:** Diagnosztikai beállítások engedélyezése az Azure Key Vault és Azure Active Directory (Azure AD) számára, és az összes naplót egy Log Analytics-munkaterületre küldi. Konfigurálja a kívánt riasztásokat (például a letiltott titkos kulcsok elérésére tett kísérleteket) a Log Analyticsben.

- [Azure AD-naplók integrálása Azure Monitor naplókba](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Áttelepítés a régi Key Vault megoldásból](/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató:** Az Azure Active Directory (Azure AD) Identity Protection és kockázatészlelési funkcióival konfigurálhatja a védett erőforrásokhoz kapcsolódó, Azure Key Vault gyanús műveletekre adott automatikus válaszokat. A szervezet biztonsági válaszait Azure Sentinel automatikus válaszokat kell engedélyeznie.

- [Kockázatos bejelentkezések jelentése az Azure AD portálon](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [How To: Configure and enable risk policies (A kockázati szabályzatok konfigurálása és engedélyezése)](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [A Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Bizalmas adatok leltárának fenntartása

**Útmutató:** Címkék használata az engedélyezett erőforrásokon bizalmas adatokat tároló vagy fel Azure Key Vault Azure-erőforrások nyomon követéséhez. 

- [Címkék használata az Azure-erőforrások rendszerezéséhez](/azure/azure-resource-manager/resource-group-using-tags)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Bizalmas adatokat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** A hozzáférés biztonságossá Azure Key Vault az egyes alhálózatokra való hozzáférés korlátozására konfigurált virtuális hálózati szolgáltatásvégpontokkal.

A tűzfalszabályok életbe lépését követően csak akkor hajthat végre Azure Key Vault adatsíkműveleteket, ha a kérés engedélyezett alhálózatról vagy IP-címtartományból származik. Ez a Azure Key Vault hozzáférésre is Azure Portal. Bár a kulcstartót a Azure Portal-ból is megkeresheti, előfordulhat, hogy nem tudja listába sorolni a kulcsokat, titkos kulcsokat vagy tanúsítványokat, ha az ügyfélszámítógép nem szerepel az engedélyezett listában. Ez hatással van a Azure Key Vault és más Azure-szolgáltatásokra is. Előfordulhat, hogy láthatja a Kulcstartók listáját, a kulcsok listáját azonban nem, ha a tűzfalszabályok megakadályozzák, hogy az ügyfélszámítógép ezt meg tudja tenni.

- [Tűzfalak Azure Key Vault virtuális hálózatok konfigurálása](/azure/key-vault/key-vault-network-security)

- [Virtuális hálózati szolgáltatásvégpontok az Azure Key Vaulthoz](/azure/key-vault/key-vault-overview-vnet-service-endpoints)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Bizalmas adatok jogosulatlan átvitelének figyelése és blokkolása

**Útmutató:** A Azure Key Vault adatok bizalmasnak minősülnek. Adatsík Azure Key Vault hozzáférés-vezérléssel szabályozhatja a titkos Azure Key Vault hozzáférését. Használhatja a Key Vault beépített tűzfalát is a hálózati réteg hozzáférésének szabályozásához. A hozzáférés figyelése érdekében engedélyezze Azure Key Vault diagnosztikai Key Vault, és küldje el a naplókat egy Azure Storage-fióknak vagy Log Analytics-munkaterületnek.

- [Biztonságos hozzáférés egy kulcstartóhoz](security-overview.md)

- [Tűzfalak Azure Key Vault virtuális hálózatok konfigurálása](/azure/key-vault/key-vault-network-security)

- [Azure Key Vault naplózás](/azure/key-vault/key-vault-logging)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Az erőforrásokhoz való hozzáférés kezelése az Azure RBAC használatával

**Útmutató:** Biztonságos hozzáférés a felügyeleti és adatsíkhoz a Azure Key Vault példányokhoz.

- [Biztonságos hozzáférés egy kulcstartóhoz](security-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Kritikus fontosságú Azure-erőforrások változásainak naplózása és riasztása

**Útmutató:** A Azure Key Vault Analytics megoldás használatával Azure Monitor naplókat Azure Key Vault naplók áttekintésére.

- [Azure Key Vault Analytics megoldás a Azure Monitor](/azure/azure-monitor/insights/azure-key-vault)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ: [Azure biztonsági teljesítményteszt: Biztonsági rések kezelése.](../../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Kockázatminősítési folyamat használata a felderített biztonsági rések szervizelésének rangsorolására

**Útmutató:** Használja a rendszer által biztosított alapértelmezett kockázatminősítéseket (Azure Security Center.

- [A biztonsági pontszám javítása a Azure Security Center](/azure/security-center/security-center-secure-score)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Azure biztonsági teljesítményteszt: Leltár és eszközkezelés.](../../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Automatizált eszközfelderítési megoldás használata

**Útmutató:** A Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetésben található összes erőforrást (Azure Key Vault példányokat is). Győződjön meg arról, hogy rendelkezik a megfelelő (olvasási) engedélyekkel a bérlőben, és képes enumerálni az összes Azure-előfizetést és az előfizetésen belüli erőforrásokat.

- [Az első lekérdezés Resource Graph az Azure Resource Graph Explorerrel](../../governance/resource-graph/first-query-portal.md)

- [Az aktuális fiók által elérhető előfizetések lekért](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Azure-alapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="62-maintain-asset-metadata"></a>6.2: Objektum metaadatainak karbantartása

**Útmutató:** Címkék alkalmazása Azure Key Vault erőforrásokra, amelyek metaadatokat adva logikailag rendszerezik őket egy taxonómiába.

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Nem engedélyezett Azure-erőforrások törlése

**Útmutató:** Címkék, felügyeleti csoportok és különálló előfizetések használata , ahol szükséges, a példányok és Azure Key Vault erőforrások rendszerezéséhez és nyomon követéséhez. Rendszeresen összeegyezteti a leltárat, és gondoskodik arról, hogy a jogosulatlan erőforrások időben törlődjenek az előfizetésből.

- [További Azure-előfizetés létrehozása](/azure/billing/billing-create-subscription)

- [Felügyeleti csoportok létrehozása az erőforrások rendszerezéséhez és kezeléséhez](/azure/governance/management-groups/create)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](/azure/azure-resource-manager/resource-group-using-tags)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Nem jóváhagyott Azure-erőforrások figyelése

**Útmutató:** Az Azure-szabályzatok használatával korlátozásokat korlátozhat az ügyfél-előfizetés(ök)ben az alábbi beépített szabályzatdefiníciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Emellett a Azure Resource Graph erőforrások lekérdezéséhez/felderítéséhez is használhatja az előfizetés(ek)et.

- [Oktatóanyag: Szabályzatok létrehozása és kezelése a megfelelőség kikényszeríteni érdekében](../../governance/policy/tutorials/create-and-manage.md)

- [Rövid útmutató: Az első lekérdezés Resource Graph a Azure Resource Graph Explorerrel](../../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="69-use-only-approved-azure-services"></a>6.9: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató:** Az Azure-szabályzatok használatával korlátozásokat korlátozhat az ügyfél-előfizetés(ök)ben az alábbi beépített szabályzatdefiníciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

További információkat az alábbi hivatkozásokon találhat:

- [Oktatóanyag: Szabályzatok létrehozása és kezelése a megfelelőség kényszerítéséhez](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy-minták](/azure/governance/policy/samples/built-in-policies#general)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Korlátozza a felhasználók számára a Azure Resource Manager

**Útmutató:** Az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók Azure Resource Manager-hozzáférését a "hozzáférés blokkolása" beállításával a "Microsoft Azure Management" alkalmazáshoz. Ez megakadályozhatja a magas biztonsági környezetben található erőforrások létrehozását és változásait, például az olyan erőforrásokét, amelyek Key Vault konfigurálva.

- [Az Azure-felügyelethez való hozzáférés kezelése feltételes hozzáféréssel](../../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információ: [Azure biztonsági teljesítményteszt: Biztonságos konfiguráció.](../../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató:** Azure Policy a "Microsoft.KeyVault" névtérben létrehozott aliasokkal egyéni szabályzatokat hozhat létre a Azure Key Vault konfigurációjának naplózására vagy kényszerítére. Beépített definíciókat is Azure Policy a Azure Key Vault például:

- Key Vault objektumoknak helyreállíthatónak kell lennie

- Diagnosztikai beállítások üzembe helyezése Key Vault Log Analytics-munkaterületen

- A diagnosztikai naplókat Key Vault engedélyezni kell

- Key Vault virtuális hálózati szolgáltatásvégpontot kell használnia

- Diagnosztikai beállítások üzembe helyezése Key Vault eseményközpontba

- A biztonsági Azure Security Center a biztonsági alapkonfigurációként használhatja a Azure Key Vault számára.

További információkat az alábbi hivatkozásokon találhat:

- [Az elérhető aliasok Azure Policy megtekintése](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Oktatóanyag: Szabályzatok létrehozása és kezelése a megfelelőség kényszerítéséhez](../../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Biztonságos Azure-erőforrás-konfigurációk fenntartása

**Útmutató:** A Azure Policy [deny] és a [deploy if not exist] használatával kényszerítheti a biztonságos beállításokat a Azure Key Vault-kompatibilis erőforrásokon. 

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../../governance/policy/tutorials/create-and-manage.md)

  
- [A Azure Policy hatásainak](../../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások konfigurációjának biztonságos tárolása

**Útmutató:** Ha egyéni Azure Policy használ a Azure Key Vault-kompatibilis erőforrásokhoz, az Azure Repos használatával biztonságosan tárolhatja és kezelheti a kódot.

- [Kód tárolása az Azure DevOpsban](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Az Azure Repos dokumentációja](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Konfigurációkezelési eszközök üzembe helyezése Azure-erőforrásokhoz

**Útmutató:** A Azure Policy a "Microsoft.KeyVault" névtérben létrehozott aliasokkal egyéni szabályzatokat hozhat létre a rendszerkonfigurációk riasztására, naplózására és kényszerítésére. Emellett dolgozzon ki egy folyamatot és folyamatot a szabályzati kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Automatizált konfigurációfigyelés megvalósítása az Azure-erőforrásokhoz

**Útmutató:** Azure Security Center használatával alapkonfiguráció-vizsgálatot végezhet Azure Key Vault által védett erőforrásokon.

- [Javaslatok szervizlének Azure Security Center](../../security-center/security-center-remediate-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-beli titkos kulcsok biztonságos kezelése

**Útmutató:** A Felügyeltszolgáltatás-identitás és a Azure Key Vault leegyszerűsítheti és biztonságossá teheti a titkos kulcsok kezelését a felhőalkalmazások számára. Győződjön meg Azure Key Vault, hogy a soft-delete engedélyezve van.

- [Integrálás az Azure Managed Identities használatával](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Új Key Vault](quick-create-portal.md)

- [Hitelesítés a Key Vaultban](authentication.md)

- [Hozzáférési szabályzat Key Vault hozzárendelése](assign-access-policy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.KeyVault:**

[!INCLUDE [Resource Policy for Microsoft.KeyVault 7.11](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-7-11.md)]

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identitások biztonságos és automatikus kezelése

**Útmutató:** A Managed Service Identity és a Azure Key Vault együtt használva leegyszerűsítheti és biztonságossá teheti a titkos adatok kezelését a felhőalkalmazások számára.

  

- [Az Azure Managed Identities integrálása](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Új Key Vault](quick-create-portal.md)    

- [Hitelesítés a Key Vault](authentication.md)

- [Hozzáférési szabályzat Key Vault hozzárendelése](assign-access-policy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: A hitelesítő adatok nem szándékolt kitettségének megszüntetése

**Útmutató:** Hitelesítőadat-ellenőrző implementálja a hitelesítő adatokat a kódon belül. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.  
  
- [ Hitelesítőadat-ellenőrző beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információ: [Azure biztonsági teljesítményteszt: Kártevők elleni védelem.](../../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: A nem számítási Azure-erőforrásokba feltölteni szükséges fájlok előzetes beolvasása

**Útmutató:** A Microsoft kártevőirtó engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure Key Vault), de nem fut az ügyfelek tartalmai között.

A feltöltött vagy nem számítási Azure-erőforrásokra (például az azure-erőforrásokra) küldött tartalmak előzetes Azure Key Vault. A Microsoft ezekben az esetekben nem fér hozzá az Ön adataihoz.

- [A Microsoft Antimalware és Azure Cloud Services és Virtual Machines](../../security/fundamentals/antimalware.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Azure biztonsági teljesítményteszt: Adat-helyreállítás.](../../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatikus biztonságimentek biztosítása

**Útmutató:** Az alábbi PowerShell-parancsokkal biztosíthatja az Key Vault tanúsítványok, kulcsok, felügyelt tárfiókok és titkos kulcsok rendszeres automatikus biztonsági mentését:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

A biztonsági másolatokat a Key Vault is Azure Backup.

- [Tanúsítványok biztonsági Key Vault biztonsági mentése](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Biztonsági másolat készítése Key Vault kulcsokról](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Felügyelt tárfiókok Key Vault biztonsági mentése](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Titkos kulcsok Key Vault biztonsági mentése](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

- [A Azure Backup](/azure/backup)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszer-biztonsági mentések és az ügyfelek által kezelt kulcsok biztonsági mentése

**Útmutató:** Biztonsági másolatot készít Key Vault tanúsítványokról, kulcsokról, felügyelt tárfiókról és titkos kulcsokról az alábbi PowerShell-parancsokkal:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

A biztonsági másolatokat a Key Vault is Azure Backup.

- [Biztonsági másolat készítése Key Vault tanúsítványokról](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Biztonsági másolat készítése Key Vault kulcsokról](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Felügyelt tárfiókok Key Vault biztonsági mentése](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Titkos kulcsok biztonsági Key Vault biztonsági mentése](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

- [A Azure Backup](/azure/backup)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági mentés ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató:** A következő PowerShell-parancsokkal rendszeresen végrehajtja a Key Vault-tanúsítványok, kulcsok, felügyelt tárfiókok és titkos kulcsok visszaállítását:

- Restore-AzKeyVaultCertificate

- Restore-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Restore-AzKeyVaultSecret

További információkat az alábbi hivatkozásokon találhat:

- [A tanúsítványok Key Vault visszaállítása](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [A kulcskulcsok Key Vault visszaállítása](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Felügyelt tárfiókok Key Vault visszaállítása](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Titkos kulcsok Key Vault visszaállítása](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági másolatok és az ügyfél által kezelt kulcsok védelmének biztosítása

**Útmutató:** Győződjön meg arról, hogy a soft delete engedélyezve van a Azure Key Vault. A helyreállított törlés lehetővé teszi a törölt kulcstartók és tárolóobjektumok, például kulcsok, titkos kulcsok és tanúsítványok helyreállítását. 

- [A Azure Key Vault törlési parancsának használata](/azure/key-vault/key-vault-soft-delete-powershell)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.KeyVault:**

[!INCLUDE [Resource Policy for Microsoft.KeyVault 9.4](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-9-4.md)]

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Incidensválasz-útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig. Ezeknek a folyamatoknak a bizalmas rendszerek védelmére kell összpontosítaniuk, például a titkos Key Vault védelmére.

- [Munkafolyamat-automatizálások konfigurálása a Azure Security Center](../../security-center/security-center-planning-and-operations-guide.md)   

- [Útmutató saját biztonsági incidensek válaszadási folyamatának építéshez](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Biztonsági reagálási központ az incidensek felépítése](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)   

- [Az ügyfél az NIST számítógépes biztonsági incidenskezelési útmutatóját is felhasználhatja, hogy segítséget nyújt a saját incidenskezelési tervének létrehozásában](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és -rangsorolási eljárás létrehozása

**Útmutató:** Security Center súlyosságot rendel az egyes riasztásokhoz, így priorizálhatja, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, Security Center mennyire magabiztos a riasztás kiadásához használt eredmény vagy metrika, valamint a riasztáshoz vezető tevékenység mögötti rosszindulatú szándék megbízhatósági szintje. Emellett egyértelműen jelöljük meg az előfizetéseket (például: éles környezetben, nem éles környezetben), és hozzon létre egy elnevezési rendszert, amely egyértelműen azonosítja és kategorizálja az Azure-erőforrásokat, különösen a bizalmas adatokat, például a titkos Azure Key Vault feldolgozását.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="103-test-security-response-procedures"></a>10.3: Biztonsági reagálási eljárások tesztelése

**Útmutató:** Rendszeres időközönként végezzen gyakorlatokat a rendszerek incidensválasz-képességeinek teszteléséhez, hogy segítsen megvédeni a Azure Key Vault példányait és a kapcsolódó erőforrásokat. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [Tekintse meg az NIST kiadványát: Útmutató az IT-tervekhez és -képességekhez szükséges tesztelési, képzési és gyakorlatprogramokhoz](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidens kapcsolattartási adatait, és konfigurálja a riasztási értesítéseket a biztonsági incidensek esetén

**Útmutató:** A Biztonsági incidens kapcsolattartási adatait a Microsoft arra használja, hogy kapcsolatba lépjen Önnel, ha a Microsoft Biztonsági reagálási központ (MSRC) azt tapasztalja, hogy az Ön adatait illetéktelen vagy illetéktelen fél férte hozzá.  Tekintse át az incidenseket a probléma megoldása érdekében.

- [A biztonsági kapcsolattartó Azure Security Center beállítása](../../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidensválasz-rendszerbe

**Útmutató:** A Azure Security Center riasztások és javaslatok exportálása a Folyamatos exportálás funkcióval, amely segít azonosítani a Azure Key Vault erőforrásokra vonatkozó kockázatokat. A folyamatos exportálás lehetővé teszi, hogy manuálisan vagy folyamatosan exportálja a riasztásokat és javaslatokat.  A riasztásokat a Azure Security Center adat-összekötővel streamelheti a Azure Sentinel. 

 

- [Folyamatos exportálás konfigurálása](../../security-center/continuous-export.md) 

  

- [Riasztások streamelése az Azure Sentinelbe](../../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: A biztonsági riasztások válaszának automatizálása

**Útmutató:** A Azure Security Center-ban a munkafolyamat-automatizálási szolgáltatással automatikusan aktiválhat válaszokat a biztonsági riasztások és javaslatok Logic Apps a védett erőforrások Azure Key Vault érdekében. 

 

- [A munkafolyamat-automatizálás és -Logic Apps](../../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információ: [Azure biztonsági teljesítményteszt: Behatolási tesztek és Red Team-gyakorlatok.](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Az Azure-erőforrások rendszeres behatolástesztelése és az összes kritikus fontosságú biztonsági eredmény szervizelésének biztosítása

**Útmutató:** Kövesse a Microsoft Cloud behatolástesztelési szabályait annak ellenőrzéséhez, hogy a behatolási tesztek nem sértik-e meg a Microsoft szabályzatát. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét.

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
