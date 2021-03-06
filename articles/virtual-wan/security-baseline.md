---
title: Virtuális WAN Azure biztonsági alapterve
description: A virtuális WAN biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e4bbc1565cec2c356e916f813a6a334648d82754
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101726733"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>Virtuális WAN Azure biztonsági alapterve

Ez a biztonsági alapterv az [Azure biztonsági teljesítményteszt 2,0-es verziójáról](../security/benchmarks/overview.md) Microsoft Azure virtuális WAN-ra vonatkozó útmutatást alkalmaz. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** és a virtuális WAN-ra vonatkozó kapcsolódó útmutatás szerint van csoportosítva. A virtuális WAN-ra nem alkalmazható **vezérlőelemek** ki lettek zárva.

Ha szeretné megtudni, hogy a virtuális WAN hogyan teljes mértékben leképezi az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes virtuális WAN biztonsági alapkonfiguráció-hozzárendelési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: a belső forgalom biztonságának megvalósítása

**Útmutató**: a Microsoft Azure Virtual WAN egyéni útválasztási funkciókat biztosít, és titkosítást biztosít a ExpressRoute-forgalmához. Az összes útválasztási kezelést a virtuális központ útválasztója biztosítja, amely a virtuális hálózatok közötti átvitelt is lehetővé teszi. A ExpressRoute-forgalom virtuális WAN-kapcsolaton keresztüli titkosítása titkosított átvitelt biztosít a helyszíni hálózatok és az Azure-beli virtuális hálózatok között a ExpressRoute-en keresztül, a nyilvános interneten keresztül vagy nyilvános IP-címek használata nélkül. 

