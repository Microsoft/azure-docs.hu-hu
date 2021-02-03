---
title: Folyamatos hozzáférés kiértékelése az Azure AD-ben
description: Válaszadás a felhasználói állapot változásaira a folyamatos hozzáférés kiértékelésével az Azure AD-ben
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 562c90dcc4f802290b0ed8b4d544fce9d526fa10
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99524668"
---
# <a name="continuous-access-evaluation"></a>Folyamatos hozzáférés-kiértékelés

A jogkivonat lejárata és frissítése egy standard mechanizmus az iparágban. Ha egy ügyfélalkalmazás, például az Outlook csatlakozik egy szolgáltatáshoz, például az Exchange Online-hoz, az API-kérések a OAuth 2,0 hozzáférési jogkivonatok használatával engedélyezettek. Alapértelmezés szerint ezek a hozzáférési jogkivonatok egy órára érvényesek, amikor lejárnak, az ügyfelet a rendszer visszairányítja az Azure AD-re a frissítéshez. Ez a frissítési időszak lehetőséget biztosít a felhasználói hozzáférésre vonatkozó szabályzatok újraértékelésére. Például: dönthet úgy, hogy nem frissíti a jogkivonatot egy feltételes hozzáférési szabályzat miatt, vagy mert a felhasználó le van tiltva a címtárban. 

Az ügyfelek a felhasználóra vonatkozó feltételek változása, például a hálózati hely vagy a hitelesítő adatok ellopása, valamint az adott változással kapcsolatos szabályzatok kikényszerített eltérései közötti késésre vonatkozó aggályok merültek fel. A "tompa objektum" megközelítéssel kísérleteztek a kisebb jogkivonat-élettartamok miatt, de a felhasználók a kockázatok kiküszöbölése nélkül csökkenthetik a felhasználói élményt és a megbízhatóságot.

A Szabályzat megsértésével vagy biztonsági problémákkal kapcsolatos időben történő válaszadás esetén a token kiállítója, például az Azure AD és a függő entitás (például az Exchange Online) között "beszélgetésre van szükség. Ez a kétirányú beszélgetés két fontos képességet biztosít nekünk. A függő entitás megfigyelheti, hogy mikor változott meg a dolgok, például egy új helyről érkező ügyfél, és tájékoztassa a jogkivonat kiállítóját. Azt is megadja a jogkivonat kiállítójának, hogy elmondja, hogy a függő entitás leállítja a tokenek betartását egy adott felhasználó számára a fiókok veszélyeztetése, a nem megfelelőség vagy más problémák miatt. A beszélgetés mechanizmusa folyamatos hozzáférés kiértékelése (CAE). A cél az, hogy a válasz közel valós idejű legyen, de bizonyos esetekben az esemény propagálási ideje miatt akár 15 percet is megfigyelhető.

A folyamatos hozzáférés kiértékelésének kezdeti megvalósítása az Exchange, a Teams és a SharePoint Online rendszerre összpontosít.

Az alkalmazások a CAE használatára való előkészítésével kapcsolatban lásd: a [folyamatos hozzáférés kiértékelése engedélyezett API-k használata az alkalmazásokban](../develop/app-resilience-continuous-access-evaluation.md).

### <a name="key-benefits"></a>Főbb előnyök

- Felhasználó leállítása vagy jelszavának módosítása/alaphelyzetbe állítása: a felhasználói munkamenetek visszavonása a közel valós időben lesz érvényesítve.
- Hálózati hely módosítása: a feltételes hozzáférési hely házirendjei a közel valós időben lesznek kikényszerítve.
- A jogkivonat a megbízható hálózaton kívüli gépre való exportálását a feltételes hozzáférési hely házirendjével lehet megakadályozni.

## <a name="scenarios"></a>Forgatókönyvek 

A folyamatos hozzáférés kiértékelése, a kritikus események kiértékelése és a feltételes hozzáférési szabályzatok kiértékelése két forgatókönyvből áll.

### <a name="critical-event-evaluation"></a>Kritikus események kiértékelése

