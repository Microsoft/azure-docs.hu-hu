---
title: Container Registry Azure biztonsági alapterve
description: A Container Registry biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: container-registry
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 795b938913dad149739c4591bc2a9c221d784c84
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101716371"
---
# <a name="azure-security-baseline-for-container-registry"></a>Container Registry Azure biztonsági alapterve

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 1,0-es verziójáról](../security/benchmarks/overview-v1.md) Container Registryre vonatkozó útmutatást alkalmaz. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Container Registry vonatkozó kapcsolódó útmutatás. A Container Registry nem alkalmazható **vezérlők** ki vannak zárva.

 
Ha szeretné megtekinteni, hogyan Container Registry teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Container Registry biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: az Azure Virtual Network biztonságos, privát hálózatkezelést biztosít az Azure-hoz és a helyszíni erőforrásokhoz. Ha egy Azure-beli virtuális hálózatról korlátozza a privát Azure Container registryhez való hozzáférést, győződjön meg arról, hogy csak a virtuális hálózat erőforrásai férnek hozzá a beállításjegyzékhez. A létesítmények közötti forgatókönyvek esetében a tűzfalszabályok konfigurálásával is engedélyezhető, hogy a beállításjegyzék-hozzáférés csak adott IP-címekről legyen elérhető.
Tűzfal mögött konfigurálja a tűzfal hozzáférési szabályait és a szolgáltatás címkéit a tároló-beállításjegyzék eléréséhez.

- [Azure Container Registry-hozzáférés korlátozása Azure-beli virtuális hálózati vagy tűzfalszabályok használatával](container-registry-vnet.md) 

