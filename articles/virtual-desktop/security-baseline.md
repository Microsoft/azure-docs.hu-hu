---
title: Azure biztonsági alapkonfiguráció a Windows rendszerű virtuális asztali gépekhez
description: A Windows rendszerű virtuális asztali biztonsági alapkonfiguráció eljárási útmutatást és erőforrásokat biztosít az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához.
author: msmbaldwin
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 42795e2dda6df24e656c9c06f6a9424bd9e4b5cb
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092973"
---
# <a name="azure-security-baseline-for-windows-virtual-desktop"></a>Azure biztonsági alapkonfiguráció a Windows rendszerű virtuális asztali gépekhez

Ez a biztonsági alapterv az [Azure biztonsági teljesítményteszt 2,0-es verziójáról](../security/benchmarks/overview.md) a Windows rendszerű virtuális asztalra vonatkozó útmutatást alkalmazza. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Windows rendszerű virtuális asztalra vonatkozó kapcsolódó útmutatás. A Windows rendszerű virtuális asztalra nem alkalmazható **vezérlők** ki vannak zárva.

A Windows Virtual Desktop szolgáltatás magában foglalja maga a szolgáltatást, a Windows 10 Enterprise for multi-session Virtual SKU-t, valamint a FSLogix. A FSLogix kapcsolatos biztonsági javaslatokért tekintse meg a [tárterület biztonsági alaptervét](../storage/common/security-baseline.md). A szolgáltatás rendelkezik virtuális gépeken futó ügynökkel, de mivel a virtuális gépek teljes körű felügyelet alatt állnak az ügyfélen, kövesse a [számítási biztonsági javaslatokat](../virtual-machines/windows/security-baseline.md) .

