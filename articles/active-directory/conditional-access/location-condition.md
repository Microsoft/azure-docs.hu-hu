---
title: Hely feltétele Azure Active Directory feltételes hozzáférésben
description: Megtudhatja, hogyan szabályozhatja a felhőalkalmazások hozzáférését a felhasználó hálózati helye alapján a hely feltételével.
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
ms.openlocfilehash: 8b46ca16fc32a7b96c071a745f49bf5d5557f34b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530226"
---
# <a name="using-the-location-condition-in-a-conditional-access-policy"></a>A hely feltételének használata feltételes hozzáférési szabályzatban 

Ahogy az [áttekintő](overview.md) cikkben is olvasható, a feltételes hozzáférési szabályzatok a jelek kombinálásával, a döntéshozatal és a szervezeti szabályzatok betartatása érdekében a legapvetőbb, "if-then" utasításokkal hajtják végre. A döntési folyamatba beépíthető jelek egyike a hálózati hely.

![Fogalmi feltételes jel plusz döntés a kényszerítési folyamat érvénybe hozásában](./media/location-condition/conditional-access-signal-decision-enforcement.png)

A szervezetek ezt a hálózati helyet a következő gyakori feladatokhoz használhatja: 

- Többtényezős hitelesítés megkövetelása a szolgáltatáshoz hozzáférő felhasználók számára, amikor nem a vállalati hálózaton vannak.
- Adott országokból vagy régiókból származó szolgáltatásokhoz hozzáférő felhasználók hozzáférésének letiltása.

A hálózati helyet az ügyfél által megadott nyilvános IP-cím határozza meg Azure Active Directory. A feltételes hozzáférési szabályzatok alapértelmezés szerint minden IPv4- és IPv6-címre érvényesek. 

## <a name="named-locations"></a>Nevesített helyek

A helyek a következő helyen vannak Azure Portal a Azure Active Directory feltételes **hozzáférés**  >    >    >  **neves helyek alatt.** Ezek a megnevezett hálózati helyek olyan helyeket tartalmazhatnak, mint a szervezet központi hálózati tartományai, VPN-hálózati tartományok vagy blokkolni kívánt tartományok. A megnevezett helyeket IPv4/IPv6-címtartományok vagy országok/régiók definiálják. 

![Nevestűs helyek a Azure Portal](./media/location-condition/new-named-location.png)

### <a name="ip-address-ranges"></a>IP-címtartományok

Ha egy elnevezett helyet IPv4/IPv6-címtartományok alapján kell meghatároznia, meg kell adnia egy **nevet** és egy IP-címtartományt. 

Az IPv4/IPv6-címtartományok által meghatározott nevestűdő helyekre a következő korlátozások vonatkoznak: 
- Legfeljebb 195 megnevezett hely konfigurálása
- Névvel megadott helyenként legfeljebb 2000 IP-címtartomány konfigurálása
- Az IPv4- és az IPv6-tartományok is támogatottak
- Magánhálózati IP-címtartományok kapcsolat konfigurálása
- A tartományokban található IP-címek száma korlátozott. IP-címtartomány meghatározásakor csak a /8-asnál nagyobb CIDR-maszkok engedélyezettek. 

### <a name="trusted-locations"></a>Megbízható helyek

A rendszergazdák ip-címtartományok által meghatározott neves helyeket jelölhet ki megbízhatónak nevesitett helyekként. 

![Megbízható helyek a Azure Portal](./media/location-condition/new-trusted-location.png)

A megbízható megnevezett helyekről való bejelentkezések javítják a Azure AD Identity Protection kockázatszámításának pontosságát, csökkentve a felhasználók bejelentkezési kockázatát, amikor megbízhatóként megjelölt helyről hitelesítik magukat. Emellett a megbízható elnevezett helyek is célzottak a feltételes hozzáférési szabályzatok között. Előfordulhat például, hogy a többtényezős hitelesítés regisztrációját megbízható megnevezett helyekre kell korlátoznia. 

### <a name="countries-and-regions"></a>Országok és régiók

Egyes szervezetek dönthetnek úgy, hogy korlátozzák a hozzáférést bizonyos országokra vagy régiókra a feltételes hozzáférés használatával. A nevestűs helyek IP-címtartományok alapján történő meghatározása mellett a rendszergazdák ország vagy régió szerint is meghatározhat elnevezett helyeket. Amikor egy felhasználó bejelentkezik, az Azure AD feloldja a felhasználó IPv4-címét egy országra vagy régióra, és a leképezés rendszeres időközönként frissül. A szervezetek az országok által meghatározott megnevezett helyeket használhatnak az olyan országból származó forgalom blokkolásra, amelyekben nem üzleti tevékenységük van, például Észak-Korea. 