- [ExpressRoute-forgalom titkosítása](virtual-wan-about.md#encryption)

- [Privát hivatkozás használata a virtuális WAN-ban](howto-private-link.md)

- [Egyéni útválasztási forgatókönyvek](scenario-any-to-any.md)

- [Egyéni útválasztási táblázat dokumentációja](how-to-virtual-hub-routing.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="ns-2-connect-private-networks-together"></a>NS-2: magánhálózati hálózatok összekapcsolása 

**Útmutató**: a Microsoft Azure ExpressRoute privát kapcsolatot biztosít az Azure Virtual WAN-ral. Mivel a ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át, a ExpressRoute nagyobb megbízhatóságot, gyorsabb sebességet és kisebb késést biztosít, mint a szokásos internetes kapcsolatok. A virtuális magánhálózat használatával a helyek közötti (S2S) VPN-en vagy a pont – hely (P2S) VPN-en keresztül is csatlakozhat az Azure-hoz.

- [ExpressRoute a virtuális WAN-ban](virtual-wan-expressroute-portal.md)

- [Helyek közötti VPN – áttekintés](virtual-wan-site-to-site-portal.md)

- [Pont – hely VPN – áttekintés](virtual-wan-point-to-site-portal.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: a külső hálózati támadásoktól származó alkalmazások és szolgáltatások elleni védelem

**Útmutató**: a virtuális WAN nem tesz elérhetővé olyan végpontokat külső hálózatokra, amelyeknek a hagyományos hálózati védelemmel kell rendelkezniük. A Virtual Network Protection Services használatával szabadon biztosíthatja a küllős virtuális hálózatok (virtuális hubhoz csatlakozó virtuális hálózatok) erőforrásainak védelmét. 

A Azure Firewall használatával védelmet biztosíthat az alkalmazások és szolgáltatások számára az internetről és más külső helyekről származó, potenciálisan rosszindulatú forgalom ellen. 

Az Azure által biztosított DDoS Protection az Azure-beli virtuális hálózatokon található támadások elleni védelem érdekében válassza ki az eszközöket. A Azure Security Center használatával azonosíthatja a hálózattal kapcsolatos erőforrásokkal kapcsolatos helytelen konfigurációkat.

- [Azure Firewall dokumentáció](../firewall/index.yml)

- [Azure DDoS Protection standard kezelése a Azure Portal használatával](../ddos-protection/manage-ddos-protection.md) 

- [Az Azure Security Centerre vonatkozó ajánlások](../security-center/recommendations-reference.md#recs-networking)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: a Virtual WAN egy Microsoft által felügyelt szolgáltatás. Nem nyújt natív behatolás-észlelési vagy Behatolás-megelőzési képességeket. Vannak azonban olyan biztonsági képességek, amelyeket a Azure Firewall a virtuális WAN számára biztosít a házirendek egységes vezérlésének lehetővé tételéhez. Létrehozhat egy Azure Firewall szabályzatot, és összekapcsolhatja a házirendet egy virtuális WAN-hubhoz, hogy a meglévő virtuális WAN-központ biztonságos virtuális központként működjön, és a szükséges Azure Firewall-erőforrások telepítve legyenek.

- [Azure Firewall konfigurálása virtuális WAN-központban](howto-firewall.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: a hálózati biztonsági szabályok egyszerűsítése

**Útmutató**: a hálózati biztonsági szabályok egyszerűsítése Virtual Network szolgáltatás-címkék használatával a hálózati biztonsági csoportok vagy Azure Firewall hálózati hozzáférés-vezérlésének definiálásához. A szolgáltatás címkéi a biztonsági szabályok létrehozásakor a megadott IP-címek helyett használhatók. A szolgáltatási címke nevének megadásával a szabály forrás vagy cél mezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

- [A szolgáltatási címkék megismerése és használata](../virtual-network/service-tags-overview.md)

- [Az alkalmazás biztonsági csoportjai megismerése és használata](../virtual-network/network-security-groups-overview.md#application-security-groups)

- [Azure Firewall dokumentáció](../firewall/index.yml)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: biztonságos tartománynév szolgáltatás (DNS)

**Útmutató**: a biztonságos DNS-képességek a Azure Firewall használatával érhetők el a virtuális WAN számára. Konfigurálja úgy a Azure Firewallt, hogy DNS-proxyként működjön, amely az ügyfél virtuális gépei által a DNS-kiszolgálóra irányuló DNS-kérések közötti közvetítővé válik. Az egyéni DNS-kiszolgálói konfigurációk esetében engedélyezze a DNS-proxyk számára a DNS-feloldási eltérések elkerülését, és engedélyezze a teljes tartománynevek szűrését a hálózati szabályokban. 

- [Azure Firewall dokumentáció](../firewall/index.yml)

- [Azure Firewall DNS-beállítások](../firewall/dns-settings.md)

- [Azure Firewall használata DNS-továbbítóként privát hivatkozással](https://github.com/adstuart/azure-privatelink-dns-azurefirewall)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="identity-management"></a>Identitáskezelés

*További információ: [Azure Security Benchmark: Identitáskezelés](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Az Azure Active Directory, mint központi identitáskezelő és hitelesítési rendszer szabványosítása

**Útmutató**: a Azure Active Directory (Azure ad) az Azure-szolgáltatások alapértelmezett identitás-és hozzáférés-kezelési szolgáltatása. a virtuális WAN-t is beleértve. Az Azure AD szabványosítása a szervezet identitás-és hozzáférés-kezelésének szabályozására a ben:

- Microsoft Cloud olyan erőforrásokat, mint például a Azure Portal, az Azure Storage, az Azure Virtual Machines (Linux és Windows), a Azure Key Vault, a szolgáltatásként szolgáló platform, valamint a szolgáltatott szoftver (SaaS) alkalmazások.
- A szervezet erőforrásai, például az Azure-beli alkalmazások vagy a vállalati hálózati erőforrások

Az Azure AD magas prioritást biztosít a szervezet Felhőbeli biztonsági gyakorlatában. Mérje fel az identitást és a biztonságot a Security Center biztonsági pontszám funkciójával, és mérje fel, hogy a konfiguráció milyen mértékben illeszkedik a Microsoft ajánlott eljárási javaslataihoz. Szükség esetén alkalmazza a Microsoft ajánlott eljárásait a biztonsági helyzetének fejlesztéséhez.

Az Azure AD a külső identitásokat is támogatja, amelyek lehetővé teszik a felhasználók számára, hogy Microsoft-fiók nélkül bejelentkezzenek alkalmazásaiba és erőforrásaiba külső identitásával. 

Tekintse át az Azure AD-t a hivatkozott hivatkozásokon a pont – hely VPN-forgatókönyvekben található információk alapján.

- [Azure Active Directory (AD) bérlő létrehozása a P2S OpenVPN protokoll kapcsolataihoz](openvpn-azure-ad-tenant-multi-app.md)

- [Azure Active Directory hitelesítés konfigurálása a felhasználói VPN-hez](virtual-wan-point-to-site-azure-ad.md)

- [Bérlői viszony az Azure Active Directoryban](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Erős hitelesítési vezérlők használata minden Azure Active Directory-alapú hozzáféréshez

**Útmutató**: jelenleg Azure Active Directory (Azure ad) hitelesítés a virtuális WAN pont – hely típusú VPN-kapcsolattal való integráción keresztül érhető el.

Azure Active Directory (Azure AD) az Azure-szolgáltatások alapértelmezett identitás-és hozzáférés-kezelési szolgáltatása. Az Azure AD a többtényezős hitelesítéssel és erős, jelszóval nem rendelkező módszerekkel támogatja az erős hitelesítési vezérlőket.

Az Azure AD a következőket javasolja az erős hitelesítési vezérlőkhöz:

- Többtényezős hitelesítés – az Azure AD többtényezős hitelesítésének engedélyezése, valamint a Azure Security Center az azonosítási és hozzáférés-kezelési javaslatok követése az ajánlott biztonsági eljárásokhoz. A többtényezős hitelesítés érvényesítése az összes, a felhasználók és a felhasználónkénti szinten a bejelentkezési feltételek és a kockázati tényezők alapján

- Jelszóval nem rendelkező hitelesítés – a rendszer három jelszavas hitelesítési lehetőséget kínál. Ezek közé tartoznak a vállalati Windows Hello, Microsoft Authenticator alkalmazás és a helyszíni hitelesítési módszerek, például az intelligens kártyák

Győződjön meg arról, hogy az erős hitelesítési módszer legmagasabb szintje a rendszergazda és a rendszerjogosultságú felhasználók számára van használatban, és ezt követően egy erős hitelesítési házirendet kell kivonnia más felhasználók számára.

- [Az MFA engedélyezése a P2S VPN-ben virtuális WAN-hoz](openvpn-azure-ad-mfa.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Azure-erőforrásokhoz való hozzáférés korlátozása feltételek alapján

**Útmutató**: engedélyezze Azure Active Directory (Azure ad) többtényezős hitelesítés használatát a VPN-felhasználók (pont – hely) számára az Azure ad-hitelesítés használatával. Többtényezős hitelesítés konfigurálása felhasználónkénti alapon, vagy többtényezős hitelesítés használata feltételes hozzáféréssel. A feltételes hozzáférés lehetővé teszi, hogy finomabban szabályozható legyen a második tényező előléptetése. Lehetővé teheti a többtényezős hitelesítés hozzárendelését csak VPN-re, és kizárhatja az Azure AD-bérlőhöz kötött más alkalmazásokat. 

Vegye figyelembe, hogy az Azure AD-hitelesítés csak az OpenVPN protokollt használó átjárók és a Windows rendszerű ügyfelek esetében érhető el.

- [Mi a feltételes hozzáférés?](../active-directory/conditional-access/overview.md)

- [Azure Active Directory hitelesítés konfigurálása a felhasználói VPN-hez](virtual-wan-point-to-site-azure-ad.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Hitelesítő adatok nem szándékos elérhetővé tételének kizárása

**Útmutató**: a virtuális WAN-helyek közötti VPN-kapcsolat előmegosztott kulcsokat (PSK) használ, amelyeket az ügyfél a Azure Key Vault felderített, hozott létre és felügyel. Hitelesítő adatok beolvasása a kódban a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

A GitHubhoz használhatja a natív titkoskód-szűrési funkciót a kódon belüli hitelesítő adatok vagy másféle titkos kódok felismerésére.

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [Titkos kódok szűrése a GitHubban](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="privileged-access"></a>Emelt szintű hozzáférés

*További információ: [Azure Security Benchmark: Emelt jogosultságú hozzáférés](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Az üzletileg kritikus rendszerek rendszergazdai elérésének korlátozása

**Útmutató**: az Azure Virtual WAN az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával elkülöníti az üzleti szempontból kritikus rendszerek hozzáférését azáltal, hogy korlátozza, hogy mely fiókok kapnak jogosultsági szintű hozzáférést az előfizetésekhez és a felügyeleti csoportokhoz.

Korlátozza továbbá az üzleti szempontból kritikus Active Directory-tartomány fontosságú rendszerekre telepített ügynökökkel való rendszergazdai hozzáféréssel rendelkező felügyeleti, identitási és biztonsági rendszerekhez való hozzáférést is. Azok a támadók, akik veszélyeztetik ezeket a felügyeleti és biztonsági rendszereket, azonnal weaponize azokat az üzleti szempontból kritikus fontosságú eszközök biztonsága érdekében.

A hozzáférés-vezérlés összes típusát a vállalati szegmentálási stratégiához kell igazítani, hogy biztosítsa a konzisztens hozzáférés-vezérlést.

- [Azure-összetevők és-hivatkozási modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Felügyeleti csoport elérése](../governance/management-groups/overview.md#management-group-access) 

- [Azure-előfizetés rendszergazdái](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Bizalmas információk átvitel közbeni titkosítása

**Útmutató**: pont – hely VPN, helyek közötti VPN és titkosított expressz útvonal használata a kapcsolati követelményekhez a virtuális WAN használatával. A VPN-titkosítás védi az adatokat a sávon kívüli támadásoktól (például a forgalom rögzítése), így biztosítva, hogy a támadók nem tudják olvasni vagy módosítani az adatokat. 

- [Pont-hely típusú VPN](virtual-wan-point-to-site-portal.md)

- [Helyek közötti VPN](virtual-wan-site-to-site-portal.md)

- [Titkosított ExpressRoute](vpn-over-expressroute.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

## <a name="asset-management"></a>Asset Management (Eszközkezelés)

*További információ: [Azure Security Benchmark: Összetevők kezelése](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Gondoskodás arról, hogy a biztonsági csapat átlássa az adategységek kockázatait

**Útmutató**: Győződjön meg arról, hogy a biztonsági csapatok biztonsági olvasó engedélyekkel rendelkeznek az Azure-bérlőben és -előfizetésekben, hogy monitorozni tudják a biztonsági kockázatokat az Azure Security Centerrel. 

A biztonsági kockázatok monitorozása a biztonsági csapat felelősségi köreinek struktúrájától függően egy központi biztonsági csapat vagy egy helyi csapat felelőssége lehet. Ezzel a biztonsági ismereteket és a kockázatokat mindig központilag összesíteni kell egy szervezeten belül. 

A biztonsági olvasó engedélyei széles körben alkalmazhatók egy teljes bérlőre (gyökérszintű felügyeleti csoportra), vagy a felügyeleti csoportokra vagy az adott előfizetésekre is. 

Megjegyzés: A számítási feladatok és a szolgáltatások átláthatóvá tételéhez további engedélyek lehetnek szükségesek. 

- [A biztonsági olvasó szerepkör áttekintése](../role-based-access-control/built-in-roles.md#security-reader)

- [Az Azure-beli felügyeleti csoportok áttekintése](../governance/management-groups/overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Az összetevőleltár és a metaadatok biztonsági csapat általi elérhetőségének biztosítása

**Útmutató**: címkék alkalmazása az Azure-erőforrásokra,-csoportokra és-előfizetésekre, amelyek logikailag rendszerezik őket egy besorolásban. Minden címke egy nevet és egy érték párokat tartalmaz. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra. Az Azure Virtual WAN olyan Azure Resource Manager-alapú erőforrás-telepítéseket is támogat, amelyekkel exportálhatja az eszközök sablonjait. 

- [Útmutató az erőforrások elnevezésével és címkézésével kapcsolatos döntésekhez](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

- [Azure Security Center Asset Inventory Management](../security-center/asset-inventory.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure monitor használatával hozzon létre szabályokat a riasztások elindításához, ha a rendszer nem jóváhagyott szolgáltatást észlel. A virtuális WAN számos hálózati, biztonsági és útválasztási funkciót egyesít, hogy egyetlen operatív felületet biztosítson. A virtuális WAN VPN-átjárók, a ExpressRoute-átjárók és a Azure Firewall Azure Monitoron keresztül elérhetők naplózással és metrikákkal. 
 

- [Virtuális WAN-naplók és-metrikák](logs-metrics.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek az Azure erőforrás-kezelővel való interakcióra az "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával.

- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="logging-and-threat-detection"></a>Naplózás és fenyegetésészlelés

*További információ: [Azure Security Benchmark: Naplózás és fenyegetésészlelés](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: az Azure-erőforrások fenyegetés-észlelésének engedélyezése

**Útmutató**: a virtuális WAN-t használó pont – hely VPN integrálva van Azure Active Directory (Azure ad) szolgáltatással. Az Azure AD a következő felhasználói naplókat tartalmazza, amelyeket megtekintheti az Azure AD Reporting szolgáltatásban, vagy integrálható a Azure Monitor, az Azure Sentinel, a SIEM vagy a monitoring eszközeivel összetettebb veszélyforrások figyelésére és elemzési használatára. Ezek a következők:

- Bejelentkezés – a bejelentkezési jelentés információt nyújt a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról.
- Auditnaplók – az Azure AD-n belül különböző szolgáltatások által végrehajtott összes módosításra vonatkozó nyomkövetési naplókat biztosít. A naplók példái közé tartoznak az Azure AD-ban található erőforrások változásai, például felhasználók, alkalmazások, csoportok, szerepkörök és házirendek hozzáadása vagy eltávolítása.
- Kockázatos bejelentkezés – A kockázatos bejelentkezés egy olyan bejelentkezési kísérlet jelzése, amelyet a felhasználói fiók jogos tulajdonosaként elvégeztek.
- Kockázatosként megjelölt felhasználók – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága.

A Azure Security Center használatával riasztásokat hozhat létre bizonyos gyanús tevékenységekhez, például a sikertelen hitelesítési kísérletek túlzott számához, beleértve az előfizetésben elavult fiókokat is. Az alapszintű biztonsági higiénia figyelése mellett a veszélyforrások elleni védelmi modul használatával Security Center több részletes biztonsági riasztást gyűjthet az egyes Azure számítási erőforrásokból (Virtual Machines, containers, app Service), az adatforrásokból (SQL DB és Storage) és az Azure szolgáltatási rétegeiből. Ezzel a funkcióval rálátást nyerhet az egyes erőforrásokban lévő fiók-rendellenességekre.

- [Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directoryban](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Az Azure Identity Protection engedélyezése](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Firewall konfigurálása virtuális WAN-központban](howto-firewall.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Fenyegetések észlelésének engedélyezése az Azure-beli identitás- és hozzáférés-kezeléshez

**Útmutató**: a virtuális WAN-t használó pont – hely VPN integrálva van Azure Active Directory (Azure ad) szolgáltatással. Az Azure AD a következő felhasználói naplókat tartalmazza, amelyeket megtekintheti az Azure AD Reporting szolgáltatásban, vagy integrálható a Azure Monitor, az Azure Sentinel, a SIEM vagy a monitoring eszközeivel összetettebb veszélyforrások figyelésére és elemzési használatára. Ezek a következők:

- Bejelentkezés – a bejelentkezési jelentés információt nyújt a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról.
- Auditnaplók – az Azure AD-n belül különböző szolgáltatások által végrehajtott összes módosításra vonatkozó nyomkövetési naplókat biztosít. A naplók példái közé tartoznak az Azure AD-ban található erőforrások változásai, például felhasználók, alkalmazások, csoportok, szerepkörök és házirendek hozzáadása vagy eltávolítása.
- Kockázatos bejelentkezés – A kockázatos bejelentkezés egy olyan bejelentkezési kísérlet jelzése, amelyet a felhasználói fiók jogos tulajdonosaként elvégeztek.
- Kockázatosként megjelölt felhasználók – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága.

A Azure Security Center használatával riasztásokat hozhat létre bizonyos gyanús tevékenységekhez, például a sikertelen hitelesítési kísérletek túlzott számához, beleértve az előfizetésben elavult fiókokat is. Az alapszintű biztonsági higiénia figyelése mellett a veszélyforrások elleni védelmi modul használatával Security Center több részletes biztonsági riasztást gyűjthet az egyes Azure számítási erőforrásokból (Virtual Machines, containers, app Service), az adatforrásokból (SQL DB és Storage) és az Azure szolgáltatási rétegeiből. Ezzel a funkcióval rálátást nyerhet az egyes erőforrásokban lévő fiók-rendellenességekre.

- [Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directoryban](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Az Azure Identity Protection engedélyezése](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Firewall konfigurálása virtuális WAN-központban](howto-firewall.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Naplózás engedélyezése Azure-beli hálózati tevékenységekhez

**Útmutató**: az Azure Virtual WAN figyelése Azure monitor használatával. A virtuális WAN számos hálózati, biztonsági és útválasztási funkciót egyesít, hogy egyetlen operatív felületet biztosítson. A virtuális WAN VPN-átjárók, a ExpressRoute-átjárók és a Azure Firewall Azure Monitoron keresztül elérhetők naplózással és metrikákkal. A rendszer alapértelmezés szerint gyűjti a tevékenység naplójának bejegyzéseit, és megtekintheti őket a Azure Portalban. Az Azure-tevékenység naplóinak (korábbi nevén operatív naplók és naplók) használatával megtekintheti az Azure-előfizetéséhez benyújtott összes műveletet.

Számos diagnosztikai napló is elérhető a virtuális WAN számára, és a Azure Portal használatával konfigurálható a virtuális WAN-erőforráshoz.  Dönthet úgy is, hogy elküldi Log Analytics, streamet egy Event hub-ba, vagy egyszerűen archiválja a Storage-fiókba. 
 

- [Virtuális WAN-naplók és-metrikák](logs-metrics.md)

- [Az Azure Firewall naplói és metrikái](../firewall/logs-and-metrics.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Naplózás engedélyezése Azure-erőforrásokhoz

**Útmutató**: az Azure-beli tevékenység naplófájljai automatikusan engedélyezve vannak az Azure-beli virtuális WAN-erőforrások összes írási műveletét (Put, post, DELETE), kivéve az OLVASÁSI (Get) műveleteket. A Tevékenységnaplók segítségével hibát kereshet a hibaelhárítás során, vagy megfigyelheti, hogy a szervezet felhasználója hogyan módosított egy erőforrást.

Engedélyezze az Azure-beli erőforrás-naplókat a virtuális WAN számára. Azure Security Center és Azure Policy használatával engedélyezheti az erőforrás-naplókat és naplózhatja az adatgyűjtést. Ezek a naplók kritikus fontosságúak lehetnek a biztonsági incidensek kivizsgálásához és a kriminalisztikai gyakorlatok végrehajtásához.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [A naplózás és a különböző naplózási típusok megismerése az Azure-ban](../azure-monitor/essentials/platform-logs-overview.md) 

- [Az Azure Security Center adatgyűjtés ismertetése](../security-center/security-center-enable-data-collection.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Biztonsági naplók kezelésének és elemzésének központosítása

**Útmutató**: a virtuális WAN biztonsági naplózásának engedélyezése Azure monitor használatával. A virtuális WAN számos hálózati, biztonsági és útválasztási funkciót egyesít, hogy egyetlen operatív felületet biztosítson. A virtuális WAN VPN-átjárók, a ExpressRoute-átjárók és a Azure Firewall Azure Monitoron keresztül elérhetők naplózással és metrikákkal. A rendszer alapértelmezés szerint gyűjti a tevékenység naplójának bejegyzéseit, és megtekintheti őket a Azure Portalban. Az Azure-tevékenység naplóinak (korábbi nevén operatív naplók és naplók) használatával megtekintheti az Azure-előfizetéséhez benyújtott összes műveletet. 

Számos diagnosztikai napló is elérhető a virtuális WAN számára, és a Azure Portal használatával konfigurálható a virtuális WAN-erőforráshoz. Küldés Log Analytics, Stream egy Event hub-ba, vagy egyszerűen archiválható egy Storage-fiókba. Emellett az Azure Sentinel vagy egy harmadik féltől származó biztonsági információk és rendezvényszervezés megoldás számára is lehetővé teszi az adatok bevezetését. 
 

- [Virtuális WAN-naplók és-metrikák](logs-metrics.md)

- [Az Azure Firewall naplói és metrikái](../firewall/logs-and-metrics.md)

Az Azure Virtual WAN biztonsági szolgáltatásait Azure Firewallon keresztül biztosítjuk. 

- [Azure Firewall dokumentáció](../firewall/overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Tárolt naplók megőrzésének konfigurálása

**Útmutató**: a naplók megőrzésének beállítása a megfelelőségi, a szabályozási és az üzleti igényeknek megfelelően. Azure Monitor az Log Analytics munkaterület megőrzési időszakát a szervezet megfelelőségi előírásai szerint állíthatja be. Az Azure Storage, a Data Lake vagy a Log Analytics munkaterület-fiókok használata hosszú távú és archiválási tároláshoz.

- [Az adatmegőrzési időszak módosítása Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Adatmegőrzési szabályzat konfigurálása az Azure Storage-fiók naplóihoz](../storage/common/manage-storage-analytics-logs.md#configure-logging)

- [Azure Security Center riasztások és javaslatok exportálása](../security-center/continuous-export.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

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

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Észlelés és elemzés – incidensek létrehozása kiváló minőségű riasztások alapján

**Útmutató**: Győződjön meg arról, hogy rendelkezik a kiváló minőségű riasztások létrehozásához és a riasztások minőségének felméréséhez szükséges folyamattal. Ezzel tanulhat a korábbi incidensekből, és megadhatja a riasztások fontossági sorrendjét az elemzők számára, akik így nem pazarolnak időt a vakriasztásokra. 

Kiváló minőségű riasztások a korábbi incidensek tapasztalatai és ellenőrzött közösségi források alapján, valamint a riasztások létrehozásához és tisztításához tervezett eszközökkel hozhatók létre a különböző jelforrások egyesítésével és összehasonlításával. 

Az Azure Security Center számos Azure-objektum esetében kiváló minőségű riasztásokat biztosít. Az ASC adatösszekötővel streamelheti a riasztásokat az Azure Sentinelbe. Az Azure Sentinellel speciális riasztási szabályokat hozhat létre, amelyekkel automatikusan hozhat létre incidenseket a vizsgálatokhoz. 

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

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Rendszeres támadásszimulációk végrehajtása

**Útmutató**: Szükség esetén végezzen behatolási teszteket vagy riasztási gyakorlatokat az Azure-erőforrásokon, hogy biztosítva legyen az összes kritikus biztonsági találat megoldása.
A Microsoft-felhő behatolástesztelési beavatkozási szabályai szerint eljárva biztosíthatja, hogy a behatolási tesztek nem sértik a Microsoft szabályzatait. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét.

- [Behatolási tesztek az Azure-ban](../security/fundamentals/pen-testing.md)

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="endpoint-security"></a>Endpoint Security

*További információt az [Azure biztonsági teljesítményteszt: Endpoint Security](../security/benchmarks/security-controls-v2-endpoint-security.md)című témakörben talál.*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: végpont-észlelés és-válasz használata (EDR)

**Útmutató**: az ügyfelek számára nincs kifejezetten engedélyezve a végpont-észlelés és a válasz beállításainak konfigurálása. Az Azure virtuális WAN-ajánlatokban használt Virtual Machines azonban ezeket a képességeket használják. További információ ezekről az általános képességekről a hivatkozott hivatkozásokon. 

- [A Microsoft Defender komplex veszélyforrások elleni védelem áttekintése](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Microsoft Defender ATP-szolgáltatás Windows-kiszolgálókhoz](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Microsoft Defender ATP szolgáltatás a nem Windows rendszerű kiszolgálókhoz](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Az Azure Security Center monitorozása**: Igen

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

- [Azure Security Benchmark – Emelt jogosultságú hozzáférés](../security/benchmarks//security-controls-v2-privileged-access.md)

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