A folyamatos hozzáférés kiértékelése úgy történik, hogy a szolgáltatások, például az Exchange Online, a SharePoint Online és a Teams szolgáltatásokkal való előfizetéssel előfizethetnek az Azure AD kritikus eseményeire, így az események kiértékelése és érvényesítése közel valós idejű lehet. A kritikus események kiértékelése nem támaszkodik a feltételes hozzáférési szabályzatokra, így bármely bérlőben elérhető. A következő események értékelése folyamatban van:

- A felhasználói fiók törölve vagy Letiltva
- A felhasználó jelszavának módosítása vagy alaphelyzetbe állítása
- A többtényezős hitelesítés engedélyezve van a felhasználó számára
- A rendszergazda explicit módon visszavonja a felhasználó összes frissítési jogkivonatát.
- A Azure AD Identity Protection által észlelt magas felhasználói kockázat

Ez a folyamat lehetővé teszi, hogy a felhasználók ne férhessenek hozzá a szervezeti SharePoint Online-fájlokhoz, e-mailekhez, naptárhoz vagy feladatokhoz, illetve a Microsoft 365 ügyfélalkalmazások tagjaihoz, a kritikus események egyike után. 

### <a name="conditional-access-policy-evaluation-preview"></a>Feltételes hozzáférési szabályzat kiértékelése (előzetes verzió)

Az Exchange és a SharePoint képes szinkronizálni a feltételes hozzáférési házirendeket, hogy azok a szolgáltatáson belül is kiértékelhető legyenek.

Ez a folyamat lehetővé teszi, hogy a felhasználók a hálózati hely megváltozása után azonnal hozzáférjenek a szervezeti fájlokhoz, e-mailekhez, naptárhoz vagy feladatokhoz Microsoft 365 ügyfélalkalmazások vagy a SharePoint Online számára.

> [!NOTE]
> Nem minden alkalmazás-és erőforrás-szolgáltató kombináció támogatott. Lásd az alábbi táblázatot. Az Office a Word, az Excel és a PowerPoint alkalmazásra hivatkozik

| | Outlook Web | Outlook Win32 | Outlook iOS | Outlook Android | Outlook Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **szabályozásáról** | Támogatott | Támogatott | Nem támogatott | Nem támogatott | Támogatott |
| **Exchange Online** | Támogatott | Támogatott | Támogatott | Támogatott | Támogatott |

| | Office Web Apps | Office Win32-alkalmazások | IOS-es Office | Androidos Office | Mac-iroda |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **szabályozásáról** | Nem támogatott | Támogatott | Támogatott | Támogatott | Támogatott |
| **Exchange Online** | Nem támogatott | Támogatott | Támogatott | Támogatott | Támogatott |

### <a name="client-side-claim-challenge"></a>Ügyféloldali jogcímek kihívása

A folyamatos hozzáférés kiértékelése előtt az ügyfelek mindig megpróbálják visszajátszani a hozzáférési tokent a gyorsítótárból mindaddig, amíg nem járt le. A CAE-vel új esetet vezetünk be, hogy egy erőforrás-szolgáltató akkor is elutasíthatja a tokent, ha az nem járt le. Ahhoz, hogy tájékoztassa az ügyfeleket a gyorsítótár megkerüléséről annak ellenére, hogy a gyorsítótárazott tokenek nem jártak le, bevezetünk egy **jogcím-kihívás** nevű mechanizmust, amely jelzi, hogy a jogkivonat el lett utasítva, és az Azure ad új hozzáférési jogkivonatot kell kiállítania. A CAE-nek szüksége van egy ügyfél frissítésére a jogcím-kihívás megismerése érdekében. A következő alkalmazások legújabb verziója támogatja a jogcím kihívását:

- Outlook Windows
- Outlook iOS
- Outlook Android
- Outlook Mac
- Outlook Web App
- Windows-csapatok (csak a csapatok erőforrásához)
- Csapatok iOS (csak a csapatok erőforrásához)
- Csapatok Android (csak a csapatok erőforrásához)
- Csapatok Mac (csak a csapatok erőforrásához)
- Word/Excel/PowerPoint Windows rendszerhez
- Word/Excel/PowerPoint iOS rendszerhez
- Word/Excel/PowerPoint Android rendszerhez
- Word/Excel/PowerPoint Mac rendszerhez

