---
title: Container Instances Azure biztonsági alapterve
description: Container Instances Azure biztonsági alapterve
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/27/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 3f4e855c52488652b88a3b14e9817f1755167b67
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806121"
---
# <a name="azure-security-baseline-for-container-instances"></a>Container Instances Azure biztonsági alapterve

Az Container Instances Azure biztonsági alapkonfigurációja olyan javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági állapotát.

A szolgáltatás alapterve az [Azure Security Benchmark 1,0-es verziójából](../security/benchmarks/overview.md)származik, amely javaslatokat tesz arra vonatkozóan, hogy miként védheti meg felhőalapú megoldásait az Azure-ban az ajánlott eljárásokat ismertető útmutató segítségével.

További információ: az [Azure biztonsági alaptervek áttekintése](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Hálózati biztonság

*További információkért lásd [: biztonsági ellenőrzés: hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: az erőforrások védelme hálózati biztonsági csoportokkal vagy Azure Firewall a Virtual Network

**Útmutató**: az Azure Virtual Network biztonságos, privát hálózatkezelést biztosít az Azure-hoz és a helyszíni erőforrásokhoz. Az Azure-beli virtuális hálózattal integrálhatja Azure Container Instances tároló csoportjait. 

* [Virtuális hálózati forgatókönyvek és erőforrások – Azure Container Instances](./container-instances-virtual-network-concepts.md)

* [Tárolópéldányok üzembe helyezése egy Azure virtuális hálózaton](./container-instances-vnet.md)

* [Biztonsági konfigurációval rendelkező NSG létrehozása](../virtual-network/tutorial-filter-network-traffic.md)

* [Azure Firewall üzembe helyezése és konfigurálása](../firewall/tutorial-firewall-deploy-portal.md)


**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: az Azure-ban a hálózati erőforrások védelme érdekében a Azure Security Center használata és a hálózati védelmi javaslatok szervizelése. Engedélyezze a NSG folyamat naplóit, és küldje el a naplókat egy Storage-fiókba a forgalom naplózása érdekében.

* [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [A hálózati erőforrások megóvása](../security-center/security-center-network-recommendations.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: az Azure webalkalmazási TŰZFAL (WAF) üzembe helyezése a Azure Container instances üzemeltetett kritikus fontosságú webalkalmazások előtt a bejövő forgalom további ellenőrzéséhez. Diagnosztikai beállítás engedélyezése a WAF és a naplók betöltéséhez egy Storage-fiókba, az Event hub-ba vagy a Log Analytics-munkaterületre.

* [Az Azure WAF üzembe helyezése](../web-application-firewall/ag/create-waf-policy-ag.md)



**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: a DDoS standard Protection engedélyezése Azure-beli virtuális hálózatokon a DDOS-támadások elleni védelem érdekében. Az ismert kártékony IP-címekkel folytatott kommunikáció megtagadásához használja Azure Security Center integrált veszélyforrások felderítését. Azure Firewall üzembe helyezése minden szervezet hálózati határain, a fenyegetések felderítése engedélyezve van, és a "riasztás és megtagadás" értékre van állítva a kártékony hálózati forgalom esetében. A NSG konfigurálásához használja a Azure Security Center igény szerinti hálózati hozzáférését, hogy korlátozza a végpontok a jóváhagyott IP-címekre való kitettségét korlátozott időtartamra. Azure Security Center adaptív hálózati korlátozással olyan NSG-konfigurációkat javasolhat, amelyek a portok és a forrás IP-címeket korlátozzák a tényleges forgalom és a veszélyforrások felderítése alapján. 

* [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

* [Az Azure Security Center integrált veszélyforrások felderítésének megismerése](../security-center/azure-defender.md)

* [Azure Security Center adaptív hálózat megerősítésének ismertetése](../security-center/security-center-adaptive-network-hardening.md)

* [Azure Security Center az időponthoz tartozó hálózati Access Control](../security-center/security-center-just-in-time.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: hálózati csomagok és adatforgalmi naplók rögzítése

**Útmutató**: Ha hálózati biztonsági csoportokat (NSG) használ a virtuális hálózat implementációjában, engedélyezze a NSG-folyamat naplóit a Azure Container Instanceshoz delegált alhálózathoz csatolt NSG számára. Jegyezze fel a NSG folyamat naplóit egy Azure Storage-fiókba a flow-rekordok létrehozásához. Ha a rendellenes tevékenységek kivizsgálásához szükséges, engedélyezze az Azure Network Watcher a csomagok rögzítését.

* [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [A Network Watcher engedélyezése](../network-watcher/network-watcher-create.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati alapú behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: válasszon ki egy olyan ajánlatot az Azure piactéren, amely támogatja az azonosítók/IP-címek funkciót a hasznos adatok ellenőrzésére szolgáló funkciókkal. Ha a betörési észlelés és/vagy a tartalom-ellenőrzésen alapuló megelőzés nem követelmény, akkor az Azure Firewall a veszélyforrások felderítésére használható. Azure Firewall fenyegetés intelligencián alapuló szűréssel riasztást kaphat, és megtagadhatja az ismert kártékony IP-címek és tartományok felé irányuló forgalmat. Az IP-címek és -tartományok forrása a Microsoft veszélyforrás-felderítési hírcsatornája.

Az Ön által választott tűzfal-megoldás üzembe helyezésével azonosíthatja és/vagy megtagadhatja a kártékony forgalmat.

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

* [Riasztások konfigurálása Azure Firewall](../firewall/threat-intel.md)

* [Üzembe helyezés virtuális hálózatban – Azure Container Instances](./container-instances-vnet.md) 



**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: Azure-Application Gateway üzembe helyezése a megbízható tanúsítványokhoz engedélyezett https/SSL-alapú webalkalmazásokhoz.

* [Application Gateway üzembe helyezése](../application-gateway/quick-create-portal.md)

* [A Application Gateway konfigurálása a HTTPS használatára](../application-gateway/create-ssl-portal.md) 

* [A 7. rétegbeli terheléselosztás és az Azure-webalkalmazás-átjárók ismertetése](../application-gateway/overview.md)

* [Statikus IP-cím közzététele egy tároló csoport számára](./container-instances-application-gateway.md)

* [Üzembe helyezés virtuális hálózatban – Azure Container Instances](./container-instances-vnet.md)



**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: hálózati biztonsági csoportok vagy Azure Firewall hálózati hozzáférés-vezérlésének definiálásához használjon Virtual Network szolgáltatás-címkéket. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például ApiManagement), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával. 

Emellett az alkalmazás biztonsági csoportjaival is egyszerűsítheti az összetett biztonsági konfigurációt. Az alkalmazásbiztonsági csoportokkal az alkalmazás struktúrájának természetes bővítményeként konfigurálhatja a hálózati biztonságot, így csoportosíthatja a virtuális gépeket, és ezen csoportok alapján meghatározhatja a hálózati biztonsági szabályokat. 

* [A szolgáltatási címkék megismerése és használata](../virtual-network/service-tags-overview.md) 

* [Az alkalmazás biztonsági csoportjai megismerése és használata](../virtual-network/network-security-groups-overview.md#application-security-groups)

* [Üzembe helyezés virtuális hálózatban – Azure Container Instances](./container-instances-vnet.md)



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: az Azure-tervrajzok használatával egyszerűsítheti a nagyméretű Azure-alapú üzembe helyezéseket, például az Azure Resources Manager-sablonokat, az Azure RBAC-vezérlőket és a szabályzatokat egyetlen terv definíciójában. A tervrajzot új előfizetésekre alkalmazhatja, és az irányítás és felügyelet finomhangolását a verziószámozás segítségével végezheti el. 

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy minták a hálózatkezeléshez](../governance/policy/samples/built-in-policies.md#network)

* [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: a NSG és a hálózati biztonsággal és a forgalommal kapcsolatos egyéb erőforrásokhoz használható címkék használata. Az egyes NSG-szabályok esetében a "Leírás" mezővel adhatja meg az üzleti igényeket és/vagy időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatra irányuló adatforgalmat.

A címkézéshez kapcsolódó beépített Azure Policy-definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.

A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon.

* [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

* [Üzembe helyezés virtuális hálózatban – Azure Container Instances](./container-instances-vnet.md)

* [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata a hálózati erőforrás-konfigurációk figyelésére és a tároló példányaihoz kapcsolódó hálózati erőforrások változásainak észlelésére. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

* [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Riasztások létrehozása a Azure Monitorban](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

*További információ [: Security Control: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: a Microsoft fenntartja az Azure-erőforrások időforrásait, azonban lehetősége van a számítási erőforrások időszinkronizálási beállításainak kezelésére. Futtasson például egy időzített szinkronizációs parancsot egy futó tárolóban.

* [Az Azure számítási erőforrások időszinkronizálásának konfigurálása](../virtual-machines/windows/time-sync.md)

* [Parancs végrehajtása egy futó Azure Container-példányban](./container-instances-exec.md)



**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: naplók beolvasása Azure monitor használatával az Azure-beli tárolók csoportja által generált biztonsági adatokat összesítve. A Azure Monitoron belül Log Analytics munkaterület (ek) használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat a hosszú távú/archiválási tároláshoz.

* [Tároló csoport és példány naplózása Azure Monitor naplókkal](./container-instances-log-analytics.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a Azure monitor erőforrás-naplókat (korábbi néven diagnosztikai naplókat) gyűjt a beállításjegyzékben lévő felhasználó által vezérelt eseményekhez. A Azure Container Instances beépített támogatást biztosít a tároló-és az események, valamint a tároló-naplók küldéséhez Azure Monitor naplókba.

* [Tároló csoport és példány naplózása Azure Monitor naplókkal](../container-registry/container-registry-diagnostics-audit-logs.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: nem alkalmazható. Ez az útmutató számítási erőforrások IaaS szolgál.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: Azure monitoron belül állítsa be a log Analytics munkaterület megőrzési időszakát a szervezet megfelelőségi előírásai szerint. Használja az Azure Storage-fiókokat hosszú távú/archiválási tároláshoz.

* [Log Analytics-munkaterületek naplózási megőrzési paramétereinek beállítása](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: a rendellenes viselkedésre és a rendszeres eredményekre vonatkozó naplók elemzése és figyelése. Az Azure Monitor Log Analytics munkaterülete segítségével tekintheti át a naplókat, és lekérdezéseket végezhet a naplózási adatokon. 

* [Tároló csoport és példány naplózása Azure Monitor naplókkal](./container-instances-log-analytics.md)

* [Log Analytics munkaterület ismertetése](../azure-monitor/log-query/log-analytics-tutorial.md)

* [Egyéni lekérdezések végrehajtása a Azure Monitorban](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: riasztások engedélyezése rendellenes tevékenységhez

**Útmutató**: log Analytics munkaterület használata a biztonsági naplókban és eseményekben található rendellenes tevékenységekkel kapcsolatos figyeléshez és riasztásokhoz. 

* [Tároló csoport és példány naplózása Azure Monitor naplókkal](./container-instances-log-analytics.md)

* [Riasztás a log Analytics-naplófájlok adatkezeléséről](../azure-monitor/learn/tutorial-response.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: Ha tárolóban szeretné futtatni, a saját kártevő-ellenes megoldást és az események gyűjtését kell megadnia. 


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: Ha a tárolóban a DNS-naplók lekérdezéséhez szükség van, adja meg a saját megoldását.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: ha szükséges, konfigurálja a konzol naplózását egy futó tároló-példányban.

* [Parancs végrehajtása egy futó Azure Container-példányban](./container-instances-exec.md)



**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Ügyfél

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információkért lásd: a [biztonság szabályozása: identitás-és hozzáférés-vezérlés](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: Azure Active Directory (Azure ad) beépített szerepkörökkel rendelkezik, amelyeket explicit módon kell hozzárendelni, és lekérdezhető. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

Ha Azure Container Instances-vel rendelkező Azure Container registryt használ, minden egyes Azure Container Registry esetében kövesse nyomon, hogy a beépített rendszergazdai fiók engedélyezve van-e vagy le van-e tiltva. Ha nincs használatban, tiltsa le a fiókot.

* [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [Azure Container Registry rendszergazdai fiók](../container-registry/container-registry-authentication.md#admin-account)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: Azure Active Directory (Azure ad) nem az alapértelmezett jelszavak fogalma. Más Azure-erőforrások, amelyek jelszó megadását igénylik a bonyolultsági követelményekkel és a jelszó minimális hosszával, amely a szolgáltatástól függően eltérő lehet. Ön felelős harmadik féltől származó alkalmazásokért és piactér-szolgáltatásért, amelyek az alapértelmezett jelszavakat használhatják.

Ha Azure Container registryt használ a Azure Container Instances használatával, ha az Azure Container Registry alapértelmezett rendszergazdai fiókja engedélyezve van, az összetett jelszavak automatikusan létrejönnek, és el kell forgatni őket. Ha nincs használatban, tiltsa le a fiókot.

* [Azure Container Registry rendszergazdai fiók](../container-registry/container-registry-authentication.md#admin-account)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget.

Ha Azure Container Instances használatával Azure Container registryt használ, hozzon létre eljárásokat a tároló-beállításjegyzék beépített rendszergazdai fiókjának engedélyezéséhez. Ha nincs használatban, tiltsa le a fiókot.

* [Azure Security Center identitás és hozzáférés ismertetése](../security-center/security-center-identity-access.md)

* [Azure Container Registry rendszergazdai fiók](../container-registry/container-registry-authentication.md#admin-account)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: ha lehetséges, használjon Azure Active Directory SSO-t a különálló önálló hitelesítő adatok konfigurálása helyett. Azure Security Center identitás-és hozzáférés-kezelési javaslatok használata.

* [Az egyszeri bejelentkezés ismertetése az Azure AD-vel](../active-directory/manage-apps/what-is-single-sign-on.md)


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: engedélyezze az Azure Active Directory (Azure ad) multi-Factor Authentication (MFA) használatát, és kövesse Azure Security Center identitás-és hozzáférés-kezelési javaslatait.

* [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

* [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: az Azure-erőforrások bejelentkezésére és konfigurálására szolgáló, az MFA használatára konfigurált, a mancsok (emelt szintű hozzáférési munkaállomások) használata.

* [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: Azure Active Directory (Azure ad) biztonsági jelentések használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik. A Azure Security Center használatával figyelheti az identitás-és hozzáférési tevékenységeket.

* [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md)

* [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel engedélyezheti, hogy a hozzáférés csak az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz legyen elérhető.

* [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerrel. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

* [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (Azure ad) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá.

* [Az Azure AD jelentéskészítés ismertetése](../active-directory/reports-monitoring/index.yml)

* [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: az inaktivált fiókok elérésére irányuló kísérletek figyelése

**Útmutató**: hozzáférhet Azure Active Directory (Azure ad) bejelentkezési tevékenységhez, a naplózási és a kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik, hogy integrálható legyen a biztonsági információkkal és az Event Management (SIEM)/monitoring eszközzel.

Ezt a folyamatot leegyszerűsítheti Azure Active Directory felhasználói fiókok diagnosztikai beállításainak létrehozásával, valamint a naplók és a bejelentkezési naplók Log Analytics munkaterületre való elküldésével. Log Analytics munkaterületen belül konfigurálhatja a kívánt riasztásokat.

* [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: az Azure Active Directory (Azure ad) kockázati és Identity Protection-funkciókkal konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat.

* [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

* [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: jelenleg nem érhető el; A Ügyfélszéf jelenleg nem támogatott Azure Container Instances esetén.

* [Ügyfélszéf támogatott szolgáltatások listája](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="data-protection"></a>Adatvédelem

*További információkért lásd [: biztonsági ellenőrzés:](../security/benchmarks/security-control-data-protection.md)adatvédelem.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a bizalmas adatokat tároló vagy feldolgozó Azure Container instances követéséhez használjon erőforrás-címkéket. 

A címke-és verziójú tároló lemezképei a bizalmas adatokat tároló vagy feldolgozó lemezképek nyomon követésében nyújtanak segítséget.

* [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

* [A tároló lemezképének címkézésére és verziószámozására vonatkozó javaslatok](../container-registry/container-registry-image-tag-version.md)


**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. Az erőforrásokat VNet/alhálózattal kell elválasztani, és megfelelő címkével kell ellátni, és egy NSG vagy Azure Firewall által védettnek kell lennie. A bizalmas adatok tárolására vagy feldolgozására szolgáló erőforrásoknak elég elszigeteltnek kell lenniük.

* [Parancs végrehajtása egy futó Azure Container-példányban](./container-instances-exec.md)

* [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

* [Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)

* [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

* [Üzembe helyezés virtuális hálózatban – Azure Container Instances](./container-instances-vnet.md) 
* [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

* [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

* [Riasztások vagy riasztások konfigurálása és a Azure Firewall megtagadása](../firewall/threat-intel.md)


**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: automatikus eszköz üzembe helyezése olyan hálózati peremhálózaton, amely figyeli a bizalmas adatok jogosulatlan átvitelét, és blokkolja az ilyen átviteleket, miközben riasztást küld az információs biztonsági szakemberek számára. A jogosulatlan adatátvitel figyelése és letiltása az Azure-fájlmegosztás és a Container instances szolgáltatáshoz csatlakoztatott egyéb kötetek között.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

* [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

* [Üzembe helyezés virtuális hálózatban – Azure Container Instances](./container-instances-vnet.md) 

* [Azure-fájlmegosztás csatlakoztatása az Azure Container Instancesben](./container-instances-volume-azure-files.md)


**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: Ellenőrizze, hogy az Azure Container-csoportokhoz csatlakozó ügyfelek képesek-e a TLS 1,2-es vagy újabb egyeztetésére. Microsoft Azure erőforrások alapértelmezés szerint a TLS 1,2-et tárgyalják.

Kövesse Azure Security Center a inaktív adatok titkosítására és az átvitel közbeni titkosításra vonatkozó ajánlásokat, ahol lehetséges.

* [A titkosítás ismertetése az Azure-ban](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az adatazonosítási, besorolási és veszteség-megelőzési funkciók jelenleg nem érhetők el Azure Container instances számára. Olyan tároló-csoportokat címkéz fel, amelyek bizalmas adatokat dolgoznak fel, és amelyek megfelelőségi célokból a harmadik féltől származó megoldást implementálnak.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

* [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Megosztott

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata a Azure Container instances-adatokhoz és erőforrásokhoz való hozzáférés szabályozásához. 

* [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások IaaS szolgál.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

* [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Megosztott

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: alapértelmezés szerint a Azure Container instances összes központi telepítési adatai a Microsoft által felügyelt kulcsok használatával titkosítva vannak. Igény szerint kezelheti a titkosítást a saját kulcsával (ügyfél által felügyelt kulcs).

* [Inaktív adatok Azure-ban való titkosításának ismertetése](../security/fundamentals/encryption-atrest.md)

* [Központi telepítési adataik titkosítása Azure Container Instances](./container-instances-encrypt-data.md)



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használatával riasztásokat hozhat létre, amelyekkel a rendszer a tároló-csoportokat és a tároló-példányokat érintő módosításokat hajt végre. 

* [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/platform/alerts-activity-log.md)


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ [: Security Control: sebezhetőségi kezelés](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: megoldások megvalósítása a tároló-lemezképek egy privát beállításjegyzékben való vizsgálatához és a lehetséges biztonsági rések azonosításához. A Azure Container Registryban tárolt tároló-lemezképeken a sebezhetőségi felmérések elvégzéséhez Azure Security Center ajánlásokat követve. Az Azure Marketplace-en külső gyártótól származó megoldásokat is telepíthet a lemezkép sebezhetőségi felmérések elvégzéséhez.

* [A Azure Container Instances biztonsági szempontjai](./container-instances-image-security.md)

* [Azure Container Registry integráció a Security Center](../security-center/defender-for-container-registries-introduction.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: a Microsoft a futó tárolókat támogató mögöttes rendszereken végzi a javítások kezelését.

Egyéni vagy harmadik féltől származó megoldás használata a tároló lemezképének javításához. Ha Azure Container Registry tároló lemezképeit tárolja, futtassa Azure Container Registry feladatokat, hogy automatizálja a frissítéseket a tároló beállításjegyzékében a biztonsági javítások vagy az operációsrendszer-lemezképek egyéb frissítései alapján.

* [A Azure Container Instances biztonsági szempontjai](./container-instances-image-security.md)

* [Az Azure Container Registry-feladatok alaprendszerképének frissítései](../container-registry/container-registry-tasks-base-images.md)


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: a harmadik féltől származó szoftveres javításokat kezelő megoldás telepítése

**Útmutató**: egyéni vagy harmadik féltől származó megoldás használata a tároló lemezképének javításához. Ha Azure Container Registry tároló lemezképeit tárolja, futtassa Azure Container Registry feladatokat, hogy automatizálja a frissítéseket a tároló beállításjegyzékében a biztonsági javítások vagy az operációsrendszer-lemezképek egyéb frissítései alapján.

* [A Azure Container Instances biztonsági szempontjai](./container-instances-image-security.md)

* [Az ACR-feladatok alaprendszerképének frissítései](../container-registry/container-registry-tasks-base-images.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: a képvizsgálat eredményeinek exportálása konzisztens időközönként, valamint az eredmények összehasonlítása annak ellenőrzéséhez, hogy a biztonsági rések szervizelése megtörtént-e. Ha Azure Container Registry tároló lemezképeit tárolja, a beállításjegyzéket integrálja Azure Security Center a biztonsági rések rendszeres ellenőrzésének engedélyezéséhez. Igény szerint központilag telepítheti az Azure Marketplace külső gyártótól származó megoldásait, amelyekkel időnként biztonsági réseket vizsgálhat.

* [A Azure Container Instances biztonsági szempontjai](./container-instances-image-security.md)

* [Azure Container Registry integráció a Security Center](../security-center/defender-for-container-registries-introduction.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: Ha Azure Container Registry tároló lemezképeit tárolja, integrálja a beállításjegyzéket Azure Security Center a biztonsági rések rendszeres vizsgálatának engedélyezéséhez és a kockázatok besorolásához. Igény szerint központilag telepítheti az Azure Marketplace külső gyártótól származó megoldásait, amelyekkel időnként biztonsági réseket vizsgálhat és kockázati besorolást végezhet.

* [A Azure Container Instances biztonsági szempontjai](./container-instances-image-security.md)

* [Azure Container Registry integráció a Security Center](../security-center/defender-for-container-registries-introduction.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="inventory-and-asset-management"></a>Leltár- és eszközfelügyelet

*További információkért lásd [: biztonsági vezérlés: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: az Azure Asset Discovery használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti vagy felderítheti az összes erőforrást (például számítási, tárolási, hálózati, portok és protokollok stb.) az előfizetés (ok) n belül. Győződjön meg arról, hogy a bérlőben a megfelelő (olvasási) engedélyek szerepelnek, valamint az összes Azure-előfizetés, valamint az előfizetésekben lévő erőforrások számbavétele.

Bár a klasszikus Azure-erőforrások felderítése az erőforrás-gráfon keresztül lehetséges, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

* [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

* [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkék alkalmazása Azure Container instances és kapcsolódó erőforrásokhoz, amelyekkel a metaadatok logikailag rendezhetők a besorolásba.

* [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és az elkülönített előfizetések használata, ha szükséges, az eszközök rendszerezéséhez és nyomon követéséhez. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

* [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

* [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

* [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: a jóváhagyott Azure-erőforrások és-szoftverek leltárának fenntartása

**Útmutató**: a szervezeti igényeknek megfelelően létre kell hoznia a jóváhagyott Azure-erőforrások leltárát.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az előfizetése (i) ban létrehozható erőforrások típusára.

Az Azure Resource Graph segítségével lekérdezheti vagy felderítheti az előfizetése (ke) n belüli erőforrásait. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

* [Az Azure Container-nyilvántartások megfelelőségének naplózása Azure Policy használatával](../container-registry/container-registry-azure-policy.md)

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: saját megoldás implementálása, vagy harmadik féltől származó megoldás a jóváhagyott tároló alkalmazások leltározására. 

Hozzon létre megoldásokat a tárolói rendszerképek vizsgálatához egy privát beállításjegyzékben, és azonosítsa a lehetséges biztonsági réseket. A Azure Security Center ajánlásainak követésével elvégezheti a sebezhetőségi felméréseket a Azure Container Registry tárolt tároló-lemezképeken. Az Azure Marketplace-en külső gyártótól származó megoldásokat is telepíthet a lemezkép sebezhetőségi felmérések elvégzéséhez.

Figyelje Azure Container Instances naplókat a rendellenes működéshez, és rendszeresen tekintse át az eredményeket. Az Azure Monitor Log Analytics munkaterülete segítségével tekintheti át a naplókat, és lekérdezéseket végezhet a naplózási adatokon.

* [Tároló csoport és példány naplózása Azure Monitor naplókkal](./container-instances-log-analytics.md)

* [Log Analytics munkaterület ismertetése](../azure-monitor/log-query/log-analytics-tutorial.md)

* [Egyéni lekérdezések végrehajtása a Azure Monitorban](../azure-monitor/log-query/get-started-queries.md)

* [A Azure Container Instances biztonsági szempontjai](./container-instances-image-security.md)
* [Azure Container Registry integráció a Security Center](../security-center/defender-for-container-registries-introduction.md)



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: a Azure Automation teljes körű irányítást biztosít a számítási feladatok és erőforrások üzembe helyezése, üzemeltetése és leszerelése során. Saját megoldást alkalmazhat a jogosulatlan Azure-erőforrások és szoftveralkalmazások eltávolítására.

* [Bevezetés az Azure Automationbe](../automation/automation-intro.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: a címkék és a verziók tárolójának lemezképei a jóváhagyott alkalmazásokat futtató rendszerképek nyomon követéséhez.
* [A tároló lemezképének címkézésére és verziószámozására vonatkozó javaslatok](../container-registry/container-registry-image-tag-version.md)


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozhatja, hogy mely szolgáltatásokat lehet kiépíteni a környezetében.

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="610-implement-approved-application-list"></a>6,10: jóváhagyott alkalmazások listájának implementálása

**Útmutató**: a címkék és a verziók tárolójának lemezképei a jóváhagyott alkalmazásokat futtató rendszerképek nyomon követéséhez.
* [A tároló lemezképének címkézésére és verziószámozására vonatkozó javaslatok](../container-registry/container-registry-image-tag-version.md)


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsbrdiv"></a>6,11: <div>A felhasználók Azure Resource Manager interakciójának korlátozása parancsfájlok használatával<br></div>

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek az Azure Resources Managerrel való interakcióra az "Microsoft Azure felügyelet" alkalmazás "hozzáférés letiltása" beállításával. 

* [Feltételes hozzáférés konfigurálása az Azure Resources Manager elérésének blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül

**Útmutató**: a Azure Container Instanceshoz hozzáféréssel rendelkező összes felhasználó futtathat parancsfájlokat a tárolón belül.

Különböző Azure-előfizetések vagy-felügyeleti csoportok használatával kezelheti és tekintheti meg Azure Container Instances erőforrásokhoz való hozzáférést, illetve virtuális hálózatokkal és NSG vagy Azure Firewallekkel elkülönítheti az erőforrásokat.

* [Parancs végrehajtása egy futó Azure Container-példányban](./container-instances-exec.md)

* [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

* [Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)

* [Üzembe helyezés virtuális hálózatban – Azure Container Instances](./container-instances-vnet.md)

* [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

* [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: az üzleti műveletekhez szükséges szoftverek, de nagyobb kockázatot jelenthetnek a szervezet számára, el kell különíteni a saját virtuális hálózatán belül, és megfelelő védelemmel kell rendelkeznie egy Azure Firewall vagy hálózati biztonsági csoporttal.

* [Üzembe helyezés virtuális hálózatban – Azure Container Instances](./container-instances-vnet.md) 

* [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információkért lásd [: biztonság-vezérlés: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: jóváhagyott tároló csoport konfigurációjának fenntartása Azure Resource Manager sablon használatával vagy YAML-fájlba való exportálással. Használja a Azure Policy a kapcsolódó Azure-erőforrások biztonsági konfigurációinak fenntartásához.

* [Tárolócsoportok az Azure Container Instancesben](container-instances-container-groups.md#deployment)

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: egyéni vagy harmadik féltől származó megoldás használata a tároló lemezképének javításához. Ha Azure Container Registry tároló lemezképeit tárolja, futtassa Azure Container Registry feladatokat, hogy automatizálja a frissítéseket a tároló beállításjegyzékében a biztonsági javítások vagy az operációsrendszer-lemezképek egyéb frissítései alapján. 

* [Az Azure Container Registry-feladatok alaprendszerképének frissítései](../container-registry/container-registry-tasks-base-images.md)



**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure Policy [megtagadás] és a [telepítés, ha nem létezik] az Azure-erőforrások biztonságos beállításainak betartatására használható.

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: megoldások megvalósítása a tároló-lemezképek egy privát beállításjegyzékben való vizsgálatához és az operációsrendszer-konfigurációk lehetséges sebezhetőségének azonosításához. A Azure Security Center ajánlásainak követésével elvégezheti a sebezhetőségi felméréseket a Azure Container Registry tárolt tároló-lemezképeken. Az Azure Marketplace-en külső gyártótól származó megoldásokat is telepíthet a lemezkép sebezhetőségi felmérések elvégzéséhez.

Egyéni vagy harmadik féltől származó megoldás használata a tároló lemezképének javításához. Ha Azure Container Registry tároló lemezképeit tárolja, futtassa Azure Container Registry feladatokat, hogy automatizálja a frissítéseket a tároló beállításjegyzékében a biztonsági javítások vagy az operációsrendszer-lemezképek egyéb frissítései alapján. 

* [Tároló-figyelési és-ellenőrzési biztonsági javaslatok a Azure Container Instances](./container-instances-image-security.md)

* [Azure Container Registry integráció a Security Center](../security-center/defender-for-container-registries-introduction.md)
* [Az Azure Container Registry-feladatok alaprendszerképének frissítései](../container-registry/container-registry-tasks-base-images.md)



**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: ARM-sablonok, YAML-fájlok és egyéni Azure-házirend-definíciók biztonságos tárolása és kezelése a verziókövetés számára.

* [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Az Azure Repos dokumentációja](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: tároló rendszerképeinek tárolása Azure Container Registry és az Azure RBAC használatával biztosítható, hogy csak a jogosult felhasználók férhessenek hozzá a lemezképekhez.

* [Az Azure RBAC ismertetése](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Az Azure-RBAC megismerése Container Registry](../container-registry/container-registry-roles.md)

* [Az Azure RBAC konfigurálása](../role-based-access-control/quickstart-assign-role-user-portal.md)


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: rendszerkonfiguráció-felügyeleti eszközök telepítése

**Útmutató**: Azure Policy használata a rendszerkonfigurációk riasztására, naplózására és betartatására. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: rendszerkonfiguráció-felügyeleti eszközök központi telepítése operációs rendszerekhez

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások IaaS szolgál.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: az Azure-szolgáltatások automatizált konfigurációs figyelésének megvalósítása

**Útmutató**: a Azure Security Center használata az Azure-erőforrások alapkonfigurációjának vizsgálatához.

A Azure Policy használatával korlátozásokat állíthat be az előfizetése (i) ban létrehozható erőforrások típusára.

* [Javaslatok szervizelése Azure Security Center](../security-center/security-center-remediate-recommendations.md)

* [Az Azure Container-nyilvántartások megfelelőségének naplózása Azure Policy használatával](../container-registry/container-registry-azure-policy.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: Ha Azure Container registryt használ a tároló-lemezképek tárolására, a Azure Security Center segítségével elvégezheti a tárolók operációsrendszer-és Docker-beállításainak alapkonfigurációját.

* [Az Azure Security Center tárolókra vonatkozó ajánlásainak értelmezése](../security-center/container-security.md)


**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: a Managed Service Identity együttes használata a Azure Key Vault a Felhőbeli alkalmazások titkos felügyeletének egyszerűsítése és biztonságossá tétele érdekében.

* [Integráció az Azure felügyelt identitásokkal](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Key Vault létrehozása](../key-vault/secrets/quick-create-portal.md)

* [Hitelesítés Key Vault](../key-vault/general/authentication.md)

* [Key Vault hozzáférési szabályzatok kiosztása](../key-vault/general/assign-access-policy-portal.md)

* [Felügyelt identitások használata az Azure Container Instancesszel](./container-instances-managed-identity.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: felügyelt identitások használatával biztosíthatja az Azure-szolgáltatások automatikus felügyelt identitását az Azure ad-ben. A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

* [Felügyelt identitások konfigurálása](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

* [Felügyelt identitások használata az Azure Container Instancesszel](./container-instances-managed-identity.md)



**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

* [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információkért lásd [: biztonsági ellenőrzés: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások IaaS szolgál.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft antimalware engedélyezve van a mögöttes gazdagépen, amely támogatja az Azure-szolgáltatásokat (például Azure Container instances), azonban nem az ügyfél tartalmán fut.

A nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata, például App Service, Data Lake Storage, Blob Storage stb. 


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások IaaS szolgál.

A Microsoft kártevő szoftveres verziója engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure Container Instances), azonban az nem az ügyfél tartalmán fut.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="data-recovery"></a>Adat-helyreállítás

*További információkért lásd [: biztonsági ellenőrzés: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutatás**: engedélyezze Azure Backup és konfigurálja a biztonsági mentési forrást (például a tároló csoportokhoz csatlakoztatott fájlmegosztást), valamint a kívánt gyakoriságot és megőrzési időt. 

* [A Azure Backup engedélyezése](../backup/index.yml)

* [Azure-fájlmegosztás csatlakoztatása az Azure Container Instancesben](./container-instances-volume-azure-files.md)


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és biztonsági másolat készítése bármely ügyfél által felügyelt kulcsról

**Útmutató**: az Azure Key Vault ügyfél által felügyelt kulcsok biztonsági mentése az Azure parancssori eszközeivel vagy SDK-k használatával.

A tároló lemezképeit a beállításjegyzékből egy másikba importálva is igény szerint biztonsági mentést készíthet.
* [Key Vault-kulcsok biztonsági mentése az Azure-ban](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

* [Tároló-lemezképek importálása egy tároló-beállításjegyzékbe](../container-registry/container-registry-import-images.md)



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: az Azure parancssori eszközeivel vagy SDK-k használatával a Azure Key Vault felügyelt ügyfelek biztonsági másolatának visszaállítása

* [Azure Key Vault kulcsok visszaállítása az Azure-ban](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Központi telepítési adatai titkosítása – Azure Container Instances](./container-instances-encrypt-data.md)



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: engedélyezheti Soft-Deleteét Azure Key Vault a kulcsok véletlen vagy rosszindulatú Törlés elleni védelemmel való ellátásához.

* [A Soft-Delete engedélyezése Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ [: Security Control: incidens válasza](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásához.

* [Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül](../security-center/security-center-planning-and-operations-guide.md)

* [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Az NIST útmutatója a biztonsági incidensek kezeléséhez](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Azure Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett egyértelműen megjelölheti az előfizetéseket (például:. éles üzemben, nem éles környezetben), és hozzon létre egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához.


**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

* [Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és edzésprogramjának kidolgozásához](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az ügyfél adatait egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után.

* [Az Azure Security Center biztonsági kapcsolattartójának beállítása](../security-center/security-center-provide-security-contact-details.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és-javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

* [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

* [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a válaszokat a "Logic apps" használatával a biztonsági riasztások és javaslatok esetében.

* [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../security-center/workflow-automation.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért lásd [: biztonsági ellenőrzés: behatolási tesztek és Red Team-gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: rendszeres penetrációs tesztelést végez az Azure-erőforrásokon, és gondoskodik az összes kritikus biztonsági vizsgálat 60 napon belüli szervizeléséről

**Útmutató**: kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatban a Microsoft stratégiáját és a Red Teaming és a Live site penetráció tesztelését is használhatja.

* [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure biztonsági teljesítménytesztét](../security/benchmarks/overview.md)
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)