Ha szeretné megtudni, hogy a Windows virtuális asztal hogyan teljes mértékben leképezi az Azure biztonsági Teljesítménytesztét, tekintse meg a [Windows rendszerű virtuális asztali biztonsági alapkonfiguráció-hozzárendelési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: a belső forgalom biztonságának megvalósítása

**Útmutató**: meglévő virtuális hálózatot kell létrehoznia vagy használnia, amikor virtuális gépeket telepít a Windows rendszerű virtuális asztalra. Győződjön meg arról, hogy az összes Azure-beli virtuális hálózat olyan vállalati szegmentálási elvet követ, amely az üzleti kockázatokhoz igazodik. A szervezet számára nagyobb kockázatot jelentő rendszereknek el kell különíteni a saját virtuális hálózatán belül, és megfelelően védeni kell őket egy hálózati biztonsági csoporttal vagy Azure Firewallával.

A Azure Security Center adaptív hálózati korlátozási funkcióinak használata a hálózati biztonsági csoportok olyan konfigurációinak ajánlása érdekében, amelyek korlátozzák a portok és a forrás IP-címeket a külső hálózati forgalomra vonatkozó szabályokra való hivatkozással.

Az alkalmazások és a vállalati szegmentálási stratégia alapján korlátozhatja vagy engedélyezheti a belső erőforrások közötti forgalmat a hálózati biztonsági csoport szabályai alapján. A meghatározott, jól definiált alkalmazások (például egy 3 rétegű alkalmazás) esetében ez lehet egy nagyon biztonságos "megtagadás alapértelmezés szerint, kivétel" módszer. Előfordulhat, hogy ez nem jól méretezhető, ha sok alkalmazás és végpont kommunikál egymással. A Azure Firewall olyan helyzetekben is használhatja, ahol a központi felügyelet nagyszámú vállalati szegmensben vagy küllőn (egy sugaras vagy küllős topológiában) szükséges.

A virtuális géphez társított hálózati biztonsági csoportok (amelyek a Windows rendszerű virtuális asztali számítógépek részét képezik) esetében engedélyezni kell a kimenő forgalmat adott végpontokra. 

- [Megtudhatja, milyen URL-címekre van szükség a Windows rendszerű virtuális asztalok hozzáférésének engedélyezése érdekében](safe-url-list.md)

- [Adaptív hálózati megerősítés Azure Security Center](../security-center/security-center-adaptive-network-hardening.md) 

- [Azure Firewall a Windows rendszerű virtuális asztalhoz ](../firewall/protect-windows-virtual-desktop.md)

- [Hálózati biztonsági csoport létrehozása biztonsági szabályokkal](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Azure Firewall üzembe helyezése és konfigurálása](../firewall/tutorial-firewall-deploy-portal.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="ns-2-connect-private-networks-together"></a>NS-2: magánhálózati hálózatok összekapcsolása

**Útmutató**: az Azure ExpressRoute vagy az Azure virtual private Network használatával privát kapcsolatokat hozhat létre az Azure-adatközpontok és a helyszíni infrastruktúra között egy közös elhelyezésű környezetben. A ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át, így megbízhatóbbak, gyorsabbak és kevesebb késéssel rendelkeznek, mint a szokásos internetes kapcsolatok. 

A pont – hely és a helyek közötti virtuális magánhálózatok esetében a virtuális magánhálózati lehetőségek és az Azure-ExpressRoute bármely kombinációja használatával kapcsolódhat a helyszíni eszközökhöz vagy hálózatokhoz egy virtuális hálózathoz.

A virtuális hálózatok közötti kapcsolat használatával két vagy több virtuális hálózatot kapcsolhat össze az Azure-ban. A kiszolgált virtuális hálózatok közötti hálózati forgalom privát, és az Azure-beli gerinces hálózaton marad.

- [A ExpressRoute kapcsolati modelljei](../expressroute/expressroute-connectivity-models.md) 

- [Az Azure VPN áttekintése](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [Virtuális hálózati társviszony](/azure/virtual-network/virtual-network-peering-overview)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: a külső hálózati támadásoktól származó alkalmazások és szolgáltatások elleni védelem

**Útmutató**: a Azure Firewall használatával biztosíthatja az alkalmazások és szolgáltatások elleni védelmet az internetről és más külső helyekről származó potenciálisan rosszindulatú forgalom ellen. A Windows rendszerű virtuális asztali erőforrásai védelmet nyújthatnak a külső hálózatokból érkező támadások ellen, beleértve az elosztott szolgáltatásmegtagadási támadásokat, az alkalmazás-specifikus támadásokat, a kéretlen és potenciálisan kártékony internetes forgalmat. A DDoS standard Protection Azure-beli virtuális hálózatokon történő engedélyezésével biztosíthatja az eszközök védelmét az elosztott szolgáltatásmegtagadási támadásokkal szemben. A Azure Security Center használatával azonosíthatja a hálózati kapcsolódó erőforrásokkal kapcsolatos hibás konfigurációs kockázatokat.

A Windows virtuális asztal nem a webalkalmazások futtatására szolgál, és nem igényli további beállítások konfigurálását vagy további hálózati szolgáltatások üzembe helyezését a webalkalmazásokra irányuló külső hálózati támadások elleni védelemhez.

- [Azure Firewall dokumentáció](/azure/firewall)

- [Azure DDoS Protection standard kezelése a Azure Portal használatával](/azure/virtual-network/manage-ddos-protection) 

- [Az Azure Security Centerre vonatkozó ajánlások](../security-center/recommendations-reference.md#networking-recommendations)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: a Azure Firewall és a fenyegetésekkel kapcsolatos intelligencián alapuló szűrés használatával riasztást kaphat, és opcionálisan blokkolhatja az ismert kártékony IP-címekre és tartományokra irányuló forgalmat. Az IP-címek és -tartományok forrása a Microsoft veszélyforrás-felderítési hírcsatornája. Ha szükség van a hasznos adatok vizsgálatára, egy külső gyártótól származó behatolás-észlelési vagy-megelőzési megoldást is telepíthet az Azure piactéren. 

Ha a behatolás észlelésére vagy a megelőzési megoldás használatára vonatkozó szabályozás vagy egyéb követelmény van, ügyeljen arra, hogy a rendszer mindig hangolja a biztonsági információk és az Event Management (SIEM) megoldás magas színvonalú riasztásait.

- [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md) 

- [Az Azure Marketplace harmadik féltől származó azonosító képességeket is tartalmaz](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [Microsoft Defender ATP EDR-képesség](/bs-cyrl-ba/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: a hálózati biztonsági szabályok egyszerűsítése

**Útmutató**: az Azure Virtual Network szolgáltatás-címkék használatával meghatározhatja a hálózati biztonsági csoportokhoz vagy a Windows rendszerű virtuális asztali erőforrásokhoz konfigurált Azure Firewallokhoz tartozó hálózati hozzáférés-vezérlést. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatási címke nevének (például: WindowsVirtualDesktop) megadásával a szabály megfelelő forrás vagy cél mezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

- [A szolgáltatási címkék megismerése és használata](../virtual-network/service-tags-overview.md)

- [További információ a Windows rendszerű virtuális asztali szolgáltatás címkéje által jelzett tudnivalókról ](safe-url-list.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="identity-management"></a>Identitáskezelés

*További információ: [Azure Security Benchmark: Identitáskezelés](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Az Azure Active Directory, mint központi identitáskezelő és hitelesítési rendszer szabványosítása

**Útmutató**: a Windows rendszerű virtuális asztali szolgáltatások az alapértelmezett identitás-és hozzáférés-kezelési szolgáltatásként használják Azure Active Directory (Azure ad) szolgáltatást. Egységesítse az Azure AD-t a szervezet identitás-és hozzáférés-kezelésének szabályozásához a ben:

- Microsoft Cloud erőforrások, például a Azure Portal, az Azure Storage, az Azure virtuális gép (Linux és Windows), a Azure Key Vault, a Pásti és az SaaS-alkalmazások.

- a szervezet erőforrásaiban, például az Azure-on vagy a vállalati hálózat erőforrásain lévő alkalmazásokban.

Az Azure AD biztonságának kiemelten fontosnak kell lennie a vállalat felhőbiztonsági gyakorlatában. Az Azure AD egy identitásbiztonsági pontszámmal segít felmérni az identitásbiztonsági helyzetet a Microsoft ajánlott eljárásaihoz viszonyítva. A pontszám alapján felmérheti, mennyire felel meg a konfiguráció az ajánlott eljárásoknak, és javíthatja a biztonság állapotát.

Az Azure AD olyan külső identitásokat támogat, amelyek lehetővé teszik a felhasználók számára, hogy a külső identitással való bejelentkezéshez Microsoft-fiók nélkül jelentkezzenek be az alkalmazásaiba és erőforrásaiba.

- [Bérlők az Azure AD-ben](../active-directory/develop/single-and-multi-tenant-apps.md)

- [Külső identitásszolgáltatók használata alkalmazáshoz](/azure/active-directory/b2b/identity-providers)

- [Mi az Azure AD identitásbiztonsági pontszáma?](../active-directory/fundamentals/identity-secure-score.md)

- [A Windows rendszerű virtuális asztali gépek üzemeltetéséhez szükséges konkrét szerepkörök ](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Alkalmazásidentitások biztonságos és automatikus kezelése

**Útmutató**: a Windows virtuális asztal támogatja az Azure által felügyelt identitásokat a nem emberi fiókok, például a szolgáltatások vagy az automatizálás számára. Javasoljuk, hogy az Azure felügyelt identitás funkciót használja, és ne hozzon létre erősebb emberi fiókot az erőforrások eléréséhez vagy végrehajtásához. 

A Windows Virtual Desktop azt javasolja, hogy az Azure Active Directory (Azure AD) használatával hozzon létre egy egyszerű szolgáltatást az erőforrás szintjén korlátozott engedélyekkel az egyszerű szolgáltatások konfigurálásához a tanúsítvány hitelesítő adataival, és térjen vissza az ügyfél titkos kulcsaihoz. Mindkét esetben a Azure Key Vault az Azure által felügyelt identitásokkal együtt használható, így a futásidejű környezet (például egy Azure-függvény) lekérheti a hitelesítő adatokat a kulcstartóból.

- [Az Azure-erőforrások felügyelt identitásait támogató szolgáltatások](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Azure-szolgáltatásnév](/powershell/azure/create-azure-service-principal-azureps) 

- [Egyszerű szolgáltatás létrehozása tanúsítványokkal](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [Rendszerbiztonsági tag regisztrációjának Azure Key Vault használata](../key-vault/general/authentication.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Az Azure AD-beli egyszeri bejelentkezés használata alkalmazások eléréséhez

**Útmutató**: a Windows virtuális asztal Azure Active Directory (Azure ad) használatával biztosítja az identitás-és hozzáférés-kezelést az Azure-erőforrásokhoz, a felhőalapú alkalmazásokhoz és a helyszíni alkalmazásokhoz. Ez vonatkozik az olyan nagyvállalati identitásokra, mint az alkalmazottak, valamint az olyan külső identitásokra is, mint a partnerek, szállítók és ellátók. Ez lehetővé teszi a vállalati adatok és erőforrások egyszeri bejelentkezéssel (SSO) megvalósított kezelését és védelmét a helyszínen és a felhőben. Az összes felhasználó, alkalmazás és eszköz csatlakoztatása az Azure AD-hez zökkenőmentes biztonságos hozzáférés érdekében, nagyobb láthatósággal és szabályozással.

- [Az Azure AD-vel megvalósított alkalmazás-SSO ismertetése](../active-directory/manage-apps/what-is-single-sign-on.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Erős hitelesítési vezérlők használata minden Azure Active Directory-alapú hozzáféréshez 

**Útmutató**: a Windows rendszerű virtuális asztali szolgáltatás Azure Active Directory (Azure ad) használatát teszi lehetővé, amely támogatja a többtényezős hitelesítéssel és az erős, jelszóval nem rendelkező metódusokkal történő erős hitelesítési vezérlést

- Többtényezős hitelesítés – az Azure AD többtényezős hitelesítésének engedélyezése és az identitás-és hozzáférés-kezelési javaslatok követése Azure Security Center a többtényezős hitelesítés beállítása során ajánlott eljárások. A többtényezős hitelesítés az összes, a felhasználók vagy a felhasználónkénti szinten a bejelentkezési feltételek és a kockázati tényezők alapján kényszeríthető ki.

- Jelszó nélküli hitelesítés – Három jelszó nélküli hitelesítési lehetőség érhető el: a Windows Hello for Business, a Microsoft Authenticator alkalmazás, és az olyan helyszíni hitelesítési módszerek, mint az intelligens kártyák.

A Windows virtuális asztal támogatja a örökölt jelszó-alapú hitelesítést, például a csak felhőalapú fiókokat (az Azure-ban közvetlenül létrehozott felhasználói fiókokat), amelyek alapjelszó-házirendtel vagy hibrid fiókkal rendelkeznek (a helyszíni Azure AD-ből származó felhasználói fiókok, amelyek a helyszíni jelszóházirend alapján) rendelkeznek. A jelszó-alapú hitelesítés használatakor az Azure AD jelszavas védelmet biztosít, amely megakadályozza, hogy a felhasználók könnyen kitalált jelszavakat állítsanak be. A Microsoft globális listát biztosít a telemetria alapján frissített tiltott jelszavakról, és az ügyfelek igényeik alapján is kiterjeszthetik a listát (például branding, kulturális referenciák stb.). Ez a jelszavas védelem csak felhőalapú és hibrid fiókok esetében használható.

A jelszó-hitelesítő adatokon alapuló hitelesítés csak a népszerű támadási módszerekre van kitéve. A nagyobb biztonság érdekében használjon erős hitelesítést, például a többtényezős hitelesítést és egy erős jelszavas házirendet. A külső gyártóktól származó alkalmazások és Marketplace-szolgáltatások esetében, amelyek alapértelmezett jelszavai lehetnek, ezeket a szolgáltatás kezdeti beállításakor kell módosítani.

A rendszergazda és a Kiemelt jogosultságú felhasználók számára ellenőrizze, hogy a legmagasabb szintű erős hitelesítési módszereket használják-e, majd ezt követően a megfelelő erős hitelesítési házirendet a többi felhasználóra is kivezeti.

- [Az Azure Active Directory jelszó nélküli hitelesítési lehetőségeinek ismertetése](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Az Azure AD alapértelmezett jelszószabályzata](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts) 

- [Helytelen jelszavak eltávolítása Azure Active Directory jelszavas védelem használatával](../active-directory/authentication/concept-password-ban-bad.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Fiókok rendellenességeinek monitorozása, és riasztás azok alapján

**Útmutató**: a Windows rendszerű virtuális asztal integrálva van a Azure Active Directory (Azure ad) szolgáltatással, amely a következő adatforrásokat biztosítja:

- Bejelentkezés – a bejelentkezési jelentés információt nyújt a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról.

- Auditnaplók – az Azure AD-n belül különböző szolgáltatások által végrehajtott összes módosításra vonatkozó nyomkövetési naplókat biztosít. A naplók rögzítik például az erőforrások módosításait az Azure AD-n belül, például a felhasználók, alkalmazások, csoportok, szerepkörök és szabályzatok hozzáadását vagy eltávolítását.

- Kockázatos bejelentkezés – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletre utal, amelyet a felhasználói fiók tulajdonosának nem jogos tulajdonosa hajt végre.

- Kockázatosként megjelölt felhasználók – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága.

Ezek az adatforrások a Azure Monitor, az Azure Sentinel vagy egy harmadik féltől származó biztonsági információkkal és Event Management-(SIEM-) rendszerekkel integrálhatók. Az Azure Security Center riasztásokat is küldhet bizonyos gyanús tevékenységek észlelésekor. Ezek közé tartozik például a sikertelen hitelesítési kísérletek kiugróan magas száma vagy az előfizetésben talált elavult fiókok. Az Azure Advanced Threat Protection (ATP) olyan biztonsági megoldás, amely Active Directory-jelek használatával tudja azonosítani, észlelni és kivizsgálni a súlyos fenyegetéseket, a sérült identitásokat és a rosszindulatú belső műveleteket.

- [Tevékenység-jelentések naplózása az Azure AD-ben](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Riasztások az Azure Security Center fenyegetések felderítésére szolgáló védelmi moduljában](../security-center/alerts-reference.md)

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Azure-erőforrásokhoz való hozzáférés korlátozása feltételek alapján

**Útmutató**: a Windows virtuális asztal támogatja a feltételes hozzáférést a Azure Active Directory (Azure ad) használatával egy részletes hozzáférés-vezérléshez a felhasználó által definiált feltételek alapján. Előfordulhat például, hogy bizonyos IP-tartományokból származó felhasználói bejelentkezésekhez többtényezős hitelesítésre van szükség a hozzáféréshez. 

Emellett a részletes hitelesítési munkamenet-kezelési házirend is használható a különböző használati esetekben.

- [Az Azure-beli feltételes hozzáférés áttekintése](../active-directory/conditional-access/overview.md) 

- [Gyakori feltételes hozzáférési szabályzatok](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Hitelesítési munkamenet-kezelés konfigurálása feltételes hozzáféréssel](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md) 

- [A Windows rendszerű virtuális asztali környezethez megadott feltételes hozzáférés beállítási adatai itt találhatók](set-up-mfa.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="privileged-access"></a>Emelt szintű hozzáférés

*További információ: [Azure Security Benchmark: Emelt jogosultságú hozzáférés](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Az üzletileg kritikus rendszerek rendszergazdai elérésének korlátozása

**Útmutató**: a Windows virtuális asztal az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával elkülöníti az üzleti szempontból kritikus rendszerek hozzáférését. Győződjön meg arról, hogy a felügyeleti, identitási és biztonsági rendszerekhez való hozzáférést is korlátozza, amelyek rendszergazdai hozzáféréssel rendelkeznek az üzleti szempontból kritikus fontosságú hozzáférésekhez, például Active Directory-tartomány vezérlőkhöz, a biztonsági eszközökhöz és a Rendszerfelügyeleti eszközökhöz, amelyeken az üzleti szempontból kritikus rendszerekre telepített ügynökök vannak. Azok a támadók, akik veszélyeztetik ezeket a felügyeleti és biztonsági rendszereket, azonnal weaponize azokat az üzleti szempontból kritikus fontosságú eszközök biztonsága érdekében.

A hozzáférés-vezérlés összes típusát a vállalati szegmentálási stratégiához kell igazítani, hogy biztosítsa a konzisztens hozzáférés-vezérlést.

- [Azure-összetevők és-hivatkozási modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Felügyeleti csoport elérése](../governance/management-groups/overview.md#management-group-access) 

- [Azure-előfizetés rendszergazdái](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [A Windows virtuális asztal kezeléséhez szükséges minimális rendszergazdai engedélyek](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Felhasználói hozzáférés rendszerességének áttekintése és egyeztetése

**Útmutató**: a Windows rendszerű virtuális asztali Azure Active Directory (Azure ad) fiókokat használ az erőforrások kezeléséhez, a fiókok és a hozzáférésük érvényességének biztosítása érdekében rendszeresen tekintse át a felhasználói fiókokat és a hozzáférés-hozzárendelést.

Az Azure AD hozzáférési felülvizsgálatok segítségével áttekintheti a csoporttagságok, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. Az Azure AD jelentéskészítési szolgáltatása lehetővé teszi, hogy a naplók segítséget nyújtsanak az elavult fiókok felderítéséhez.

Emellett az Azure Privileged Identity Management is beállítható úgy, hogy riasztást hozzon létre túl sok rendszergazdai fiók létrehozásakor, valamint a elavult vagy helytelenül konfigurált rendszergazdai fiókok azonosításához.

Bizonyos Azure-szolgáltatások támogatják a helyi felhasználókat és szerepköröket, amelyek nem az Azure AD-n keresztül kezelhetők. Ezeket a felhasználókat külön kell kezelnie.

- [Beépített szerepkörök a Windows rendszerű virtuális asztalhoz](rbac.md)

- [Azure-beli erőforrás-szerepkörök hozzáférési felülvizsgálatának létrehozása Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Az Azure AD identitás- és hozzáférési felülvizsgálatainak használata](../active-directory/governance/access-reviews-overview.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Vészhelyzeti hozzáférés beállítása az Azure AD-ben

**Útmutató**: a Windows virtuális asztal a Azure Active Directory (Azure ad) használatával kezeli az erőforrásait. Ha nem szeretné megakadályozni, hogy véletlenül kizárja az Azure AD-szervezetét, állítson be egy vészhelyzeti hozzáférési fiókot a hozzáféréshez, ha a normál rendszergazdai fiókok nem használhatók. A vészhelyzeti hozzáférési fiókok általában magas szintű jogosultságokkal rendelkeznek, és nem ajánlott azokat egyes személyekhez társítani. A vészhelyzeti hozzáférési fiókok csak az olyan vészhelyzeti esetekre valók, amikor a normál rendszergazdai fiókok nem használhatók.

Érdemes biztosítani, hogy a vészhelyzeti hozzáférési fiókok hitelesítő adatai (például jelszó, tanúsítvány vagy intelligens kártya) biztonságos helyen vannak tárolva, amelyet csak azok ismernek, akik jogosultak azokat használni, kizárólag vészhelyzet esetén.

- [Vészhelyzeti hozzáférési fiókok kezelése az Azure AD-ben](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="pa-5-automate-entitlement-management"></a>PA-5: a jogosultságok kezelésének automatizálása 

**Útmutató**: a Windows rendszerű virtuális asztal integrálva van a Azure Active Directory (Azure ad) szolgáltatás erőforrásainak kezeléséhez. Az Azure AD jogosultság-kezelési funkciói segítségével automatizálhatja a hozzáférési kérelmek munkafolyamatait, beleértve a hozzáférési hozzárendeléseket, az értékeléseket és a lejáratokat. A további, kettős vagy többfázisú jóváhagyások is támogatottak.

- [Az Azure AD hozzáférési felülvizsgálatai](../active-directory/governance/access-reviews-overview.md) 

- [Mi az Azure AD-jogosultságok kezelése?](../active-directory/governance/entitlement-management-overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Emelt szintű hozzáférésű munkaállomások használata

**Útmutató**: a biztonságos és elkülönített munkaállomások kritikus fontosságúak a bizalmas szerepkörök, például a rendszergazdák, a fejlesztők és a kritikus fontosságú szolgáltatók biztonsága szempontjából. A biztonságos felhasználói munkaállomásokat és/vagy az Azure Bastion-t a felügyeleti feladatokhoz használhatja. 

Az Azure Active Directory (Azure AD), a Microsoft Defender komplex veszélyforrások elleni védelem (ATP) vagy a Microsoft Intune használatával biztonságos és felügyelt felhasználói munkaállomás helyezhető üzembe felügyeleti feladatokhoz. A biztonságos munkaállomás központilag felügyelhető a biztonságos konfiguráció kikényszerítéséhez, beleértve az erős hitelesítést, a szoftver-és a hardver alapkonfigurációit, a korlátozott logikai és hálózati hozzáférést.

- [Az emelt szintű hozzáférésű munkaállomások ismertetése](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Emelt szintű hozzáférésű munkaállomás üzembe helyezése](/azure/active-directory/devices/howto-azure-managed-workstation)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: A Just Enough Administration (legkisebb jogosultsági alapelv) követése 

**Útmutató**: a Windows virtuális asztal integrálva van az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) szolgáltatással az erőforrások kezeléséhez. Az Azure RBAC lehetővé teszi, hogy szerepkörök hozzárendelésével felügyelje az Azure-erőforrások hozzáférését. Ezeket a szerepköröket hozzárendelheti a felhasználókhoz, a csoportok egyszerű szolgáltatásaihoz és a felügyelt identitásokhoz. Bizonyos erőforrásokhoz előre meghatározott beépített szerepkörök tartoznak, és ezek a szerepkörök olyan eszközökkel leltározhatók vagy kérdezhetők le, mint az Azure CLI, az Azure PowerShell vagy az Azure Portal. 

Az erőforrásokhoz az Azure RBAC hozzárendelt jogosultságokat mindig a szerepkörök által megkövetelt értékekre kell korlátozni. Ez kiegészíti a Privileged Identity Management (PIM) igény szerinti (JIT) megközelítését Azure Active Directory (Azure AD-val), és rendszeresen felül kell vizsgálni.

Emellett a beépített szerepkörök használatával is lefoglalhatja az engedélyeket, és szükség esetén csak egyéni szerepköröket hozhat létre.

- [Mi az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../role-based-access-control/overview.md) 

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md) 

- [Az Azure AD identitás- és hozzáférési felülvizsgálatainak használata](../active-directory/governance/access-reviews-overview.md)

- [Beépített szerepkörök a Windows rendszerű virtuális asztalhoz](rbac.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: a Microsoft támogatási szolgálatának jóváhagyási folyamatának kiválasztása  

**Útmutató**: az olyan forgatókönyvek esetén, ahol a Microsoftnak hozzá kell férnie az ügyféladatok eléréséhez, a Windows virtuális asztal támogatja a Ügyfélszéft, hogy biztosítson egy felületet, amellyel áttekintheti és jóváhagyhatja az ügyféladatok-hozzáférési kérelmeket.

- [A Ügyfélszéf megismerése](../security/fundamentals/customer-lockbox-overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: Bizalmas adatok felderítése, besorolása és címkézése

**Útmutató**: a bizalmas adatok felderítése, besorolása és címkézése, hogy megtervezze a megfelelő vezérlőket. Ez biztosítja a bizalmas adatok tárolását, feldolgozását és biztonságos továbbítását a szervezet technológiai rendszerei számára.

Az Azure-ban, a helyszínen, az Office 365-ban és más helyeken található Office-dokumentumokban lévő bizalmas információkhoz használja a Azure Information Protection (és a hozzá tartozó ellenőrzési eszközt).

Az Azure SQL Information Protection segítséget nyújthat az Azure SQL-adatbázisokban tárolt adatok besorolásában és címkézésében.

- [Bizalmas információk címkézése az Azure Information Protection használatával](/azure/information-protection/what-is-information-protection) 

- [Az Azure SQL-adatfelderítés implementálása](/azure/sql-database/sql-database-data-discovery-and-classification)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="dp-2-protect-sensitive-data"></a>DP-2: A bizalmas adatok védelme

**Útmutató**: a bizalmas adatok védelme az azure szerepköralapú Access Control (Azure RBAC), a hálózati hozzáférés-vezérlés és az Azure-szolgáltatások adott vezérlői (például az SQL és más adatbázisok titkosítása) használatával történő hozzáférés korlátozásával.

A következetes hozzáférés-vezérlés biztosításához a hozzáférés-vezérlés összes típusát a vállalati szegmentálási stratégiához kell igazítani. A vállalati szegmentálási stratégiát a bizalmas vagy üzleti szempontból kritikus fontosságú adatok és rendszerek helyének tudatában kell kialakítani.

A Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és védelmet biztosít az ügyfelek adatvesztése és a kitettség ellen. Ahhoz, hogy az ügyféladatok az Azure-on belül biztonságban maradjanak, a Microsoft implementált néhány alapértelmezett adatvédelmi vezérlőt és képességet.

- [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md) 

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: A bizalmas adatok jogosulatlan átvitelének monitorozása

**Útmutató**: Monitorozhatja az adatok jogosulatlan átvitelét olyan területekre, amelyek kívül esnek a vállalat látóterén és irányításán. Ez általában az olyan rendellenes tevékenységek (nagy méretű vagy szokatlan átvitelek) monitorozását jelenti, amelyek jogosulatlan adatkiszivárogtatást jelezhetnek.

Az Azure Storage és az Azure SQL ATP komplex veszélyforrások elleni védelem (ATP) funkciói az adatok rendellenes átvitelével kapcsolatos riasztásokat jelezhetnek, ami azt jelzi, hogy mi lehet a bizalmas adatok jogosulatlan továbbítása.

Az Azure Information Protection (AIP) a besorolt és címkézett információk monitorozását teszi lehetővé.

Az adatveszteség-megelőzési megoldások, például a gazdagép-alapú adatvesztés-megelőzési megoldások kikényszerítik a nyomozót és/vagy a megelőző ellenőrzéseket az kiszűrése megakadályozása érdekében.

- [Az Azure SQL ATP engedélyezése](../azure-sql/database/threat-detection-overview.md) 

- [Az Azure Storage ATP engedélyezése](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-security-center)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="asset-management"></a>Asset Management (Eszközkezelés)

*További információ: [Azure Security Benchmark: Összetevők kezelése](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Gondoskodás arról, hogy a biztonsági csapat átlássa az adategységek kockázatait

**Útmutató**: Győződjön meg arról, hogy a biztonsági csapatok biztonsági olvasó engedélyekkel rendelkeznek az Azure-bérlőben és -előfizetésekben, hogy monitorozni tudják a biztonsági kockázatokat az Azure Security Centerrel. 

A biztonsági csapat feladatainak strukturálása, a biztonsági kockázatok figyelése a központi biztonsági csapat vagy egy helyi csapat feladata lehet. A biztonsági megállapításokat és kockázatokat azonban mindig központilag kell összesíteni egy szervezeten belül. 

A biztonsági olvasó engedélyek széles körben alkalmazhatók egy teljes bérlőre (gyökérszintű felügyeleti csoport), vagy a hatókör alkalmazható adott felügyeleti csoportokra vagy előfizetésekre. 

További engedélyek szükségesek lehetnek a számítási feladatokhoz és szolgáltatásokhoz. 

- [A biztonsági olvasó szerepkör áttekintése](../role-based-access-control/built-in-roles.md#security-reader)

- [Az Azure-beli felügyeleti csoportok áttekintése](../governance/management-groups/overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Az összetevőleltár és a metaadatok biztonsági csapat általi elérhetőségének biztosítása

**Útmutató**: címkék alkalmazása az Azure-erőforrásokra,-csoportokra és-előfizetésekre, amelyek logikailag rendszerezik őket egy besorolásban. Minden címke egy nevet és egy érték párokat tartalmaz. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra.

Az Azure-beli virtuális gépek Leltározásával automatizálhatja a Virtual Machines szoftverrel kapcsolatos információk gyűjtését. A szoftver neve, verziója, közzétevője és frissítési ideje elérhető a Azure Portal. A telepítés dátumának és egyéb információinak eléréséhez engedélyezze a vendég szintű diagnosztikát, és a Windows-eseménynaplókat egy Log Analytics-munkaterületre vigye.

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center Asset Inventory Management](../security-center/asset-inventory.md) 

- [Útmutató az erőforrások elnevezésével és címkézésével kapcsolatos döntésekhez](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

- [Az Azure-beli virtuális gépek leltározásának engedélyezése](../automation/automation-tutorial-installed-software.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával naplózhatja és korlátozhatja, hogy a felhasználók mely szolgáltatásokat tudják kiépíteni a környezetben. Az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetésükön belüli erőforrásokat. Az Azure Monitort is használhatja olyan szabályok létrehozásához, amelyek riasztást aktiválnak nem jóváhagyott szolgáltatás észlelésekor.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Az objektuméletciklus-kezelés biztonságának garantálása

**Útmutató**: nem alkalmazható. A Windows virtuális asztal nem használható az eszközök biztonságának biztosításához az életciklus-kezelési folyamatokban. Az ügyfél feladata, hogy fenntartsa a nagy hatásnak számító eszközök attribútumait és hálózati konfigurációit. 

Azt javasoljuk, hogy az ügyfél hozzon létre egy folyamatot az attribútum és a hálózat konfigurációjának módosításainak rögzítéséhez, mérje fel a változást érintő hatásokat, és hozzon létre szervizelési feladatokat a megfelelő módon.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek az Azure Resources Managerrel való interakcióra az "Microsoft Azure felügyelet" alkalmazás "hozzáférés letiltása" beállításával.

- [Feltételes hozzáférés konfigurálása az Azure Resources Manager elérésének blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: csak jóváhagyott alkalmazások használata számítási erőforrásokban

**Útmutató**: az Azure-beli virtuálisgép-leltár használatával automatizálhatja a virtuális gépeken futó összes szoftver információinak gyűjtését. A szoftver neve, verziója, közzétevője és frissítési ideje elérhető a Azure Portal. A telepítés dátumának és egyéb információinak eléréséhez engedélyezze a vendég szintű diagnosztikát, és a Windows-eseménynaplókat egy Log Analytics-munkaterületre vigye.

- [Az Azure-beli virtuális gépek leltározásának engedélyezése](../automation/automation-tutorial-installed-software.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="logging-and-threat-detection"></a>Naplózás és fenyegetésészlelés

*További információ: [Azure Security Benchmark: Naplózás és fenyegetésészlelés](/azure/security/benchmarks/security-controls-v2-logging-threat-detection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: az Azure-erőforrások fenyegetés-észlelésének engedélyezése

**Útmutató**: használja a Azure Security Center beépített veszélyforrások észlelésére szolgáló funkciót, és engedélyezze a Windows rendszerű virtuális asztali erőforrásainak az Azure Defender (az Azure Advanced Threat Protection) használatát. A Windows rendszerhez készült Azure Defender egy további biztonsági intelligenciát biztosít, amely szokatlan és potenciálisan ártalmas kísérleteket észlel a Windows rendszerű virtuális asztali erőforrások eléréséhez vagy kihasználásához.

Továbbítson minden naplót a Windows rendszerű virtuális asztalról a biztonsági információ-eseménykezelő (SIEM) megoldáshoz, amely az egyéni veszélyforrások észlelésének beállítására használható. Győződjön meg arról, hogy a különböző típusú Azure-eszközöket figyeli a lehetséges fenyegetések és rendellenességek tekintetében. A magas színvonalú riasztások beszerzésével csökkentheti a hamis pozitív értéket az elemzők számára a rendezéshez. A riasztások naplózási adatokból, ügynökökből vagy más adatokból is származnak.

- [Fenyegetésvédelem az Azure Security Centerben](/azure/security-center/threat-protection) 

- [Azure Security Center biztonsági riasztások referenciája](../security-center/alerts-reference.md)

- [Egyéni elemzési szabályok létrehozása a fenyegetések észleléséhez](../sentinel/tutorial-detect-threats-custom.md) 

- [A Cyber Threat Intelligence és az Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Fenyegetések észlelésének engedélyezése az Azure-beli identitás- és hozzáférés-kezeléshez

**Útmutató**: a Azure Active Directory (Azure ad) a következő felhasználói naplókat nyújtja, amelyek megtekinthetők az Azure ad jelentéskészítési szolgáltatásban, illetve a Azure monitor, az Azure Sentinel vagy más biztonsági információk és rendezvényszervezés (SIEM) vagy figyelési eszközök segítségével, amelyek további kifinomult monitorozási és elemzési használati eseteket tesznek lehetővé:

- Bejelentkezés – a bejelentkezési jelentés információt nyújt a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról.

- Auditnaplók – az Azure AD-n belül különböző szolgáltatások által végrehajtott összes módosításra vonatkozó nyomkövetési naplókat biztosít. A naplók rögzítik például az erőforrások módosításait az Azure AD-n belül, például a felhasználók, alkalmazások, csoportok, szerepkörök és szabályzatok hozzáadását vagy eltávolítását.

- Kockázatos bejelentkezés – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletre utal, amelyet a felhasználói fiók tulajdonosának nem jogos tulajdonosa végez.

- Kockázatosként megjelölt felhasználók – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága.

A Azure Security Center riasztást is beállíthat bizonyos gyanús tevékenységekre, például az előfizetésben található sikertelen hitelesítési kísérletekre és elavult fiókokra vonatkozóan. Az alapszintű biztonsági higiénia monitorozása mellett a Azure Security Center veszélyforrások elleni védelmi modulja több részletes biztonsági riasztást is gyűjthet az egyes Azure számítási erőforrásokból (virtuális gépekből, tárolóból, app Service-ből), az adatforrásokból (SQL-ADATBÁZISból és-tárolóból) és az Azure szolgáltatási rétegeiből. Ez a funkció lehetővé teszi, hogy megtekintse az egyes erőforrásokon belüli fiókok rendellenességeit.

- [Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directoryban](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Az Azure Identity Protection engedélyezése](../active-directory/identity-protection/overview-identity-protection.md) 

- [Fenyegetésvédelem az Azure Security Centerben](/azure/security-center/threat-protection)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Naplózás engedélyezése Azure-beli hálózati tevékenységekhez

**Útmutató**: a Windows virtuális asztal nem hozza létre vagy dolgozza fel a tartománynév-szolgáltatás (DNS) lekérdezési naplóit. A szolgáltatásban regisztrált erőforrások azonban létrehozhatnak folyamat-naplókat.

Engedélyezheti és gyűjtheti a hálózati biztonsági csoport erőforrásait és folyamatait, Azure Firewall naplókat és webalkalmazási tűzfal (WAF) naplókat a biztonsági elemzésekhez az incidensek vizsgálatának, a fenyegetések vadászatának és a biztonsági riasztások generálásának támogatásához. A folyamat naplóit elküldheti egy Azure Monitor Log Analytics munkaterületre, majd a Traffic Analytics használatával betekintést biztosíthat.

- [Hálózati biztonsági csoport folyamatábráinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Az Azure Firewall naplói és metrikái](/azure/firewall/logs-and-metrics) 

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md) 

- [Azure hálózatkezelési figyelési megoldások Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Naplózás engedélyezése Azure-erőforrásokhoz

**Útmutató**: a tevékenység-naplók, amelyek automatikusan engedélyezve vannak, a Windows rendszerű virtuális asztali erőforrások összes írási műveletét (Put, post, DELETE) tartalmazzák, kivéve az olvasási MŰVELETEKET (Get). A Tevékenységnaplók segítségével hibát kereshet a hibaelhárítás során, vagy megfigyelheti, hogy a szervezet felhasználója hogyan módosított egy erőforrást.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [A naplózás és a különböző naplózási típusok megismerése az Azure-ban](../azure-monitor/platform/platform-logs-overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Biztonsági naplók kezelésének és elemzésének központosítása

**Útmutató**: a korreláció engedélyezéséhez központosítsa a naplózási tárolást és az elemzést. Minden naplózási forrás esetében ellenőrizze, hogy van-e hozzárendelve adat-tulajdonos, hozzáférési útmutató, tárolási hely, az adatok feldolgozásához és eléréséhez használt eszközök, valamint az adatmegőrzési követelmények.

Győződjön meg arról, hogy integrálja az Azure-tevékenységek naplóit a központi naplózásba. Naplók betöltése Azure Monitor használatával a végponti eszközök, a hálózati erőforrások és más biztonsági rendszerek által generált biztonsági adatokat összesítve. Azure Monitor a Log Analytics-munkaterületek használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat hosszú távú és archiválási tároláshoz.

Emellett az Azure Sentinel vagy egy harmadik féltől származó biztonsági információkkal foglalkozó esemény kezelése (SIEM) is lehetővé teszi az adatok bevezetését. Számos szervezet úgy dönt, hogy a gyakran használt és az Azure-tárolót használja a ritkábban használt adatokhoz az Azure Sentinel használatával.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Előkészítés – incidensválasz-folyamat frissítése az Azure-hoz

**Útmutató**: Gondoskodjon róla, hogy a vállalata rendelkezik a biztonsági incidensek kezelésére szolgáló folyamattal, ezeket az folyamatokat az Azure-hoz igazította, és rendszeresen gyakorlja a készenlét érdekében.

- [Biztonság implementálása a teljes nagyvállalati környezetben](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Incidensválasz referencia-útmutató](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Előkészítés – incidensről szóló értesítés beállítása

**Útmutató**: Állítson be a biztonsági incidensekre vonatkozó kapcsolattartási adatokat az Azure Security Centerben. A Microsoft ezen kapcsolattartási adatok használatával keresi meg Önt, ha a Microsoft Security Response Center (MSCR) felfedezi, hogy az adataihoz törvénytelen vagy jogosulatlan módon fértek hozzá. Lehetősége van az incidensekkel kapcsolatos riasztások és értesítések testreszabására a különböző Azure-szolgáltatásokban az incidensválasz-igények alapján. 

- [Az Azure Security Center biztonsági kapcsolattartójának beállítása](../security-center/security-center-provide-security-contact-details.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Észlelés és elemzés – incidensek létrehozása jó minőségű riasztások alapján

**Útmutató**: Gondoskodjon egy, a magas minőségű riasztások létrehozására és a riasztások minőségének mérésére szolgáló folyamatról. Ez lehetővé teszi a korábbi incidensek tanulságainak levonását és a riasztások rangsorolását az elemzők számára, hogy ne veszítsenek időt a téves riasztások miatt. 

A magas minőségű riasztások készítése épülhet a korábbi incidensek tapasztalataira, az ellenőrzött közösségi forrásokra, valamint azokra az eszközökre, amelyek a riasztások generálását és tisztítását a különböző jelforrások egyesítésével és egyeztetésével végzik. 

Azure Security Center kiváló minőségű riasztásokat biztosít számos Azure-eszközön. Az ASC adatösszekötővel streamelheti a riasztásokat az Azure Sentinelbe. Az Azure Sentinellel speciális riasztási szabályokat hozhat létre, amelyekkel automatikusan hozhat létre incidenseket a vizsgálatokhoz. 

Az exportálási funkcióval exportálhatja az Azure Security Center riasztásait és javaslatait, amelyek segítenek az Azure-erőforrások kockázatainak azonosításában. A riasztásokat és javaslatokat manuálisan vagy folyamatosan is exportálhatja.

- [Az exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Észlelés és elemzés – incidens vizsgálata

**Útmutató**: Győződjön meg arról, hogy az elemzők különböző adatforrásokat kérdezhetnek le és használhatnak a lehetséges incidensek vizsgálata során, és átfogó képet adhatnak a történtekről. A vakfoltok elkerülése érdekében különböző naplókat kell gyűjteni a potenciális támadók tevékenységének követéséhez a támadási útvonalon.  A megállapítások és eredmények rögzítéséről is érdemes gondoskodnia, hogy később más elemzők referenciaként használhassák őket.  

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

Az Azure Security Center súlyossági szintet rendel az egyes riasztásokhoz, és segít rangsorolni, hogy melyik riasztásokat kell először kivizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett a címkék használatával megjelölheti az erőforrásokat, és létrehozhat egy elnevezési rendszert az Azure-erőforrások, különösen a bizalmas adatok feldolgozását végző erőforrások azonosításához és kategorizálásához. Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](/azure/azure-resource-manager/resource-group-using-tags)

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

*További információ: [Azure Security Benchmark: A biztonsági állapot és a biztonsági rések kezelése](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: biztonságos konfigurációk létrehozása számítási erőforrásokhoz

**Útmutató**: a Azure Security Center és Azure Policy használatával biztonságos konfigurációkat hozhat létre minden számítási erőforráson, beleértve a virtuális gépeket, a tárolókat és másokat is.

A szervezete által igényelt operációs rendszer biztonsági konfigurációjának létrehozásához egyéni operációsrendszer-lemezképeket használhat, vagy Azure Automation állapot konfigurációját.

- [Azure Security Center javaslatok figyelése](../security-center/security-center-recommendations.md) 

- [Azure Automation állapot konfigurációjának áttekintése](../automation/automation-dsc-overview.md) 

- [A Windows Virtual Desktop környezete](environment-setup.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4: a számítási erőforrások biztonságos konfigurációjának fenntartása

**Útmutató**: a Azure Security Center és Azure Policy használatával rendszeresen értékelheti és javíthatja az Azure-beli számítási erőforrások, például a virtuális gépek, a tárolók és mások konfigurációs kockázatait. Emellett Azure Resource Manager sablonokat, egyéni operációsrendszer-lemezképeket vagy Azure Automation állapot konfigurációját is használhatja a szervezete számára szükséges operációs rendszer biztonsági beállításainak fenntartásához. A Azure Automation állapot-konfigurációval együtt a Microsoft virtuálisgép-sablonjai segíthetnek a biztonsági követelmények teljesítésében és fenntartásában.

Az Azure Marketplace-en a Microsoft által közzétett virtuálisgép-rendszerképeket a Microsoft felügyeli és tartja karban.

A Azure Security Center a tároló rendszerképében lévő biztonsági réseket is bevizsgálhatja, és a tárolóban lévő Docker-konfiguráció folyamatos figyelését végezheti el a központ internetes biztonság Docker-teljesítménytesztének használatával. A javaslatok megtekintéséhez és a problémák megoldásához használja a Azure Security Center javaslatok lapot.

- [Azure Security Center sebezhetőségi felméréssel kapcsolatos javaslatok megvalósítása](/azure/security-center/security-center-vulnerability-assessment-recommendations) 

- [Azure-beli virtuális gép létrehozása ARM-sablonból](../virtual-machines/windows/ps-template.md) 

- [Azure Automation állapot konfigurációjának áttekintése](../automation/automation-dsc-overview.md) 

- [Tárolóbiztonság a Security Centerben](../security-center/container-security.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>PV-5: az egyéni operációs rendszer és a tároló lemezképének biztonságos tárolása

**Útmutató**: a Windows rendszerű virtuális asztali szolgáltatás lehetővé teszi az ügyfeleknek az operációsrendszer-lemezképek kezelését. Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával biztosíthatja, hogy csak a jogosult felhasználók férhessenek hozzá az egyéni lemezképekhez. Az Azure megosztott képtárat használva képeket oszthat meg különböző felhasználókhoz, egyszerű szolgáltatásokhoz vagy Active Directory-csoportokhoz a szervezeten belül. Tárolja a Azure Container Registry tároló lemezképeit, és a RBAC használatával győződjön meg arról, hogy csak a jogosult felhasználók férhetnek hozzá.

- [Az Azure RBAC ismertetése](../role-based-access-control/rbac-and-directory-admin-roles.md) 

- [Az Azure RBAC konfigurálása](../role-based-access-control/quickstart-assign-role-user-portal.md) 

- [Megosztott képgyűjtemény – áttekintés](/azure/virtual-machines/windows/shared-image-galleries)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="pv-6-perform-software-vulnerability-assessments"></a>PV-6: a szoftver sebezhetőségi felmérések végrehajtása

**Útmutató**: a Windows rendszerű virtuális asztalok lehetővé teszik a saját virtuális gépek üzembe helyezését és a szolgáltatásba való regisztrálását, valamint az SQL-adatbázis futtatását a környezetben.

A Windows virtuális asztal egy külső gyártótól származó megoldást használhat a sebezhetőségi felmérések elvégzésére a hálózati eszközökön és a webalkalmazásokon. Távoli vizsgálatok végrehajtásakor ne használjon egyetlen, örökös rendszergazdai fiókot. Érdemes lehet a beolvasni a JIT üzembe helyezési módszertant alkalmazni. A vizsgálati fiók hitelesítő adatait védeni kell, figyelni kell, és csak a sebezhetőségi vizsgálathoz kell használni.

Szükség esetén a vizsgálat eredményeit konzisztens időközönként exportálja, és összehasonlítja az eredményeket a korábbi vizsgálatokkal annak ellenőrzéséhez, hogy a biztonsági rések szervizelése megtörtént-e.

Az Azure-beli virtuális gépeken (és az SQL-kiszolgálókon) a sebezhetőségi felmérések elvégzéséhez kövesse az Azure Security Center ajánlásait. A Azure Security Center a virtuális gép, a tároló lemezképei és az SQL Database beépített sebezhetőségi ellenőrzőeszközével rendelkezik.

Szükség szerint a vizsgálat eredményeit konzisztens időközönként exportálja, és összehasonlítja az eredményeket a korábbi vizsgálatokkal annak ellenőrzéséhez, hogy a biztonsági rések szervizelése megtörtént-e. A Azure Security Center által javasolt sebezhetőség-kezelési javaslatok használatakor a kiválasztott megoldás portálján megtekintheti a korábbi vizsgálati adataikat.

- [Azure Security Center sebezhetőségi felméréssel kapcsolatos javaslatok megvalósítása](/azure/security-center/security-center-vulnerability-assessment-recommendations) 

- [A virtuális gépekhez készült integrált sebezhetőségi képolvasó](/azure/security-center/built-in-vulnerability-assessment) 
- [SQL sebezhetőségi felmérés](../azure-sql/database/sql-vulnerability-assessment.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: Szoftveres biztonsági rések gyors és automatikus javítása

**Útmutató**: a Windows rendszerű virtuális asztal nem használ vagy nem igényel harmadik féltől származó szoftvert. A Windows rendszerű virtuális asztalok azonban lehetővé teszik saját virtuális gépek üzembe helyezését és a szolgáltatásba való regisztrálását.

Azure Automation Update Management vagy egy harmadik féltől származó megoldás használatával győződjön meg arról, hogy a legújabb biztonsági frissítések telepítve vannak a Windows Server rendszerű virtuális gépeken. Windows rendszerű virtuális gépek esetén győződjön meg arról, hogy Windows Update engedélyezve van, és automatikus frissítésre van beállítva.

Külső gyártótól származó szoftverekhez vagy System Center Updates Publisherokhoz is használhat harmadik féltől származó javítási felügyeleti megoldást Configuration Managerhoz.

- [Virtuális gépek Update Management konfigurálása az Azure-ban](/azure/automation/update-management/overview) 

- [Azure-beli virtuális gépek frissítéseinek és javításának kezelése](/azure/automation/update-management/manage-updates-for-vm)

- [A Microsoft Endpoint Configuration Manager konfigurálása a Windows rendszerű virtuális asztali környezethez](configure-automatic-updates.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Rendszeres támadásszimulációk végrehajtása

**Útmutató**: a Windows rendszerű virtuális asztal nem teszi lehetővé, hogy az ügyfelek a Windows rendszerű virtuális asztali erőforrásaik számára saját behatolási tesztelést végezzenek.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="endpoint-security"></a>Endpoint Security

*További információt az [Azure biztonsági teljesítményteszt: Endpoint Security](/azure/security/benchmarks/security-controls-v2-endpoint-security)című témakörben talál.*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: végpont-észlelés és-válasz használata (EDR)

**Útmutató**: a Windows virtuális asztal nem biztosít semmilyen konkrét képességet a végpont-észlelési és a reagálási (EDR) folyamatokhoz. A szolgáltatáshoz regisztrált erőforrások azonban kihasználhatják a végpont-észlelési és a reagálási képességeket. 

Engedélyezze a végpontok észlelését és válaszait a kiszolgálók és az ügyfelek számára, és integrálja azokat a biztonsági információkkal és az eseménykezelő (SIEM) megoldásokkal és a biztonsági műveletek folyamataival.

A Microsoft Defender komplex veszélyforrások elleni védelme lehetővé teszi a végpontok észlelését és a reagálási képességeket a speciális fenyegetések megelőzésére, észlelésére, kivizsgálására és reagálására szolgáló vállalati végpontok biztonsági platformjának részeként.

- [A Microsoft Defender komplex veszélyforrások elleni védelem áttekintése](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) 

- [Microsoft Defender ATP-szolgáltatás Windows-kiszolgálókhoz](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints) 

- [Microsoft Defender ATP szolgáltatás a nem Windows rendszerű kiszolgálókhoz](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

- [Microsoft Defender ATP a nem állandó virtuális asztali infrastruktúrához](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-vdi)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: központilag felügyelt, modern kártevő szoftverek használata

**Útmutató**: a Windows rendszerű virtuális asztali erőforrásainak a valós idejű és rendszeres vizsgálatra alkalmas, központilag felügyelt és modern Endpoint anti-malware megoldással való ellátása érdekében.

A Azure Security Center automatikusan képes azonosítani a virtuális gépek számos népszerű kártevő szoftveres megoldását, és jelentést készít az Endpoint Protection futási állapotáról, és javaslatokat tesz.

A Microsoft antimalware for Azure Cloud Services a Windows rendszerű virtuális gépek (VM) alapértelmezett kártevő szoftvere. Az Azure Storage-fiókokba feltöltött kártevők észleléséhez a veszélyforrások észlelése Azure Security Center is használható az adatszolgáltatások számára.

- [A Microsoft antimalware konfigurálása Cloud Services és Virtual Machines](../security/fundamentals/antimalware.md) 

- [Támogatott Endpoint Protection-megoldások](https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows#supported-endpoint-protection-solutions)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: a kártevők elleni aláírások gyors és konzisztens frissítése.

Kövesse a Azure Security Centerban található javaslatokat: "számítási &amp; alkalmazások", hogy az összes virtuális gép és/vagy tároló naprakész legyen a legújabb aláírásokkal.

A Microsoft antimalware alapértelmezés szerint automatikusan telepíti a legújabb aláírásokat és a motor-frissítéseket.

- [A Microsoft antimalware telepítése Azure Cloud Services és Virtual Machines](../security/fundamentals/antimalware.md) 

- [Endpoint Protection-Értékelés és javaslatok a Azure Security Center](../security-center/security-center-endpoint-protection.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="backup-and-recovery"></a>Biztonsági másolat és helyreállítás

*További információ: [Azure Security Benchmark: Biztonsági mentés és helyreállítás](/azure/security/benchmarks/security-controls-v2-backup-recovery).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: rendszeres automatikus biztonsági mentések biztosítása

**Útmutató**: gondoskodjon arról, hogy biztonsági mentést készít a rendszerekről és az adatról, hogy egy váratlan esemény után fenntartsa az üzletmenet folytonosságát. Ehhez útmutatást kell adni a helyreállítási pontok célkitűzésének (RPO) és a helyreállítási idő célkitűzésének (RTO) bármely célkitűzésének.

Engedélyezze Azure Backup és konfigurálja a biztonsági mentési forrást (például Azure-beli virtuális gépeket, SQL Server, HANA-adatbázisokat vagy fájlmegosztást), valamint a kívánt gyakoriságot és megőrzési időt.

A magasabb szintű redundancia érdekében engedélyezheti a Geo-redundáns tárolási lehetőséget, hogy a biztonsági mentési adatokat egy másodlagos régióba replikálja, és a helyreállítást a régiók közötti visszaállítással.

- [Nagyvállalati szintű üzletmenet-folytonosság és vészhelyreállítás](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery) 

- [A Azure Backup engedélyezése](/azure/backup/) 

- [Hogyan engedélyezhető a régiók közötti visszaállítás?](/azure/backup/backup-azure-arm-restore-vms#cross-region-restore) 

- [Üzletmenet-folytonossági és vész-helyreállítási terv beállítása a Windows rendszerű virtuális asztali gépeken](disaster-recovery.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="br-2-encrypt-backup-data"></a>BR-2: biztonsági mentési adataik titkosítása

**Útmutató**: gondoskodjon arról, hogy a biztonsági másolatok védve legyenek a támadásokkal szemben. Ennek tartalmaznia kell a biztonsági másolatok titkosítását a titkosság elvesztése elleni védelem érdekében.

Az Azure-szolgáltatások rendszeres biztonsági mentéséhez a biztonsági mentési adatai automatikusan titkosítva vannak az Azure platform által felügyelt kulcsok használatával. A biztonsági mentést az ügyfél által felügyelt kulcs használatával titkosíthatja. Ebben az esetben ügyeljen arra, hogy az ügyfél által felügyelt kulcs a Key vaultban is a biztonsági mentési hatókörben legyen.

A biztonsági másolatok és az ügyfél által felügyelt kulcsok kezeléséhez használja a Azure Backup, Azure Key Vault vagy más erőforrások szerepköralapú hozzáférés-vezérlését. Emellett a speciális biztonsági funkciók lehetővé teszik a többtényezős hitelesítés megkövetelését a biztonsági mentések módosítása vagy törlése előtt.

A Azure Backup/Azure/Backup/Security-Overview biztonsági funkcióinak áttekintése 

- [Biztonsági mentési adatok titkosítása ügyfelek által felügyelt kulcsok használatával](/azure/backup/encryption-at-rest-with-cmk) 

- [Key Vault kulcsok biztonsági mentése az Azure-ban](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0&amp;preserve-view=true)

- [Biztonsági funkciók a hibrid biztonsági mentések elleni védelemhez](/azure/backup/backup-azure-security-feature#prevent-attacks)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Az összes biztonsági másolat és az ügyfelek által kezelt kulcsok ellenőrzése

**Útmutató**: javasoljuk, hogy rendszeresen ellenőrizze az adatok integritását a biztonsági mentési adathordozón az adatok visszaállítási folyamatának elvégzésével, hogy a biztonsági mentés megfelelően működjön.

- [Fájlok helyreállítása az Azure-beli virtuális gépek biztonsági másolatából](/azure/backup/backup-azure-restore-files-from-vm)

- [Biztonsági megvalósítás](/azure/backup/backup-azure-restore-files-from-vm#security-implementations)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="governance-and-strategy"></a>Irányítás és stratégia

*További információ: [Azure Security Benchmark: Irányítás és stratégia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

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

További információkat az alábbi hivatkozásokon találhat:
- [Az Azure Security architektúrára vonatkozó ajánlásai – Tárolás, adatok és titkosítás](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Az Azure Security alapjai – Azure-beli adatbiztonság, titkosítás és tárolás](../security/fundamentals/encryption-overview.md)

- [Felhőadaptálási keretrendszer – Az Azure-beli adatbiztonsághoz és titkosításhoz ajánlott eljárások](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure Security Benchmark – Eszközkezelés](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Azure Security Benchmark – Adatvédelem](/azure/security/benchmarks/security-controls-v2-data-protection)

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

**Útmutató**: Folyamatosan mérje és mérsékelje a kockázatokat, amelyeknek az egyes összetevők, valamint az a környezet van kitéve, amelyben üzemeltetve vannak. Kezelje kiemelten az olyan nagy értékű összetevőket és leginkább elérhetővé tett támadási felületeket, mint a közzétett alkalmazások, a hálózat be- és kilépési pontjai, a felhasználói és rendszergazdai végpontok stb.

- [Azure Security Benchmark - Biztonsági állapot és biztonsági rések felmérése](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

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

További információkat az alábbi hivatkozásokon találhat:
- [Ajánlott Azure-biztonsági eljárások 11 – Architektúra. Egyetlen egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark – Hálózati biztonság](/azure/security/benchmarks/security-controls-v2-network-security)

- [A nagyvállalati hálózati biztonság áttekintése](../security/fundamentals/network-overview.md)

- [Nagyvállalati hálózati architektúrára vonatkozó stratégia](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

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

- [Azure Security Benchmark – Identitáskezelés](/azure/automation/update-management/overview)

- [Azure Security Benchmark – Emelt jogosultságú hozzáférés](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Ajánlott Azure-biztonsági eljárások 11 – Architektúra. Egyetlen egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Az Azure identitáskezelésének biztonsági áttekintése](../security/fundamentals/identity-management-overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Naplózási és veszélyforrás-kezelési stratégia definiálása

**Útmutató**: Naplózási és veszélyforrás-kezelési stratégia kidolgozásával gyorsan észlelheti és elháríthatja a fenyegetéseket, ugyanakkor betartva a megfelelőségi követelményeket. Kezelje kiemelten a jó minőségű riasztások biztosítását az elemzők számára, és a könnyen használható felületeket, hogy az integráció és a manuális lépések helyett a veszélyforrásokra összpontosíthassanak. 

Ennek a stratégiának magában kell foglalnia a dokumentált útmutatót, és az alábbi elemek szabványait: 

-   A biztonsági üzemeltetési (SecOps) szervezet szerepköre és feladatai 

-   Jól definiált incidenskezelési folyamat az NIST-vel vagy más iparági keretrendszerrel összhangban 

-   Naplórögzítés és -megőrzés a veszélyforrások észlelése, az incidensek kezelése és a megfelelőségi igények támogatására

-   A fenyegetésekkel kapcsolatos információk központosított láthatósága és összevetése SIEM, natív Azure-képességek és más források használatával 

-   Kommunikációs és értesítési terv az ügyfelek, szállítók és külső érdekelt felek számára

-   Natív Azure-beli és külső platformok használata incidensek kezelésére, például naplózásra és veszélyforrások észlelésére, kivizsgálásra és a támadások megfékezésére és megszüntetésére

-   Folyamatok az olyan incidenskezelési és incidens utáni tevékenységekhez, mint a tanulságok levonása és a nyomok megőrzése

További információkat az alábbi hivatkozásokon találhat:

- [Azure Security Benchmark – Naplózás és fenyegetésészlelés](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure Security Benchmark – Incidenskezelés](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Ajánlott Azure-biztonsági eljárások 4 – Folyamat. Incidensválasz-folyamat frissítése a felhőhöz](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure-adaptálási keretrendszer, útmutató naplózási és jelentéskészítési döntésekhez](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure-beli nagyvállalati szintű skálázás, felügyelet és monitorozás](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