### <a name="token-lifetime"></a>Jogkivonat élettartama

Mivel a kockázatok és a házirendek valós időben lettek kiértékelve, a folyamatos hozzáférés-értékelést tárgyaló ügyfelek a meglévő statikus hozzáférési jogkivonat élettartam-szabályzatai helyett a CAE-t használják, ami azt jelenti, hogy a konfigurálható jogkivonat-élettartamra vonatkozó házirend többé nem lesz betartva a CAE-kompatibilis munkameneteket tárgyaló, a CAE-t támogató ügyfelek számára.

A jogkivonat élettartama hosszú életű, a CAE-munkamenetekben akár 28 óráig is nő. A visszavonást kritikus események és szabályzatok kiértékelése vezérli, nem csupán egy tetszőleges időszakra. Ez a változás növeli az alkalmazások stabilitását anélkül, hogy ez hatással lenne a biztonsági helyzetre. 

Ha nem használ CAE-kompatibilis ügyfeleket, akkor az alapértelmezett hozzáférési jogkivonat élettartama 1 óra marad, hacsak nem konfigurálta a hozzáférési jogkivonat élettartamát a [konfigurálható jogkivonat élettartama (CTL)](../develop/active-directory-configurable-token-lifetimes.md) előzetes verziójával.

## <a name="example-flows"></a>Példa folyamatokra

### <a name="user-revocation-event-flow"></a>Felhasználó visszavonási eseményének folyamata:

![Felhasználó visszavonási eseményének folyamata](./media/concept-continuous-access-evaluation/user-revocation-event-flow.png)

1. A CAE-kompatibilis ügyfelek bemutatják a hitelesítő adatokat vagy frissítési jogkivonatot az Azure AD-nek, amely bizonyos erőforrások hozzáférési jogkivonatát kéri.
1. A rendszer a hozzáférési jogkivonatot más összetevőkkel együtt adja vissza az ügyfélnek.
1. A rendszergazda explicit módon [visszavonja a felhasználó összes frissítési jogkivonatát](/powershell/module/azuread/revoke-azureaduserallrefreshtoken). A rendszer visszavonási eseményt küld az erőforrás-szolgáltatónak az Azure AD-től.
1. Az erőforrás-szolgáltató hozzáférési tokent mutat be. Az erőforrás-szolgáltató kiértékeli a jogkivonat érvényességét, és ellenőrzi, hogy van-e visszavonási esemény a felhasználó számára. Az erőforrás-szolgáltató ezt az információt használja annak eldöntésére, hogy hozzáférést biztosítson az erőforráshoz, vagy sem.
1. Ebben az esetben az erőforrás-szolgáltató megtagadja a hozzáférést, és egy 401 + jogcím-feladatot küld vissza az ügyfélnek.
1. A CAE-kompatibilis ügyfél tisztában van a 401 + jogcím kihívással. Megkerüli a gyorsítótárat, és visszakerül az 1. lépésre, és visszaküldi a frissítési tokent az Azure AD-re vonatkozó jogcím-kihívással együtt. Az Azure AD ezután újraértékeli az összes feltételt, és megkéri a felhasználót, hogy végezze el az újrahitelesítést ebben az esetben.

### <a name="user-condition-change-flow-preview"></a>Felhasználói feltétel változási folyamata (előzetes verzió):

A következő példában egy feltételes hozzáférési rendszergazda konfigurált egy helyspecifikus feltételes hozzáférési szabályzatot, amely csak adott IP-címtartományok elérését engedélyezi:

![Felhasználói feltétel eseményének folyamata](./media/concept-continuous-access-evaluation/user-condition-change-flow.png)

