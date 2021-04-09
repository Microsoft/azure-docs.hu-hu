---
title: Azure-beli biztonsági alaptervek az Azure-alkalmazások konfigurálásához
description: Az Azure app Configuration biztonsági alapkonfigurációja az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8b68e22e69bf2d4a1655883b249f883f98f565a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101091241"
---
# <a name="azure-security-baseline-for-azure-app-configuration"></a>Azure-beli biztonsági alaptervek az Azure-alkalmazások konfigurálásához

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 2,0-es verziójáról](../security/benchmarks/overview.md) az Azure-alkalmazások konfigurálására vonatkozó útmutatást alkalmazza. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint az Azure-alkalmazás konfigurálására vonatkozó kapcsolódó útmutatás. Az Azure-alkalmazás konfigurációjához nem alkalmazható **vezérlők** ki lettek zárva.

Az Azure-alkalmazások konfigurációjának teljes leképezése az Azure-os biztonsági Teljesítménytesztre című cikkből megtudhatja, hogy az Azure-alkalmazás [konfigurációjának teljes biztonsági alapmegfeleltetési fájlja](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: a belső forgalom biztonságának megvalósítása

**Útmutató**: az Azure-alkalmazás konfigurációja nem helyez üzembe semmilyen erőforrást közvetlenül egy virtuális hálózatban. Mivel a szolgáltatás nem virtuális hálózatra van telepítve, bizonyos hálózati szolgáltatások nem használhatók a szolgáltatás belső forgalmának biztonságossá tételéhez, például: hálózati biztonsági csoportok, útválasztási táblák vagy más hálózati berendezések, például egy Azure Firewall. Az alkalmazás konfigurációja azonban lehetővé teszi privát végpontok használatát az Azure-alkalmazások konfigurációjának biztonságos csatlakoztatásához egy virtuális hálózatból.

Az Azure Sentinel használatával felderítheti az örökölt nem biztonságos protokollok, például az SSL/TLSv1, a SMBv1, az LM/NTLMv1, a wDigest, az aláíratlan LDAP-kötések és a Kerberos-alapú gyenge titkosítás használatát.

- [Privát végpontok használata az Azure app Configuration szolgáltatáshoz](concept-private-endpoint.md)

- [Azure Sentinel – nem biztonságos protokollok – munkafüzet](../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="ns-2-connect-private-networks-together"></a>NS-2: magánhálózati hálózatok összekapcsolása

**Útmutató**: az Azure-alkalmazás konfigurációja támogatja a privát végpontok használatát az adatküldés privát kapcsolaton keresztüli biztonságos elküldéséhez. Az Azure ExpressRoute vagy az Azure virtual private Network (VPN) használatával privát kapcsolatokat hozhat létre az Azure-adatközpontok és a helyszíni infrastruktúra között egy közös elhelyezésű környezetben. A ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át, így megbízhatóbbak, gyorsabbak és kevesebb késéssel rendelkeznek, mint a szokásos internetes kapcsolatok. Pont – hely típusú VPN-és helyek közötti VPN esetén a VPN-beállítások és az Azure ExpressRoute bármely kombinációja segítségével a helyszíni eszközöket vagy hálózatokat egy virtuális hálózathoz is összekapcsolhatja.

Ha két vagy több virtuális hálózatot szeretne összekapcsolni az Azure-ban, használja a virtuális hálózati társítást. A kiszolgált virtuális hálózatok közötti hálózati forgalom magán, és az Azure-beli gerinces hálózaton marad.

- [A ExpressRoute kapcsolati modelljei](../expressroute/expressroute-connectivity-models.md)

- [Az Azure VPN áttekintése](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Virtuális hálózati társviszony](../virtual-network/virtual-network-peering-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Privát hálózati hozzáférés létesítése Azure-szolgáltatásokhoz

**Útmutató**: az Azure privát hivatkozásának használatával engedélyezheti az Azure-alkalmazások konfigurációját a virtuális hálózatokról anélkül, hogy az interneten keresztül kellene eljutnia.

A privát hozzáférés az Azure-szolgáltatások által kínált hitelesítéssel és adatforgalommal kapcsolatos további védelmi részletes mérték.

- [Az Azure privát hivatkozásának megismerése](../private-link/private-link-overview.md)

- [Privát hivatkozás beállítása az Azure-alkalmazások konfigurációjában](concept-private-endpoint.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: a külső hálózati támadásoktól származó alkalmazások és szolgáltatások elleni védelem

**Útmutatás**: Ha virtuális hálózaton keresztül éri el a konfigurációs értékeket, az erőforrásokat védeni kell a külső hálózatoktól érkező támadások ellen, beleértve az elosztott szolgáltatásmegtagadási (DDoS) támadásokat, az alkalmazásokra vonatkozó támadásokat, valamint a kéretlen és potenciálisan kártékony internetes forgalmat. A Azure Firewall használatával védelmet biztosíthat az alkalmazások és szolgáltatások számára az internetről és más külső helyekről származó, potenciálisan rosszindulatú forgalom ellen. Az adatvédelmet a DDoS-támadások ellen biztosíthatja az Azure-beli virtuális hálózatokon a DDoS standard szintű védelem engedélyezésével. A Azure Security Center használatával azonosíthatja a hálózattal kapcsolatos erőforrásokkal kapcsolatos hibás konfigurációs kockázatokat.

Az Azure-alkalmazás konfigurációja nem a webalkalmazások futtatására készült, hanem a webalkalmazások konfigurálását is lehetővé teszi. Semmilyen további beállítást nem kell konfigurálnia, vagy további hálózati szolgáltatásokat kell telepítenie a webalkalmazásokra irányuló külső hálózati támadások elleni védelemhez.

- [Azure Firewall dokumentáció](../firewall/index.yml)

- [Azure DDoS Protection standard kezelése a Azure Portal használatával](../ddos-protection/manage-ddos-protection.md)

- [Az Azure Security Centerre vonatkozó ajánlások](../security-center/recommendations-reference.md#recs-networking)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: a Azure Firewall és az ismert kártékony IP-címekre és tartományokra vonatkozó riasztások és/vagy azok közötti adatforgalom blokkolása veszélyforrások elleni szűréssel. Az IP-címek és -tartományok forrása a Microsoft veszélyforrás-felderítési hírcsatornája. Ha szükség van a hasznos adatok vizsgálatára, az Azure Marketplace-en üzembe helyezhet egy harmadik féltől származó azonosító/IP-megoldást az adattartalom-ellenőrzési képességekkel. Másik lehetőségként dönthet úgy, hogy gazdagép-alapú AZONOSÍTÓkat/IP-címeket vagy gazdagép-alapú végpont-észlelési és-gyorsreagáló (EDR) megoldást használ a hálózat-alapú AZONOSÍTÓk/IP-címek helyett vagy ahelyett.

Megjegyzés: Ha az AZONOSÍTÓk/IP-címek használatára vonatkozó előírás vagy egyéb követelmény van, győződjön meg arról, hogy mindig úgy van hangolva, hogy magas színvonalú riasztásokat nyújtson az SIEM-megoldáshoz.

- [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

- [Az Azure Marketplace harmadik féltől származó azonosító képességeket is tartalmaz](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Microsoft Defender ATP EDR-képesség](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: a hálózati biztonsági szabályok egyszerűsítése

**Útmutató**: az Azure Virtual Network szolgáltatás-címkék használatával meghatározhatja a hálózati biztonsági csoportokhoz vagy az alkalmazás-konfigurációs erőforrásokhoz konfigurált Azure Firewallhoz tartozó hálózati hozzáférés-vezérlést. A "AppConfiguration" szolgáltatási címkét adott IP-címek helyett használhatja a kimenő forgalomra vonatkozó biztonsági szabályok létrehozásakor az alkalmazás hálózatában. Ha a szolgáltatási címke nevét egy szabály megfelelő forrás vagy cél mezőjében adja meg, akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

- [A szolgáltatási címkék megismerése és használata](../virtual-network/service-tags-overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="identity-management"></a>Identitáskezelés

*További információ: [Azure Security Benchmark: Identitáskezelés](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Az Azure Active Directory, mint központi identitáskezelő és hitelesítési rendszer szabványosítása

**Útmutató**: az Azure-alkalmazás konfigurációja integrálva van az Azure alapértelmezett identitás-és hozzáférés-kezelési szolgáltatásának Azure Active Directory (Azure ad) szolgáltatásával. Egységesítse az Azure AD-t a szervezet identitás-és hozzáférés-kezelésének szabályozásához a ben:
- Microsoft Cloud erőforrások, például a Azure Portal, az Azure Storage, az Azure virtuális gép (Linux és Windows), a Azure Key Vault, a Pásti és az SaaS-alkalmazások.
- a szervezet erőforrásaiban, például az Azure-on vagy a vállalati hálózat erőforrásain lévő alkalmazásokban.

Az Azure AD biztonságának kiemelten fontosnak kell lennie a vállalat felhőbiztonsági gyakorlatában. Az Azure AD egy identitásbiztonsági pontszámmal segít felmérni az identitásbiztonsági helyzetet a Microsoft ajánlott eljárásaihoz viszonyítva. A pontszám alapján felmérheti, mennyire felel meg a konfiguráció az ajánlott eljárásoknak, és javíthatja a biztonság állapotát.

Az Azure az Azure AD és a OAuth használatával engedélyezi az alkalmazások konfigurációs adataihoz való hozzáférés engedélyezését az alábbi Azure beépített szerepkörökkel:

- Alkalmazás-konfigurációs adat tulajdonosa: ezt a szerepkört használva olvasási/írási/törlési hozzáférést biztosíthat az alkalmazás konfigurációs adatához. Ez nem biztosít hozzáférést az alkalmazás konfigurációs erőforrásához.

- Alkalmazás-konfigurációs Adatolvasó: ezt a szerepkört használva olvasási hozzáférést adhat az alkalmazás konfigurációs adatához. Ez nem biztosít hozzáférést az alkalmazás konfigurációs erőforrásához.

- Közreműködő: használja ezt a szerepkört az alkalmazás konfigurációs erőforrásának kezeléséhez. Az alkalmazás konfigurációs adatai hozzáférési kulcsok használatával érhetők el, ez a szerepkör nem biztosít közvetlen hozzáférést az Azure AD-vel az adathoz.

- Olvasó: ezt a szerepkört használva olvasási hozzáférést adhat az alkalmazás konfigurációs erőforrásához. Ez nem biztosít hozzáférést az erőforrás elérési kulcsaihoz, és nem az alkalmazás konfigurációjában tárolt adathoz.

További információkat az alábbi hivatkozásokon találhat:

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Az identitásbiztonsági pontszám fogalma az Azure Active Directoryban](../active-directory/fundamentals/identity-secure-score.md)

- [Hozzáférés engedélyezése az Azure app Configuration szolgáltatáshoz az Azure AD használatával](concept-enable-rbac.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Alkalmazásidentitások biztonságos és automatikus kezelése

**Útmutató**: az Azure által felügyelt identitások használata az Azure-alkalmazások konfigurálásához nem emberi fiókokból, például más Azure-szolgáltatásokból. Azt javasoljuk, hogy az Azure Managed Identity szolgáltatást használja ahelyett, hogy hatékonyabb emberi fiókot hozzon létre az erőforrások eléréséhez vagy végrehajtásához, hogy korlátozza a további hitelesítő adatok kezelésének szükségességét. Az Azure-alkalmazások konfigurálása olyan felügyelt identitáshoz is hozzárendelhető, amely natív módon hitelesíthető az Azure AD-hitelesítést támogató egyéb Azure-szolgáltatásokkal/erőforrásokkal. Ez akkor lehet hasznos, ha lehetővé szeretné tenni az alkalmazások konfigurációjának egyszerű elérését Azure Key Vault a titkok beolvasása során. A felügyelt identitások használatakor az identitást az Azure platform felügyeli, és nem igényli semmilyen titok kiépítését és elforgatását.

Az Azure-alkalmazás konfigurációja támogatja, hogy az alkalmazás két típusú identitást biztosítson:
- A rendszer által hozzárendelt identitás a konfigurációs erőforráshoz van kötve. A konfigurációs erőforrás törlése után törlődik. Egy konfigurációs erőforrásnak csak egy rendszerhez rendelt identitása lehet.
- A felhasználó által hozzárendelt identitás egy önálló Azure-erőforrás, amelyet a konfigurációs erőforráshoz rendelhet hozzá. A konfigurációs erőforrásokhoz több felhasználó által hozzárendelt identitás is tartozhat.

Ha a felügyelt identitásokat nem lehet kihasználni, hozzon létre egy egyszerű szolgáltatásnevet az Azure-alkalmazás konfigurációs erőforrás szintjén, amely korlátozott engedélyekkel rendelkezik. Konfigurálja ezeket a egyszerű szolgáltatásokat a tanúsítvány hitelesítő adataival, és csak az ügyfél titkos kulcsaira térjen vissza. Mindkét esetben Azure Key Vault használható az Azure felügyelt identitásokkal együtt, így a futásidejű környezet (például egy Azure-függvény) beolvashatja a hitelesítő adatokat a kulcstartóból.

- [Felügyelt identitások használata az Azure-alkalmazások konfigurálásához](overview-managed-identity.md)

- [Azure-beli felügyelt identitások](../active-directory/managed-identities-azure-resources/overview.md)

- [Az Azure-erőforrások felügyelt identitásait támogató szolgáltatások](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Felügyelt identitások használata az App Configuration eléréséhez](howto-integrate-azure-managed-service-identity.md)

- [Azure-szolgáltatásnév](/powershell/azure/create-azure-service-principal-azureps) 

- [Egyszerű szolgáltatás létrehozása tanúsítványokkal](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [Rendszerbiztonsági tag regisztrációjának Azure Key Vault használata](../key-vault/general/authentication.md#app-identity-and-security-principals)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Az Azure AD-beli egyszeri bejelentkezés használata alkalmazások eléréséhez

**Útmutató**: az Azure App Configuration Azure Active Directory (Azure ad) használatával biztosítja az identitás-és hozzáférés-kezelést az Azure-erőforrásokhoz, a felhőalapú alkalmazásokhoz és a helyszíni alkalmazásokhoz. Ez vonatkozik az olyan nagyvállalati identitásokra, mint az alkalmazottak, valamint az olyan külső identitásokra is, mint a partnerek, szállítók és ellátók. Az Azure AD lehetővé teszi az egyszeri bejelentkezést (SSO) az alkalmazás konfigurációs szolgáltatásának a Azure Portalon keresztüli kezeléséhez a szinkronizált vállalati Active Directory-identitások használatával. Az összes felhasználót, alkalmazást és eszközt beléptetheti az Azure AD-be a zökkenőmentes, biztonságos hozzáférés, valamint a jobb átláthatóság és vezérlés érdekében.

- [Az Azure AD-vel megvalósított alkalmazás-SSO ismertetése](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Erős hitelesítési vezérlők használata minden Azure Active Directory-alapú hozzáféréshez

**Útmutató**: az Azure app Configuration olyan Azure Active Directory használ, amely támogatja a többtényezős hitelesítés (MFA) és az erős, jelszóval nem használható metódusok erős hitelesítési vezérlését.
- Többtényezős hitelesítés – Engedélyezheti az Azure AD MFA-t, és az Azure Security Center identitás- és hozzáférés-kezelésre vonatkozó javaslatait követve megvalósíthat néhány, az MFA beállításához ajánlott eljárást. Az MFA megkövetelhető az összes kijelölt felhasználótól, vagy felhasználónként a bejelentkezési feltételek és a kockázati tényezők alapján.
- Jelszó nélküli hitelesítés – Három jelszó nélküli hitelesítési lehetőség érhető el: a Windows Hello for Business, a Microsoft Authenticator alkalmazás, és az olyan helyszíni hitelesítési módszerek, mint az intelligens kártyák.

A rendszergazda és a Kiemelt jogosultságú felhasználók számára ellenőrizze, hogy a legmagasabb szintű erős hitelesítési módszert használja-e, majd ezt követően a megfelelő erős hitelesítési házirendet a többi felhasználó számára.

Megjegyzés: az MFA kikényszeríthető az alkalmazások konfigurációját elérő és kezelő felhasználói fiókokon, a programozott szolgáltatásfiókok esetében nem. Használjon jelszó nélküli hitelesítést, például a felügyelt identitásokat, ahol lehetséges, és minden felhasználói fiókon érvényesítse az MFA-t.

- [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Az Azure Active Directory jelszó nélküli hitelesítési lehetőségeinek bemutatása](../active-directory/authentication/concept-authentication-passwordless.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Fiókok rendellenességeinek monitorozása, és riasztás azok alapján

**Útmutató**: az Azure-alkalmazás konfigurációja integrálva van Azure Active Directory, amely a következő adatforrásokat biztosítja:

-   Bejelentkezések – a bejelentkezési jelentés a felügyelt alkalmazások használatával és a felhasználók bejelentkezési tevékenységeivel kapcsolatos információkat biztosít.

-   Naplók – az Azure AD különböző funkciói által végrehajtott módosítások naplókban való nyomon követését teszi lehetővé. Naplózott változások naplózása például felhasználók, alkalmazások, csoportok, szerepkörök és házirendek hozzáadásával vagy eltávolításával kapcsolatos naplók.

-   Kockázatos bejelentkezések – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa.

-   Kockázatosként megjelölt felhasználók – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága.

Ezek az adatforrások a Azure Monitor, az Azure Sentinel vagy a harmadik féltől származó SIEM rendszerekkel integrálhatók.

Azure Security Center riasztást is beállíthat bizonyos gyanús tevékenységekről, például a sikertelen hitelesítési kísérletekről, valamint az előfizetésben elavult fiókokról. 

Az Azure komplex veszélyforrások elleni védelem (ATP) olyan biztonsági megoldás, amely helyszíni Active Directory jeleket használ a fejlett fenyegetések, a feltört identitások és a rosszindulatú bennfentes műveletek azonosítására, észlelésére és kivizsgálására.

- [Tevékenység-jelentések naplózása az Azure AD-ben](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md)

- [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../security-center/security-center-identity-access.md)

- [Riasztások az Azure Security Center fenyegetések felderítésére szolgáló védelmi moduljában](../security-center/alerts-reference.md)

- [Azure-beli tevékenység-naplók integrálása a Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Adatok összekapcsolásának Azure AD Identity Protection](../sentinel/connect-azure-ad-identity-protection.md)

- [Azure Advanced Threat Protection](/azure-advanced-threat-protection/what-is-atp)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Azure-erőforrásokhoz való hozzáférés korlátozása feltételek alapján

**Útmutató**: az Azure app Configuration támogatja Azure Active Directory (Azure ad) feltételes hozzáférését egy részletesebb hozzáférés-vezérléshez felhasználó által definiált feltételek alapján, például bizonyos IP-címtartományok felhasználói bejelentkezéséhez az MFA-t kell használnia a bejelentkezéshez. A részletes hitelesítési munkamenet-kezelési szabályzat többféle helyzetben is felhasználható. Ezek a feltételes hozzáférési szabályzatok csak azokra a felhasználói fiókokra vonatkoznak, amelyek az Azure AD-ben hitelesítik az alkalmazást az alkalmazás konfigurációs szolgáltatásának eléréséhez és kezeléséhez, de nem vonatkoznak a konfigurációs erőforráshoz csatlakozó egyszerű szolgáltatásokra vagy kapcsolati karakterláncokra.

- [Az Azure-beli feltételes hozzáférés áttekintése](../active-directory/conditional-access/overview.md)

- [Gyakori feltételes hozzáférési szabályzatok](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Hitelesítési munkamenet-kezelés konfigurálása feltételes hozzáféréssel](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Hitelesítő adatok nem szándékos elérhetővé tételének kizárása

**Útmutató**: az Azure-alkalmazások konfigurálása lehetővé teszi, hogy az ügyfelek olyan konfigurációkat tároljanak, amelyek esetleg identitásokat vagy titkos kódokat tartalmazhatnak. A hitelesítő adatoknak a konfigurációkon belüli azonosításához javasolt a hitelesítőadat-olvasó bevezetése. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

Az Azure app Configuration szolgáltatást és a Azure Key Vault együtt használhatja. Tárolja a hitelesítő adatokat a Key Vaultban, majd csatolja ezeket a hitelesítő adatokat egy Key Vault-hivatkozás létrehozásához az alkalmazás konfigurációs erőforrásában. Amikor az alkalmazás konfigurációja létrehozza ezeket a hivatkozásokat, az értékek helyett a Key Vault értékek URI-azonosítóit tárolja. Az alkalmazások az alkalmazás-konfigurációhoz kapcsolódhatnak, hogy beolvassák a hitelesítő adatokat a Key Vaultból.

A GitHubhoz használhatja a natív titkoskód-szűrési funkciót a kódon belüli hitelesítő adatok vagy másféle titkos kódok felismerésére.

- [Oktatóanyag Key Vault referenciák ASP.NET Core alkalmazásban való használatához](use-key-vault-references-dotnet-core.md)

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Titkos kódok szűrése a GitHubban](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="privileged-access"></a>Emelt szintű hozzáférés

*További információ: [Azure Security Benchmark: Emelt jogosultságú hozzáférés](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Emelt jogosultságú felhasználók védelme és korlátozása

**Útmutatás**: korlátozza a magas jogosultsági szintű fiókok vagy szerepkörök számát, és emelt szintű védelemmel látja el ezeket a fiókokat, mert az ezzel a jogosultsággal rendelkező felhasználók közvetlenül vagy közvetve elolvashatják és módosíthatják az Azure-környezet minden erőforrását.

Az Azure AD Privileged Identity Management (PIM) használatával engedélyezheti az Azure-erőforrások és az Azure AD igény szerinti (just-in-time, JIT) jogosultságú hozzáférését. A JIT ideiglenes engedélyeket biztosít az érintett feladatok végrehajtásához, csak annyi időre, ameddig a felhasználóknak erre szükségük van. A PIM biztonsági riasztásokat is képes kiadni, amikor gyanús vagy nem biztonságos tevékenységeket észlel az Azure AD-szervezetben.

A hozzáférési kulcsok magas jogosultsággal rendelkeznek, és az ajánlott biztonsági gyakorlat szerint rendszeresen el kell forgatni őket. A hozzáférési kulcsok olyan kapcsolati karakterláncokat tartalmaznak, amelyek hitelesítő adatokat tartalmaznak, és titkosnak számítanak. Ezeket a titkokat Azure Key Vault kell tárolni, és a kódnak hitelesítenie kell a Key Vault a lekéréséhez. A hozzáférési kulcsok írási-olvasási vagy csak olvasási hozzáférést biztosíthatnak egy alkalmazáshoz. Győződjön meg arról, hogy a megfelelő típusú hozzáférési kulcs van kiállítva a jogosulatlan hozzáférés megakadályozása érdekében. A biztonság érdekében használja az Azure AD felügyelt identitások szolgáltatását. Ehhez csak az szükséges, hogy a konfigurációs végpont URL-címe hozzáférjen a konfigurációs értékekhez.

- [Alkalmazás-konfiguráció – ajánlott eljárások](howto-best-practices.md#app-configuration-bootstrap)

- [Felügyelt identitások használata az App Configuration eléréséhez](howto-integrate-azure-managed-service-identity.md)
- [Rendszergazdai szerepköri engedélyek az Azure AD-ben](../active-directory/roles/permissions-reference.md)

- [Az Azure Privileged Identity Management biztonsági riasztásainak használata](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Emelt szintű hozzáférés biztosítása Azure AD hibrid- és felhőkörnyezetekhez](../active-directory/roles/security-planning.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Az üzletileg kritikus rendszerek rendszergazdai elérésének korlátozása

**Útmutató**: az Azure-alkalmazások konfigurálása az Azure RBAC segítségével elkülöníti az üzleti szempontból kritikus rendszerek hozzáférését azáltal, hogy korlátozza, hogy mely fiókok emelt szintű hozzáférést kapnak. Az alkalmazás konfigurációja az Azure RBAC az erőforrás szintjén támogatja. Az üzleti szempontból kritikus fontosságú konfigurációk biztonságos kiépítéséhez a saját alkalmazás-konfigurációs erőforrásában tárolja ezeket az információkat. Egy erőforráson belül a részletes hozzáférés csak olvasási hozzáférésű fiókokon vagy kulcsokon, valamint címkézésen és címkézésen keresztül is elérhető.

A hozzáférés-vezérlés összes típusát a vállalati szegmentálási stratégiához kell igazítani, hogy biztosítsa a konzisztens hozzáférés-vezérlést.

- [Felügyeleti csoport elérése](../governance/management-groups/overview.md#management-group-access)

- [Azure-előfizetés rendszergazdái](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [A RBAC integrálása az Azure AD-val az alkalmazás konfigurációjának használatával](concept-enable-rbac.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Felhasználói hozzáférés rendszerességének áttekintése és egyeztetése

**Útmutató**: az Azure App Configuration Azure Active Directory (Azure ad-) fiókokat használ az erőforrások kezeléséhez, a fiókok és a hozzáférésük érvényességének biztosításához rendszeresen tekintse át a felhasználói fiókokat és a hozzáférés-hozzárendelést. 

Az Azure az Azure AD és a OAuth használatával engedélyezi az alkalmazások konfigurációs adataihoz való hozzáférés engedélyezését az alábbi Azure beépített szerepkörökkel:

- Alkalmazás-konfigurációs adat tulajdonosa: ezt a szerepkört használva olvasási/írási/törlési hozzáférést biztosíthat az alkalmazás konfigurációs adatához. Ez nem biztosít hozzáférést az alkalmazás konfigurációs erőforrásához.

- Alkalmazás-konfigurációs Adatolvasó: ezt a szerepkört használva olvasási hozzáférést adhat az alkalmazás konfigurációs adatához. Ez nem biztosít hozzáférést az alkalmazás konfigurációs erőforrásához

Az Azure AD hozzáférési felülvizsgálatok segítségével áttekintheti a csoporttagság, a vállalati alkalmazásokhoz való hozzáférés és a szerepkör-hozzárendelések, például az alkalmazás konfigurációs szerepkörei. Az Azure AD jelentéskészítési szolgáltatása lehetővé teszi, hogy a naplók segítséget nyújtsanak az elavult fiókok felderítéséhez. A felülvizsgálati folyamat megkönnyítése érdekében a Azure AD Privileged Identity Management használatával hozzáférési felülvizsgálati jelentési munkafolyamatot is létrehozhat.

Megjegyzés: a felügyelt identitásokat akkor javasoljuk, ha az alkalmazások konfigurációját egy másik szolgáltatásból vagy alkalmazásból szeretné hitelesíteni. A használat során külön kell kezelnie az alkalmazás-konfigurációhoz való hozzáféréshez konfigurált egyszerű szolgáltatásokat és kapcsolati karakterláncokat.

- [Azure-beli erőforrás-szerepkörök hozzáférési felülvizsgálatának létrehozása Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Az Azure AD identitás- és hozzáférési felülvizsgálatainak használata](../active-directory/governance/access-reviews-overview.md)

- [Hozzáférés engedélyezése az Azure app Configuration szolgáltatáshoz az Azure AD használatával](concept-enable-rbac.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Vészhelyzeti hozzáférés beállítása az Azure AD-ben

**Útmutató**: az Azure-alkalmazások konfigurálása az erőforrások kezeléséhez Azure Active Directory integrált. Ha nem szeretné megakadályozni, hogy véletlenül kizárja az Azure AD-szervezetét, állítson be egy vészhelyzeti hozzáférési fiókot a hozzáféréshez, ha a normál rendszergazdai fiókok nem használhatók. A vészhelyzeti hozzáférési fiókok általában magas szintű jogosultságokkal rendelkeznek, és nem ajánlott azokat egyes személyekhez társítani. A vészhelyzeti hozzáférési fiókok csak az olyan vészhelyzeti esetekre valók, amikor a normál rendszergazdai fiókok nem használhatók.

Érdemes biztosítani, hogy a vészhelyzeti hozzáférési fiókok hitelesítő adatai (például jelszó, tanúsítvány vagy intelligens kártya) biztonságos helyen vannak tárolva, amelyet csak azok ismernek, akik jogosultak azokat használni, kizárólag vészhelyzet esetén.

- [Vészhelyzeti hozzáférési fiókok kezelése az Azure AD-ben](../active-directory/roles/security-emergency-access.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="pa-5-automate-entitlement-management"></a>PA-5: a jogosultságok kezelésének automatizálása 

**Útmutató**: az Azure-alkalmazások konfigurálása az erőforrások kezeléséhez Azure Active Directory integrált. Az Azure AD-jogosultságok kezelési funkciói segítségével automatizálhatja a hozzáférési kérelmek munkafolyamatait, beleértve a hozzáférési hozzárendeléseket, az értékeléseket és a lejáratokat. A kettős vagy többfázisú jóváhagyás is támogatott.

- [Az Azure AD hozzáférési felülvizsgálatai](../active-directory/governance/access-reviews-overview.md)

- [Mi az Azure AD-jogosultságok kezelése?](../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Emelt szintű hozzáférésű munkaállomások használata

**Útmutató**: A biztonságos, elkülönített munkaállomások kritikus fontosságúak az olyan bizalmas szerepkörök biztonsága szempontjából, mint a rendszergazdák, a fejlesztők vagy a kritikus fontosságú szolgáltatások üzemeltetői. A biztonságos felhasználói munkaállomások és/vagy az Azure Bastion használata az alkalmazás konfigurálásával kapcsolatos adminisztratív feladatokhoz. Az Azure Active Directory, a Microsoft Defender Advanced Threat Protection (ATP) és/vagy a Microsoft Intune használatával biztonságos és felügyelt felhasználói munkaállomásokat helyezhet üzembe a rendszergazdai tevékenységekhez. A védett munkaállomások központi kezelésével kikényszeríthető a biztonságos konfiguráció, beleértve az erős hitelesítést, a szoftveres és hardveres alapkonfigurációikat, valamint a korlátozott logikai és hálózati hozzáférést.

- [Az emelt szintű hozzáférésű munkaállomások ismertetése](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Emelt szintű hozzáférésű munkaállomás üzembe helyezése](/security/compass/privileged-access-deployment)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: A Just Enough Administration (legkisebb jogosultsági alapelv) követése 

**Útmutató**: az Azure-alkalmazás konfigurációja integrálva van az Azure szerepköralapú hozzáférés-vezérléssel (RBAC) az erőforrások kezeléséhez. Az Azure RBAC lehetővé teszi, hogy szerepkörök hozzárendelésével felügyelje az Azure-erőforrások hozzáférését. Ezeket a szerepköröket hozzárendelheti a felhasználókhoz, a csoportok egyszerű szolgáltatásaihoz és a felügyelt identitásokhoz. Az Azure-alkalmazások konfigurációjának előre definiált beépített szerepkörei vannak, és ezeket a szerepköröket az Azure CLI-vel, a Azure PowerShell-vel vagy a Azure Portal eszközzel is leltározott vagy lekérdezheti. Az erőforrásokhoz az Azure RBAC-n keresztül hozzárendelt jogosultságokat mindig arra kell korlátozni, amit a szerepkörök megkövetelnek. Ez a megközelítés megfelel az Azure AD Privileged Identity Management (PIM) igény szerinti (JIT) módszerének, és a jogosultságok rendszeres felülvizsgálatával jár.

Az Azure az Azure AD és a OAuth használatával engedélyezi az alkalmazások konfigurációs adataihoz való hozzáférés engedélyezését az alábbi Azure beépített szerepkörökkel:
- Alkalmazás-konfigurációs adat tulajdonosa: ezt a szerepkört használva olvasási/írási/törlési hozzáférést biztosíthat az alkalmazás konfigurációs adatához. Ez nem biztosít hozzáférést az alkalmazás konfigurációs erőforrásához.
- Alkalmazás-konfigurációs Adatolvasó: ezt a szerepkört használva olvasási hozzáférést adhat az alkalmazás konfigurációs adatához. Ez nem biztosít hozzáférést az alkalmazás konfigurációs erőforrásához.

A beépített szerepkörök használatával lefoglalhatja az engedélyt, és szükség esetén csak egyéni szerepköröket hozhat létre. 

Az alkalmazás konfigurációja támogatja több alkalmazás konfigurációjának egyetlen alkalmazás-konfigurációs erőforrásban való tárolását. Az alkalmazások közötti hozzáférés korlátozásához hozzon létre egy alkalmazás-konfigurációs erőforrást, és állítsa be ennek megfelelően az Azure RBAC-t.

- [Mi az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../role-based-access-control/overview.md)

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

- [Az Azure AD identitás- és hozzáférési felülvizsgálatainak használata](../active-directory/governance/access-reviews-overview.md)

- [Hozzáférés engedélyezése az Azure app Configuration szolgáltatáshoz az Azure AD használatával](concept-enable-rbac.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: a Microsoft támogatási szolgálatának jóváhagyási folyamatának kiválasztása  

**Útmutató**: szervezeti jóváhagyási folyamat megvalósítása olyan támogatási forgatókönyvek esetén, ahol a Microsoft számára szükség lehet az alkalmazás konfigurációs adataihoz való hozzáférésre. Ügyfélszéf jelenleg nem érhető el az alkalmazások konfigurálását támogató forgatókönyvekhez.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1: bizalmas adatok felderítése, osztályozása és címkézése

**Útmutató**: bizalmas adatok felderítése, osztályozása és címkézése, hogy megtervezze a megfelelő vezérlőket, hogy a bizalmas adatok tárolása, feldolgozása és továbbítása biztonságos legyen a szervezet technológiai rendszereinek megfelelően. Az alkalmazás konfigurációs erőforrásai esetében a bizalmas adatok címkézése a címkézési formában is támogatott, de a bennük található konfigurációs értékekhez nem. Ha egy alkalmazás olvasási vagy írási/olvasási hozzáféréssel rendelkezik egy konfigurációs tárolóhoz, teljes hozzáférése van a tárolóban található bármelyik konfigurációhoz.

- [Bizalmas információk címkézése az Azure Information Protection használatával](/azure/information-protection/what-is-information-protection)

- [Az adatbesorolások címkézése az Azure-ban](/azure/cloud-adoption-framework/govern/policy-compliance/data-classification#tagging-data-classification-in-azure)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="dp-2-protect-sensitive-data"></a>DP-2: A bizalmas adatok védelme

**Útmutató**: a Microsoft által felügyelt mögöttes platformhoz a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és védelmet nyújt az ügyfelek adatvesztése és a kitettség ellen. Ahhoz, hogy az ügyféladatok az Azure-on belül biztonságban maradjanak, a Microsoft implementált néhány alapértelmezett adatvédelmi vezérlőt és képességet. Győződjön meg arról, hogy rendszeresen elforgatja a hozzáférési kulcsokat az alkalmazás konfigurációs erőforrásaihoz. A kapcsolódási karakterláncokból származó hitelesítő adatokat Azure Key Vault tárolhatja, és a kódnak hitelesítenie kell Key Vault a lekéréséhez. A hozzáférési kulcsok írási-olvasási vagy csak olvasási hozzáférést biztosíthatnak egy alkalmazáshoz. Győződjön meg arról, hogy a megfelelő típusú hozzáférési kulcs van kiállítva a jogosulatlan hozzáférés megakadályozása érdekében. A biztonság érdekében használja az Azure AD felügyelt identitások szolgáltatását. Ehhez csak az szükséges, hogy a konfigurációs végpont URL-címe hozzáférjen a konfigurációs értékekhez.

Hozzáférés korlátozása az Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC):

- Különítse el a bizalmas adatokat a saját alkalmazás-konfigurációs erőforrásában, majd a RBAC szabályzatok kiosztása ennek megfelelően, hogy csak a jogosult hozzáférés legyen engedélyezve 

- Hálózati hozzáférés-vezérlés használata

- Az Azure-szolgáltatások (például az SQL és más adatbázisok titkosítása) specifikus vezérlői, valamint a konzisztens hozzáférés-vezérlés biztosítása érdekében a hozzáférés-vezérlés összes típusát össze kell hangolni a vállalati szegmentálási stratégiával.

- A vállalati szegmentálási stratégiát a bizalmas vagy üzleti szempontból kritikus fontosságú adatok és rendszerek helyének tudatában kell kialakítani.

További információkat az alábbi hivatkozásokon találhat:

- [Hozzáférés engedélyezése az Azure-alkalmazás konfigurációjához Azure Active Directory használatával](concept-enable-rbac.md)

- [Az alkalmazás konfigurációjának adattitkosítása](faq.yml#does-app-configuration-encrypt-my-data)

- [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md) 

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Megosztott

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Bizalmas információk átvitel közbeni titkosítása

**Útmutató**: a hozzáférés-vezérlés kiegészítéseként a forgalomban lévő "sávon kívüli" támadások ellen védeni kell az adatátvitelt. Ezzel biztosíthatja, hogy a támadók ne tudják egyszerűen beolvasni vagy módosítani az adatforrást.

Az Azure-alkalmazás konfigurációja TLS-titkosítást használ az összes HTTP-kérelemhez. Az Azure-infrastruktúra további hálózati szintű titkosítást biztosít az Azure-adatközpontok közötti összes kéréshez. Győződjön meg arról, hogy a HTTP-forgalom, amelyet az alkalmazás-konfiguráció erőforrásaihoz csatlakozó ügyfelek a TLS v 1.2-es vagy újabb verzió egyeztetéséhez használhatnak.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Inaktív bizalmas adatok titkosítása

**Útmutató**: a hozzáférés-vezérlés kiegészítéseként a REST-adatok védelme a "sávon kívüli" támadásoktól (például a mögöttes tárolóhoz való hozzáférés) a titkosítás használatával történik. Ezzel biztosíthatja, hogy a támadók ne tudják egyszerűen beolvasni vagy módosítani az adatforrást.

Az Azure alapértelmezés szerint biztosítja a REST-titkosítást. A szigorúan bizalmas adatok esetében lehetősége van arra, hogy az összes Azure-erőforráson további titkosítást hajtsanak végre, ahol elérhetők. Az Azure alapértelmezés szerint kezeli a titkosítási kulcsokat, az Azure azonban lehetőséget biztosít a saját kulcsok (az ügyfél által felügyelt kulcsok) kezelésére az Azure-alkalmazások konfigurálásához.

- [Az Azure-alkalmazások konfigurációjában az ügyfelek által felügyelt kulcsok használatával titkosíthatja az adatait](concept-customer-managed-keys.md)

- [Inaktív adatok Azure-ban való titkosításának ismertetése](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Titkosítási modell és kulcskezelő tábla](../security/fundamentals/encryption-atrest.md#azure-resource-providers-encryption-model-support)

- [Inaktív adatok az Azure-ban – Rest kettős titkosítás](../security/fundamentals/double-encryption.md#data-at-rest)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="asset-management"></a>Asset Management (Eszközkezelés)

*További információ: [Azure Security Benchmark: Összetevők kezelése](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Gondoskodás arról, hogy a biztonsági csapat átlássa az adategységek kockázatait

**Útmutató**: Győződjön meg arról, hogy a biztonsági csapatok biztonsági olvasó engedélyekkel rendelkeznek az Azure-bérlőben és -előfizetésekben, hogy monitorozni tudják a biztonsági kockázatokat az Azure Security Centerrel. 

A biztonsági kockázatok monitorozása a biztonsági csapat felelősségi köreinek struktúrájától függően egy központi biztonsági csapat vagy egy helyi csapat felelőssége lehet. A biztonsági megállapításokat és kockázatokat azonban mindig központilag kell összesíteni egy szervezeten belül. 

A biztonsági olvasó engedélyek széles körben alkalmazhatók egy teljes bérlőre (gyökérszintű felügyeleti csoport), vagy a hatókör alkalmazható adott felügyeleti csoportokra vagy előfizetésekre. 

Megjegyzés: A számítási feladatok és a szolgáltatások átláthatóvá tételéhez további engedélyek lehetnek szükségesek. 

- [A biztonsági olvasó szerepkör áttekintése](../role-based-access-control/built-in-roles.md#security-reader)

- [Az Azure-beli felügyeleti csoportok áttekintése](../governance/management-groups/overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Az összetevőleltár és a metaadatok biztonsági csapat általi elérhetőségének biztosítása

**Útmutató**: gondoskodjon arról, hogy a biztonsági csapatok hozzáférjenek az Azure-ban folyamatosan frissített leltárhoz, például az Azure-alkalmazások konfigurációjában. A biztonsági csapatoknak gyakran van szükségük erre a leltárra a vállalat új kockázatokkal szembeni kitettségének felméréséhez, és a folyamatos biztonsági fejlesztéséket szolgáló információként. Hozzon létre egy Azure Active Directory csoportot, amely tartalmazza a szervezete jogosult biztonsági csapatát, és olvasási hozzáférést rendel hozzájuk az összes Azure-alkalmazás konfigurációs erőforrásához, ezt az előfizetésében egyetlen magas szintű szerepkör-hozzárendeléssel lehet egyszerűsíteni.

A Azure Security Center Inventory szolgáltatás és az Azure Resource Graph az előfizetések összes erőforrását lekérdezheti és felderítheti, beleértve az Azure-szolgáltatásokat, az alkalmazásokat és a hálózati erőforrásokat is.

Címkéket alkalmazhat az Azure-erőforrások,-erőforráscsoportok és-előfizetések számára, hogy logikailag szervezze őket egy besorolásba. Minden címke egy nevet és egy érték párokat tartalmaz. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra.

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

- [Azure Security Center Asset Inventory Management](../security-center/asset-inventory.md)

- [További információ az eszközök címkézéséről: erőforrás-elnevezési és címkézési döntési útmutató](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: az Azure app Configuration a Azure Policy használatával támogatja Azure Resource Manager-alapú telepítéseket és a konfiguráció kényszerítését. Az Azure Policy használatával ellenőrizheti és korlátozhatja a felhasználók által a környezetben kiépíthető szolgáltatások körét. Az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetésükön belüli erőforrásokat. Az Azure Monitort is használhatja olyan szabályok létrehozásához, amelyek riasztást aktiválnak nem jóváhagyott szolgáltatás észlelésekor.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Az objektuméletciklus-kezelés biztonságának garantálása

**Útmutató**: Hozzon létre a potenciálisan nagy hatású módosítások objektuméletciklus-kezelési folyamataira vonatkozó biztonsági szabályzatokat, vagy frissítse a meglévőket. Ezek a módosítások magukban foglalják a módosításokat, de nem korlátozódnak a következőkre: identitás-szolgáltatók és hozzáférés, adatok érzékenysége, hálózati konfiguráció és rendszergazdai jogosultságok hozzárendelése.

Távolítsa el az Azure-erőforrásokat, ha már nincs rájuk szükség. Győződjön meg arról, hogy a rendszergazdák a hozzáférési kulcsokat rendszeresen elforgatva biztosítják, hogy csak a hitelesített felhasználók férhessenek hozzá a konfigurációs erőforráshoz.

- [Az alkalmazás konfigurálásához használt titkosítási kulcsok elforgatása](concept-customer-managed-keys.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek az Azure Resources Managerrel való interakcióra az "Microsoft Azure felügyelet" alkalmazás "hozzáférés letiltása" beállításával.

- [Feltételes hozzáférés konfigurálása az Azure Resources Manager elérésének blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="logging-and-threat-detection"></a>Naplózás és fenyegetésészlelés

*További információ: [Azure Security Benchmark: Naplózás és fenyegetésészlelés](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Fenyegetések észlelésének engedélyezése az Azure-beli identitás- és hozzáférés-kezeléshez

**Útmutató**: az alkalmazás konfigurálása a Azure Active Directory (Azure ad) szolgáltatással integrálható. Ez a következő felhasználói naplókat tartalmazza, amelyeket megtekintheti az Azure AD jelentéskészítési szolgáltatásban, vagy integrálható a Azure Monitor, az Azure Sentinel vagy más SIEM/monitoring eszközökkel a kifinomultabb monitorozási és elemzési felhasználási esetekben:
- Bejelentkezések – a bejelentkezési jelentés a felügyelt alkalmazások használatával és a felhasználók bejelentkezési tevékenységeivel kapcsolatos információkat biztosít.
- Auditnaplók – az Azure AD-n belül különböző szolgáltatások által végrehajtott összes módosításra vonatkozó nyomkövetési naplókat biztosít. A naplók rögzítik például az erőforrások módosításait az Azure AD-n belül, például a felhasználók, alkalmazások, csoportok, szerepkörök és szabályzatok hozzáadását vagy eltávolítását.
- Kockázatos bejelentkezések – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa.
- Kockázatosként megjelölt felhasználók – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága.

Az Azure Security Center riasztásokat is küldhet bizonyos gyanús tevékenységek észlelésekor. Ezek közé tartozik például a sikertelen hitelesítési kísérletek kiugróan magas száma vagy az előfizetésben talált elavult fiókok. Az alapszintű biztonsági higiénia monitorozása mellett a Azure Security Center veszélyforrások elleni védelmi modul is gyűjthet részletesebb biztonsági riasztásokat az Azure szolgáltatási rétegeiről. Ezzel a képességgel megtekintheti az egyes erőforrásokon belüli fiókok rendellenességeit.

Az alkalmazás-konfigurációs erőforráshoz való hozzáférés másik módja hozzáférési kulcsok használata. Ezeket rendszeresen el kell forgatni annak biztosítása érdekében, hogy a jogosulatlan ügynökök ne férhessenek hozzá a konfigurációs erőforráshoz. Az elforgatása közvetlenül a portálon, a "hozzáférési kulcsok" területen végezhető el.

- [Az Azure-alkalmazások konfigurációjának engedélyezése más Azure AD-beli védett erőforrások eléréséhez felügyelt identitás használatával](overview-managed-identity.md)

- [Felügyelt identitások használata az Azure app Configuration használatával](howto-integrate-azure-managed-service-identity.md)

- [Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directoryban](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Az Azure Identity Protection engedélyezése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Azure app Configuration szolgáltatáshoz való hozzáférés engedélyezése az Azure AD-vel](concept-enable-rbac.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Naplózás engedélyezése Azure-beli hálózati tevékenységekhez

**Útmutató**: az Azure-alkalmazás konfigurációja nem helyez üzembe semmilyen erőforrást közvetlenül egy virtuális hálózatban. Az alkalmazás konfigurációja azonban lehetővé teszi privát végpontok használatát az Azure-alkalmazások konfigurációjának biztonságos csatlakoztatásához egy virtuális hálózatból. Az Azure app Configuration emellett nem állít elő vagy dolgoz fel olyan DNS-lekérdezési naplókat, amelyeket engedélyezni kell.

Engedélyezze a naplózást a konfigurált alkalmazás-konfiguráció saját végpontján a rögzítéshez:
- A magánhálózati végpont által feldolgozott adatértékek (be/ki)
- A privát kapcsolati szolgáltatás által feldolgozott adatértékek (be/ki)
- NAT-port elérhetősége

További információkat az alábbi hivatkozásokon találhat:

- [Azure Private link-figyelés](../private-link/private-link-overview.md#logging-and-monitoring)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Naplózás engedélyezése Azure-erőforrásokhoz

**Útmutató**: az automatikusan elérhető tevékenység-naplók tartalmazzák az alkalmazás konfigurációs erőforrásainak összes írási műveletét (Put, post, DELETE), kivéve az olvasási MŰVELETEKET (Get). A Tevékenységnaplók segítségével hibát kereshet a hibaelhárítás során, vagy megfigyelheti, hogy a szervezet felhasználója hogyan módosított egy erőforrást. Az alkalmazás konfigurálásakor a tevékenységi naplók csak a vezérlési síkon érhetők el, és a Azure Resource Manager (ARM) felületen vannak felkészítve. Az alkalmazás-konfigurációhoz tartozó ügyfél-adatsíkok naplózása jelenleg nem támogatott. Az Azure-erőforrás-naplók is konfigurálhatók.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [A naplózás és a különböző naplózási típusok megismerése az Azure-ban](../azure-monitor/essentials/platform-logs-overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Biztonsági naplók kezelésének és elemzésének központosítása

**Útmutató**: a korreláció engedélyezéséhez központosítsa a naplózási tárolást és az elemzést. Minden naplózási forrás esetében ellenőrizze, hogy rendelkezik-e az adatok tulajdonosával, a hozzáférési útmutatással, a tárolási hellyel, az adatok feldolgozásához és eléréséhez, valamint az adatmegőrzési követelményekhez használt eszközökhöz.

Győződjön meg arról, hogy integrálja az Azure-tevékenységek naplóit a központi naplózásba. Naplók betöltése Azure Monitor használatával a végponti eszközök, a hálózati erőforrások és más biztonsági rendszerek által generált biztonsági adatokat összesítve. Azure Monitor a Log Analytics-munkaterületek használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat hosszú távú és archiválási tároláshoz.

Emellett az Azure Sentinel vagy egy harmadik féltől származó SIEM-nek is engedélyezi az adatok bevezetését. Számos szervezet úgy dönt, hogy a gyakran használt és az Azure-tárolót használja a ritkábban használt adatokhoz az Azure Sentinel használatával.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Tárolt naplók megőrzésének konfigurálása

**Útmutató**: gondoskodjon arról, hogy minden olyan Storage-fiók vagy log Analytics-munkaterület, amely az alkalmazás-konfigurációs naplók tárolásához használatos, a szervezet megfelelőségi szabályainak megfelelően állítsa be a napló megőrzési időtartamát. Az Azure Storage, a Data Lake vagy a Log Analytics munkaterület-fiókok használata hosszú távú és archiválási tároláshoz.

Azure Monitor az Log Analytics munkaterület megőrzési időszakát a szervezet megfelelőségi előírásai szerint állíthatja be.

- [Log Analytics munkaterület megőrzési időtartamának konfigurálása](../azure-monitor/logs/manage-cost-storage.md)

- [Erőforrás-naplók tárolása Azure Storage-fiókban](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-controls-v2-incident-response.md).*

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

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Biztonságos konfigurációk kialakítása Azure-szolgáltatásokhoz 

**Útmutató**: az Azure-alkalmazások konfigurálása az Azure-erőforrások konfigurációjának naplózásához és érvényesítéséhez Azure Security Center elérhető, a szolgáltatásra vonatkozó házirendeket támogatja. Ezt Azure Security Center vagy Azure Policy kezdeményezésekben lehet konfigurálni.
- Az alkalmazás konfigurációjának az ügyfél által felügyelt kulcsot kell használnia: az ügyfél által felügyelt kulcsok biztosítják a fokozott adatvédelmet, mivel lehetővé teszi a titkosítási kulcsok kezelését. Ez gyakran szükséges a megfelelőségi követelmények teljesítéséhez.
- Az alkalmazás konfigurációjának privát hivatkozást kell használnia: a magánhálózati végponti kapcsolatok lehetővé teszik a virtuális hálózat ügyfelei számára, hogy privát kapcsolaton keresztül biztonságosan hozzáférjenek az Azure-alkalmazások konfigurációjához.

Az Azure tervrajzai segítségével automatizálhatja a szolgáltatások és az alkalmazások környezetének üzembe helyezését és konfigurálását, beleértve az Azure Resources Manager-sablonokat, az Azure RBAC-vezérlőket és a házirendeket egyetlen terv definíciójában.

- [További információ az alkalmazás konfigurációs házirendjeiről](../governance/policy/samples/built-in-policies.md#app-configuration)

- [A biztonsági szabályzatok használata a Azure Security Centerban](../security-center/tutorial-security-policy.md)

- [A Guardrails megvalósításának illusztrációja a vállalati léptékű kirakodási zónában](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Oktatóanyag: szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints](../governance/blueprints/overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Biztonságos konfigurációk fenntartása Azure-szolgáltatásokhoz

**Útmutató**: a Azure Security Center használata az alapkonfiguráció figyelésére és a Azure Policy használatával történő betartatására. Az alkalmazások konfigurációjának Azure Policy a következőket tartalmazza: 
- Az alkalmazás konfigurációjának az ügyfél által felügyelt kulcsot kell használnia: az ügyfél által felügyelt kulcsok biztosítják a fokozott adatvédelmet, mivel lehetővé teszi a titkosítási kulcsok kezelését. Ez gyakran szükséges a megfelelőségi követelmények teljesítéséhez.
- Az alkalmazás konfigurációjának privát hivatkozást kell használnia: a magánhálózati végponti kapcsolatok lehetővé teszik a virtuális hálózat ügyfelei számára, hogy privát kapcsolaton keresztül biztonságosan hozzáférjenek az Azure-alkalmazások konfigurációjához.

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md) 

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../governance/policy/tutorials/create-and-manage.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Rendszeres támadásszimulációk végrehajtása

**Útmutató**: Szükség esetén végezzen behatolási teszteket vagy riasztási gyakorlatokat az Azure-erőforrásokon, hogy biztosítva legyen az összes kritikus biztonsági találat megoldása.
A Microsoft-felhő behatolástesztelési beavatkozási szabályai szerint eljárva biztosíthatja, hogy a behatolási tesztek nem sértik a Microsoft szabályzatait. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét.

- [Behatolási tesztek az Azure-ban](../security/fundamentals/pen-testing.md)

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="backup-and-recovery"></a>Biztonsági másolat és helyreállítás

*További információ: [Azure Security Benchmark: Biztonsági mentés és helyreállítás](../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: A kulcsok elvesztésével járó kockázat csökkentése

**Útmutató**: Ellenőrizze, hogy vannak-e olyan intézkedések, amelyek megakadályozzák a kulcsok elvesztését és helyreállítását. Engedélyezze a helyreállítható törlést és a végleges törléssel szembeni védelmet az Azure Key Vaultban, hogy megvédje a kulcsokat a véletlen vagy rosszindulatú törléssel szemben.

- [Helyreállítható törlés és a végleges törléssel szembeni védelem engedélyezése kulcstartóban](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="governance-and-strategy"></a>Irányítás és stratégia

*További információ: [Azure Security Benchmark: Irányítás és stratégia](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Eszközkezelési és adatvédelmi stratégia 

**Útmutató**: Mindig dokumentáljon és tegyen közzé a rendszerek és adatok folyamatos monitorozására és védelmére vonatkozó egyértelmű stratégiát. Állítsa fel az üzletileg kritikus adatok és rendszerek felmérésének, védelmének és monitorozásának fontossági sorrendjét. 

Ennek a stratégiának magában kell foglalnia a dokumentált útmutatót, és az alábbi elemek szabványait: 

-   Az üzleti kockázattal összhangban lévő adatbesorolási szabványok

-   A kockázatok és az eszközleltár biztonsági szervezet általi láthatósága 

-   A használandó Azure-szolgáltatások biztonsági szervezet általi jóváhagyása 

-   Az biztonsága azok teljes életciklusában

-   A vállalati adabesorolással összhangban szükséges hozzáférés-vezérlési stratégia

-   Natív Azure-beli és külső adatvédelmi képességek használata

-   Átvitel közbeni és inaktív adattitkosításra vonatkozó követelmények

-   A megfelelő titkosítási szabványok

További információkat az alábbi hivatkozásokon találhat:
- [Az Azure Security architektúrára vonatkozó ajánlásai – Tárolás, adatok és titkosítás](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Az Azure Security alapjai – Azure-beli adatbiztonság, titkosítás és tárolás](../security/fundamentals/encryption-overview.md)

- [Felhőadaptálási keretrendszer – Az Azure-beli adatbiztonsághoz és titkosításhoz ajánlott eljárások](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark – Eszközkezelés](../security/benchmarks/security-controls-v2-asset-management.md)

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

**Útmutató**: Folyamatosan mérje és mérsékelje a kockázatokat, amelyeknek az egyes összetevők, valamint az a környezet van kitéve, amelyben üzemeltetve vannak. Kezelje kiemelten az olyan nagy értékű összetevőket és leginkább elérhetővé tett támadási felületeket, mint a közzétett alkalmazások, a hálózat be- és kilépési pontjai, a felhasználói és rendszergazdai végpontok stb.

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

További információkat az alábbi hivatkozásokon találhat:
- [Ajánlott Azure-biztonsági eljárások 11 – Architektúra. Egyetlen egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark – Hálózati biztonság](../security/benchmarks/security-controls-v2-network-security.md)

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

További információkat az alábbi hivatkozásokon találhat:

- [Azure Security Benchmark – Naplózás és fenyegetésészlelés](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark – Incidenskezelés](../security/benchmarks/security-controls-v2-incident-response.md)

- [Ajánlott Azure-biztonsági eljárások 4 – Folyamat. Incidensválasz-folyamat frissítése a felhőhöz](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure-adaptálási keretrendszer, útmutató naplózási és jelentéskészítési döntésekhez](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure-beli nagyvállalati szintű skálázás, felügyelet és monitorozás](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](../security/benchmarks/overview.md) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)