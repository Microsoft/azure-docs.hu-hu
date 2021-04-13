---
title: A hely feltétele Azure Active Directory feltételes hozzáférésben
description: Megtudhatja, hogyan használhatja a hely feltételét a felhőalapú alkalmazásokhoz való hozzáférés vezérlésére a felhasználó hálózati helye alapján.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 07af586bac71ee9b33ef314756454cb3c52ec912
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305922"
---
# <a name="using-the-location-condition-in-a-conditional-access-policy"></a>A hely feltételének használata feltételes hozzáférési házirendben 

Ahogy az [áttekintő cikkben](overview.md) is látható, a feltételes hozzáférési szabályzatok az alapszintű egy if-then utasításban a jelek kombinálásával, a döntések elvégzéséhez és a szervezeti szabályzatok betartatásához szükségesek. A döntéshozatali folyamatba beépíthető jelek egyike a hálózati hely.

![Koncepcionális feltételes jel plusz döntés a kényszerítés beszerzéséhez](./media/location-condition/conditional-access-signal-decision-enforcement.png)

A szervezetek a következő általános feladatokhoz használhatják ezt a hálózati helyet: 

- Többtényezős hitelesítés megkövetelése a szolgáltatáshoz hozzáférő felhasználók számára a vállalati hálózaton kívülről.
- Hozzáférés letiltása adott országokból vagy régiókból származó szolgáltatáshoz hozzáférő felhasználók számára.

A hálózati helyet az ügyfél által a Azure Active Directory számára biztosított nyilvános IP-cím határozza meg. A feltételes hozzáférési szabályzatok alapértelmezés szerint az összes IPv4-és IPv6-címre érvényesek. 

## <a name="named-locations"></a>Nevesített helyek

A helyszínek a Azure Portal **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférés**  >  **névvel ellátott helyei** területen vannak kijelölve. Ezek a nevesített hálózati helyek olyan helyekhez tartozhatnak, mint például a szervezeti központ hálózati tartományai, a VPN-hálózatok tartományai vagy a blokkolni kívánt tartományok. A nevesített helyszíneket IPv4/IPv6-címtartományok vagy országok/régiók határozzák meg. 

![Elnevezett helyszínek a Azure Portal](./media/location-condition/new-named-location.png)

### <a name="ip-address-ranges"></a>IP-címtartományok

Egy elnevezett hely IPv4/IPv6-címtartományok alapján való definiálásához meg kell adnia egy **nevet** és egy IP-címtartományt. 

Az IPv4/IPv6-címtartományok által definiált nevesített helyszínek a következő korlátozások alá esnek: 
- Akár 195 elnevezett helyszín konfigurálása
- Akár 2000 IP-tartomány konfigurálása egy nevesített helyen
- Az IPv4-és IPv6-tartományok is támogatottak
- A magánhálózati IP-címtartományok konfigurálása connot
- A tartományokban található IP-címek száma korlátozott. IP-címtartomány definiálásakor csak a/8 értéknél nagyobb CIDR-maszk megengedett. 

### <a name="trusted-locations"></a>Megbízható helyek

A rendszergazdák megadhatják, hogy az IP-címtartományok által meghatározott megnevezett helyek megbízható elnevezett helyek legyenek. 

![Megbízható helyek a Azure Portal](./media/location-condition/new-trusted-location.png)

A megbízható elnevezett helyekről érkező bejelentkezések javítják Azure AD Identity Protection kockázati számításának pontosságát, és csökkentik a felhasználók bejelentkezési kockázatát, ha a hitelesítés a megbízható helyről történik. A megbízható elnevezett helyek emellett feltételes hozzáférési szabályzatokban is megadhatók. Előfordulhat például, hogy a többtényezős hitelesítés regisztrációját csak megbízható elnevezett helyekre korlátozza. 

### <a name="countries-and-regions"></a>Országok és régiók

Néhány szervezet dönthet úgy, hogy a feltételes hozzáférés használatával korlátozza bizonyos országok vagy régiók hozzáférését. A nevesített helyszínek IP-címtartományok alapján történő definiálásán kívül a rendszergazdák ország vagy régiók szerint is meghatározhatnak nevesített helyszíneket. Amikor egy felhasználó bejelentkezik, az Azure AD feloldja a felhasználó IPv4-címeit egy ország vagy régió számára, és a leképezést rendszeresen frissíti. A szervezetek az országok által meghatározott elnevezett helyekkel letilthatják a forgalmat olyan országokból, ahol nem üzleti tevékenységet végeznek, például Észak-Koreában. 

