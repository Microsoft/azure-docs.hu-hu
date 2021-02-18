---
title: Azure biztonsági alapkonfiguráció Azure-beli bejárati ajtóhoz
description: Az Azure bejárati ajtó biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: frontdoor
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d2a5dddc8a6d654703ad5da34d775df0d3e4110f
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093152"
---
# <a name="azure-security-baseline-for-azure-front-door"></a>Azure biztonsági alapkonfiguráció Azure-beli bejárati ajtóhoz

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 2,0-es verziójáról](../security/benchmarks/overview.md) az Azure-ba irányuló útmutatót alkalmazza. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint az Azure-előtérben érvényes kapcsolódó útmutatás. Az Azure-előtérben nem alkalmazható **vezérlőelemek** ki lettek zárva.

Ha szeretné megtekinteni, hogy az Azure-beli bejárati ajtó hogyan teljes mértékben leképezi az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)-beli előtérben

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: a külső hálózati támadásoktól származó alkalmazások és szolgáltatások elleni védelem

**Útmutató**: a Azure PowerShell használatával hozzon létre egy geo-szűrési házirendet, és társítsa a szabályzatot a meglévő Azure Front Door előtér-gazdagéphez. Ez a Geo-szűrési házirend letiltja a külső hálózatokról érkező kéréseket, például a többi országból vagy régióból érkező kérelmeket, kivéve a Egyesült Államok.