1. A CAE-kompatibilis ügyfelek bemutatják a hitelesítő adatokat vagy frissítési jogkivonatot az Azure AD-nek, amely bizonyos erőforrások hozzáférési jogkivonatát kéri.
1. Az Azure AD kiértékeli az összes feltételes hozzáférési szabályzatot, hogy megtudja, a felhasználó és az ügyfél teljesíti-e a feltételeket.
1. A rendszer a hozzáférési jogkivonatot más összetevőkkel együtt adja vissza az ügyfélnek.
1. A felhasználó kiköltözik az engedélyezett IP-címtartományből
1. Az ügyfél hozzáférési jogkivonatot biztosít az erőforrás-szolgáltató számára az engedélyezett IP-címtartomány kívülről.
1. Az erőforrás-szolgáltató kiértékeli a jogkivonat érvényességét, és ellenőrzi az Azure AD-ből szinkronizált tartózkodási hely házirendjét.
1. Ebben az esetben az erőforrás-szolgáltató megtagadja a hozzáférést, és egy 401 + jogcímet küld vissza az ügyfélnek, mivel az nem az engedélyezett IP-címről érkezik.
1. A CAE-kompatibilis ügyfél tisztában van a 401 + jogcím kihívással. Megkerüli a gyorsítótárat, és visszakerül az 1. lépésre, és visszaküldi a frissítési tokent az Azure AD-re vonatkozó jogcím-kihívással együtt. Az Azure AD újraértékeli az összes feltételt, és ebben az esetben letiltja a hozzáférést.

## <a name="enable-or-disable-cae-preview"></a>A CAE engedélyezése vagy letiltása (előzetes verzió)

1. Jelentkezzen be a **Azure Portalba** feltételes hozzáférési rendszergazdaként, biztonsági rendszergazdaként vagy globális rendszergazdaként
1. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **folyamatos hozzáférés kiértékelését**.
1. Válassza az **előnézet engedélyezése** lehetőséget.

Ezen a lapon korlátozhatja az előzetes verzióra érvényes felhasználókat és csoportokat.

![A CAE-előnézet engedélyezése a Azure Portal](./media/concept-continuous-access-evaluation/enable-cae-preview.png)

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="supported-location-policies"></a>Támogatott tartózkodási hely házirendjei

