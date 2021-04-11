---
title: Cognitive Services Azure biztonsági alapterve
description: A Cognitive Services biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: a7f9570a68102eb20c84942ddf9ac73de033f01c
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076598"
---
# <a name="azure-security-baseline-for-cognitive-services"></a>Cognitive Services Azure biztonsági alapterve

Ez a biztonsági alapkonfiguráció az [Azure Security benchmark 1.0-s verziójának](../security/benchmarks/overview-v1.md) Cognitive Servicesára vonatkozó útmutatást alkalmazza. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Cognitive Services vonatkozó kapcsolódó útmutatás. Az Cognitive Servicesre nem alkalmazható **vezérlők** , vagy amelyek esetében a felelősség a Microsoft által lett kizárva.

Ha szeretné megtekinteni, hogyan Cognitive Services teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Cognitive Services biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: a Microsoft Azure Cognitive Services többrétegű biztonsági modellt biztosít. Ez a modell lehetővé teszi a Cognitive Services-fiókok védelmét a hálózatok egy adott részhalmaza számára. A hálózati szabályok konfigurálásakor csak azok az alkalmazások férhetnek hozzá a fiókhoz, amelyek a megadott hálózatokon keresztül kérik le az adatokat. A kérelmek szűrésével korlátozhatja az erőforrásokhoz való hozzáférést, így csak a megadott IP-címekről, IP-tartományokból vagy az Azure Virtual Networks-alhálózatok listájáról származó kérelmeket lehet korlátozni.

A virtuális hálózat és a szolgáltatás végpontjának támogatása Cognitive Services a régiók egy meghatározott készletére korlátozódik.

- [Az Azure Cognitive Services virtuális hálózatok konfigurálása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks?tabs=portal)

- [Az Azure Virtual Networks áttekintése](../virtual-network/virtual-networks-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: ha a Virtual Machines a Cognitive Services tárolóval megegyező virtuális hálózaton vannak telepítve, akkor a hálózati biztonsági csoportok segítségével csökkentheti az adatkiszűrése kockázatát. Engedélyezze a hálózati biztonsági csoportok adatfolyam-naplóit, és küldje el a naplókat egy Azure Storage-fiókba a forgalom naplózásához. Hálózati biztonsági csoportokat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: Ha Cognitive Servicest használ egy tárolón belül, a tároló üzembe helyezését kibővítheti egy olyan előtér-webalkalmazási tűzfallal, amely kártékony forgalmat szűr, és támogatja a végpontok közötti TLS-titkosítást, és a tároló végpontját magán-és biztonságos módon kezeli. 

Vegye figyelembe, hogy Cognitive Services tárolóra van szükség a mérési adatok számlázási célból történő elküldéséhez. Az egyetlen kivétel, a kapcsolat nélküli tárolók, amelyek eltérő számlázási módszert követnek. Nem sikerült engedélyezni a különböző hálózati csatornák listáját, amelyeken a Cognitive Services tárolók támaszkodnak, így nem fog működni a tároló. A gazdagépnek engedélyezni kell a 443-es port és a következő tartományok listáját:

- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

Azt is vegye figyelembe, hogy a Cognitive Services tárolók Microsoft-kiszolgálókra való létrehozásához szükséges biztonságos csatornákon le kell tiltania a részletes csomagok vizsgálatát a tűzfal megoldásához. Ennek elmulasztása megakadályozza, hogy a tároló megfelelően működjön.

- [Az Azure Cognitive Services tárolók biztonságának megismerése](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártevő IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: Ha a virtuális gépeket a Cognitive Services tárolóval megegyező virtuális hálózatban telepítik, akkor a kapcsolódó hálózati erőforrásokra vonatkozó szabványos biztonsági konfigurációk meghatározása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. CognitiveServices" és a "Microsoft. Network" névterekben egyéni szabályzatok létrehozásához az Azure cache Redis-példányok hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Olyan beépített szabályzat-definíciókat is használhat, mint például a következők:

- DDoS Protection a standardot engedélyezni kell

Az Azure-tervezetek segítségével leegyszerűsítheti a nagyméretű Azure-környezetek nagy léptékű üzembe helyezését a főbb környezeti összetevők, például a Azure Resource Manager sablonok, az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) és a házirendek alapján, egyetlen tervezet-definícióban. Egyszerűen alkalmazhatja a tervrajzot új előfizetésekre és környezetekre, és részletesen beállíthatja a vezérlést és a felügyeletet a verziószámozással.

Ha Cognitive Servicest használ egy tárolón belül, a tároló üzembe helyezését kibővítheti egy olyan előtér-webalkalmazási tűzfallal, amely kártékony forgalmat szűr, és támogatja a végpontok közötti TLS-titkosítást, így a tároló végpontja magán és biztonságos marad. 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)