> [!NOTE]
> Az IPv6-címekről érkező bejelentkezések nem képezhetők le országokra vagy régiókra, és ismeretlen területnek számítanak. Csak IPv4-címek képezhetők le országokra vagy régiókra.

![Új ország vagy régió alapú hely létrehozása a Azure Portal](./media/location-condition/new-named-location-country-region.png)

#### <a name="include-unknown-areas"></a>Ismeretlen területek belefoglalása

Néhány IP-cím nincs leképezve egy adott országra vagy régióra, beleértve az összes IPv6-címet is. Ezeknek az IP-helyeknek a rögzítéséhez jelölje be az **ismeretlen területek** megadása a hely meghatározásakor jelölőnégyzetet. Ezzel a beállítással megadhatja, hogy ezek az IP-címek szerepeljenek-e a megnevezett helyen. Akkor használja ezt a beállítást, ha a nevesített helyet használó házirendet ismeretlen helyekre kell alkalmazni.

### <a name="configure-mfa-trusted-ips"></a>MFA megbízható IP-címeinek konfigurálása

A [multi-Factor Authentication szolgáltatás beállításaiban](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx)konfigurálhatja a szervezet helyi intranetét JELKÉPEZŐ IP-címtartományt is. Ez a funkció lehetővé teszi, hogy legfeljebb 50 IP-címtartományt konfiguráljon. Az IP-címtartományok CIDR formátumúak. További információ: [megbízható IP](../authentication/howto-mfa-mfasettings.md#trusted-ips)-címek.  

Ha megbízható IP-címek vannak konfigurálva, a hely feltételének helyein **MFA megbízható IP** -címekként jelennek meg.

### <a name="skipping-multi-factor-authentication"></a>Multi-Factor Authentication kihagyása

A multi-Factor Authentication szolgáltatás beállításai lapon azonosíthatja a vállalati intranetes felhasználókat a  **többtényezős hitelesítés kihagyása az összevont felhasználóktól az intraneten lévő kérelmekhez** lehetőség kiválasztásával. Ez a beállítás azt jelzi, hogy a AD FS által kiadott vállalati hálózati jogcímet megbízhatónak kell tekinteni, és a felhasználó azonosítására kell használni a vállalati hálózaton. További információ: [a megbízható IP-címek engedélyezése funkció feltételes hozzáférés használatával](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

A beállítás ellenőrzése után a megnevezett hely **MFA megbízható IP** -címei minden olyan házirendre érvényesek lesznek, amelyik ezt a lehetőséget választja.

A hosszú élettartamú munkamenetek élettartamát tartalmazó mobil-és asztali alkalmazások esetében a feltételes hozzáférés időszakosan újraértékelve lesz. Az alapértelmezett érték óránként egyszer. Ha a vállalati hálózati jogcímek csak a kezdeti hitelesítéskor jelennek meg, előfordulhat, hogy az Azure AD nem rendelkezik a megbízható IP-címtartományok listájával. Ebben az esetben nehezebb megállapítani, hogy a felhasználó továbbra is a vállalati hálózaton van-e:

1. Ellenőrizze, hogy a felhasználó IP-címe a megbízható IP-címtartományok egyikén van-e.
1. Győződjön meg arról, hogy a felhasználó IP-címének első három oktettje megegyezik-e a kezdeti hitelesítés IP-címének első három oktettével. A rendszer összehasonlítja az IP-címet a kezdeti hitelesítéssel, ha a belső vállalati hálózati jogcím eredetileg ki lett állítva, és a felhasználói hely ellenőrzése megtörtént.

Ha mindkét lépés meghiúsul, a rendszer a felhasználót már nem megbízható IP-címekre tekinti.

## <a name="location-condition-in-policy"></a>Hely feltétele a házirendben

A hely feltételének konfigurálásakor lehetősége van a következők megkülönböztetésére:

- Bármely hely
- Minden megbízható helyszín
- Kiválasztott helyszínek

### <a name="any-location"></a>Bármely hely

Alapértelmezés szerint a **bármely hely** kiválasztásakor a rendszer minden IP-címre alkalmazza a házirendet, ami bármilyen címet jelent az interneten. Ez a beállítás nem korlátozódik az elnevezett helyként konfigurált IP-címekre. Ha **bármelyik helyet** kijelöli, akkor továbbra is kizárhat bizonyos helyeket a szabályzatból. Például alkalmazhat egy házirendet az összes helyszínre, kivéve a megbízható helyek beállítást a hatókör minden helyszínre való beállításához, a vállalati hálózat kivételével.

### <a name="all-trusted-locations"></a>Minden megbízható helyszín

Ez a beállítás a következőkre vonatkozik:

- Minden olyan hely, amely megbízható helyként van megjelölve
- **MFA megbízható IP** -címei (ha be van állítva)

### <a name="selected-locations"></a>Kiválasztott helyszínek

Ezzel a beállítással egy vagy több elnevezett helyet választhat ki. Ahhoz, hogy egy házirend alkalmazza ezt a beállítást, a felhasználónak csatlakoznia kell a kiválasztott helyekről. Ha a megnevezett hálózati kijelölési vezérlő **kijelölése** elemre kattint, megjelenik a megnevezett hálózatok listája. A lista azt is megjeleníti, hogy a hálózati hely megbízhatóként van-e megjelölve. Az **MFA megbízható IP** -címek nevű hely a multi-Factor Authentication szolgáltatás beállítási oldalán konfigurálható IP-beállításokat tartalmazza.

## <a name="ipv6-traffic"></a>IPv6-forgalom

Alapértelmezés szerint a feltételes hozzáférési házirendek minden IPv6-forgalomra érvényesek lesznek. Ha nem szeretné, hogy a házirendek kényszerítve legyenek bizonyos IPv6-tartományokra, kizárhatja az adott IPv6-címtartományt egy feltételes hozzáférési szabályzatból. Ha például nem kívánja kikényszeríteni a vállalati hálózat használatára vonatkozó szabályzatot, és a vállalati hálózat nyilvános IPv6-tartományokon fut.  

### <a name="when-will-my-tenant-have-ipv6-traffic"></a>Mikor lesz a bérlőnek IPv6-forgalma?

Azure Active Directory (Azure AD) jelenleg nem támogatja az IPv6 protokollt használó közvetlen hálózati kapcsolatokat. Vannak azonban olyan esetek, amikor a hitelesítési forgalom egy másik szolgáltatáson keresztül történik. Ezekben az esetekben a szabályzat kiértékelése során az IPv6-cím lesz használatban.

Az Azure AD-ben a proxyn áthaladó IPv6-forgalom nagy része a Microsoft Exchange Online szolgáltatásból származik. Ha elérhető, az Exchange előnyben részesíti az IPv6-kapcsolatokat. **Tehát ha bármilyen feltételes hozzáférési szabályzattal rendelkezik, amelyet adott IPv4-tartományokhoz konfiguráltak, érdemes meggyőződnie arról, hogy a szervezet IPv6-tartományait is felvette.** Az IPv6-tartományokat nem, például a következő két eset esetében nem várt viselkedést eredményez:

- Ha egy levelezési ügyfélprogram örökölt hitelesítéssel csatlakozik az Exchange Online-hoz, az Azure AD IPv6-címeket is kaphat. A kezdeti hitelesítési kérelem az Exchange-be kerül, majd az Azure AD-be való proxyn keresztül történik.
- Ha a böngészőben az Outlook Web Access (OWA) használatban van, a rendszer rendszeres időközönként ellenőrzi, hogy a feltételes hozzáférési szabályzatok továbbra is teljesülnek-e. Ez az ellenőrzési szolgáltatás arra szolgál, hogy olyan eseteket kapjon, amikor egy felhasználó egy engedélyezett IP-címről egy új helyre költözött, például a kávézóban az utcán. Ebben az esetben, ha egy IPv6-cím van használatban, és ha az IPv6-cím nem egy konfigurált tartományban van, akkor a felhasználó munkamenete megszakadhat, és visszairányítható az Azure AD-be az ismételt hitelesítéshez. 

Ezek a leggyakoribb okok, amiért előfordulhat, hogy az IPv6-tartományokat az elnevezett helyekre kell konfigurálnia. Emellett, ha az Azure virtuális hálózatok-t használja, az IPv6-címről érkező forgalmat is elérheti. Ha a VNet-forgalmat egy feltételes hozzáférési szabályzat tiltja le, ellenőrizze az Azure AD bejelentkezési naplóját. Miután azonosította a forgalmat, lekérheti a használt IPv6-címeket, és kizárja azt a szabályzatból. 

> [!NOTE]
> Ha egyetlen címhez IP-CIDR szeretne megadni, alkalmazza a/128 bit maszkot. Ha az IPv6-2607: fb90: b27a: 6f69: f8d5: dea0: fb39:74A, és azt szeretné, hogy az egyetlen cím legyen tartományként kizárva, akkor a 2607: fb90: b27a: 6f69: f8d5: dea0: fb39:74A/128.

### <a name="identifying-ipv6-traffic-in-the-azure-ad-sign-in-activity-reports"></a>IPv6-forgalom azonosítása az Azure AD bejelentkezési tevékenység jelentéseiben

A bérlőben található IPv6-forgalmat az [Azure ad bejelentkezési tevékenységek jelentéseiben](../reports-monitoring/concept-sign-ins.md)derítheti fel. Miután megnyitotta a tevékenység jelentését, adja hozzá az "IP-cím" oszlopot. Ez az oszlop az IPv6-forgalom azonosítását teszi lehetővé.

Az ügyfél IP-címét a jelentés egyik sorára kattintva is megkeresheti, majd a bejelentkezési tevékenység részletei között a "location" (hely) lapra kerül. 

## <a name="what-you-should-know"></a>Alapismeretek

### <a name="when-is-a-location-evaluated"></a>Mikor kerül kiértékelésre a hely?

A feltételes hozzáférési szabályzatokat a rendszer a következők szerint értékeli ki:

- A felhasználó először bejelentkezik egy webalkalmazásba, mobilba vagy asztali alkalmazásba.
- A modern hitelesítést használó mobil-vagy asztali alkalmazások frissítési tokent használnak új hozzáférési jogkivonat beszerzéséhez. Alapértelmezés szerint ez az ellenőrzés óránként egyszer történik.

Ez az ellenőrzési módszer a modern hitelesítést használó mobil-és asztali alkalmazások esetén a hely változását a hálózati hely módosítása után egy órán belül észleli. A modern hitelesítést nem használó mobil-és asztali alkalmazások esetén a szabályzatot minden jogkivonat-kérelemre alkalmazza a rendszer. A kérés gyakorisága az alkalmazástól függően változhat. Hasonlóképpen, a webalkalmazások esetében a szabályzatot a rendszer az első bejelentkezéskor alkalmazza, és jó választás a webalkalmazásban található munkamenet élettartamára. Az alkalmazások munkamenet-élettartama közötti különbségek miatt a szabályzat kiértékelése közötti idő is változhat. Minden alkalommal, amikor az alkalmazás új bejelentkezési jogkivonatot kér, a rendszer alkalmazza a házirendet.

Alapértelmezés szerint az Azure AD egy tokent bocsát ki óránként. A vállalati hálózat kikapcsolását követően egy órán belül kikényszeríti a szabályzatot a modern hitelesítést használó alkalmazásokhoz.

### <a name="user-ip-address"></a>Felhasználói IP-cím

A házirend kiértékeléséhez használt IP-cím a felhasználó nyilvános IP-címe. A magánhálózaton lévő eszközök esetében ez az IP-cím nem az intraneten lévő felhasználó eszközének ügyfél-IP-címe, hanem a hálózat által a nyilvános internethez való kapcsolódáshoz használt cím.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Elnevezett helyszínek tömeges feltöltése és letöltése

Ha elnevezett helyeket hoz létre vagy frissít, a tömeges frissítésekhez feltölthet vagy letölthet egy CSV-fájlt az IP-címtartományok használatával. A feltöltés felváltja a listában szereplő IP-tartományokat a fájlból származó tartományokkal. A fájl minden sora egy IP-címtartományt tartalmaz CIDR formátumban.

### <a name="cloud-proxies-and-vpns"></a>Felhőalapú proxyk és VPN-EK

Ha felhőben üzemeltetett proxy-vagy VPN-megoldást használ, az Azure AD IP-címe a szabályzat kiértékelése során a proxy IP-címét használja. A felhasználó nyilvános IP-címét tartalmazó X-továbbított (XFF) fejléc nem használatos, mert nincs olyan érvényesítés, amely megbízható forrásból származik, ezért az IP-címek szimulálása egy metódust jelent.

Ha egy felhőalapú proxy van érvényben, a hibrid Azure AD-hez csatlakoztatott eszközök használatához vagy a AD FSon belüli Corpnet-jogcímek használatára vonatkozó szabályzatot lehet használni.

### <a name="api-support-and-powershell"></a>API-támogatás és PowerShell

Az elnevezett helyszínek Graph API előzetes verziója érhető el, további információért lásd a [NAMEDLOCATION API](/graph/api/resources/namedlocation?view=graph-rest-beta)-t.

> [!NOTE]
> A PowerShell-megjelenítéssel létrehozott elnevezett helyszínek csak névvel ellátott helyen (előzetes verzió) jelennek meg. A nevesített helyszínek nem láthatók a régi nézetben.  

## <a name="next-steps"></a>Következő lépések

- Ha tudni szeretné, hogyan kell konfigurálni a feltételes hozzáférési szabályzatot, tekintse meg a [feltételes hozzáférési szabályzat létrehozásával](concept-conditional-access-policies.md)foglalkozó cikket.
- A hely feltételét használó példát keres? Tekintse meg a következő cikket [: feltételes hozzáférés letiltása hely szerint](howto-conditional-access-policy-location.md)
