---
title: Az Azure biztonsági alapkonfigurációja Container Instances
description: A Container Instances alapkonfigurációja eljárásokkal kapcsolatos útmutatást és forrásforrásokat biztosít az Azure biztonsági teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához.
author: msmbaldwin
ms.service: container-instances
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8790e05edbaeb40debd997ea9b35d31b25947761
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598788"
---
# <a name="azure-security-baseline-for-container-instances"></a>Az Azure biztonsági alapkonfigurációja Container Instances

Ez a biztonsági alapkonfiguráció az Azure biztonsági teljesítményteszt [1.0-s](../security/benchmarks/overview-v1.md) verziójának útmutatását alkalmazza a Container Instances. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalmat az Azure  biztonsági teljesítményteszt által meghatározott biztonsági vezérlők és a kapcsolódó, az azure-beli biztonsági Container Instances. **Az** olyan Container Instances, amelyekért a Felelősség a Microsoft felelős, ki vannak zárva.

Ha meg Container Instances, hogyan lehet teljes mértékben leképezni az Azure-biztonsági teljesítménytesztre, tekintse meg a teljes Container Instances alapkonfiguráció [leképezési fájlját.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-erőforrások védelme virtuális hálózatokon belül

**Útmutató:** A tárolócsoportok integrálása Azure Container Instances azure-beli virtuális hálózattal.  Az Azure-beli virtuális hálózatok lehetővé teszik, hogy számos Azure-erőforrást, például tárolócsoportokat helyezzen el egy nem internetes átirányítható hálózatban.

Az alhálózatra delegált alhálózat kimenő hálózati hozzáférésének Azure Container Instances a Azure Firewall. 

- [Tárolópéldányok üzembe helyezése egy Azure virtuális hálózaton](/azure/container-instances/container-instances-vnet)

- [A virtuális gép üzembe helyezése és Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** A Azure Security Center és kövesse a hálózatvédelmi javaslatokat a hálózati erőforrások Azure-ban való biztonságossá te védelmének érdekében. Engedélyezze az NSG forgalmi naplóit, és küldje el a naplókat egy tárfiókba a forgalom naplózása érdekében. Az NSG-forgalom naplóit egy Log Analytics-munkaterületre is elküldheti, és a Traffic Analytics segítségével betekintést nyerhet az Azure-felhő adatforgalmába. A hálózati Traffic Analytics a hálózati tevékenységek vizualizációja és a gyors helyek azonosítása, a biztonsági fenyegetések azonosítása, a forgalmi minták azonosítása és a hálózati konfigurációk észlelése. 

- [NSG-forgalom naplóinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [A Traffic Analytics](../network-watcher/traffic-analytics.md) 

- [A hálózati biztonság Azure Security Center](../security-center/security-center-network-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="13-protect-critical-web-applications"></a>1.3: Kritikus fontosságú webalkalmazások védelme

**Útmutató:** Nem alkalmazható. A teljesítményteszt webalkalmazásokat Azure App Service számítási erőforrásokhoz való.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert kártékony IP-címekkel való kommunikáció megtagadása

**Útmutató:** A DDoS Standard szintű védelmének engedélyezése a virtuális hálózatokon a DDoS-támadások elleni védelem érdekében. Az Azure Security Center intelligencia használatával megtagadhatja az ismert rosszindulatú vagy nem használt internetes IP-címekkel való kommunikációt.  A Azure Firewall a szervezet minden hálózati határán úgy helyezheti üzembe a riasztásokat, hogy a Fenyegetési intelligencia engedélyezve van, és "Riasztás és megtagadás" beállítással van konfigurálva a kártékony hálózati forgalomhoz.

A közvetlen Azure Security Center hálózati hozzáféréssel konfigurálhatja úgy az NSG-ket, hogy a végpontok számára korlátozott ideig korlátozza a jóváhagyott IP-címeknek való kitettséget. Emellett használja a Azure Security Center az adaptív hálózatkorlátozást, hogy olyan NSG-konfigurációkat javasoljon, amelyek a tényleges forgalom és a fenyegetési intelligencia alapján korlátozzák a portokat és a forrás IP-eket.

- [A DDoS Protection konfigurálása](/azure/virtual-network/manage-ddos-protection)

- [A virtuális gép Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Az Azure Security Center fenyegetésintelligencia](../security-center/azure-defender.md)

- [Az Azure Security Center-erőforrások adaptív hálózat-megtartósodása](../security-center/security-center-adaptive-network-hardening.md)

- [Azure Security Center időben való hálózati Access Control](../security-center/security-center-just-in-time.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="15-record-network-packets"></a>1.5: Hálózati csomagok rekordja

**Útmutató:** Ha felhőalapú privát beállításjegyzéket használ, például az Azure Container Registryt az Azure Container Instances-val, engedélyezheti a hálózati biztonsági csoport (NSG) folyamatnaplóit az Azure Container Registry védelméhez használt alhálózathoz csatolt NSG-hez. Az NSG-forgalom naplóit rögzítheti egy Azure Storage-fiókban a folyamatrekordok létrehozásához. Ha szükséges a rendellenes tevékenységek kivizsgálásához, engedélyezze az Azure Network Watcher csomagrögzítését.

- [NSG-forgalom naplóinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Network Watcher](../network-watcher/network-watcher-create.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Hálózatalapú behatolásészlelési/behatolásvédelmi rendszerek (IDS/IPS) üzembe helyezése

**Útmutató:** Válasszon ki egy olyan ajánlatot a Azure Marketplace, amely támogatja az IDS/IPS funkciót és a hasznos vizsgálatot. Ha a behatolásészlelés és/vagy a hasznos adatok vizsgálatán alapuló megelőzés nem követelmény, Azure Firewall fenyegetésészlelési intelligencia használata is használható. Azure Firewall veszélyforrás-felderítésen alapuló szűrés képes riasztást generálni és megtagadni az ismert kártékony IP-címekre és tartományokra vonatkozó forgalmat. Az IP-címek és -tartományok forrása a Microsoft veszélyforrás-felderítési hírcsatornája.

Telepítse a választott tűzfalmegoldást a szervezet minden hálózati határán, hogy észlelje és/vagy megtagadja a rosszindulatú forgalmat.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [A Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Riasztások konfigurálása Azure Firewall](../firewall/threat-intel.md)

- [Üzembe helyezés virtuális hálózaton – Azure Container Instances](container-instances-vnet.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Webalkalmazások forgalmának kezelése

**Útmutató:** Nem alkalmazható. A teljesítményteszt olyan webalkalmazások esetében használható, amelyek Azure App Service számítási erőforrásokon futnak.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** Ha felhőalapú privát beállításjegyzéket használ, például az Azure Container Registryt az Azure Container Instances-val, a tároló-beállításjegyzékhez hozzáféréssel bíró erőforrások esetében a hálózati biztonsági csoportok vagy szolgáltatások hálózati hozzáférés-vezérlésének meghatározásához használja a Azure Container Registry-szolgáltatás virtuális hálózati szolgáltatáscímkéit Azure Firewall. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha megadja az "AzureContainerRegistry" szolgáltatáscímkenevet egy szabály megfelelő forrás- vagy célmezőben, engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkét a címek változásával.

- [Hozzáférés engedélyezése szolgáltatáscímke alapján](https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációjának fenntartása

**Útmutató:** A Azure Container Registry és Azure Container Instances ajánlott szabványos biztonsági konfigurációkat meghatározni és megvalósítani az Azure Container Registryvel társított hálózati erőforrásokhoz.

A **Azure Policy a Microsoft.ContainerRegistry** és **a Microsoft.Network** névterek aliasai segítségével egyéni szabályzatokat hozhat létre a tárolóregisztrációs adatbázis hálózati konfigurációjának naplózásához vagy kikényszerításához.

Az Azure Blueprints segítségével leegyszerűsítheti a nagy méretű Azure-környezeteket, mivel egyetlen tervdefinícióba csomagolásával egyetlen tervdefinícióba csomagolásával kulcsfontosságú környezeti összetevők, például Azure Resource Manager-sablonok, Azure RBAC-vezérlők és szabályzatdefiníciók használhatók. Könnyedén alkalmazhatja a tervet az új előfizetésekre, és finomhangolhatja a vezérlést és a felügyeletet a verziószámozáson keresztül.

- [Azure-beli tárolóregisztrálók megfelelőségi naplózása a Azure Policy](../container-registry/container-registry-azure-policy.md)

- [Azure Blueprints-terv létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutató:** Az ügyfél a Azure Blueprints használatával leegyszerűsítheti a nagy méretű Azure-beli üzembe helyezéseket, mivel egyetlen tervdefinícióba csomagolásával egyetlen tervdefinícióba teheti a legfontosabb környezeti összetevők, például Azure Resource Manager-sablonok, Azure RBAC-vezérlők és -szabályzatok használatát. Könnyedén alkalmazhatja a tervet az új előfizetésekre, és finomhangolhatja a vezérlést és a felügyeletet a verziószámozás segítségével.

- [Azure Blueprints-terv létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrások konfigurációjának figyelése és a változások észlelése érdekében

**Útmutató:** Az Azure-tevékenységnapló segítségével monitorozható a hálózati erőforrások konfigurációja, és észlelhetők a tárolóregisztrációs adatbázisokkal kapcsolatos hálózati erőforrások változásai. Hozzon létre riasztásokat a Azure Monitor, amelyek akkor aktiválódnak, ha a kritikus hálózati erőforrások módosulnak.

- [Az Azure-tevékenységnapló eseményeinek megtekintése és lekérése](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Azure biztonsági teljesítményteszt: Naplózás és monitorozás.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Központi biztonsági naplókezelés konfigurálása

**Útmutató:** Naplók Azure Monitor azure container instance által létrehozott biztonsági adatok összesítéséhez. A Azure Monitor Log Analytics-munkaterület használatával végezhet lekérdezést és elemzést, valamint használhatja az Azure Storage-fiókokat hosszú távú/archiválási tárterülethez.

- [Tárolócsoport és példány naplózása Azure Monitor naplókban](container-instances-log-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure-erőforrások naplózásának engedélyezése

**Útmutató:** Azure Monitor gyűjti a felhasználó által vezérelt események erőforrásnaplóit (korábbi nevén diagnosztikai naplókat). Gyűjtse össze és használja fel ezeket az adatokat a tárolóhitelesítési események naplózásához, és biztosítson egy teljes tevékenységi naplót az olyan összetevőkről, mint a leküldéses és leküldéses események, így diagnosztizálhatja a tárolócsoport biztonsági problémáit.

- [Tárolócsoport és példány naplózása Azure Monitor naplókban](container-instances-log-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="25-configure-security-log-storage-retention"></a>2.5: A biztonsági naplók tárolásának megőrzésének konfigurálása

**Útmutató:** Azure Monitor log Analytics-munkaterület megőrzési időszakát a szervezet megfelelőségi szabályozásának megfelelően állíthatja be. Az Azure Storage-fiókokat hosszú távú/archiválási tárterülethez használhatja.

- [Naplómegőrzési paraméterek beállítása Log Analytics-munkaterületeken](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és áttekintése

**Útmutató:** Elemezze és figyelje Azure Container Instances a rendellenes viselkedést, és rendszeresen tekintse át az eredményeket. A Azure Monitor Log Analytics-munkaterületén áttekinthet naplókat, és lekérdezéseket hajthatja végre a naplóadatokon.

- [A Log Analytics-munkaterület](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Egyéni lekérdezések végrehajtása a Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

- [Naplóval kompatibilis tárolócsoport és lekérdezési naplók létrehozása](container-instances-log-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató:** Ha felhőalapú privát regisztrációs adatbázist használ, például az Azure Container Registryt az Azure Container Instances-val, az Azure Log Analytics-munkaterülettel monitorozást és riasztást használhat az Azure Container Registryvel kapcsolatos biztonsági naplók és események rendellenes tevékenységeinek figyeléséhez és riasztásához.

- [Azure Container Registry diagnosztikai kiértékelési és naplózási naplókhoz](../container-registry/container-registry-diagnostics-audit-logs.md)

- [Naplóelemzési naplóadatokra vonatkozó riasztások](/azure/azure-monitor/learn/tutorial-response)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="28-centralize-anti-malware-logging"></a>2.8: A kártevőirtó naplózás központosította

**Útmutató:** Nem alkalmazható. Ha felhőalapú privát regisztrációs adatbázist használ, például az Azure Container Registryt Azure Container Instances, az Azure Container Registry nem készít kártevőirtó naplókat.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="29-enable-dns-query-logging"></a>2.9: DNS-lekérdezésnaplózás engedélyezése

**Útmutató:** Nem alkalmazható. Ha felhőalapú privát regisztrációs adatbázist használ, például az Azure Container Registryt Azure Container Instances-val, az Azure Container Registry egy végpont, és nem kezdeményez kommunikációt, és a szolgáltatás nemkérdezi le a DNS-t.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ: [Azure Biztonsági teljesítményteszt: Identitás és Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: A rendszergazdai fiókok leltárának fenntartása

**Útmutató:** Azure Active Directory (Azure AD) beépített szerepkörökkel rendelkezik, amelyekhez explicit módon kell hozzárendelni és lekérdezhetőnek kell lennie. Az Azure AD PowerShell-modullal alkalmi lekérdezéseket hajt végre a rendszergazdai csoportok tagjainak felderítéséhez.

Ha felhőalapú privát regisztrációs adatbázist használ, például az Azure Container Registryt Azure Container Instances-val, minden Azure Container Registry-beállításjegyzékhez nyomon követheti, hogy a beépített rendszergazdai fiók engedélyezve van-e vagy le van-e tiltva. Tiltsa le a fiókot, ha nincs használatban.

- [Címtárszerepk lekért szerepkör az Azure AD-ban a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrole)

- [Címtárbeli szerepkör tagjainak lekérte az Azure AD-ban a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Azure Container Registry fiók létrehozása](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Az alapértelmezett jelszavak módosítása, ha vannak

**Útmutató:** Azure Active Directory (Azure AD) nem rendelkezik az alapértelmezett jelszavak fogalmával. A jelszót igénylő egyéb Azure-erőforrások megkövetelik a jelszó összetettségi követelményekkel és a jelszó minimális hosszával való létrehozást, amelyek a szolgáltatástól függően eltérőek. Ön felelős az olyan külső alkalmazásokért és Marketplace-szolgáltatásokért, amelyek alapértelmezett jelszavakat használhatnak.

Ha felhőalapú privát regisztrációs adatbázist használ, például az Azure Container Registryt az Azure Container Instances-val, akkor ha egy Azure Container Registry alapértelmezett rendszergazdai fiókja engedélyezve van, a rendszer automatikusan összetett jelszavakat hoz létre, és el kellforgatni. Tiltsa le a fiókot, ha nincs használatban.

- [Azure Container Registry fiók létrehozása](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Dedikált rendszergazdai fiókok használata

**Útmutató:** Szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használatával kapcsolatban. A Azure Security Center és hozzáférés-kezelés használatával figyelheti a rendszergazdai fiókok számát.

Ha felhőalapú privát regisztrációs adatbázist használ, például az Azure Container Registryt Azure Container Instances, hozzon létre eljárásokat a tároló-beállításjegyzék beépített rendszergazdai fiókjának engedélyezéséhez. Tiltsa le a fiókot, ha nincs használatban.

- [Az Azure Security Center és a hozzáférések](../security-center/security-center-identity-access.md)

- [Azure Container Registry fiók létrehozása](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Egyszeri Azure Active Directory (SSO) használata

**Útmutató:** Amikor csak lehetséges, használjon Azure Active Directory (Azure AD) SSO-t az önálló hitelesítő adatok szolgáltatásonkénti konfigurálása helyett. Használja Azure Security Center Identity and Access Management (Identitás- és hozzáférés-kezelés) javaslatokat.

Ha felhőalapú privát regisztrációs adatbázist használ, például az Azure Container Registryt az Azure Container Instances-val, a tároló-beállításjegyzékhez való egyéni hozzáféréshez használjon az Azure AD-be való egyéni bejelentkezést.

- [Az SSO azure aD-val való használatának a](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Egyéni bejelentkezés egy tároló-beállításjegyzékbe](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata minden Azure Active Directory-alapú hozzáféréshez

**Útmutató:** Engedélyezze Azure Active Directory (Azure AD) többtényezős hitelesítést, és kövesse az Azure Security Center és hozzáférés-kezelésre vonatkozó javaslatokat.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése a Azure Security Center](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Dedikált gépek (Emelt szintű hozzáférésű munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató:** Emelt hozzáférési szintű munkaállomások (emelt szintű hozzáférésű munkaállomások) használata többtényezős hitelesítéssel, konfigurálva az Azure-erőforrásokba való bejelentkezéshez és konfiguráláshoz.

- [Tudnivalók az emelt szintű hozzáférésű munkaállomásokkal kapcsolatban](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: A rendszergazdai fiókokból származó gyanús tevékenységek naplózása és riasztása

**Útmutató:** Azure Active Directory (Azure AD) biztonsági jelentésekkel naplókat és riasztásokat generál, ha gyanús vagy nem biztonságos tevékenység történik a környezetben. A Azure Security Center identitás- és hozzáférési tevékenységek figyelése.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md)

- [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató:** A feltételes hozzáférés elnevezett helyei segítségével csak az IP-címtartományok vagy országok/régiók meghatározott logikai csoportosításai számára engedélyezi a hozzáférést.

- [Elnevezett helyek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="39-use-azure-active-directory"></a>3.9: A Azure Active Directory

**Útmutató:** A Azure Active Directory (Azure AD) használata központi hitelesítési és engedélyezési rendszerként. Az Azure AD erős titkosítással védi az adatokat az átvitel alatt és az átvitel alatt. Az Azure AD emellett a felhasználói hitelesítő adatokat is megsokossa, hashes és biztonságosan tárolja.

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: A felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató:** Azure Active Directory (Azure AD) naplókat biztosít az elavult fiókok felderítésének segítésekor. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férnek hozzá.

- [Az Azure AD-jelentéskészítés](/azure/active-directory/reports-monitoring/)

- [Az Azure identitás-hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Az inaktivált hitelesítő adatok elérésére tett kísérletek figyelése

**Útmutató:** Hozzáféréssel rendelkezik az Azure Active Directory (Azure AD) bejelentkezési tevékenység-, audit- és kockázatiesemény-naplóforrásokhoz, amelyek lehetővé teszik a biztonsági információk és események kezelésével (SIEM) /Monitoring eszközzel való integrációt.

Ezt a folyamatot leegyszerűsítheti, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókokhoz, és elküldi az auditnaplókat és a bejelentkezési naplókat egy Log Analytics-munkaterületre. A kívánt riasztásokat a Log Analytics-munkaterületen konfigurálhatja.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltéréséről

**Útmutató:** A Azure Active Directory (Azure AD) kockázat- és identitásvédelmi szolgáltatásaival konfigurálhatja a felhasználói identitásokkal kapcsolatos észlelt gyanús műveletekre adott automatikus válaszokat.

- [Kockázatos Azure AD-bejelentkezések megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati szabályzatok konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Hozzáférés a Microsoft számára a releváns ügyféladatokhoz a támogatási forgatókönyvek során

**Útmutató:** Nem érhető el; Ügyfélszéf jelenleg nem támogatott a Azure Container Instances.

- [A támogatott Ügyfélszéf szolgáltatások listája](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Bizalmas adatok leltárának fenntartása

**Útmutató:** Az erőforráscímkék segítségével nyomon lehet követni a bizalmas adatokat tároló vagy felgyógyító Azure-beli tárolóregisztrációs rendszereket.

A tárolórendszerképek vagy más összetevők címkézése és verziószámolása a regisztrációs adatbázisban, valamint a lemezképek vagy adattárak zárolása a bizalmas adatokat tároló vagy felkezelő rendszerképek nyomon követéséhez.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Tároló-rendszerképek címkézésére és verziószámozásra vonatkozó javaslatok](../container-registry/container-registry-image-tag-version.md)

- [Tároló rendszerképének zárolása egy Azure Container Registryben](../container-registry/container-registry-image-lock.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Bizalmas adatokat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** Különálló tárolóregisztrációs rendszereket, előfizetéseket és/vagy felügyeleti csoportokat implementálhat fejlesztéshez, teszteléshez és éles környezethez. A bizalmas adatokat tároló vagy feldolgozó erőforrásokat megfelelően el kell különíteni.

Az erőforrásokat virtuális hálózattal vagy alhálózattal kell elválasztani, megfelelően címkézve, és egy hálózati biztonsági csoport (NSG) vagy hálózati biztonsági csoport (NSG) Azure Firewall.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Az Azure Container Registryhez való hozzáférés korlátozása Azure-beli virtuális hálózat vagy tűzfalszabályok használatával](../container-registry/container-registry-vnet.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

- [A Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Riasztás vagy riasztás konfigurálása és megtagadás Azure Firewall](../firewall/threat-intel.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Bizalmas adatok jogosulatlan átvitelének figyelése és blokkolása

**Útmutató:** Automatikus eszköz üzembe helyezése a szegélyhálózatokon, amely figyeli a bizalmas adatok jogosulatlan átvitelét, és blokkolja az ilyen átviteleket, miközben riasztja az információbiztonsági szakembereket.

A Microsoft által felügyelt mögöttes platform esetén a Microsoft bizalmasként kezeli az összes ügyféladatot, és nagy terjedelmüknek megfelelő védelmet biztosít az ügyfelek adatvesztése és kitettsége ellen. Annak érdekében, hogy az Azure-beli ügyféladatok biztonságban maradnak, a Microsoft robusztus adatvédelmi vezérlőket és képességeket valósított meg és tart karban.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Az átvitel során bizalmas adatok titkosítása

**Útmutató:** Győződjön meg arról, hogy a Azure Container Registry csatlakozó ügyfelek képesek egyeztetni a TLS 1.2-t vagy annál nagyobbat. Microsoft Azure erőforrások alapértelmezés szerint egyeztetik a TLS 1.2-t.

Kövesse Azure Security Center az átvitel közbeni, valamint az átvitel közbeni titkosításra vonatkozó javaslatokat.

- [Az átvitel során az Azure-ral történő titkosítás](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Aktív felderítési eszköz használata a bizalmas adatok azonosításához

**Útmutató:** Ha felhőalapú privát regisztrációs adatbázist használ, például az Azure Container Registryt Azure Container Instances-val, akkor az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el a Azure Container Registry. Ha megfelelőségi célokból szükség van rá, implementálja a harmadik féltől származó megoldást.

A Microsoft által felügyelt mögöttes platform esetén a Microsoft bizalmasként kezeli az összes ügyféladatot, és nagy terjedelmüknek megfelelő védelmet biztosít az ügyfelek adatvesztéssel és -kitettséggel szemben. Annak érdekében, hogy az Azure-beli ügyféladatok biztonságban maradnak, a Microsoft robusztus adatvédelmi vezérlőket és képességeket valósított meg és tart karban.

- [Telepítési adatok titkosítása Azure Container Instances](container-instances-encrypt-data.md)

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Szerepköralapú hozzáférés-vezérlés használata az erőforrásokhoz való hozzáférés szabályozása érdekében

**Útmutató:** Felhőalapú privát beállításjegyzék, például a Azure Container Registry és a Azure Container Instances használata esetén az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával kezelheti az adatokhoz és erőforrásokhoz való hozzáférést egy Azure-beli tároló-beállításjegyzékben.

- [Az Azure RBAC konfigurálása az Azure-ban](../role-based-access-control/role-assignments-portal.md)

- [Azure Container Registry és engedélyek](../container-registry/container-registry-roles.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Hozzáférés-vezérlés kényszerítése gazdagépalapú adatveszteség-megelőzéssel

**Útmutató:** Ha a számítási erőforrások megfelelőségéhez szükséges, valósítson meg egy harmadik féltől származó eszközt, például egy automatizált gazdagépalapú adatveszteség-megelőzési megoldást, hogy akkor is kikényszeríteni tudja az adatok hozzáférés-vezérlését, ha az adatokat egy rendszerről másják ki.

A Microsoft által kezelt mögöttes platform esetén a Microsoft bizalmasként kezeli az összes ügyféladatot, és nagy terjedelmüknek megfelelő védelmet biztosít az ügyfelek adatvesztése és kitettsége ellen. Annak érdekében, hogy az Azure-beli ügyféladatok biztonságban maradnak, a Microsoft robusztus adatvédelmi vezérlőket és képességeket valósított meg és tart karban.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas adatok titkosítása

**Útmutató:** Az összes Azure-erőforráson használja az adattitkosítást. Ha felhőalapú privát regisztrációs adatbázist használ, például az Azure Container Registryt az Azure Container Instances-val, alapértelmezés szerint az Azure Container Registryben lévő összes adat titkosítva van a Microsoft által felügyelt kulcsokkal.

- [Inaktív adatok Azure-ban való titkosításának ismertetése](../security/fundamentals/encryption-atrest.md)

- [Felhasználó által kezelt kulcsok a Azure Container Registry](https://aka.ms/acr/cmk)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Kritikus fontosságú Azure-erőforrások változásainak naplózása és riasztása

**Útmutató:** A Log Analytics-munkaterületek központi helyet biztosítanak a naplóadatok tárolásához és lekérdezéséhez nemcsak az Azure-erőforrásokból, hanem más felhőkben lévő helyszíni erőforrásokból és erőforrásokból is. Azure Container Instances beépített támogatást nyújt a naplók és eseményadatok a naplókba való Azure Monitor küldésekor.

- [Tárolócsoport- és példánynaplózás a Azure Monitor naplókban](container-instances-log-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ: [Azure biztonsági teljesítményteszt: Biztonsági rések kezelése.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Automatizált sebezhetőség-ellenőrzési eszközök futtatása

**Útmutató:** A megoldások előnyeinek kihasználása a privát regisztrációs adatbázisban található tárolórendszerképek vizsgálatához és a potenciális biztonsági rések azonosításához. Fontos megérteni a különböző megoldások által jelentett fenyegetésészlelés mélységét. Kövesse a biztonsági rések Azure Security Center a tároló rendszerképen való végrehajtásához szükséges javaslatokat. Ha szükséges, külső gyártótól származó megoldásokat telepíthet Azure Marketplace rendszerkép biztonsági rések felméréséhez.

- [Tároló monitorozása és biztonsági javaslatok vizsgálata Azure Container Instances](container-instances-image-security.md)

- [Azure Container Registry integráció a Security Center](/azure/security-center/azure-container-registry-integration)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Az operációs rendszer automatikus javításkezelési megoldásának üzembe helyezése

**Útmutató:** A Microsoft javításkezelést végez a tárolók futtatását támogató mögöttes rendszereken.

Automatizálhatja a tárolórendszerképek frissítéseit, ha a rendszer az operációs rendszerből és más javításokból származó alapként szolgáló lemezképek frissítéseit észleli.

- [Alapként elérhető rendszerképfrissítések a Azure Container Registry feladatokhoz](../container-registry/container-registry-tasks-base-images.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Automatikus javításkezelési megoldás üzembe helyezése külső szoftvercímek számára

**Útmutató:** Harmadik féltől származó megoldással javíthatja az alkalmazásképeket. Emellett ha felhőalapú privát beállításjegyzéket használ, például az Azure Container Registryt az Azure Container Instances-val, futtathat Azure Container Registry-feladatokat az alkalmazás-rendszerképek frissítésének automatizálásához egy tároló-beállításjegyzékben az alapként szolgáló rendszerképek biztonsági javításokkal vagy más frissítésekkel.

- [Az alapként ACR-feladatok](../container-registry/container-registry-tasks-base-images.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: A biztonsági rések biztonsági rések biztonsági vizsgálatának összehasonlítása

**Útmutató:** Ha felhőalapú privát beállításjegyzéket használ, például az Azure Container Registryt az Azure Container Instances-val, integrálja az Azure Container Registryt (ACR) az Azure Security Center-val, hogy lehetővé tegye a tárolórendszerképek biztonsági rések utáni rendszeres keresését. Telepíthet külső gyártótól származó megoldásokat a Azure Marketplace biztonsági rések rendszeres vizsgálatának elvégzéséhez.

- [Azure Container Registry integráció a Security Center](../security-center/defender-for-container-registries-introduction.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Kockázatminősítési folyamat használata a felderített biztonsági rések szervizelésének rangsorolására

**Útmutató:** Ha felhőalapú privát regisztrációs adatbázist használ, például az Azure Container Registryt Azure Container Instances-val, integrálja az Azure Container Registry-t (ACR) az Azure Security Center-val, hogy lehetővé tegye a tárolórendszerképek biztonsági rések utáni rendszeres keresését és a kockázatok besorolását. Külső féltől származó megoldásokat is telepíthet a Azure Marketplace rendszerkép biztonsági rések rendszeres vizsgálatának és kockázatbesorolásának végrehajtásához.

- [Azure Container Registry integráció a Security Center](../security-center/defender-for-container-registries-introduction.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Azure biztonsági teljesítményteszt: Leltár és eszközkezelés.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Automatizált eszközfelderítési megoldás használata

**Útmutató:** A Azure Resource Graph használatával lekérdezheti/felderítheti az előfizetés(ek)beli összes erőforrást (pl. számítás, tárolás, hálózat, portok és protokollok stb.). Győződjön meg arról, hogy megfelelő (olvasási) engedélyeket ad meg a bérlőben, és számba veszi az összes Azure-előfizetést és az előfizetésen belüli erőforrásokat.

Bár a klasszikus Azure-erőforrások a Resource Graph felderíthetőek, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

- [Lekérdezések létrehozása Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Az Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription)

- [Az Azure RBAC](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="62-maintain-asset-metadata"></a>6.2: Objektum metaadatainak karbantartása

**Útmutató:** Felhőalapú privát beállításjegyzék, például az Azure Container Registry (ACR) és a Azure Container Instances használata esetén az ACR megőrzi a metaadatokat, beleértve a regisztrációs adatbázisban lévő rendszerképek címkéit és jegyzékfájlját. Kövesse az összetevők címkézésére vonatkozó ajánlott eljárásokat.

- [A beállításregisztrálók, adattárak és rendszerképek](../container-registry/container-registry-concepts.md)

- [Tároló-rendszerképek címkézésére és verziószámozásra vonatkozó javaslatok](../container-registry/container-registry-image-tag-version.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Nem engedélyezett Azure-erőforrások törlése

**Útmutató:** Felhőalapú privát beállításjegyzék, például az Azure Container Registry (ACR) és a Azure Container Instances használata esetén az ACR fenntartja a metaadatokat, beleértve a regisztrációs adatbázisban lévő rendszerképek címkéit és jegyzékfájlját. Kövesse az összetevők címkézésére vonatkozó ajánlott eljárásokat.

- [A beállításregisztrálók, adattárak és rendszerképek](../container-registry/container-registry-concepts.md)

- [Tároló-rendszerképek címkézésére és verziószámozásra vonatkozó javaslatok](../container-registry/container-registry-image-tag-version.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Jóváhagyott Azure-erőforrások meghatározása és leltározása

**Útmutató:** Létre kell hoznia egy leltárt a jóváhagyott Azure-erőforrásokról a szervezeti igényeknek megfelelően.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Nem jóváhagyott Azure-erőforrások figyelése

**Útmutató:** Azure Policy az előfizetésben vagy előfizetésben létrehozható erőforrások típusának korlátozására.

A Azure Resource Graph erőforrások lekérdezésére/felderítésére az előfizetése(ek)en belül. Győződjön meg arról, hogy a környezetben jelen van minden Azure-erőforrás jóvá van hagyva.

- [Azure-beli tárolóregisztrálók megfelelőségi naplózása a Azure Policy](../container-registry/container-registry-azure-policy.md)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató:** Felhőalapú privát beállításjegyzék, például az Azure Container Registry (ACR) és Azure Container Instances használata esetén elemezze és figyelje a Azure Container Registry-naplókat a rendellenes viselkedés érdekében, és rendszeresen tekintse át az eredményeket. A Azure Monitor Log Analytics-munkaterületén áttekinthet naplókat, és lekérdezéseket hajthatja végre a naplóadatokon.

- [Azure Container Registry diagnosztikai kiértékelési és naplózási naplókhoz](../container-registry/container-registry-diagnostics-audit-logs.md)

- [A Log Analytics-munkaterület](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Egyéni lekérdezések végrehajtása a Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Nem jóváhagyott Azure-erőforrások és -szoftveralkalmazások eltávolítása

**Útmutató:** Azure Automation teljes körű vezérlést biztosít a számítási feladatok és erőforrások üzembe helyezése, üzemeltetése és leszerelése során. Megvalósíthat egy saját megoldást a jogosulatlan Azure-erőforrások eltávolításához. 

- [Bevezetés az Azure Automationbe](../automation/automation-intro.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="68-use-only-approved-applications"></a>6.8: Csak jóváhagyott alkalmazások használata

**Útmutató:** Nem alkalmazható. A teljesítményteszt számítási erőforrásokhoz lett kialakítva.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="69-use-only-approved-azure-services"></a>6.9: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató:** A Azure Policy korlátozhatja, mely szolgáltatásokat lehet kiépítani a környezetben.

- [Azure-beli tárolóregisztrálók megfelelőségét a Azure Policy](../container-registry/container-registry-azure-policy.md)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: A jóváhagyott szoftvercímek leltárának fenntartása

**Útmutató:** Nem alkalmazható. A teljesítményteszt számítási erőforrásokhoz lett tervezve.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Korlátozza a felhasználók számára a Azure Resource Manager

**Útmutató:** Az operációs rendszerre vonatkozó konfigurációk vagy külső erőforrások használatával korlátozhatja a felhasználók szkriptek végrehajtására való képességét az Azure számítási erőforrásain belül.

- [Feltételes hozzáférés konfigurálása az Azure Resources Managerhez való hozzáférés letiltása érdekében](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: A felhasználók szkriptek számítási erőforrásokon belüli végrehajtásának korlátozása

**Útmutató:** Az operációs rendszerre vonatkozó konfigurációk vagy külső erőforrások használatával korlátozhatja a felhasználók szkriptek végrehajtására való képességét az Azure számítási erőforrásain belül.

- [Például a PowerShell-parancsfájlok végrehajtásának szabályozása Windows-környezetekben](/powershell/module/microsoft.powershell.security/set-executionpolicy)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Magas kockázatú alkalmazások fizikai vagy logikai elkülönítése

**Útmutató:** Az üzleti műveletekhez szükséges, de a szervezet számára nagyobb kockázatot jelentő szoftvereket el kell különíteni a saját virtuális gépüktől és/vagy virtuális hálózatuktól, és egy Azure Firewall- vagy hálózati biztonsági csoporttal megfelelően biztonságosnak kell lenniük.

- [Virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információ: [Azure biztonsági teljesítményteszt: Biztonságos konfiguráció.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató:** A Azure Policy vagy Azure Security Center az összes Azure-erőforrás biztonsági konfigurációjának fenntartásához.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure-beli tárolóregisztrálók megfelelőségét a Azure Policy](../container-registry/container-registry-azure-policy.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató:** Használja Azure Security Center "Biztonsági konfigurációk biztonsági réseinek szervize a saját Virtual Machines" javaslatát az összes számítási erőforrás biztonsági konfigurációjának fenntartásához.

- [A javaslatok Azure Security Center figyelése](../security-center/security-center-recommendations.md)

- [A javaslatok Azure Security Center szervize](../security-center/security-center-remediate-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Biztonságos Azure-erőforráskonfigurációk fenntartása

**Útmutató:** Az Azure Policy [megtagadás] és [üzembe helyezés, ha nem létezik] hatásaival kényszerítheti a biztonságos beállításokat az Azure-erőforrásokon.

Ha felhőalapú privát regisztrációs adatbázist használ, például Azure Container Registry (ACR) Azure Container Instances, az Azure-beli tárolóregisztrálók megfelelőségét a következő Azure Policy:

- [Azure-beli tárolóregisztrálók megfelelőségét a Azure Policy](../container-registry/container-registry-azure-policy.md)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [A Azure Policy hatásainak](../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató:** Nem alkalmazható; Ez a vezérlő a számítási erőforrásokhoz való.

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások konfigurációjának biztonságos tárolása

**Útmutató:** Ha egyéni Azure-szabályzatdefiníciókat használ, használja az Azure Repost a kód biztonságos tárolására és kezelésére.

- [Kód tárolása az Azure DevOpsban](/azure/devops/repos/git/gitworkflow)

- [Az Azure Repos dokumentációja](/azure/devops/repos/)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató:** Nem alkalmazható; Ez a vezérlő csak a számítási erőforrásokra vonatkozik.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Konfigurációkezelési eszközök üzembe helyezése Azure-erőforrásokhoz

**Útmutató:** A Azure Policy a rendszerkonfigurációk riasztására, naplózására és kényszerítésére szolgál. Emellett dolgozzon ki egy folyamatot és folyamatot a szabályzati kivételek kezeléséhez.

Ha felhőalapú privát regisztrációs adatbázist használ, például Azure Container Registry (ACR) Azure Container Instances, az Azure tárolóregisztrálók megfelelőségét a következő Azure Policy:

- [Azure-beli tárolóregisztrálók megfelelőségi naplózása a Azure Policy](../container-registry/container-registry-azure-policy.md)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [A Azure Policy hatásainak](../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Konfigurációkezelő eszközök központi telepítése operációs rendszerekhez

**Útmutató:** Nem alkalmazható. A teljesítményteszt a számítási erőforrásokra vonatkozik.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Automatizált konfigurációfigyelés megvalósítása Azure-erőforrásokhoz

**Útmutató:** A Azure Security Center az Azure-erőforrások alapkonfiguráció-vizsgálatának elvégzéséhez. 

Az Azure Policy alkalmazással korlátozásokat alkalmazhat az előfizetésében létrehozható erőforrások típusára.

Ha felhőalapú privát regisztrációs adatbázist használ, például Azure Container Registry (ACR) Azure Container Instances, az Azure-beli tárolóregisztrálók megfelelőségét a következő Azure Policy:

- [Javaslatok szervizlének Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Azure-beli tárolóregisztrálók megfelelőségét a Azure Policy](../container-registry/container-registry-azure-policy.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Automatikus konfigurációfigyelés megvalósítása operációs rendszerekhez

**Útmutató:** A Azure Security Center az operációs rendszer és a Docker-beállítások alapkonfiguráció-vizsgálatának végrehajtásához tárolókhoz. 

- [Az Azure Security Center tárolókra vonatkozó ajánlásainak értelmezése](../security-center/container-security.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-beli titkos kulcsok biztonságos kezelése

**Útmutató:** A Felügyeltszolgáltatás-identitás és a Azure Key Vault segítségével leegyszerűsítheti és biztonságossá teheti a titkos adatok kezelését a felhőalkalmazások számára.

- [Az Azure Managed Identities integrálása](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Új Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Hitelesítés a Key Vault](container-instances-managed-identity.md)

- [Új hozzáférési szabályzat Key Vault hozzárendelése](../key-vault/general/assign-access-policy-portal.md)

- [Felügyelt identitások használata az Azure Container Instancesszel](../container-registry/container-registry-tasks-authentication-managed-identity.md)

- [Adatok titkosítása Container Instances](container-instances-encrypt-data.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identitások biztonságos és automatikus kezelése

**Útmutató:** Felügyelt identitások használata az Azure-szolgáltatások automatikus felügyelt identitással való Azure Active Directory (Azure AD) használatával. A felügyelt identitások lehetővé teszik az Azure AD-hitelesítést támogató bármely szolgáltatásban történő hitelesítést, beleértve a Azure Key Vault szolgáltatást is, anélkül, hogy hitelesítő adatokat ad meg a kódban.

Ha felhőalapú privát regisztrációs adatbázist használ, például Azure Container Registry (ACR) Azure Container Instances, az Azure-beli tárolóregisztrálók megfelelőségét a következő Azure Policy:

- [Azure-beli tárolóregisztrálók megfelelőségét a Azure Policy](../container-registry/container-registry-azure-policy.md)

- [Felügyelt identitások konfigurálása](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Felügyelt identitások használata az Azure Container Instancesszel](container-instances-managed-identity.md)

- [Azure-beli felügyelt identitás használata az Azure Container Registryben való hitelesítéshez](../container-registry/container-registry-authentication-managed-identity.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: A hitelesítő adatok nem szándékolt kitettségének megszüntetése

**Útmutató:** Hitelesítőadat-ellenőrző implementálja a hitelesítő adatokat a kódban. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

- [Hitelesítőadat-ellenőrző beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információ: [Azure biztonsági teljesítményteszt: Kártevővédelem.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Központilag felügyelt kártevőirtó szoftverek használata

**Útmutató:** A Microsoft Antimalware az Azure Cloud Services és Virtual Machines erőforrások folyamatos figyelése és megvédése érdekében. Linux rendszeren használjon külső gyártótól származó kártevőirtó megoldást.

- [A Microsoft Antimalware és Cloud Services konfigurálása Virtual Machines](../security/fundamentals/antimalware.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: A nem számítási Azure-erőforrásokba feltölteni szükséges fájlok előzetes beolvasása

**Útmutató:** Microsoft Antimalware engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure Container Instances), de nem fut az ügyféladatokon.

Vizsgálja meg előre a nem számítási Azure-erőforrásokra feltöltött fájlokat, például App Service, Data Lake Storage, Blob Storage stb.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Azure biztonsági teljesítményteszt: Adat-helyreállítás.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatikus biztonságimentek biztosítása

**Útmutató:** Felhőalapú privát beállításjegyzék, például az Azure Container Registry (ACR) és az Azure Container Instances használata esetén a Microsoft Azure-tárolójegyzékben lévő adatok replikálása mindig automatikusan megtörténik a tartósság és a magas rendelkezésre állás biztosítása érdekében. Azure Container Registry az adatokat, hogy védve vannak a tervezett és nem tervezett eseményektől.

Igény szerint georeplikával is replikálhat egy tároló-beállításjegyzéket, így több Azure-régióban is fenntarthatja a beállításjegyzék-replikákat.

- [Georeplikáció a Azure Container Registry](../container-registry/container-registry-geo-replication.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszer-biztonsági mentések és ügyfél által kezelt kulcsok biztonsági mentése

**Útmutató:** Opcionálisan biztonságimentés a tárolórendszerképekről az egyik regisztrációs adatbázisból a másikba történő importálással.

Az ügyfelek által felügyelt kulcsok biztonsági Azure Key Vault Azure parancssori eszközök vagy az SDK-k használatával.

- [Tároló-rendszerképek importálása egy tároló-beállításjegyzékbe](../container-registry/container-registry-import-images.md)

- [Kulcstartókulcsok biztonsági mentése az Azure-ban](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Telepítési adatok titkosítása Container Instances](container-instances-encrypt-data.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági mentés ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató:** Tesztelje az ügyfelek által felügyelt kulcsok biztonsági Azure Key Vault Azure parancssori eszközök vagy AZDK-k használatával.

- [A kulcskulcsok Azure Key Vault az Azure-ban](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági másolatok és az ügyfél által kezelt kulcsok védelmének biztosítása

**Útmutató:** Engedélyezheti a Soft-Delete a Azure Key Vault a kulcsok véletlen vagy rosszindulatú törléssel szembeni védelme érdekében.

- [Az Soft-Delete engedélyezése Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Incidensválasz-útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

- [Munkafolyamat-automatizálások konfigurálása a Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Útmutatás saját biztonsági incidensek válaszadási folyamatának építéshez](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Biztonsági reagálási központ az incidensek felépítése](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Az NIST számítógépes biztonsági incidenskezelési útmutatója](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és -rangsorolási eljárás létrehozása

**Útmutató:** Azure Security Center súlyosságot rendel az egyes riasztásokhoz, így fontossági sorrendbe adhatja, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy Security Center mennyire biztos a riasztás kiadásához használt eredményben vagy az analyticiában, valamint hogy a riasztáshoz vezető tevékenység mögött rosszindulatú szándék volt-e.

Emellett címkék használatával jelölje meg az előfizetéseket, és hozzon létre egy elnevezési rendszert az Azure-erőforrások azonosításához és kategorizálásához, különösen a bizalmas adatokat feldolgozók számára.  Az Ön felelőssége, hogy rangsorolja a riasztások szervizelését azon Azure-erőforrások és -környezet kritikussága alapján, ahol az incidens történt. 

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md) 

- [Címkék használata az Azure-erőforrások rendszerezéséhez](/azure/azure-resource-manager/resource-group-using-tags)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="103-test-security-response-procedures"></a>10.3: Biztonsági reagálási eljárások tesztelése

**Útmutató:** Gyakorlatok a rendszerek incidensválasz-képességeinek rendszeres teszteléséhez. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [NIST-kiadvány – Útmutató az IT-tervekhez és -képességekhez szükséges tesztelési, képzési és gyakorlatprogramokhoz](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidens kapcsolattartási adatait, és konfigurálja a riasztási értesítéseket a biztonsági incidensek esetén

**Útmutató:** A Biztonsági incidens kapcsolattartási adatait a Microsoft arra használja, hogy kapcsolatba lépjen Önnel, ha az Microsoft Biztonsági reagálási központ (MSRC) azt tapasztalja, hogy az ügyfél adatait illetéktelen vagy illetéktelen fél férte hozzá. Tekintse át az incidenseket a probléma megoldása érdekében.

- [Az Azure Security Center biztonsági kapcsolattartójának beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidensválasz-rendszerbe

**Útmutató:** Az Azure Security Center riasztások és javaslatok exportálása a Folyamatos exportálás funkcióval. A folyamatos exportálás lehetővé teszi, hogy manuálisan vagy folyamatosan exportálja a riasztásokat és javaslatokat. A riasztásokat a Azure Security Center adat-összekötővel streamelheti a Azure Sentinel.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: A biztonsági riasztások válaszának automatizálása

**Útmutató:** A munkafolyamat-automatizálási szolgáltatás Azure Security Center a biztonsági riasztások és javaslatok "Logic Apps" funkcióját használva automatikusan aktiválhatja a válaszokat.

- [A munkafolyamat-automatizálás és -Logic Apps](../security-center/workflow-automation.md)

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