- [Az Azure Cognitive Services tárolók biztonságának megismerése](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: Ha a virtuális gépeket a Cognitive Services tárolóval megegyező virtuális hálózatban telepítik, akkor a hálózati biztonsági csoportok segítségével csökkentheti az adatkiszűrése kockázatát. Engedélyezze a hálózati biztonsági csoportok adatfolyam-naplóit, és küldje el a naplókat egy Azure Storage-fiókba a forgalom naplózásához. Hálózati biztonsági csoportokat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutatás**: Ha Cognitive Servicest használ egy tárolón belül, a tároló üzembe helyezését kibővítheti egy olyan előtér-webalkalmazási tűzfallal, amely kártékony forgalmat szűr, és támogatja a végpontok közötti TLS-titkosítást, és a tároló végpontját magán és biztonságos módon kezeli.  Kiválaszthat egy ajánlatot az Azure piactéren, amely támogatja az AZONOSÍTÓk/IP-címek funkciót a hasznos adatok ellenőrzésének letiltására.

Vegye figyelembe, hogy Cognitive Services tárolóra van szükség a mérési adatok számlázási célból történő elküldéséhez. Az egyetlen kivétel a kapcsolat nélküli tárolók, mert más számlázási módszert követnek. Nem sikerült engedélyezni a különböző hálózati csatornák listáját, amelyeken a Cognitive Services tárolók támaszkodnak, így nem fog működni a tároló. A gazdagépnek engedélyezni kell a 443-es port és a következő tartományok listáját:

- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

Azt is vegye figyelembe, hogy a Cognitive Services tárolók Microsoft-kiszolgálókra való létrehozásához szükséges biztonságos csatornákon le kell tiltania a részletes csomagok vizsgálatát a tűzfal megoldásához. Ennek elmulasztása megakadályozza, hogy a tároló megfelelően működjön.

- [Az Azure Cognitive Services tárolók biztonságának megismerése](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutatás**: Ha Cognitive Servicest használ egy tárolón belül, a tároló üzembe helyezését kibővítheti egy olyan előtér-webalkalmazási tűzfallal, amely kártékony forgalmat szűr, és támogatja a végpontok közötti TLS-titkosítást, és a tároló végpontját magán és biztonságos módon kezeli. 

Vegye figyelembe, hogy Cognitive Services tárolóra van szükség a mérési adatok számlázási célból történő elküldéséhez. Az egyetlen kivétel, a kapcsolat nélküli tárolók, amelyek eltérő számlázási módszert követnek. Nem sikerült engedélyezni a különböző hálózati csatornák listáját, amelyeken a Cognitive Services tárolók támaszkodnak, így nem fog működni a tároló. A gazdagépnek engedélyezni kell a 443-es port és a következő tartományok listáját:

- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

Azt is vegye figyelembe, hogy a Cognitive Services tárolók Microsoft-kiszolgálókra való létrehozásához szükséges biztonságos csatornákon le kell tiltania a részletes csomagok vizsgálatát a tűzfal megoldásához. Ennek elmulasztása megakadályozza, hogy a tároló megfelelően működjön.

- [Az Azure Cognitive Services tárolók biztonságának megismerése](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: hálózati biztonsági csoportok vagy Azure Firewall hálózati hozzáférés-vezérlésének definiálásához használja a virtuális hálózati szolgáltatás címkéit. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatási címke nevének (például ApiManagement) megadásával a szabály megfelelő forrás vagy cél mezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

Emellett az alkalmazás biztonsági csoportjaival is egyszerűsítheti az összetett biztonsági konfigurációt. Az alkalmazásbiztonsági csoportokkal az alkalmazás struktúrájának természetes bővítményeként konfigurálhatja a hálózati biztonságot, így csoportosíthatja a virtuális gépeket, és ezen csoportok alapján meghatározhatja a hálózati biztonsági szabályokat.

- [Virtuális hálózati szolgáltatás címkéi](../virtual-network/service-tags-overview.md)

- [Alkalmazás biztonsági csoportjai](https://docs.microsoft.com/azure/virtual-network/network-security-groups-overview#application-security-groups)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: a Cognitive Services-tárolóhoz kapcsolódó hálózati erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. CognitiveServices" és a "Microsoft. Network" névterekben egyéni szabályzatok létrehozásához az Azure cache Redis-példányok hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez.

Az Azure-tervezetek segítségével leegyszerűsítheti a nagyméretű Azure-környezetek nagy léptékű üzembe helyezését, például a Azure Resource Manager sablonokat, a szerepköralapú hozzáférés-vezérlést (Azure RBAC) és a szabályzatokat egyetlen terv definíciójában. Egyszerűen alkalmazhatja a tervrajzot új előfizetésekre és környezetekre, és részletesen beállíthatja a vezérlést és a felügyeletet a verziószámozással.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: a Cognitive Services tárolóhoz társított hálózati erőforrásokhoz használjon címkéket, hogy logikailag szervezze őket a besorolásba.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használatával figyelheti a hálózati erőforrás-konfigurációkat, és azonosíthatja a Cognitive Services tárolóval kapcsolatos hálózati erőforrások változásait. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitorban](/azure/azure-monitor/platform/alerts-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: az Azure-műveletnapló diagnosztikai beállításainak engedélyezése és a naplók elküldése egy log Analytics-munkaterületre, Azure Event hub vagy Azure Storage-fiókba az archívumhoz. A tevékenységi naplók betekintést nyújtanak a Cognitive Services tárolón végrehajtott műveletekre a vezérlési sík szintjén. Az Azure-tevékenység naplójának adatai segítségével meghatározhatja a "mi, ki és mikor" típusú írási műveleteket (PUT, POST, DELETE) az Azure cache Redis-példányok esetében végzett vezérlési sík szintjén.

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](/azure/azure-monitor/platform/activity-log)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a vezérlési sík naplózásához engedélyezze az Azure-tevékenység naplójának diagnosztikai beállításait, és küldje el a naplókat egy log Analytics munkaterületre, az Azure Event hub vagy az Azure Storage-fiókba az archívumhoz. Az Azure-tevékenység naplójának adatai alapján meghatározhatja az Azure-erőforrások vezérlési síkja szintjén végrehajtott írási műveletek (PUT, közzététel, törlés) esetében a "mi, ki és mikor" lehetőséget.

Emellett a Cognitive Services diagnosztikai eseményeket küld, amelyeket az elemzés, a riasztás és a jelentéskészítés céljából gyűjthet és használhat fel. Cognitive Services tároló diagnosztikai beállításait a Azure Portal használatával konfigurálhatja. Egy vagy több diagnosztikai eseményt is elküldhet egy Storage-fiókba, az Event hub-ba vagy egy Log Analytics-munkaterületre.

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Diagnosztikai beállítások használata az Azure Cognitive Services](diagnostic-logging.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: Azure monitoron belül állítsa be a log Analytics munkaterület megőrzési időszakát a szervezet megfelelőségi előírásai szerint. Használja az Azure Storage-fiókokat hosszú távú/archiválási tároláshoz.

- [Log Analytics-munkaterületek naplózási megőrzési paramétereinek beállítása](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: az Azure-műveletnapló diagnosztikai beállításainak engedélyezése és a naplók elküldése egy log Analytics munkaterületre. Ezek a naplók részletes és gyakori információkat biztosítanak egy olyan erőforrás működéséről, amely az azonosításhoz és a hibakereséshez használatos. Lekérdezéseket hajthat végre Log Analytics a kifejezések kereséséhez, a trendek azonosításához, a mintázatok elemzéséhez, valamint számos más elemzéshez az Azure-Cognitive Services összegyűjtött tevékenységi napló adatai alapján.

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](/azure/azure-monitor/platform/activity-log)

- [Azure-Tevékenységnaplók összegyűjtése és elemzése Log Analytics munkaterületen Azure Monitor](/azure/azure-monitor/platform/activity-log)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: a Cognitive Services támogatott metrikáinak riasztásait a Azure monitor riasztások és metrikák szakaszának használatával teheti meg.

A Cognitive Services tároló diagnosztikai beállításainak konfigurálása és naplók elküldése egy Log Analytics-munkaterületre. A Log Analytics munkaterületen beállíthatja, hogy a riasztások az előre definiált feltételek betartása esetén kerüljenek érvénybe. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et.

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

- [Naplózási riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával](/azure/azure-monitor/platform/alerts-log)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: Azure Active Directory (Azure ad) beépített szerepkörökkel rendelkezik, amelyeket explicit módon kell hozzárendelni, és lekérdezhető. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: a Cognitive Serviceshoz való hozzáférés szabályozása Azure Active Directory (Azure ad) segítségével történik. Az Azure AD nem rendelkezik az alapértelmezett jelszavak fogalmával.

Az adatsík Cognitive Serviceshoz való hozzáférését hozzáférési kulcsok vezérlik. Ezeket a kulcsokat a gyorsítótárhoz csatlakozó ügyfelek használják, és bármikor újra létrehozhatók.

Nem ajánlott alapértelmezett jelszavakat létrehozni az alkalmazásba. Ehelyett a jelszavakat Azure Key Vault, majd az Azure AD használatával is tárolhatja.

- [Az Azure cache újragenerálása a Redis hozzáférési kulcsaihoz](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#settings)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül. A rendszergazdai fiókok számának figyeléséhez használja a Security Center identitás-és hozzáférés-kezelés lehetőséget.

Emellett a dedikált rendszergazdai fiókok nyomon követésének elősegítése érdekében Security Center vagy beépített Azure-szabályzatokból származó javaslatokat is használhat, például:

- Az előfizetéshez egynél több tulajdonos rendelhető hozzá
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

- [Az identitás és a hozzáférés figyelésének Azure Security Center használata (előzetes verzió)](../security-center/security-center-identity-access.md)

- [A Azure Policy használata](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory egyszeri bejelentkezés (SSO) használata

**Útmutató**: a Cognitive Services hozzáférési kulcsok használatával hitelesítik a felhasználókat, és nem támogatják az egyszeri bejelentkezést (SSO) az adatsík szintjén. A Cognitive Services vezérlési síkja elérhető a REST APIon keresztül, és támogatja az egyszeri bejelentkezést. A hitelesítéshez állítsa be a kérések engedélyezési fejlécét egy olyan JSON (JavaScript Object Notation) webes tokenre, amelyet az Azure Active Directorytól (Azure AD) szerez be.

- [Az Azure Cognitive Services REST API ismertetése](/rest/api/cognitiveservices/)

- [Az egyszeri bejelentkezés ismertetése az Azure AD-vel](../active-directory/manage-apps/what-is-single-sign-on.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: engedélyezze Azure Active Directory (Azure ad) többtényezős hitelesítését, és kövesse Security Center identitás-és hozzáférés-kezelési javaslatait.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: biztonságos, Azure által felügyelt munkaállomások használata felügyeleti feladatokhoz

**Útmutató**: az Azure-erőforrások bejelentkezéséhez és konfigurálásához konfigurált, többtényezős hitelesítéssel rendelkező emelt szintű hozzáférésű munkaállomások (Paw) használata. 

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutatás**: Azure Active Directory (Azure AD) PRIVILEGED Identity Management (PIM) használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik.

Emellett az Azure AD kockázati észleléseit is használhatja a kockázatos felhasználói viselkedéssel kapcsolatos riasztások és jelentések megtekintéséhez.

- [Privileged Identity Management (PIM) üzembe helyezése](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Az Azure AD kockázati észlelések ismertetése](../active-directory/identity-protection/overview-identity-protection.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a (z) Azure Active Directory (Azure ad) feltételes hozzáférés elnevezett helyeinek konfigurálása, hogy csak az IP-címtartományok vagy országok és régiók adott logikai csoportjaihoz férhessenek hozzá.

- [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerrel. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz, valamint a felhasználók hitelesítő adatainak tárolásához és kiszűréséhez is. Ha a használati eset támogatja az AD-hitelesítést, az Azure AD használatával hitelesítheti a Cognitive Services API-nak küldött kéréseket. 

Jelenleg csak a Computer Vision API, a Face API, a Text Analytics API, a teljes olvasó, az űrlap-felismerő, a anomália-detektor és az összes Bing-szolgáltatás, kivéve Bing Custom Search az Azure AD-vel való hitelesítés támogatása.

- [Kérelmek hitelesítése Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-azure-active-directory)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (Azure ad) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett a felhasználók az Azure Identity hozzáférési felülvizsgálatokat is használhatják a csoporttagságok hatékony kezelésére, a vállalati alkalmazásokhoz való hozzáférésre és a szerepkör-hozzárendelésekre. A felhasználó hozzáférését rendszeresen felül lehet vizsgálni, hogy csak az aktív felhasználók férhessenek hozzá.

Az ügyfél a API Management felhasználói fiókok leltárának fenntartásához, igény szerint egyeztesse a hozzáférést. API Management a fejlesztők a API Management használatával elérhető API-k felhasználói. Alapértelmezés szerint az újonnan létrehozott fejlesztői fiókok aktívak, és a fejlesztői csoporthoz vannak társítva. Az aktív állapotban lévő fejlesztői fiókok az összes olyan API elérésére használhatók, amelyhez előfizetéssel rendelkeznek.

- [Felhasználói fiókok kezelése az Azure API Managementben](../api-management/api-management-howto-create-or-invite-developers.md)

- [API Management-felhasználók listájának beolvasása](/powershell/module/az.apimanagement/get-azapimanagementuser)

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: hozzáférhet Azure Active Directory (Azure ad) bejelentkezési tevékenységhez, a naplózási és kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik az Azure Sentinel vagy egy harmadik féltől származó Siem integrálását.

Ezt a folyamatot leegyszerűsítheti, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókjaihoz, és elküldi a naplókat és a bejelentkezési naplókat egy Log Analytics munkaterületre. Log Analytics belül is konfigurálhatja a kívánt naplózási riasztásokat.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [A fedélzeti Azure Sentinel ismertetése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a fiókok bejelentkezésének viselkedése a vezérlési síkon a Azure Active Directory (Azure ad) Identity Protection és a kockázatkezelési funkciók használatával konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. További vizsgálat céljából az Azure Sentinelbe is betöltheti az adatmennyiséget.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: Cognitive Serviceshoz nem érhető el. Cognitive Services esetében Ügyfélszéf még nem támogatott.

- [Ügyfélszéf által támogatott szolgáltatások listája](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések vagy felügyeleti csoportok implementálása fejlesztési, tesztelési és éles környezetekben. Az erőforrásokat virtuális hálózatokkal vagy alhálózatokkal kell elválasztani, megfelelően címkézve, és egy hálózati biztonsági csoport vagy Azure Firewall által védettnek kell lennie. A bizalmas adatok tárolására vagy feldolgozására szolgáló erőforrásoknak elég elszigeteltnek kell lenniük. A bizalmas adatok tárolására és feldolgozására Virtual Machines a szabályzatokat és eljárásokat kell kikapcsolni, ha nincs használatban.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Virtual Network létrehozása](../virtual-network/quick-create-portal.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

- [Riasztások vagy riasztások konfigurálása és a Azure Firewall megtagadása](../firewall/threat-intel.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: még nem érhető el. Az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el Cognitive Services számára.

A Microsoft kezeli a Cognitive Services alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: az összes Cognitive Services-VÉGPONT a http-kényszerített TLS 1,2-en keresztül elérhető. A kényszerített biztonsági protokoll használatával az Cognitive Services végpontot meghívó felhasználóknak be kell tartaniuk az alábbi irányelveket:

- Az ügyfél operációs rendszerének (OS) támogatnia kell a TLS 1,2-et.
- A HTTP-híváshoz használt nyelvnek (és platformnak) a kérelem részeként meg kell adnia a TLS 1,2-es értéket. A nyelvtől és a platformtól függően a TLS meghatározása implicit módon vagy explicit módon történik.

- [Az Azure Cognitive Services Transport Layer Security megismerése](cognitive-services-security.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el Cognitive Services számára. A bizalmas adatokat tartalmazó példányok címkézése, valamint a harmadik féltől származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.

A Microsoft kezeli a mögöttes platformot, és az összes vásárlói tartalmat bizalmasként kezeli, és nagy hosszúságú időt biztosít az ügyfelek adatvesztésének és a kitettségnek a védelme érdekében. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC 

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata a Cognitive Services vezérlési síkon való hozzáférés vezérléséhez (pl. Azure Portal). 

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a Cognitive Servicesban lévő titkosítás a használatban lévő szolgáltatástól függ. Az esetek többségében a FIPS 140-2-kompatibilis 256 bites AES-titkosítással titkosítják és visszafejtik az adataikat. A titkosítás és a visszafejtés átlátható, ami azt jelenti, hogy a titkosítás és a hozzáférés kezelése a Microsoft ügyfelei számára történik. A vásárlói adatmennyiség alapértelmezés szerint biztonságos, és nem kell módosítania a kódját vagy alkalmazásait a titkosítás kihasználása érdekében.

Az ügyfél által felügyelt kulcsok tárolásához Azure Key Vault is használhatja. Létrehozhatja saját kulcsait, és tárolhatja őket egy kulcstartóban, vagy használhatja a Azure Key Vault API-kat kulcsok létrehozásához.

- [A REST-adatokat titkosító szolgáltatások listája](/azure/cognitive-services/encryption/cognitive-services-encryption-keys-portal)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások a Cognitive Services és más kritikus vagy kapcsolódó erőforrások éles példányain lépnek életbe.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](/azure/azure-monitor/platform/alerts-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetésen belüli összes erőforrást (például a számítási, tárolási, hálózati, portokat, protokollokat stb.). Győződjön meg arról, hogy a bérlőben a megfelelő (olvasási) engedélyek szerepelnek, valamint az összes Azure-előfizetés, valamint az előfizetésekben lévő erőforrások számbavétele.

Bár a klasszikus Azure-erőforrások felderítése az erőforrás-gráfon keresztül lehetséges, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription)

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, így a metaadatok logikailag rendszerezve lesznek a besorolásban.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: címkézés, felügyeleti csoportok és külön előfizetések használata, ahol szükséges, az Azure cache rendszerezése és nyomon követése Redis-példányokhoz és kapcsolódó erőforrásokhoz. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

Emellett a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph használatával kérdezheti le vagy derítheti fel az előfizetéseken belüli erőforrásokat.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférésének konfigurálása a felhasználók "Microsoft Azure felügyelet" alkalmazáshoz való hozzáférésének tiltása a Azure Resource Manager való interakcióra.

- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: a Cognitive Services-tároló szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. CognitiveServices" névtérben egyéni szabályzatok létrehozásához az Azure cache Redis-példányok konfigurációjának naplózásához vagy érvénybe léptetéséhez.

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure-erőforrások biztonságos beállításainak betartatásához használja az Azure Policy [deny] és a [telepítés ha nem létezik] hatásokat.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: ha az Cognitive Services tárolók és a kapcsolódó erőforrások számára egyéni Azure Policy definíciókat vagy Azure Resource Manager sablonokat használ, az Azure Repos használatával biztonságosan tárolhatja és kezelheti a kódot.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Az Azure Repos dokumentációja](/azure/devops/repos/)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: Azure Policy aliasok használata a "Microsoft. cache" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszer-konfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: Azure Policy aliasok használata a "Microsoft. CognitiveServices" névtérben egyéni Azure Policy-definíciók létrehozásához a riasztás, a naplózás és a rendszerkonfigurációk kényszerítése érdekében. Az Redis-példányok és a kapcsolódó erőforrások esetében az Azure cache konfigurációinak automatikus érvényesítéséhez használja a Azure Policy [audit], [megtagadás] és [üzembe helyezés, ha nem léteznek] effektusokat.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: a Azure app Service futó Azure-beli virtuális gépekhez vagy webalkalmazásokhoz, amelyek a Cognitive Services API elérésére szolgálnak, a Managed Service Identity a Azure Key Vault együtt használva egyszerűsítheti és biztonságossá teheti a Cognitive Services kulcskezelő szolgáltatását. Győződjön meg arról, Key Vault a Soft delete engedélyezve van.

- [Integráció az Azure felügyelt identitásokkal](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Key Vault létrehozása](/azure/key-vault/quick-create-portal)

- [Hitelesítés Key Vault](../key-vault/general/authentication.md)

- [Key Vault hozzáférési szabályzatok kiosztása](../key-vault/general/assign-access-policy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: a Azure app Service futó Azure-beli virtuális gépekhez vagy webalkalmazásokhoz, amelyek a Cognitive Services API elérésére szolgálnak, a Managed Service Identity a Azure Key Vault együtt használva egyszerűsítheti és biztonságossá teheti a Cognitive Services kulcskezelő szolgáltatását. Győződjön meg arról, Key Vault a Soft delete engedélyezve van.

Felügyelt identitások használatával biztosíthatja az Azure-szolgáltatások automatikus felügyelt identitását Azure Active Directoryban (Azure AD). A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Azure Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

- [Felügyelt identitások konfigurálása](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Integráció az Azure felügyelt identitásokkal](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információt az [Azure biztonsági teljesítményteszt: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md)című témakörben talál.*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft kártevő szoftverrel való ellátása engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például az Azure cache for Redis esetében), de nem fut az ügyfél tartalmán.

A nem számítási Azure-erőforrásokra feltöltött tartalom előzetes vizsgálata, például App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL stb. A Microsoft nem fér hozzá az adataihoz ezekben a példányokban.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a Microsoft Azure Storage-fiókban lévő adatai mindig automatikusan replikálódnak a tartósság és a magas rendelkezésre állás biztosítása érdekében. Az Azure Storage átmásolja az adatait, hogy védve legyen a tervezett és nem tervezett eseményekkel szemben, beleértve az átmeneti hardverhiba, a hálózati vagy áramkimaradások, valamint a súlyos természeti katasztrófákat. Dönthet úgy is, hogy ugyanazon az adatközponton belül replikálja az adatait, többek között az azonos régión belüli és a földrajzilag elkülönített régiók között. 

Az életciklus-kezelési szolgáltatással is elvégezheti az adatok biztonsági mentését az Archívum szintjére. Emellett a Storage-fiókban tárolt biztonsági másolatok esetében engedélyezze a nem kötelező törlést.

- [Az Azure Storage-redundancia és a Service-Level-szerződések ismertetése](../storage/common/storage-redundancy.md)

- [Az Azure Blob Storage-életciklus felügyelete](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Az Azure Storage-blobok helyreállítható törlése](../storage/blobs/soft-delete-blob-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: a Azure Resource Manager használatával Cognitive Services és kapcsolódó erőforrásokat telepíthet. A Azure Resource Manager a sablonok exportálásával lehetővé teszi a megoldás újbóli üzembe helyezését a fejlesztési életciklus során, és gondoskodik arról, hogy az erőforrások konzisztens állapotban legyenek üzembe helyezhetők. A Azure Automation használatával rendszeresen meghívhatja a Azure Resource Manager sablon exportálási API-ját. Előmegosztott kulcsok biztonsági mentése Azure Key Vaulton belül.

- [Az Azure Resource Manager áttekintése](../azure-resource-manager/management/overview.md)

- [Cognitive Services-erőforrás létrehozása Azure Resource Manager sablon használatával](https://docs.microsoft.com/azure/cognitive-services/resource-manager-template?tabs=portal)

- [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)

- [Erőforráscsoportok – sablon exportálása](/rest/api/resources/resourcegroups/exporttemplate)

- [Bevezetés a Azure Automationba](../automation/automation-intro.md)

- [Key Vault-kulcsok biztonsági mentése az Azure-ban](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: az Azure Resource Manager-sablonok rendszeres időközönkénti üzembe helyezésének lehetősége egy elkülönített előfizetésre, ha szükséges. Ellenőrizze az előmegosztott kulcsok biztonsági másolatának visszaállítását.

- [Erőforrások üzembe helyezése ARM-sablonokkal és Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

- [Key Vault-kulcsok visszaállítása az Azure-ban](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: az Azure DevOps segítségével biztonságosan tárolhatja és kezelheti Azure Resource Manager-sablonjait. Az Azure DevOps felügyelt erőforrások védelme érdekében engedélyeket adhat meg vagy tagadhat meg bizonyos felhasználók, beépített biztonsági csoportok vagy Azure Active Directory (Azure AD) által meghatározott csoportok számára, ha az integrálva van az Azure DevOps, vagy Active Directory, ha a Team Foundation Server integrálva van.

Az Azure szerepköralapú hozzáférés-vezérlés használatával biztosíthatja az ügyfél által felügyelt kulcsok használatát. A kulcsok véletlen vagy rosszindulatú törléssel szembeni védelme érdekében engedélyezze Soft-Delete és törölje a védelmet a Key Vaultban. 

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Engedélyek és csoportok az Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Az Azure Storage-blobok helyreállítható törlése](../storage/blobs/soft-delete-blob-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

- [Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül](../security-center/security-center-planning-and-operations-guide.md)

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [A NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásával kapcsolatos támogatáshoz](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint a riasztást eredményező tevékenység mögötti rosszindulatú szándékkal. 

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

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Security Center-riasztások és-javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a válaszokat a "Logic apps" használatával a biztonsági riasztások és javaslatok esetében.

- [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért tekintse meg az [Azure biztonsági teljesítményteszt: behatolási tesztek és a Red Team gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)című témakört.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft Cloud penetráció tesztelési szabályait, amelyekkel biztosíthatja, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét. 

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