> [!NOTE]
> Az IPv6-címekről való bejelentkezések nem leképezhetőek országokra vagy régiókra, és ismeretlen területnek számítanak. Csak IPv4-címek leképezését lehet országokra vagy régiókra.

![Hozzon létre egy új országot vagy régióalapú helyet a Azure Portal](./media/location-condition/new-named-location-country-region.png)

#### <a name="include-unknown-areas"></a>Ismeretlen területekre is kiterjed

Egyes IP-címek nincsenek leképezve egy adott országra vagy régióra, beleértve az összes IPv6-címet is. Az IP-helyek rögzítéséhez jelölje be a **Hely** meghatározásakor az Ismeretlen területek is jelölőnégyzetet. Ezzel a beállítással kiválaszthatja, hogy ezek az IP-címek szerepelnek-e a megnevezett helyen. Akkor használja ezt a beállítást, ha a megnevezett helyet használó szabályzatnak ismeretlen helyekre kell vonatkozni.

### <a name="configure-mfa-trusted-ips"></a>MFA megbízható IP-k konfigurálása

A többtényezős hitelesítési szolgáltatás beállításaiban a szervezet helyi intranetét képviselő [IP-címtartományokat is konfigurálhat.](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx) Ezzel a funkcióval legfeljebb 50 IP-címtartományt konfigurálható. Az IP-címtartományok CIDR formátumban vannak. További információ: [Megbízható IP-k.](../authentication/howto-mfa-mfasettings.md#trusted-ips)  

Ha konfigurálta a megbízható IP-címeket, azok MFA megbízható **IP-címekként** megjelenik a hely feltétel helyeinek listájában.

### <a name="skipping-multi-factor-authentication"></a>Többtényezős hitelesítés kihagyása

A multi-factor authentication szolgáltatás beállításainak lapján a Vállalati intranetes felhasználók azonosításához jelölje be a Többtényezős hitelesítés kihagyása összevont felhasználóktól az intraneten található **kérések esetén lehetőséget.** Ez a beállítás azt jelzi, hogy a AD FS által kiadott, vállalati hálózaton belüli jogcímet megbízhatónak kell lennie, és annak azonosítására kell használni, hogy a felhasználó a vállalati hálózaton legyen. További információ: [Megbízható IP-k engedélyezése feltételes hozzáféréssel.](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access)

A beállítás ellenőrzése után, beleértve a neves **helyet, az MFA megbízható IP-címei** minden olyan szabályzatra vonatkoznak, amely be van jelölve ezzel a beállítással.

A hosszú élettartamú mobil- és asztali alkalmazások esetében a feltételes hozzáférés rendszeres időközönként újra ki lesz értékelve. Az alapértelmezett érték óránként egyszer. Ha a vállalati hálózaton belüli jogcímet csak a kezdeti hitelesítéskor állítják ki, előfordulhat, hogy az Azure AD nem tartalmazza a megbízható IP-tartományok listáját. Ebben az esetben nehezebb megállapítani, hogy a felhasználó még mindig a vállalati hálózaton van-e:

1. Ellenőrizze, hogy a felhasználó IP-címe a megbízható IP-tartományok egyikében van-e.
1. Ellenőrizze, hogy a felhasználó IP-címének első három oktett-e megegyezik-e a kezdeti hitelesítés IP-címének első három oktettjével. A rendszer összehasonlítja az IP-címet a kezdeti hitelesítéssel, amikor a vállalati hálózaton belüli jogcímet eredetileg kiállították, és a felhasználó helyét ellenőrizték.

Ha mindkét lépés sikertelen, a rendszer úgy tekint egy felhasználót, hogy már nem megbízható IP-címről van szó.

## <a name="location-condition-in-policy"></a>Hely feltétele a szabályzatban

A hely feltételének konfigurálásakor lehetősége van megkülönböztetni a következő adatokat:

- Bármely hely
- Minden megbízható hely
- Kiválasztott helyek

### <a name="any-location"></a>Bármely hely

Alapértelmezés szerint a Bármely hely **lehetőség** választásával egy szabályzat lesz alkalmazva az összes IP-címre, ami azt jelenti, hogy az interneten található bármely cím. Ez a beállítás nem korlátozódik a megnevezett helyként konfigurált IP-címekre. Ha a Bármely hely **lehetőséget választja,** továbbra is kizárhat bizonyos helyeket a szabályzatból. Alkalmazhat például egy szabályzatot a megbízható helyek kivételével minden helyre, hogy a hatókört a vállalati hálózat kivételével minden helyre beállítsa.

### <a name="all-trusted-locations"></a>Minden megbízható hely

Ez a beállítás a következőkre vonatkozik:

- Minden hely, amely megbízható helyként lett megjelölve
- **MFA megbízható IPS** (ha be van állítva)

### <a name="selected-locations"></a>Kiválasztott helyek

Ezzel a lehetőséggel kiválaszthat egy vagy több elnevezett helyet. Az ezzel a beállítással beállított szabályzatok csak akkor alkalmazhatók, ha a felhasználó a kiválasztott helyek bármelyikéről csatlakozik. Amikor a **Select** the named network selection control (Elnevezett hálózat kiválasztása) gombra kattint, megjelenik a nevesedt hálózatok listája. A lista azt is megjeleníti, hogy a hálózati hely megbízhatóként lett-e megjelölve. Az **MFA** megbízható IP-címek nevű elnevezett hely tartalmazza a többtényezős hitelesítési szolgáltatás beállítási oldalán konfigurálható IP-beállításokat.

## <a name="ipv6-traffic"></a>IPv6-forgalom

Alapértelmezés szerint a feltételes hozzáférési szabályzatok az összes IPv6-forgalomra érvényesek. Bizonyos IPv6-címtartományokat kizárhat a feltételes hozzáférési szabályzatból, ha nem szeretné, hogy a házirendek adott IPv6-tartományokra vonatkozzon. Ha például nem szeretne szabályzatot kikényszeríteni a vállalati hálózaton való használatra, és a vállalati hálózat nyilvános IPv6-tartományokon üzemel.  

### <a name="when-will-my-tenant-have-ipv6-traffic"></a>Mikor lesz IPv6-forgalom a bérlőmben?

Azure Active Directory (Azure AD) jelenleg nem támogatja az IPv6-ot tartalmazó közvetlen hálózati kapcsolatokat. Vannak azonban olyan esetek, amikor a hitelesítési forgalom egy másik szolgáltatáson keresztül lesz átszűkülve. Ezekben az esetekben az IPv6-cím lesz használva a szabályzat kiértékelése során.

Az Azure AD-vel proxyra váltó IPv6-forgalom nagy része a Microsoft Exchange Online-ból származik. Ha elérhető, az Exchange az IPv6-kapcsolatokat részesíti előnyben. **Ha tehát az Exchange-hez rendelkezik feltételes hozzáférési szabályzatokkal, amelyek adott IPv4-tartományokhoz vannak konfigurálva, győződjön meg arról, hogy szervezete IPv6-tartományokat is hozzáadott.** Ha nem tartalmazza az IPv6-tartományokat, az a következő két esetben nem várt viselkedést okoz:

- Ha egy levelezőprogram régi hitelesítéssel csatlakozik az Exchange Online-hoz, előfordulhat, hogy az Azure AD IPv6-címet kap. A kezdeti hitelesítési kérelem az Exchange-hez kerül, majd az Azure AD-hez kerül.
- Amikor az Outlook Web Accesst (OWA) használja a böngészőben, rendszeres időközönként ellenőrzi, hogy az összes feltételes hozzáférési szabályzat továbbra is teljesül-e. Ez az ellenőrzés olyan esetekre használható, amikor egy felhasználó egy engedélyezett IP-címről egy új helyre helyezték át, például egy kávézóba az utca végén. Ebben az esetben, ha IPv6-címet használ, és az IPv6-cím nem egy konfigurált tartományban van, előfordulhat, hogy a felhasználó munkamenete megszakadt, és vissza lesz irányítva az Azure AD-be az újbóli hitelesítéshez. 

Ezek a leggyakoribb okok, amelyek miatt előfordulhat, hogy IPv6-tartományokat kell konfigurálnia a megnevezett helyeken. Emellett Ha Azure-beli virtuális hálózatokat használ, akkor IPv6-címről érkező forgalmat fog használni. Ha feltételes hozzáférési szabályzat blokkolja a virtuális hálózati forgalmat, ellenőrizze az Azure AD bejelentkezési naplóját. Miután azonosította a forgalmat, lekértheti a használt IPv6-címet, és kizárhatja azt a szabályzatból. 

> [!NOTE]
> Ha egyetlen címhez szeretne IP CIDR-tartományt megadni, alkalmazza a /128 bites maszkot. Ha a 2607:fb90:b27a:6f69:f8d5:dea0:fb39:74a IPv6-címet adja meg, és tartományként szeretné kizárni ezt az egyetlen címet, használja a következőt: 2607:fb90:b27a:6f69:f8d5:dea0:fb39:74a/128.

### <a name="identifying-ipv6-traffic-in-the-azure-ad-sign-in-activity-reports"></a>IPv6-forgalom azonosítása az Azure AD bejelentkezési tevékenységjelentéseiben

Az IPv6-forgalmat a bérlőben az Azure AD bejelentkezési tevékenységjelentéseiben [derítheti fel.](../reports-monitoring/concept-sign-ins.md) Miután megnyitotta a tevékenységjelentést, adja hozzá az "IP-cím" oszlopot. Ebben az oszlopban azonosíthatja az IPv6-forgalmat.

Az ügyfél IP-címét úgy is megkeresheti, ha a jelentés egy sorára kattint, majd a bejelentkezési tevékenység részletei között a "Hely" lapra lép. 

## <a name="what-you-should-know"></a>Alapismeretek

### <a name="when-is-a-location-evaluated"></a>Mikor történik a hely kiértékelése?

A feltételes hozzáférési szabályzatok a következő esetben vannak kiértékelve:

- A felhasználó először egy webalkalmazásba, mobilalkalmazásba vagy asztali alkalmazásba jelentkezik be.
- A modern hitelesítést használó mobil- vagy asztali alkalmazások frissítési jogkivonattal szereznek be új hozzáférési jogkivonatot. Alapértelmezés szerint ez az ellenőrzés óránként egyszer van beállítva.

Ez az ellenőrzés azt jelenti, hogy a modern hitelesítést használó mobil- és asztali alkalmazások esetében a rendszer a hálózati hely módosításától számított egy órán belül észleli a hely változását. A modern hitelesítést nem felhasználó mobil- és asztali alkalmazások esetén a szabályzat minden jogkivonat-kérelemre érvényes. A kérés gyakorisága az alkalmazástól függően változhat. Hasonlóképpen, webalkalmazások esetén a szabályzatot a rendszer a kezdeti bejelentkezéskor alkalmazza, és a webalkalmazás munkamenetének teljes élettartama alatt használható. Az alkalmazások munkamenet-élettartamának eltérései miatt a szabályzatok kiértékelése közötti idő is eltérő lesz. Minden alkalommal, amikor az alkalmazás új bejelentkezési jogkivonatot kér, a rendszer alkalmazza a szabályzatot.

Alapértelmezés szerint az Azure AD óránként ad ki jogkivonatot. A vállalati hálózatról való áthelyezés után egy órán belül a szabályzat érvényes lesz a modern hitelesítést használó alkalmazásokra.

### <a name="user-ip-address"></a>Felhasználói IP-cím

A szabályzatok kiértékelése során használt IP-cím a felhasználó nyilvános IP-címe. Magánhálózati eszközök esetén ez az IP-cím nem a felhasználó intranetes eszközének ügyfél-IP-címe, hanem a hálózat által a nyilvános internethez való csatlakozáshoz használt cím.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Megnevezett helyek tömeges feltöltése és letöltése

Elnevezett helyek létrehozásakor vagy frissítésekkor tömeges frissítésekhez feltölthet vagy letölthet egy CSV-fájlt az IP-címtartományokkal. A feltöltés lecseréli a listában található IP-címtartományokat a fájlból származó tartományokkal. A fájl minden egyes sorában egy CIDR formátumú IP-címtartomány található.

### <a name="cloud-proxies-and-vpns"></a>Felhőbeli proxyk és VPN-ek

Ha felhőben üzemeltetett proxyt vagy VPN-megoldást használ, az Azure AD a házirend kiértékeléskor használt IP-címe a proxy IP-címe. A rendszer nem használja a felhasználó nyilvános IP-címét tartalmazó X-Forwarded-For (XFF) fejlécet, mert nincs ellenőrzés arról, hogy megbízható forrásból származik, ezért egy IP-cím szimulálása egy módszert jelent.

Ha felhőproxy van használatban, a hibrid Azure AD-hez csatlakozott eszközre vonatkozó szabályzat, vagy a vállalati hálózat belső jogcíme használható a AD FS.

### <a name="api-support-and-powershell"></a>API-támogatás és PowerShell

A névvel Graph API api előzetes verziója érhető el. További információért lásd a [namedLocation API-t.](/graph/api/resources/namedlocation)

> [!NOTE]
> A PowerShell használatával létrehozott elnevezett helyek csak a Nevestűs helyek (előzetes verzió) nézetben jelennek meg. A nevestűs helyeket a régi nézetben nem láthatja.  

## <a name="next-steps"></a>Következő lépések

- Ha szeretné tudni, hogyan konfigurálhatja a feltételes hozzáférési szabályzatokat, tekintse meg [a Feltételes hozzáférési szabályzatok kiépítése cikket.](concept-conditional-access-policies.md)
- A hely feltételt használó példa szabályzatot keres? Lásd: Feltételes [hozzáférés: Hozzáférés blokkolása hely szerint](howto-conditional-access-policy-location.md)