- [Szabályok konfigurálása az Azure Container Registry tűzfal mögötti eléréséhez](container-registry-firewall-access-rules.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. ContainerRegistry**:

[!INCLUDE [Resource Policy for Microsoft.ContainerRegistry 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.containerregistry-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: az Azure-ban a hálózati erőforrások védelme érdekében a Azure Security Center használata és a hálózati védelmi javaslatok szervizelése. Engedélyezze a NSG folyamat naplóit, és küldje el a naplókat egy Storage-fiókba a forgalom naplózása érdekében.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A hálózati erőforrások megóvása](../security-center/security-center-network-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártevő IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: a DDoS standard Protection engedélyezése a virtuális hálózatokon a DDOS-támadások elleni védelem érdekében. A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel. Azure Firewall üzembe helyezése minden szervezet hálózati határain, a fenyegetések felderítése engedélyezve van, és a "riasztás és megtagadás" értékre van állítva a kártékony hálózati forgalom esetében.

A NSG konfigurálásához Azure Security Center igény szerinti hálózati hozzáférést is használhat, hogy korlátozott időtartamra korlátozza a végpontok jóváhagyott IP-címekre való kitettségét.
Emellett Azure Security Center adaptív hálózati korlátozással olyan NSG-konfigurációkat ajánlunk, amelyek a portok és a forrás IP-címeket a tényleges forgalom és a veszélyforrások felderítése alapján korlátozzák.

- [A DDoS Protection konfigurálása](/azure/virtual-network/manage-ddos-protection)
- [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)
- [Az Azure Security Center integrált veszélyforrások felderítésének megismerése](../security-center/security-center-alerts-service-layer.md)
- [Azure Security Center adaptív hálózat megerősítésének ismertetése](../security-center/security-center-adaptive-network-hardening.md)
- [Azure Security Center az időponthoz tartozó hálózati Access Control](../security-center/security-center-just-in-time.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: engedélyezze a hálózati biztonsági csoport (NSG) számára az Azure Container Registry védelméhez használt alhálózathoz csatolt NSG. A flow-rekordok létrehozásához rögzítheti a NSG folyamat naplóit egy Azure Storage-fiókba. Ha a rendellenes tevékenységek kivizsgálásához szükséges, engedélyezze az Azure Network Watcher a csomagok rögzítését.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Network Watcher engedélyezése](../network-watcher/network-watcher-create.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: válasszon ki egy olyan ajánlatot az Azure piactéren, amely támogatja az azonosítók/IP-címek funkciót a hasznos adatok ellenőrzésére szolgáló funkciókkal. Ha a betörési észlelés és/vagy a tartalom-ellenőrzésen alapuló megelőzés nem követelmény, akkor az Azure Firewall a veszélyforrások felderítésére használható. Azure Firewall fenyegetés intelligencián alapuló szűréssel riasztást kaphat, és megtagadhatja az ismert kártékony IP-címek és tartományok felé irányuló forgalmat. Az IP-címek és -tartományok forrása a Microsoft veszélyforrás-felderítési hírcsatornája.

Az Ön által választott tűzfal-megoldás üzembe helyezésével azonosíthatja és/vagy megtagadhatja a kártékony forgalmat.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall) 

- [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

- [Riasztások konfigurálása Azure Firewall](../firewall/threat-intel.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: a tároló-beállításjegyzékhez hozzáférést igénylő erőforrásokhoz használja a Azure Container Registry szolgáltatáshoz tartozó virtuális hálózati szolgáltatás címkéit a hálózati biztonsági csoportok vagy Azure Firewall hálózati hozzáférés-vezérlésének definiálásához. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha a "AzureContainerRegistry" nevű szolgáltatási címkét egy szabály megfelelő forrás vagy cél mezőjében adja meg, akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

- [Hozzáférés engedélyezése a Service tag számára](https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: szabványos biztonsági konfigurációk definiálása és implementálása az Azure Container-nyilvántartásokhoz társított hálózati erőforrásokhoz Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. ContainerRegistry" és a "Microsoft. Network" névterekben, hogy egyéni szabályzatokat hozzon létre a tároló-beállításjegyzékek hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. 

Az Azure-tervezetek segítségével leegyszerűsítheti a nagyméretű Azure-környezeteket, ha az egyes tervrajzok definíciójában a Azure Resource Manager sablonokat, az Azure RBAC-vezérlőket és a szabályzatokat is kicsomagolta. Egyszerűen alkalmazhatja a tervrajzot új előfizetésekre, és a verziószámozással finomhangolást és felügyeletet alkalmazhat.

- [Az Azure Container-nyilvántartások megfelelőségének naplózása Azure Policy használatával](container-registry-azure-policy.md)

- [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: az ügyfél az Azure tervrajzait használva egyszerűsítheti a nagy léptékű Azure-környezeteket a főbb környezeti összetevők, például a Azure Resource Manager sablonok, az Azure RBAC-vezérlőelemek és a házirendek egyetlen tervezet-definícióban való csomagolásával. Egyszerűen alkalmazhatja a tervrajzot új előfizetésekre, és a verziószámozással finomhangolást és felügyeletet alkalmazhat.

- [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata a hálózati erőforrás-konfigurációk figyelésére és a tároló-beállításjegyzékhez kapcsolódó hálózati erőforrások változásainak észlelésére. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](/azure/azure-monitor/platform/activity-log-view)

- [Riasztások létrehozása a Azure Monitorban](/azure/azure-monitor/platform/alerts-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: naplók beolvasása Azure monitor segítségével az Azure Container Registry által generált biztonsági adatokat összesítve. A Azure Monitoron belül Log Analytics munkaterület (ek) használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat a hosszú távú/archiválási tároláshoz.

- [Azure Container Registry naplók a diagnosztika kiértékeléséhez és a naplózáshoz](container-registry-diagnostics-audit-logs.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a Azure monitor erőforrás-naplókat (korábbi néven diagnosztikai naplókat) gyűjt a beállításjegyzékben lévő felhasználó által vezérelt eseményekhez. Gyűjtsön és fogyasszon el adatokat a beállításjegyzék-hitelesítési események naplózásához, és adjon meg egy teljes körű tevékenységi nyomvonalat a beállításjegyzék-összetevőkről, például a lekéréses és leküldéses eseményekről, hogy a beállításjegyzékben biztonsági problémákat lehessen

- [Azure Container Registry naplók a diagnosztika kiértékeléséhez és a naplózáshoz](container-registry-diagnostics-audit-logs.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: nem alkalmazható. A teljesítményteszt a számítási erőforrások számára készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: Azure monitoron belül állítsa be a log Analytics munkaterület megőrzési időszakát a szervezet megfelelőségi előírásai szerint. Használja az Azure Storage-fiókokat hosszú távú/archiválási tároláshoz.

- [Log Analytics-munkaterületek naplózási megőrzési paramétereinek beállítása](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: Azure Container Registry naplók elemzése és monitorozása a rendellenes működéshez és az eredmények rendszeres áttekintéséhez. Az Azure Monitor Log Analytics munkaterülete segítségével tekintheti át a naplókat, és lekérdezéseket végezhet a naplózási adatokon.

- [Azure Container Registry naplók a diagnosztika kiértékeléséhez és a naplózáshoz](container-registry-diagnostics-audit-logs.md)

- [Log Analytics munkaterület ismertetése](/azure/azure-monitor/log-query/get-started-portal)

- [Egyéni lekérdezések végrehajtása a Azure Monitorban](/azure/azure-monitor/log-query/get-started-queries)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: az Azure log Analytics munkaterület használata a rendellenes tevékenységekkel kapcsolatos, a biztonsági naplókban és az Azure Container registryhez kapcsolódó eseményeken történő figyeléshez és riasztáshoz.

- [Azure Container Registry naplók a diagnosztika kiértékeléséhez és a naplózáshoz](container-registry-diagnostics-audit-logs.md)

- [Riasztás a log Analytics-naplófájlok adatkezeléséről](/azure/azure-monitor/learn/tutorial-response)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: nem alkalmazható. Azure Container Registry nem dolgoz fel kártevő szoftverrel kapcsolatos naplókat, illetve nem hoz létre.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: nem alkalmazható. Azure Container Registry egy végpont, és nem kezdeményez kommunikációt, és a szolgáltatás nem kérdezi le a DNS-t.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: nem alkalmazható. A teljesítményteszt a számítási erőforrások számára készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: Azure Active Directory (Azure ad) beépített szerepkörökkel rendelkezik, amelyeket explicit módon kell hozzárendelni, és lekérdezhető. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

Minden egyes Azure Container Registry esetében kövesse nyomon, hogy a beépített rendszergazdai fiók engedélyezve van-e vagy le van-e tiltva. Ha nincs használatban, tiltsa le a fiókot.

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

- [Azure Container Registry rendszergazdai fiók](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: Azure Active Directory (Azure ad) nem az alapértelmezett jelszavak fogalma. Más Azure-erőforrások, amelyek jelszó megadását igénylik a bonyolultsági követelményekkel és a jelszó minimális hosszával, amely a szolgáltatástól függően eltérő lehet. Ön felelős harmadik féltől származó alkalmazásokért és piactér-szolgáltatásért, amelyek az alapértelmezett jelszavakat használhatják.

Ha az Azure Container Registry alapértelmezett rendszergazdai fiókja engedélyezve van, az összetett jelszavak automatikusan létrejönnek, és el kell forgatni őket. Ha nincs használatban, tiltsa le a fiókot.

- [Azure Container Registry rendszergazdai fiók](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget.

Emellett hozzon létre eljárásokat a tároló-beállításjegyzék beépített rendszergazdai fiókjának engedélyezéséhez. Ha nincs használatban, tiltsa le a fiókot.

- [Azure Security Center identitás és hozzáférés ismertetése](../security-center/security-center-identity-access.md)

- [Azure Container Registry rendszergazdai fiók](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory egyszeri bejelentkezés (SSO) használata

**Útmutató**: ha lehetséges, használjon Azure Active Directory (Azure ad) SSO-t a különálló önálló hitelesítő adatok konfigurálása helyett. Azure Security Center identitás-és hozzáférés-kezelési javaslatok használata.

A tároló-beállításjegyzékhez való egyéni hozzáféréshez használja az Azure AD-vel integrált egyéni bejelentkezést.

- [Az egyszeri bejelentkezés ismertetése az Azure AD-vel](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Egyéni bejelentkezés egy tároló-beállításjegyzékbe](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: engedélyezze Azure Active Directory (Azure ad) többtényezős hitelesítését, és kövesse Azure Security Center identitás-és hozzáférés-kezelési javaslatait.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: a mancsok (emelt szintű hozzáférésű munkaállomások) használata többtényezős hitelesítéssel, amely az Azure-erőforrások bejelentkezésére és konfigurálására van konfigurálva.

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: Azure Active Directory (Azure ad) biztonsági jelentések használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik. A Azure Security Center használatával figyelheti az identitás-és hozzáférési tevékenységeket.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel engedélyezheti, hogy a hozzáférés csak az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz legyen elérhető.

- [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerrel. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (Azure ad) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá.

- [Az Azure AD jelentéskészítés ismertetése](/azure/active-directory/reports-monitoring/)

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: hozzáférhet Azure Active Directory (Azure ad) bejelentkezési tevékenységhez, a naplózási és a kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik, hogy integrálható legyen a biztonsági információkkal és az Event Management (SIEM)/monitoring eszközzel.

Ezt a folyamatot leegyszerűsítheti, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókjaihoz, és elküldi a naplókat és a bejelentkezési naplókat egy Log Analytics munkaterületre. Log Analytics munkaterületen belül konfigurálhatja a kívánt riasztásokat.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: az Azure Active Directory (Azure ad) kockázati és Identity Protection-funkciókkal konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. 

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: nem érhető el; Ügyfélszéf jelenleg nem támogatott a Azure Container Registry.

- [Ügyfélszéf támogatott szolgáltatások listája](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a bizalmas adatokat tároló vagy feldolgozó Azure Container-nyilvántartások nyomon követéséhez használjon erőforrás-címkéket.

A beállításjegyzékben található és a tároló lemezképeit vagy más összetevőit, illetve a lemezképek vagy adattárak zárolását a bizalmas adatokat tároló vagy feldolgozó lemezképek nyomon követése érdekében segíti.

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

- [A tároló lemezképének címkézésére és verziószámozására vonatkozó javaslatok](container-registry-image-tag-version.md)

- [Tároló rendszerképének zárolása egy Azure Container registryben](container-registry-image-lock.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló tároló-beállításjegyzékek, előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. A bizalmas adatok tárolására vagy feldolgozására szolgáló erőforrásoknak elég elszigeteltnek kell lenniük.

Az erőforrásokat virtuális hálózattal vagy alhálózattal kell elválasztani, megfelelően címkézve, és egy hálózati biztonsági csoport (NSG) vagy Azure Firewall által védett.

- [További Azure-előfizetések létrehozása](/azure/billing/billing-create-subscription)

- [Felügyeleti csoportok létrehozása](/azure/governance/management-groups/create)

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

- [Azure Container Registry-hozzáférés korlátozása Azure-beli virtuális hálózati vagy tűzfalszabályok használatával](container-registry-vnet.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

- [Riasztások vagy riasztások konfigurálása és a Azure Firewall megtagadása](../firewall/threat-intel.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: automatikus eszköz üzembe helyezése olyan hálózati peremhálózaton, amely figyeli a bizalmas adatok jogosulatlan átvitelét, és blokkolja az ilyen átviteleket, miközben riasztást küld az információs biztonsági szakemberek számára.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: gondoskodjon arról, hogy a Azure Container Registryhoz csatlakozó ügyfelek képesek legyenek a TLS 1,2 vagy újabb egyeztetésre. Microsoft Azure erőforrások alapértelmezés szerint a TLS 1,2-et tárgyalják.

Kövesse Azure Security Center a inaktív adatok titkosítására és az átvitel közbeni titkosításra vonatkozó ajánlásokat, ahol lehetséges.

- [A titkosítás ismertetése az Azure-ban](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el Azure Container Registry számára. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: szerepköralapú hozzáférés-vezérlés használata az erőforrásokhoz való hozzáférés szabályozásához

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával szabályozhatja az Azure Container registryben tárolt adatokhoz és erőforrásokhoz való hozzáférést. 

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

- [Szerepkörök és engedélyek Azure Container Registry](container-registry-roles.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutatás**: Ha a számítási erőforrásoknak való megfeleléshez szükség van, egy külső gyártótól származó eszközt, például egy automatizált gazdagép-alapú adatvesztés-megelőzési megoldást kell megvalósítani, hogy az adatok a rendszerből történő másolásakor is érvénybe lépjenek.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a titkosítás használata az összes Azure-erőforráson. Alapértelmezés szerint az Azure Container registryben lévő összes adatok titkosítása a Microsoft által felügyelt kulcsok használatával történik.

- [Inaktív adatok Azure-ban való titkosításának ismertetése](../security/fundamentals/encryption-atrest.md)

- [Ügyfél által felügyelt kulcsok Azure Container Registry](https://aka.ms/acr/cmk)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor erőforrás-naplókat (korábbi néven diagnosztikai naplókat) gyűjt a beállításjegyzékben lévő felhasználó által vezérelt eseményekhez. Gyűjtsön és fogyasszon el adatokat a beállításjegyzék-hitelesítési események naplózásához, és adjon meg egy teljes körű tevékenységi nyomvonalat a beállításjegyzék-összetevőkről, például a lekéréses és a lekéréses eseményekről, így diagnosztizálhatja az üzemeltetési problémákat

- [Azure Container Registry naplók a diagnosztika kiértékeléséhez és a naplózáshoz](container-registry-diagnostics-audit-logs.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információért lásd az [Azure biztonsági teljesítményteszt: biztonsági rés kezelése](../security/benchmarks/security-control-vulnerability-management.md)című témakört.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: a Azure Security Center ajánlásainak követése a biztonsági rések felmérésének elvégzéséhez a tároló rendszerképein. Az Azure Marketplace-en külső gyártótól származó megoldásokat is telepíthet a lemezkép sebezhetőségi felmérések elvégzéséhez.

- [Azure Security Center sebezhetőségi felméréssel kapcsolatos javaslatok megvalósítása](/azure/security-center/security-center-vulnerability-assessment-recommendations)

- [Az Azure Container Registry integrálása a Security Centerrel (előzetes verzió)](/azure/security-center/azure-container-registry-integration)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: a Microsoft a Azure Container registryt támogató mögöttes rendszereken végzi a javítások kezelését.

A tároló lemezkép-frissítéseinek automatizálása, amikor az operációs rendszerből és más javításokból származó alaplemezképek frissítései észlelhetők.

- [Az Azure Container Registry-feladatok alaprendszerképének frissítései](container-registry-tasks-base-images.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: a harmadik féltől származó szoftverek címeihez készült automatizált javítási megoldás üzembe helyezése

**Útmutató**: a harmadik féltől származó megoldás használatával javíthatja az alkalmazás lemezképeit.  Emellett Azure Container Registry feladatok futtatásával automatizálhatja a tároló-beállításjegyzékben található alkalmazás-lemezképek frissítéseit a biztonsági javítások vagy az alaplemezképek egyéb frissítései alapján.

- [Az ACR-feladatok alaprendszerképének frissítései](container-registry-tasks-base-images.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: az Azure Container Registry (ACR) integrálása Azure Security Centerekkel a biztonsági rések rendszeres vizsgálatának lehetővé tételéhez. Igény szerint központilag telepítheti az Azure Marketplace külső gyártótól származó megoldásait, amelyekkel időnként biztonsági réseket vizsgálhat.

- [Az Azure Container Registry integrálása a Security Centerrel (előzetes verzió)](/azure/security-center/azure-container-registry-integration)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: a Azure Container Registry (ACR) integrálása Azure Security Centerekkel a biztonsági rések rendszeres vizsgálatának lehetővé tételéhez, valamint a kockázatok besorolásához. Igény szerint központilag telepítheti az Azure Marketplace külső gyártótól származó megoldásait, amelyekkel időnként biztonsági réseket vizsgálhat és kockázati besorolást végezhet.

- [Az Azure Container Registry integrálása a Security Centerrel (előzetes verzió)](/azure/security-center/azure-container-registry-integration)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti vagy felderítheti az összes erőforrást (például számítási, tárolási, hálózati, portok és protokollok stb.) az előfizetés (ok) n belül. Győződjön meg arról, hogy a bérlőben a megfelelő (olvasási) engedélyek szerepelnek, valamint az összes Azure-előfizetés, valamint az előfizetésekben lévő erőforrások számbavétele.

Bár a klasszikus Azure-erőforrások felderítése az erőforrás-gráfon keresztül lehetséges, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: Azure Container Registry a beállításjegyzékben található rendszerképekhez használható metaadatokat, például címkéket és jegyzékfájlokat tart fenn. Ajánlott eljárások követése az összetevők címkézéséhez.

- [A jegyzékek, adattárak és lemezképek](container-registry-concepts.md)

- [A tároló lemezképének címkézésére és verziószámozására vonatkozó javaslatok](container-registry-image-tag-version.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: Azure Container Registry a beállításjegyzékben található rendszerképekhez használható metaadatokat, például címkéket és jegyzékfájlokat tart fenn. Ajánlott eljárások követése az összetevők címkézéséhez.

- [A jegyzékek, adattárak és lemezképek](container-registry-concepts.md)

- [A tároló lemezképének címkézésére és verziószámozására vonatkozó javaslatok](container-registry-image-tag-version.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: a jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: a szervezeti igényeknek megfelelően létre kell hoznia a jóváhagyott Azure-erőforrások leltárát.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az előfizetése (i) ban létrehozható erőforrások típusára.

Az Azure Resource Graph segítségével lekérdezheti vagy felderítheti az előfizetése (ke) n belüli erőforrásait.  Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

- [Az Azure Container-nyilvántartások megfelelőségének naplózása Azure Policy használatával](container-registry-azure-policy.md)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: Azure Container Registry naplók elemzése és monitorozása a rendellenes működéshez és az eredmények rendszeres áttekintéséhez. Az Azure Monitor Log Analytics munkaterülete segítségével tekintheti át a naplókat, és lekérdezéseket végezhet a naplózási adatokon.

- [Azure Container Registry naplók a diagnosztika kiértékeléséhez és a naplózáshoz](container-registry-diagnostics-audit-logs.md)

- [Log Analytics munkaterület ismertetése](/azure/azure-monitor/log-query/get-started-portal)

- [Egyéni lekérdezések végrehajtása a Azure Monitorban](/azure/azure-monitor/log-query/get-started-queries)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: a Azure Automation teljes körű irányítást biztosít a számítási feladatok és erőforrások üzembe helyezése, üzemeltetése és leszerelése során. Saját megoldást alkalmazhat a jogosulatlan Azure-erőforrások eltávolítására.

- [Bevezetés az Azure Automationbe](../automation/automation-intro.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: nem alkalmazható. A teljesítményteszt a számítási erőforrások számára lett kialakítva.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy kihasználva korlátozhatja, hogy mely szolgáltatásokat lehet kiépíteni a környezetében.

- [Az Azure Container-nyilvántartások megfelelőségének naplózása Azure Policy használatával](container-registry-azure-policy.md)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: a jóváhagyott szoftverek leltárának fenntartása

**Útmutató**: nem alkalmazható. A teljesítményteszt a számítási erőforrások számára lett kialakítva.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az operációs rendszerre jellemző konfigurációk vagy harmadik féltől származó erőforrások használata arra, hogy korlátozza a felhasználók számára az Azure számítási erőforrásokon belüli parancsfájlok végrehajtásának lehetőségét.

- [Feltételes hozzáférés konfigurálása az Azure Resources Manager elérésének blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül

**Útmutató**: az operációs rendszer specifikus konfigurációjának vagy külső erőforrásainak használata arra, hogy korlátozza a felhasználók számára az Azure számítási erőforrásokon belüli parancsfájlok futtatását.

- [Például a PowerShell-parancsfájlok végrehajtásának szabályozása Windows-környezetekben](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: az üzleti műveletekhez szükséges szoftverek, de nagyobb kockázatot jelenthetnek a szervezet számára, el kell különíteni a saját virtuális gépén és/vagy virtuális hálózatán belül, és megfelelően biztonságossá kell tennie egy Azure Firewall vagy hálózati biztonsági csoporttal.

- [Virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: Azure Policy vagy Azure Security Center használata az összes Azure-erőforrás biztonsági konfigurációjának fenntartásához.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Az Azure Container-nyilvántartások megfelelőségének naplózása Azure Policy használatával](container-registry-azure-policy.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: a biztonsági konfigurációk az összes számítási erőforráson való fenntartásához használja a Azure Security Center javaslat "a Virtual Machines biztonsági konfigurációjában található biztonsági rések szervizelése" című témakört.

- [Azure Security Center javaslatok figyelése](../security-center/security-center-recommendations.md)

- [Azure Security Center javaslatok szervizelése](../security-center/security-center-remediate-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure-erőforrások biztonságos beállításainak betartatásához használja a Azure Policy [deny] és a [telepítés ha nem létezik] lehetőséget.

- [Az Azure Container-nyilvántartások megfelelőségének naplózása Azure Policy használatával](container-registry-azure-policy.md)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: nem alkalmazható. A teljesítményteszt a számítási erőforrások számára készült.

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: ha egyéni Azure Policy-definíciókat használ, az Azure Repos használatával biztonságosan tárolhatja és kezelheti a kódot.

- [Kód tárolása az Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Az Azure Repos dokumentációja](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: nem alkalmazható. A teljesítményteszt a számítási erőforrásokra vonatkozik.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: Azure Policy használata a rendszerkonfigurációk riasztására, naplózására és betartatására. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

- [Az Azure Container-nyilvántartások megfelelőségének naplózása Azure Policy használatával](container-registry-azure-policy.md)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: konfigurációs felügyeleti eszközök telepítése operációs rendszerekhez

**Útmutató**: nem alkalmazható. A teljesítményteszt a számítási erőforrásokra vonatkozik.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: a Azure Security Center használata az Azure-erőforrások alapkonfigurációjának vizsgálatához.

A Azure Policy használatával korlátozásokat állíthat be az előfizetése (i) ban létrehozható erőforrások típusára.

- [Javaslatok szervizelése Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Az Azure Container-nyilvántartások megfelelőségének naplózása Azure Policy használatával](container-registry-azure-policy.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: nem alkalmazható. A teljesítményteszt a számítási erőforrásokra vonatkozik.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: a Managed Service Identity együttes használata a Azure Key Vault a Felhőbeli alkalmazások titkos felügyeletének egyszerűsítése és biztonságossá tétele érdekében.

- [Integráció az Azure felügyelt identitásokkal](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Key Vault létrehozása](../key-vault/general/quick-create-portal.md)

- [Hitelesítés Key Vault](../key-vault/general/authentication.md)

- [Key Vault hozzáférési szabályzatok kiosztása](../key-vault/general/assign-access-policy-portal.md)

- [Azure által felügyelt identitás használata Azure Container Registry feladatokban](container-registry-tasks-authentication-managed-identity.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: felügyelt identitások használata az Azure-szolgáltatások automatikus felügyelt identitással való ellátásához Azure Active Directory (Azure ad). A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

- [Felügyelt identitások konfigurálása](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)

- [Felügyelt identitás használata az Azure Container registryben való hitelesítéshez](container-registry-authentication-managed-identity.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információt az [Azure biztonsági teljesítményteszt: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md)című témakörben talál.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: a Microsoft antimalware használata az Azure Cloud Services és Virtual Machines az erőforrások folyamatos monitorozásához és védelméhez. Linux esetén használjon harmadik féltől származó antimalware-megoldást.

- [A Microsoft antimalware konfigurálása Cloud Services és Virtual Machines](../security/fundamentals/antimalware.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft antimalware engedélyezve van a mögöttes gazdagépen, amely támogatja az Azure-szolgáltatásokat (például Azure Container Registry), azonban nem az ügyfél tartalmán fut.

A nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata, például App Service, Data Lake Storage, Blob Storage stb.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: nem alkalmazható. A teljesítményteszt a számítási erőforrások számára készült. A Microsoft kezeli a kártevő szoftvereket a mögöttes platformon.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a Microsoft Azure tároló beállításjegyzékében lévő adatai mindig automatikusan replikálódnak a tartósság és a magas rendelkezésre állás biztosítása érdekében. Azure Container Registry másolja az adatait úgy, hogy védve legyenek a tervezett és nem tervezett eseményektől

Szükség esetén geo-replikálhat egy tároló-beállításjegyzéket a beállításjegyzék-replikák több Azure-régióban való fenntartásához. 

- [Geo-replikálás Azure Container Registry](container-registry-geo-replication.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: a tárolók rendszerképének opcionális biztonsági mentése az egyik beállításjegyzékből a másikba való importálással.

Azure Key Vault ügyfél által felügyelt kulcsok biztonsági mentése az Azure parancssori eszközeivel vagy SDK-k használatával.

- [Tároló-lemezképek importálása egy tároló-beállításjegyzékbe](container-registry-import-images.md)

- [Key Vault-kulcsok biztonsági mentése az Azure-ban](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: az Azure parancssori eszközeivel vagy SDK-k használatával a Azure Key Vault felügyelt kulcs biztonsági másolatának visszaállítása.

- [Azure Key Vault kulcsok visszaállítása az Azure-ban](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: engedélyezheti Soft-Deleteét Azure Key Vault a kulcsok véletlen vagy rosszindulatú Törlés elleni védelemmel való ellátásához.

- [A Soft-Delete engedélyezése Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

- [Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül](../security-center/security-center-planning-and-operations-guide.md)

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásával kapcsolatos támogatáshoz](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Azure Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben nem termelt), és hozzon létre egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és edzésprogramjának kidolgozásához](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az ügyfél adatait egy törvénytelen vagy jogosulatlan fél is hozzáférte.  A problémák megoldása érdekében tekintse át az incidenseket a tény után.

- [Az Azure Security Center biztonsági kapcsolattartójának beállítása](../security-center/security-center-provide-security-contact-details.md)

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

**Útmutató**: kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket:  https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- [További információt a Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatos, a Microsoft által felügyelt felhő-infrastruktúra,-szolgáltatások és-alkalmazások fejlesztésére vonatkozó stratégiáról és végrehajtásáról itt talál.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