A CAE esetében csak az elnevezett IP-alapú, névvel ellátott helyszínekre vonatkozó betekintések találhatók meg. Nem találunk betekintést más helyekre, például [MFA megbízható IP](../authentication/howto-mfa-mfasettings.md#trusted-ips) -címekre vagy ország alapú helyekre. Ha a felhasználó olyan MFA megbízható IP-címről vagy megbízható helyről származik, amely MFA-beli megbízható IP-címeket vagy ország helyét tartalmaz, a CAE nem lesz kikényszerítve, miután a felhasználó áthelyezte a másik helyre. Ezekben az esetekben az azonnali IP-kényszerítési ellenőrzés nélkül adunk ki egy 1 órás CAE-tokent.

> [!IMPORTANT]
> Ha a helyeket a folyamatos hozzáférés kiértékeléséhez konfigurálja, csak az [IP-alapú feltételes hozzáférési hely feltételét](../conditional-access/location-condition.md#preview-features) használja, és konfigurálja az összes olyan IP-címet, **beleértve az IPv4 és az IPv6** protokollt is, amelyeket az identitás-szolgáltató és az erőforrás-szolgáltató is láthat. Ne használjon az Azure AD Multi-Factor Authentication Szolgáltatásbeállítások lapján elérhető országbeli elhelyezési feltételeket vagy a megbízható IP-címek funkciót.

### <a name="ip-address-configuration"></a>IP-címkonfiguráció

A személyazonosság-szolgáltató és az erőforrás-szolgáltatók különböző IP-címeket láthatnak. Ez az eltérés a szervezeten belüli hálózati proxy-implementációk vagy az identitás-szolgáltató és az erőforrás-szolgáltató közötti helytelen IPv4/IPv6-konfigurációk miatt fordulhat elő. Például:

- Az identitás-szolgáltató egy IP-címet lát az ügyféltől.
- Az erőforrás-szolgáltató egy másik IP-címet lát az ügyféltől a proxyn keresztüli továbbítás után.
- Az Identitáskezelő által megadott IP-cím egy engedélyezett IP-címtartomány része a házirendben, de az erőforrás-szolgáltató IP-címe nem.

Ha ez a forgatókönyv a végtelen hurkok elkerülése érdekében a környezetben található, az Azure AD egy órás CAE-tokent fog kiadni, és nem kényszeríti ki az ügyfél helyének módosítását. Még ebben az esetben is javul a biztonság, mint a hagyományos egy órás tokenek esetében, mivel továbbra is értékeljük a [többi eseményt](#critical-event-evaluation) az ügyfél helyének változási eseményei mellett.

### <a name="office-and-web-account-manager-settings"></a>Az Office és a web Account Manager beállításai

| Office frissítési csatorna | DisableADALatopWAMOverride | DisableAADWAM |
| --- | --- | --- |
| Nagyvállalati csatorna Semi-Annual | Ha az engedélyezve vagy az 1 értékre van állítva, a CAE nem támogatott. | Ha az engedélyezve vagy az 1 értékre van állítva, a CAE nem támogatott. |
| Aktuális csatorna <br> vagy <br> Havi nagyvállalati csatorna | A CAE a beállítástól függetlenül támogatott | A CAE a beállítástól függetlenül támogatott |

Az Office frissítési csatornáinak ismertetését lásd: [Microsoft 365 alkalmazások frissítési csatornáinak áttekintése](/deployoffice/overview-update-channels). Javasoljuk, hogy a szervezetek ne tiltsa le a web Account Managert (WAM).

### <a name="policy-change-timing"></a>Házirend-módosítás időzítése

Az Azure AD és az erőforrás-szolgáltatók közötti replikációs késés lehetősége miatt a rendszergazdák által végrehajtott házirend-módosítások akár 2 órát is igénybe vehetnek az Exchange Online-hoz.

Példa: a rendszergazda egy olyan házirendet hoz létre, amely letiltja az IP-címek tartományát az e-mailek eléréséhez a 11:00 ÓRAKOR, egy olyan felhasználót, aki ebből az IP-tartományból származik, és így valószínűleg továbbra is hozzáférhet 1:00 e-mailekhez

### <a name="coauthoring-in-office-apps"></a>Az Office-alkalmazások társszerzője

Ha egyszerre több felhasználó is együttműködik ugyanazon a dokumentumon, előfordulhat, hogy a felhasználó nem vonja vissza azonnal a dokumentumhoz való hozzáférést a felhasználó visszavonása vagy a házirend-változási események alapján. Ebben az esetben a felhasználó teljesen elveszti a hozzáférést a dokumentum bezárása után, bezárhatja a Word, az Excel vagy a PowerPoint alkalmazást, vagy egy 10 órás időszak után.

Ennek az időtartamnak a csökkentése érdekében a SharePoint-rendszergazda igény szerint csökkentheti a SharePoint Online-ban tárolt dokumentumok és a vállalati OneDrive közötti maximális élettartamot. ehhez [konfigurálja a hálózati hely házirendjét a SharePoint Online-ban](/sharepoint/control-access-based-on-network-location). A konfiguráció megváltozása után a beszerzői munkamenetek maximális élettartama 15 percet vesz igénybe, és tovább módosítható a "set-SPOTenant – IPAddressWACTokenLifetime" nevű SharePoint Online PowerShell-paranccsal.

### <a name="enable-after-a-user-is-disabled"></a>Engedélyezés a felhasználó letiltását követően

Ha a felhasználót a letiltást követően engedélyezi. A fiók engedélyezése némi késéssel jár. A Spongya és a csapatok 15 perces késéssel fognak rendelkezni. A késleltetés 35-40 perc az EXO esetében.

## <a name="faqs"></a>Gyakori kérdések

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Hogyan működik a CAE a bejelentkezési gyakorisággal?

A bejelentkezések gyakorisága a CAE-vel vagy anélkül is megbecsülhető.

## <a name="next-steps"></a>Következő lépések

[A folyamatos hozzáférés kiértékelésének bejelentése](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