- [Oktatóanyag – geo-szűrési WAF szabályzat beállítása a bejárati ajtóhoz](front-door-tutorial-geo-filtering.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: a hálózati biztonsági szabályok egyszerűsítése

**Útmutató**: a Virtual Network szolgáltatás-címkék használatával meghatározhatja az Azure bejárati ajtóhoz konfigurált hálózati biztonsági csoportokhoz tartozó hálózati hozzáférés-vezérlési eszközöket. A szolgáltatás címkéi a biztonsági szabályok létrehozásakor a megadott IP-címek helyett használhatók. Ha megadja a szolgáltatási címke nevét (AzureFrontDoor. frontend, AzureFrontDoor. backend, AzureFrontDoor. FirstParty) a szabály megfelelő forrás vagy cél mezőjében, engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. 

A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

- [A szolgáltatási címkék megismerése és használata](../virtual-network/service-tags-overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="identity-management"></a>Identitáskezelés

*További információ: [Azure Security Benchmark: Identitáskezelés](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Azure-erőforrásokhoz való hozzáférés korlátozása feltételek alapján

**Útmutató**: alapértelmezés szerint az Azure bejárati ajtó válaszol az összes felhasználói kérelemre, függetlenül attól, hogy honnan származik a kérelem. Az ügyfelek korlátozhatják a webalkalmazásokhoz való hozzáférést országok vagy régiók szerint is. Az Azure-beli webalkalmazási tűzfal szolgáltatás lehetővé teszi, hogy az ügyfelek egyéni hozzáférési szabályokkal definiálják a szabályzatokat a végponton megadott elérési útra, hogy engedélyezzék vagy letiltsák a hozzáférést a megadott országokból vagy régiókból.

- [Oktatóanyag – geo-szűrési WAF szabályzat beállítása az Azure-beli bejárati ajtóhoz](front-door-tutorial-geo-filtering.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="privileged-access"></a>Emelt szintű hozzáférés

*További információ: [Azure Security Benchmark: Emelt jogosultságú hozzáférés](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Az üzletileg kritikus rendszerek rendszergazdai elérésének korlátozása

**Útmutató**: az Azure-beli bejárati ajtó az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával elkülöníti az üzleti szempontból kritikus rendszerek hozzáférését. Az Azure RBAC segítségével korlátozza azokat a fiókokat, amelyek privilegizált hozzáférést kapnak az előfizetésekhez és a felügyeleti csoportokhoz, ahol azok találhatók.

Az üzleti szempontból kritikus fontosságú rendszerekhez, például Active Directory-tartomány vezérlőkhöz, a biztonsági eszközökhöz és a Rendszerfelügyeleti eszközökhöz való korlátozott hozzáférés biztosítása a felügyeleti, identitási és biztonsági rendszerekhez. A folyamatos és konzisztens megvalósítás érdekében minden típusú hozzáférés-vezérlést a vállalati szegmentálási stratégiához igazíthat.

- [Azure-összetevők és-hivatkozási modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Felügyeleti csoport elérése](../governance/management-groups/overview.md#management-group-access) 

- [Azure-előfizetés rendszergazdái](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Emelt szintű hozzáférésű munkaállomások használata

**Útmutató**: A biztonságos, elkülönített munkaállomások kritikus fontosságúak az olyan bizalmas szerepkörök biztonsága szempontjából, mint a rendszergazdák, a fejlesztők vagy a kritikus fontosságú szolgáltatások üzemeltetői. 

A fokozottan biztonságos felhasználói munkaállomások használata az Azure Bastion szolgáltatással felügyeleti feladatokhoz. Válassza a Azure Active Directory (Azure AD), a Microsoft Defender komplex veszélyforrások elleni védelem (ATP) és a Microsoft Intune lehetőséget a biztonságos és felügyelt felhasználói munkaállomások telepítéséhez a felügyeleti feladatokhoz. A biztonságos munkaállomásokat központilag kell felügyelni a biztonságos konfiguráció kikényszerítéséhez, beleértve az erős hitelesítést, a szoftveres és a hardveres alapkonfigurációkat, a korlátozott logikai és hálózati hozzáférést.

- [Az emelt szintű hozzáférésű munkaállomások ismertetése](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Emelt szintű hozzáférésű munkaállomás üzembe helyezése](/security/compass/privileged-access-deployment)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: A Just Enough Administration (legkisebb jogosultsági alapelv) követése 

**Útmutató**: az Azure-beli bejárati ajtó integrálva van az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) szolgáltatásával az erőforrások kezeléséhez. Az Azure RBAC lehetővé teszi, hogy szerepkörök hozzárendelésével felügyelje az Azure-erőforrások hozzáférését. Ezeket a szerepköröket hozzárendelheti a felhasználókhoz, a csoportok egyszerű szolgáltatásaihoz és a felügyelt identitásokhoz. Bizonyos erőforrásokhoz előre meghatározott beépített szerepkörök tartoznak, és ezek a szerepkörök olyan eszközökkel leltározhatók vagy kérdezhetők le, mint az Azure CLI, az Azure PowerShell vagy az Azure Portal. 

Kövesse a legalacsonyabb jogosultsági modellt az erőforrásokhoz az Azure RBAC rendelt szerepköralapú engedélyek számára, és gondoskodjon arról, hogy az üzleti igények alapján történjen. Ez a megközelítés megfelel az Azure AD Privileged Identity Management (PIM) igény szerinti (JIT) módszerének, és a jogosultságok rendszeres felülvizsgálatával jár.

A beépített szerepkörök használatával lefoglalhatja az engedélyt, és az üzleti követelmények alapján csak egyéni szerepköröket hozhat létre.

- [Mi az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../role-based-access-control/overview.md) 

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md) 

- [Az Azure AD identitás- és hozzáférési felülvizsgálatainak használata](../active-directory/governance/access-reviews-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Bizalmas információk átvitel közbeni titkosítása

**Útmutató**: a hozzáférés-vezérlés kiegészítéseként a forgalomban lévő adatoknak védelmet biztosítanak a "sávon kívüli" támadások (például a forgalmi rögzítés) ellen, hogy a támadók ne tudják egyszerűen beolvasni vagy módosítani az adatokat.

A bejárati ajtó támogatja a 1,0, 1,1 és 1,2 TLS-verziókat. A TLS 1,3 még nem támogatott. A 2019 szeptember után létrehozott összes bejárati profil a TLS 1,2-et használja alapértelmezett minimumként.

Habár ez nem kötelező a magánhálózaton lévő forgalom esetében, ez kritikus fontosságú a külső és a nyilvános hálózatok forgalmában. HTTP-forgalom esetén győződjön meg arról, hogy az Azure-erőforrásokhoz csatlakozó összes ügyfél egyeztetheti a TLS v 1.2-es vagy újabb verzióját. A Távoli felügyelet érdekében titkosítatlan protokoll helyett használja az SSH-t (Linuxon) vagy RDP/TLS (Windows rendszeren). Az elavult SSL-, TLS-és SSH-verziók és protokollok, valamint a gyenge titkosítások le lesznek tiltva.

Alapértelmezés szerint az Azure titkosítást biztosít az Azure-adatközpontok közötti adatátvitelhez.

- [Oktatóanyag – a HTTPS konfigurálása egy előtérben egyéni tartományon](front-door-custom-domain-https.md)

- [A titkosítás ismertetése az Azure-ban](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [Információk a TLS biztonságáról](/security/engineering/solving-tls1-problem) 

- [Kettős titkosítás az Azure-beli adatforgalomban](../security/fundamentals/double-encryption.md#data-in-transit)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

## <a name="asset-management"></a>Asset Management (Eszközkezelés)

*További információ: [Azure Security Benchmark: Összetevők kezelése](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Az összetevőket érintő kockázatok biztonsági csapat általi átláthatóságának biztosítása

**Útmutató**: gondoskodjon arról, hogy a biztonsági csapatok biztonsági olvasói engedélyeket kapjanak az Azure-bérlőben és előfizetésekben, hogy a Azure Security Center használatával felügyeljék a biztonsági kockázatokat. 

A biztonsági kockázatok monitorozása a biztonsági csapat felelősségi köreinek struktúrájától függően egy központi biztonsági csapat vagy egy helyi csapat felelőssége lehet. A biztonsági ismereteket és a kockázatokat azonban mindig központilag kell összesíteni egy szervezeten belül. 

A biztonsági olvasó engedélyek széles körben alkalmazhatók egy teljes bérlőre (gyökérszintű felügyeleti csoport), vagy a hatókör alkalmazható adott felügyeleti csoportokra vagy előfizetésekre. 

Megjegyzés: a számítási feladatok és szolgáltatások láthatóságához további engedélyekre lehet szükség. 

- [A biztonsági olvasó szerepkör áttekintése](../role-based-access-control/built-in-roles.md#security-reader)

- [Az Azure-beli felügyeleti csoportok áttekintése](../governance/management-groups/overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Az összetevőleltár és a metaadatok biztonsági csapat általi elérhetőségének biztosítása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra,-csoportokra és-előfizetésekre, amelyekkel logikailag rendszerezheti őket egy besorolásban. Minden címke egy nevet és egy érték párokat tartalmaz. Alkalmazhatja például a "környezet" nevet és az "éles" értéket az éles környezetben található összes erőforrásra.

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center Asset Inventory Management](../security-center/asset-inventory.md) 

- [Útmutató az erőforrások elnevezésével és címkézésével kapcsolatos döntésekhez](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával naplózhatja és korlátozhatja, hogy a felhasználók mely szolgáltatásokat tudják kiépíteni a környezetben. Az előfizetésekben lévő erőforrások lekérdezése és felderítése az Azure Resource Graph-ban.

A Azure Monitor használatával hozhat létre szabályokat a riasztások elindításához, ha a rendszer nem jóváhagyott szolgáltatást észlel.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Az objektuméletciklus-kezelés biztonságának garantálása

**Útmutató**: az ügyfél felelőssége, hogy karbantartsa az Azure-beli bejárati eszközök attribútumait és hálózati konfigurációit, amelyek nagy hatásnak számítanak.

Azt javasoljuk, hogy az ügyfél hozzon létre egy folyamatot az attribútum és a hálózat konfigurációjának módosításainak rögzítéséhez, mérje fel a változást érintő hatásokat, és hozzon létre szervizelési feladatokat a megfelelő módon.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="logging-and-threat-detection"></a>Naplózás és fenyegetésészlelés

*További információ: [Azure Security Benchmark: Naplózás és fenyegetésészlelés](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Naplózás engedélyezése Azure-beli hálózati tevékenységekhez

**Útmutató**: az Azure bejárati ajtaja nem telepíthető virtuális hálózatokra; Ennek az ügyfélnek a használata miatt nem lehet engedélyezni a hálózati biztonsági csoport folyamatának naplózását, tűzfalon keresztül irányítani a forgalmat, vagy elvégzi a csomagok rögzítését.

Az Azure bejárati ajtaja minden olyan hálózati forgalmat naplóz, amelyet az ügyfél-hozzáféréshez feldolgoz. Engedélyezze a hálózati folyamatok naplói szolgáltatást, és konfigurálja ezeket a naplókat, hogy a hosszú távú megőrzés és a naplózás érdekében a Storage-fiókba küldjék.

- [Oktatóanyag – a monitorozási mérőszámok és naplók beállítása az Azure-beli bejárati ajtón](front-door-diagnostics.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Naplózás engedélyezése Azure-erőforrásokhoz

**Útmutató**: az automatikusan elérhető tevékenység-naplók tartalmazzák az Azure-beli bejárati erőforrások összes írási műveletét (Put, post, DELETE), kivéve az olvasási MŰVELETEKET (Get). A Tevékenységnaplók segítségével hibát kereshet a hibaelhárítás során, vagy megfigyelheti, hogy a szervezet felhasználója hogyan módosított egy erőforrást.

Azure-erőforrás-naplók engedélyezése az Azure-beli bejárati ajtóhoz. Azure Security Center és Azure Policy használatával engedélyezheti az erőforrás-naplókat és naplózhatja az adatgyűjtést. Ezek a naplók kritikus fontosságúak lehetnek a biztonsági incidensek kivizsgálásához és a kriminalisztikai gyakorlatok végrehajtásához.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [A naplózás és a különböző naplózási típusok megismerése az Azure-ban](../azure-monitor/essentials/platform-logs-overview.md) 

- [Az Azure Security Center adatgyűjtés ismertetése](../security-center/security-center-enable-data-collection.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Előkészítés – incidensválasz-folyamat frissítése az Azure-hoz

**Útmutató**: Ellenőrizze, hogy a szervezet rendelkezik-e meghatározott folyamatokkal a biztonsági incidensekre való reagáláshoz. Győződjön meg róla, hogy a folyamatok frissítve vannak az Azure-erőforrásokkal, és rendszeresen teszteli őket a készültség biztosításához.

- [Biztonság implementálása a teljes nagyvállalati környezetben](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Incidensválasz referencia-útmutató](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Előkészítés – incidensről szóló értesítés beállítása

**Útmutató**: Állítson be a biztonsági incidensekre vonatkozó kapcsolattartási adatokat az Azure Security Centerben. A Microsoft ezen kapcsolattartási adatok használatával keresi meg Önt, ha a Microsoft Security Response Center (MSCR) felfedezi, hogy az adataihoz törvénytelen vagy jogosulatlan módon fértek hozzá. Lehetősége van az incidensekkel kapcsolatos riasztások és értesítések testreszabására a különböző Azure-szolgáltatásokban az incidensválasz-igények alapján. 

- [Az Azure Security Center biztonsági kapcsolattartójának beállítása](../security-center/security-center-provide-security-contact-details.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Észlelés és elemzés – incidensek létrehozása kiváló minőségű riasztások alapján

**Útmutató**: Győződjön meg arról, hogy rendelkezik a kiváló minőségű riasztások létrehozásához és a riasztások minőségének felméréséhez szükséges folyamattal. Ezzel tanulhat a korábbi incidensekből, és megadhatja a riasztások fontossági sorrendjét az elemzők számára, akik így nem pazarolnak időt a vakriasztásokra. 

Kiváló minőségű riasztások a korábbi incidensek tapasztalatai és ellenőrzött közösségi források alapján, valamint a riasztások létrehozásához és tisztításához tervezett eszközökkel hozhatók létre a különböző jelforrások egyesítésével és összehasonlításával. 

Az Azure Security Center számos Azure-objektum esetében kiváló minőségű riasztásokat biztosít. Az ASC adatösszekötővel streamelheti a riasztásokat az Azure Sentinelbe. Az Azure Sentinellel speciális riasztási szabályokat hozhat létre, amelyekkel automatikusan hozhat létre incidenseket a vizsgálatokhoz. 

Az exportálási funkcióval exportálhatja az Azure Security Center riasztásait és javaslatait, amelyek segítenek az Azure-erőforrások kockázatainak azonosításában. A riasztásokat és javaslatokat manuálisan vagy folyamatosan is exportálhatja.

- [Az exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Észlelés és elemzés – incidensek kivizsgálása

**Útmutató**: annak biztosítása, hogy az elemzők különböző adatforrásokat tudjanak lekérdezni és használni, mivel megvizsgálják a lehetséges incidenseket, hogy teljes képet hozzanak a szolgáltatásról. A különböző naplózási típusokat össze kell gyűjteni, hogy nyomon kövessék a lehetséges támadók tevékenységeit a kill láncban, hogy elkerülje a vak helyeket.  Győződjön meg arról, hogy az elemzések és a megtanulások rögzítése más elemzők számára és a későbbi korábbi referenciák szerint történik.  

A vizsgálathoz használt adatforrások közé tartoznak azok a központi naplózási források, amelyeket a rendszer már gyűjt a hatókörbeli szolgáltatásokból és a futó rendszerekről, de a következők is lehetnek:

- Hálózati adatok – az Azure Network Watcher, az Azure Monitor és a hálózati biztonsági csoportok forgalmi naplóinak használata a hálózati forgalom naplóinak és más elemzési adatok rögzítéséhez. 

- A futó rendszerek pillanatképei: 

    - Az Azure-beli virtuális gép pillanatkép-készítési funkciójával pillanatképet készíthet a futó rendszer lemezéről. 

    - Az operációs rendszer natív memóriakép-készítési funkciójával pillanatképet készíthet a futó rendszer memóriájáról.

    - Az Azure-szolgáltatások vagy saját szoftvere pillanatkép-készítési funkciójával pillanatképeket készíthet a futó rendszerekről.

Az Azure Sentinel széles körű adatelemzést tesz lehetővé szinte bármely naplózási forráson és egy esetfelügyeleti portálon az incidensek teljes életciklusának kezeléséhez. A vizsgálatok során kapott információk társíthatók egy incidenssel nyomkövetés és jelentéskészítés céljából. 

- [Windows rendszerű gép lemezének pillanatképe](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux rendszerű gép lemezének pillanatképe](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure-támogatás – diagnosztikai információk és memóriaképek gyűjtése](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Incidensek vizsgálata az Azure Sentinellel](../sentinel/tutorial-investigate-cases.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Észlelés és elemzés – incidensek rangsorolása

**Útmutató**: Megadhat arra vonatkozó információkat az elemzők számára, hogy melyik incidensekre kell először összpontosítaniuk a súlyosság és az objektum bizalmassága alapján. 

Az Azure Security Center súlyossági szintet rendel az egyes riasztásokhoz, és segít rangsorolni, hogy melyik riasztásokat kell először kivizsgálni. A súlyosság azon alapul, hogy a Security Center mennyire biztos a találatban vagy a riasztás kibocsátásához használt elemzésben, valamint abban, hogy a riasztáshoz vezető tevékenység rosszindulatú volt.

Emellett a címkék használatával megjelölheti az erőforrásokat, és létrehozhat egy elnevezési rendszert az Azure-erőforrások, különösen a bizalmas adatok feldolgozását végző erőforrások azonosításához és kategorizálásához.  Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Elszigetelés, kiiktatás és helyreállítás – az incidenskezelés automatizálása

**Útmutató**: Az ismétlődő manuális tevékenységek automatizálásával jobb reakcióidő érhető el, és csökkenthető az elemzők terhelése. A manuális tevékenységek végrehajtása több időt vesz igénybe, emiatt az elemzők kevesebb incidenst képesek kezelni. A manuális feladatok ki is merítik az elemzőket, ami növeli az emberi hiba lehetőségét, és az elemzők kevésbé tudnak hatékonyan összpontosítani az összetett feladatokra. Az Azure Security Center és az Azure Sentinel munkafolyamat-automatizálási funkcióival automatikusan indíthat el műveleteket és futtathat forgatókönyveket, hogy reagáljon a bejövő biztonsági riasztásokra. A forgatókönyv műveleteket hajt végre, például értesítéseket küld, fiókokat tilt le, és elkülöníti a problémás hálózatokat. 

- [Munkafolyamat-automatizálás konfigurálása a Security Centerben](../security-center/workflow-automation.md)

- [Fenyegetésre adott automatikus válaszok beállítása az Azure Security Centerben](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Fenyegetésre adott automatikus válaszok beállítása az Azure Sentinelben](../sentinel/tutorial-respond-threats-playbook.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="posture-and-vulnerability-management"></a>A biztonsági állapot és a biztonsági rések kezelése

*További információ: [Azure Security Benchmark: A biztonsági állapot és a biztonsági rések kezelése](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: biztonságos konfigurációk létrehozása számítási erőforrásokhoz

**Útmutató**: a Azure Security Center és a Azure Policy használatával biztonságos konfigurációkat hozhat létre minden számítási erőforráson, többek között a Virtual Machineson, a tárolón és egyebeken.

- [Azure Security Center javaslatok figyelése](../security-center/security-center-recommendations.md) 

- [Biztonsági javaslatok – gyorsútmutató](../security-center/recommendations-reference.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: Szoftveres biztonsági rések gyors és automatikus javítása

**Útmutató**: Gyorsan telepítheti a szoftverfrissítéseket az operációs rendszerek és alkalmazások szoftveres biztonsági réseinek javításához.

Rangsorolja a közös kockázati pontozási programot (például a biztonsági rések pontozási rendszerét) vagy az Ön által használt külső ellenőrzési eszköz által biztosított alapértelmezett kockázati minősítéseket. és a környezethez való hozzáigazításával, hogy mely alkalmazások magas biztonsági kockázatot jelentenek, és melyekhez magas üzemidő szükséges.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Rendszeres támadásszimulációk végrehajtása

**Útmutató**: Szükség esetén végezzen behatolási teszteket vagy riasztási gyakorlatokat az Azure-erőforrásokon, hogy biztosítva legyen az összes kritikus biztonsági találat megoldása.
A Microsoft-felhő behatolástesztelési beavatkozási szabályai szerint eljárva biztosíthatja, hogy a behatolási tesztek nem sértik a Microsoft szabályzatait. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét.

- [Behatolási tesztek az Azure-ban](../security/fundamentals/pen-testing.md)

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="governance-and-strategy"></a>Irányítás és stratégia

*További információ: [Azure Security Benchmark: Irányítás és stratégia](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Eszközkezelési és adatvédelmi stratégia 

**Útmutató**: Mindig dokumentáljon és tegyen közzé a rendszerek és adatok folyamatos monitorozására és védelmére vonatkozó egyértelmű stratégiát. Állítsa fel az üzletileg kritikus adatok és rendszerek felmérésének, védelmének és monitorozásának fontossági sorrendjét. 

Ennek a stratégiának magában kell foglalnia a dokumentált útmutatót, és az alábbi elemek szabványait: 

-   Az üzleti kockázattal összhangban lévő adatbesorolási szabványok

-   A kockázatok és az eszközleltár biztonsági szervezet általi láthatósága 

-   A használandó Azure-szolgáltatások biztonsági szervezet általi jóváhagyása 

-   Az biztonsága azok teljes életciklusában

-   A szükséges hozzáférés-vezérlési stratégia a szervezeti adatbesorolásnak megfelelően

-   Az Azure natív védelmi funkcióinak és külső védelmi funkciók használata

-   Adattitkosítási követelmények a használatban lévő és a jelenleg nem használt használati esetekhez

-   A megfelelő titkosítási szabványok

További információ a hivatkozott hivatkozásokon érhető el.

- [Az Azure Security architektúrára vonatkozó ajánlásai – Tárolás, adatok és titkosítás](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Az Azure Security alapjai – Azure-beli adatbiztonság, titkosítás és tárolás](../security/fundamentals/encryption-overview.md)

- [Azure Security Benchmark – Adatvédelem](../security/benchmarks/security-controls-v2-data-protection.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Nagyvállalati szegmentálási stratégia definiálása 

**Útmutató**: Alakítson ki az egész vállalatra kiterjedő stratégiát, amely identitás, alkalmazás, előfizetés, felügyeleti csoport és más vezérlők kombinációjával szegmentálja az összetevőkhöz való hozzáférést.

Gondosan egyensúlyozza ki a biztonsági elkülönítés igényét azoknak a rendszereknek a mindennapos működésével, amelyeknek kommunikálniuk kell egymással, és hozzá kell férniük az adatokhoz.

Gondoskodjon róla, hogy a szegmentálási stratégia következetesen meg legyen valósítva az olyan vezérlőtípusokon, mint a hálózati biztonság, az identitás- és hozzáférés-modellek, az alkalmazások jogosultsági és hozzáférési modelljei és az emberi folyamatokra vonatkozó vezérlők.

- [Útmutató Azure-beli szegmentálási stratégiához (videó)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Útmutató Azure-beli szegmentálási stratégiához (dokumentum)](/security/compass/governance#enterprise-segmentation-strategy)

- [Hálózati szegmentálás vállalati szegmentálási stratégiához igazítása](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Biztonsági állapotot felügyelő stratégiája definiálása

**Útmutató**: Folyamatosan mérje és mérsékelje a kockázatokat, amelyeknek az egyes összetevők, valamint az a környezet van kitéve, amelyben üzemeltetve vannak. Rangsorolja a nagy értékű eszközöket és a nagy teljesítményű támadási felületeket, például a közzétett alkalmazásokat, a hálózati bejövő és kimenő pontokat, a felhasználói és rendszergazdai végpontokat stb.

- [Azure Security Benchmark - Biztonsági állapot és biztonsági rések felmérése](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: A vállalati szerepkörök, a felelősség és az elszámoltathatóság összehangolása

**Útmutató**: Mindig dokumentálja és tegye közzé a biztonsági szervezet szerepköreire és felelősségi köreire vonatkozó egyértelmű stratégiát. Kezelje kiemelten a biztonsági döntések egyértelmű elszámoltathatóságát, a megosztott felelősségi modell oktatását mindenki számára, és a technikai csapatok oktatását a felhőbeli biztonsági technikákra.

- [Ajánlott Azure-biztonsági eljárások 1 – Személyek: Csapatok oktatása a felhőbeli biztonság kialakítására](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Ajánlott Azure-biztonsági eljárások 2 – Személyek: Csapatok oktatása a felhőbeli biztonsági technológiákra](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Ajánlott Azure-biztonsági eljárások 3 – Folyamat: Elszámoltathatóság hozzárendelése felhőbeli biztonsági döntésekhez](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Hálózati biztonsági stratégia definiálása

**Útmutató**: Azure-hálózati biztonsági módszert a vállalat átfogó biztonsági hozzáférés-vezérlési stratégiájának részeként alakíthat ki.  

Ennek a stratégiának magában kell foglalnia a dokumentált útmutatót, és az alábbi elemek szabványait: 

-   Központosított hálózatkezelési és biztonsági felelősség

-   A vállalati szegmentálási stratégiának megfelelő virtuális hálózati szegmentálási modell

-   Szervizelési stratégia különböző fenyegetési és támadási helyzetekre

-   Internetes peremhálózati, bejövő és kimenő forgalomra vonatkozó stratégia

-   Hibrid felhős és helyszíni kapcsolódási stratégia

-   Naprakész hálózati biztonsági összetevők (pl. hálózati diagramok, hálózati referencia-architektúra)

További információ a hivatkozott hivatkozásokon érhető el.

- [Ajánlott Azure-biztonsági eljárások 11 – Architektúra. Egyetlen egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark – Hálózati biztonság](../security/benchmarks/security-controls-v2-network-security.md)

- [A nagyvállalati hálózati biztonság áttekintése](../security/fundamentals/network-overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Identitáskezelési és emelt jogosultságú hozzáférési stratégia definiálása

**Útmutató**: Az identitások és az emelt jogosultságú hozzáférések kezelésének Azure-beli módszerét a vállalat átfogó biztonsági hozzáférés-vezérlési stratégiájának részeként alakíthat ki.  

Ennek a stratégiának magában kell foglalnia a dokumentált útmutatót, és az alábbi elemek szabványait: 

-   Központosított identitáskezelési és hitelesítési rendszer és annak kapcsolatai más belső és külső identitásrendszerekkel

-   Erős hitelesítési módszerek különböző használati helyzetek és feltételek esetén

-   Emelt jogosultságú felhasználók védelme

-   Rendellenes felhasználói tevékenységek monitorozása és kezelése  

-   Felhasználói identitások és hozzáférések felülvizsgálati és egyeztetési folyamata

További információkat az alábbi hivatkozásokon találhat:

- [Azure Security Benchmark – Identitáskezelés](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure Security Benchmark – Emelt jogosultságú hozzáférés](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Ajánlott Azure-biztonsági eljárások 11 – Architektúra. Egyetlen egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Az Azure identitáskezelésének biztonsági áttekintése](../security/fundamentals/identity-management-overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Naplózási és veszélyforrás-kezelési stratégia definiálása

**Útmutató**: Hozzon létre egy stratégiát a naplózáshoz és a fenyegetésekre adott válaszokhoz, amelyekkel gyorsan észlelheti és elháríthatja a fenyegetéseket, a megfelelőségi követelmények betartása mellett. Biztosítson jó minőségű riasztásokat és zökkenőmentes élményt az elemzőknek, így a fenyegetésekre összpontosíthatnak az integráció és a manuális lépések helyett. 

Ennek a stratégiának tartalmaznia kell az alábbi elemek dokumentált útmutatóit, szabályzatait és szabványait: 

-   A biztonsági üzemeltetési (SecOps) szervezet szerepköre és feladatai 

-   Jól definiált incidenskezelési folyamat az NIST-vel vagy más iparági keretrendszerrel összhangban 

-   Naplórögzítés és -megőrzés a veszélyforrások észlelése, az incidensek kezelése és a megfelelőségi igények támogatására

-   A fenyegetésekkel kapcsolatos információk központosított láthatósága és összevetése SIEM, natív Azure-képességek és más források használatával 

-   Kommunikációs és értesítési terv az ügyfelek, szállítók és külső érdekelt felek számára

-   Natív Azure-beli és külső platformok használata incidensek kezelésére, például naplózásra és veszélyforrások észlelésére, kivizsgálásra és a támadások megfékezésére és megszüntetésére

-   Folyamatok az olyan incidenskezelési és incidens utáni tevékenységekhez, mint a tanulságok levonása és a nyomok megőrzése

További információ a hivatkozott hivatkozásokon érhető el.

- [Azure Security Benchmark – Naplózás és fenyegetésészlelés](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark – Incidenskezelés](../security/benchmarks/security-controls-v2-incident-response.md)

- [Ajánlott Azure-biztonsági eljárások 4 – Folyamat. Incidensválasz-folyamat frissítése a felhőhöz](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure-adaptálási keretrendszer, útmutató naplózási és jelentéskészítési döntésekhez](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](../security/benchmarks/overview.md) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)