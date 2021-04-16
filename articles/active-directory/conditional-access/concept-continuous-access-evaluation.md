---
title: Folyamatos hozzáférés-kiértékelés az Azure AD-ban
description: Gyorsabban reagálhat a felhasználói állapot változásaira az Azure AD folyamatos hozzáférés-kiértékelése segítségével
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
ms.openlocfilehash: 74009759bb9ca2a0516148fc1387b150b67452ab
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387904"
---
# <a name="continuous-access-evaluation"></a>Folyamatos hozzáférés-kiértékelés

A jogkivonatok lejárata és frissítése szabványos mechanizmus az iparágban. Ha egy ügyfélalkalmazás, például az Outlook egy olyan szolgáltatáshoz csatlakozik, mint az Exchange Online, az API-kérések OAuth 2.0 hozzáférési jogkivonatokkal vannak engedélyezve. Alapértelmezés szerint ezek a hozzáférési jogkivonatok egy óráig érvényesek, amikor lejárnak, az ügyfél vissza lesz irányítva az Azure AD-be a frissítésük érdekében. Ez a frissítési időszak lehetőséget nyújt a felhasználói hozzáférés szabályzatának újraértékelésére. Dönthetünk például úgy, hogy nem frissítjük a jogkivonatot egy feltételes hozzáférési szabályzat miatt, vagy mert a felhasználó le lett tiltva a címtárban. 

Az ügyfelek aggodalmakat fejeznek ki a felhasználó feltételeinek megváltozása, például a hálózati hely vagy a hitelesítő adatok ellopása közötti késés, valamint a változással kapcsolatos szabályzatok kényszeríthetővé tevése között. A jogkivonatok élettartamának csökkentése érdekében már kísérleteztünk az "objektum" megközelítéssel, de úgy találtuk, hogy a kockázatok kiküszöbölése nélkül csökkenthetik a felhasználói élményt és a megbízhatóságot.

A szabályzatsértésekre vagy biztonsági problémákra adott időben történő reagáláshoz valóban szükség van egy "beszélgetésre" a jogkivonat-kibocsátó, például az Azure AD és a függő fél, például az Exchange Online között. Ez a kétútos beszélgetés két fontos képességet biztosít. A függő fél észreveheti, ha valami megváltozott, például egy új helyről érkező ügyfél, és tudathatja a jogkivonat kibocsátójával. Lehetővé teszi továbbá a jogkivonat-kibocsátó számára, hogy a fiók biztonsága, letiltása vagy más problémák miatt a függő fél ne tartsa tiszteletben a jogkivonatokat egy adott felhasználónál. Ennek a beszélgetésnek a mechanizmusa a folyamatos hozzáférés kiértékelése (CAE). A cél az, hogy a válasz közel valós idejű legyen, de bizonyos esetekben akár 15 perces késés is megfigyelhető lehet az események propagálási ideje miatt.

A folyamatos hozzáférés-kiértékelés kezdeti megvalósítása az Exchange-et, a Teamset és a SharePoint Online-t összpontosítja.

Az alkalmazások CAE használatára való előkészítéséhez lásd: [Folyamatos hozzáférés-kiértékelés api-k használata az alkalmazásokban.](../develop/app-resilience-continuous-access-evaluation.md)

### <a name="key-benefits"></a>Főbb előnyök

- Felhasználó megszakítása vagy jelszó módosítása/visszaállítása: A felhasználói munkamenet visszavonása közel valós időben lesz kényszerítve.
- Hálózati hely módosítása: A feltételes hozzáférési hely szabályzata közel valós időben lesz kényszerítve.
- A jogkivonatok megbízható hálózaton kívüli gépekre való exportálása a feltételes hozzáférési hely szabályzatával megakadályozható.

## <a name="scenarios"></a>Forgatókönyvek 

A folyamatos hozzáférés kiértékelése, a kritikus események kiértékelése és a feltételes hozzáférési szabályzatok kiértékelése két forgatókönyvből áll.

### <a name="critical-event-evaluation"></a>Kritikus események kiértékelése

A folyamatos hozzáférés-kiértékelés úgy valósul meg, hogy lehetővé teszi az olyan szolgáltatások, mint az Exchange Online, a SharePoint Online és a Teams számára, hogy kritikus eseményekre iratkoztanak fel az Azure AD-ben, hogy az események közel valós időben értékelhetőek és kényszeríthetőek ki. A kritikus események kiértékelése nem függ a feltételes hozzáférési szabályzatoktól, így bármely bérlőben elérhető. Jelenleg a következő eseményeket értékeli ki a rendszer:

- Felhasználói fiók törölve vagy letiltva
- A felhasználó jelszava megváltozott vagy alaphelyzetbe lett állítva
- A többtényezős hitelesítés engedélyezve van a felhasználó számára
- A rendszergazda explicit módon visszavonja egy felhasználó összes frissítési jogkivonatát
- A felhasználók által észlelt magas Azure AD Identity Protection

Ez a folyamat lehetővé teszi, hogy Microsoft 365 kritikus események egyike után néhány percen belül a felhasználók elveszítik a vállalati SharePoint Online-fájlokhoz, e-mailhez, naptárhoz vagy feladatokhoz, valamint a Teamshez való hozzáférést. 

> [!NOTE] 
> A Teams és a SharePoint Online egyelőre nem támogatja a felhasználói kockázati eseményeket.

### <a name="conditional-access-policy-evaluation-preview"></a>Feltételes hozzáférési szabályzat kiértékelése (előzetes verzió)

Az Exchange és a SharePoint képes szinkronizálni a kulcs feltételes hozzáférési szabályzatokat, így azok a szolgáltatáson belül is kiértékelhetőek.

Ez a folyamat lehetővé teszi, hogy a felhasználók közvetlenül a hálózati hely megváltozása után elveszítik a vállalati fájlokhoz, e-mailhez, naptárhoz vagy feladatokhoz való Microsoft 365 az ügyfélalkalmazásból vagy a SharePoint Online-ból.

> [!NOTE]
> Nem minden alkalmazás- és erőforrás-szolgáltató kombináció támogatott. Lásd az alábbi táblázatot. Az Office a Wordre, az Excelre és a PowerPointra vonatkozik.

| | Outlook Web | Outlook Win32 | Outlook iOS | Outlook Android | Outlook Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **szabályozásáról** | Támogatott | Támogatott | Támogatott | Támogatott | Támogatott |
| **Exchange Online** | Támogatott | Támogatott | Támogatott | Támogatott | Támogatott |

| | Office-webalkalmazások | Office Win32-alkalmazások | iOS-hez való Office | Androidhoz készült Office | Office for Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **szabályozásáról** | Nem támogatott | Támogatott | Támogatott | Támogatott | Támogatott |
| **Exchange Online** | Nem támogatott | Támogatott | Támogatott | Támogatott | Támogatott |

| | OneDrive web | OneDrive Win32 | OneDrive iOS | OneDrive Android | OneDrive Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **szabályozásáról** | Támogatott | Támogatott | Támogatott | Támogatott | Támogatott |

### <a name="client-side-claim-challenge"></a>Ügyféloldali jogcímre vonatkozó kérdés

A folyamatos hozzáférés kiértékelése előtt az ügyfelek mindig megpróbálják visszajátszani a hozzáférési jogkivonatot a gyorsítótárból, ha az még nem járt le. A CAE-val egy új esetet vezetünk be, amely szerint az erőforrás-szolgáltató akkor is elutasíthatja a jogkivonatot, ha az még nem járt le. Annak érdekében, hogy tájékoztassa az ügyfeleket a gyorsítótár megkerülésükről annak  ellenére, hogy a gyorsítótárazott jogkivonatok még nem lejártak, bevezetünk egy jogcímkérő mechanizmust, amely jelzi, hogy a jogkivonatot el lett utasítva, és az Azure AD-nek új hozzáférési jogkivonatot kell kiadnia. A CAE-nek szüksége van egy ügyfélfrissítésre a jogcímek igénylési kérdésének ért. Az alábbi alkalmazások legújabb verziója támogatja az igénylési kérelmet:

| | Webes | Win32 | iOS | Android | Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Outlook** | Támogatott | Támogatott | Támogatott | Támogatott | Támogatott |
| **Teams** | Támogatott | Támogatott | Támogatott | Támogatott | Támogatott |
| **Office** | Nem támogatott | Támogatott | Támogatott | Támogatott | Támogatott |
| **OneDrive** | Támogatott | Támogatott | Támogatott | Támogatott | Támogatott |

### <a name="token-lifetime"></a>Jogkivonat élettartama

Mivel a kockázat és a szabályzat valós időben van kiértékelve, a folyamatos hozzáférés-kiértékelési munkameneteket egyeztető ügyfelek a meglévő statikus hozzáférési jogkivonatok élettartam-házirendje helyett a CAE-t fogják használni, ami azt jelenti, hogy a konfigurálható jogkivonat-élettartam-házirendet a CAE-kompatibilis ügyfelek esetében, amelyek CAE-kompatibilis munkameneteket egyeztetnek.

A token élettartama a CAE-munkamenetekben hosszabb, akár 28 órás élettartamra nő. A visszavonás nem csak egy tetszőleges időszakon, de kritikus eseményeken és szabályzatértékelésen is átesik. Ez a változás növeli az alkalmazások stabilitását anélkül, hogy ez hatással lenne a biztonsági állásra. 

Ha nem használ CAE-kompatibilis ügyfeleket, a hozzáférési jogkivonat alapértelmezett élettartama 1 óra marad, hacsak nem konfigurálta a hozzáférési jogkivonat élettartamát a [Configurable Token Lifetime (CTL) előzetes](../develop/active-directory-configurable-token-lifetimes.md) verziójú funkcióval.

## <a name="example-flows"></a>Példafolyamatok

### <a name="user-revocation-event-flow"></a>Felhasználó-visszavonási eseményfolyam:

![Felhasználó-visszavonási eseményfolyam](./media/concept-continuous-access-evaluation/user-revocation-event-flow.png)

1. A CAE-kompatibilis ügyfél bemutatja a hitelesítő adatokat vagy egy frissítési jogkivonatot az Azure AD-nek, és hozzáférési jogkivonatot kér bizonyos erőforrásokhoz.
1. A rendszer egy hozzáférési jogkivonatot ad vissza az ügyfélnek a többi összetevővel együtt.
1. A rendszergazda explicit [módon visszavonja a felhasználó összes frissítési jogkivonatát.](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) Az Azure AD egy visszavonási eseményt küld az erőforrás-szolgáltatónak.
1. A rendszer egy hozzáférési jogkivonatot mutat be az erőforrás-szolgáltatónak. Az erőforrás-szolgáltató kiértékeli a jogkivonat érvényességét, és ellenőrzi, hogy van-e visszavonási esemény a felhasználó számára. Az erőforrás-szolgáltató ezt az információt felhasználva dönti el, hogy hozzáférést ad-e az erőforráshoz.
1. Ebben az esetben az erőforrás-szolgáltató megtagadja a hozzáférést, és egy több mint 401-es, jogcímekkel kapcsolatos kérést küld vissza az ügyfélnek.
1. A CAE-kompatibilis ügyfél tisztában van a 401+-es igényigényekkel. Megkerüli a gyorsítótárat, és visszalép az 1. lépésre, és visszaküldi a frissítési jogkivonatát a jogcímekkel együtt az Azure AD-nek. Az Azure AD ezután újraértékeli az összes feltételt, és felkéri a felhasználót, hogy ebben az esetben újrahitelesítsen.

### <a name="user-condition-change-flow-preview"></a>Felhasználói feltételek változási folyamatának (előzetes verzió):

A következő példában a feltételes hozzáférés rendszergazdája úgy konfigurált egy helyalapú feltételes hozzáférési szabályzatot, hogy csak bizonyos IP-tartományokból engedélyezze a hozzáférést:

![Felhasználói feltételek eseményfolyama](./media/concept-continuous-access-evaluation/user-condition-change-flow.png)

1. A CAE-kompatibilis ügyfél bemutatja a hitelesítő adatokat vagy egy frissítési jogkivonatot az Azure AD-nek, és hozzáférési jogkivonatot kér bizonyos erőforrásokhoz.
1. Az Azure AD kiértékeli az összes feltételes hozzáférési szabályzatot, hogy lássa, hogy a felhasználó és az ügyfél megfelel-e a feltételeknek.
1. A rendszer egy hozzáférési jogkivonatot ad vissza az ügyfélnek a többi összetevővel együtt.
1. A felhasználó egy engedélyezett IP-címtartományon kívülre kerül
1. Az ügyfél egy engedélyezett IP-címtartományon kívülről származó hozzáférési jogkivonatot biztosít az erőforrás-szolgáltatónak.
1. Az erőforrás-szolgáltató kiértékeli a jogkivonat érvényességét, és ellenőrzi az Azure AD-ból szinkronizált hely szabályzatot.
1. Ebben az esetben az erőforrás-szolgáltató megtagadja a hozzáférést, és egy több mint 401-es, jogcímekkel kapcsolatos kérést küld vissza az ügyfélnek, mert az nem az engedélyezett IP-címtartományból érkezik.
1. A CAE-kompatibilis ügyfél tisztában van a 401+-es igényigényekkel. Megkerüli a gyorsítótárat, és visszalép az 1. lépésre, és visszaküldi a frissítési jogkivonatát a jogcímekkel együtt az Azure AD-nek. Az Azure AD újraértékeli az összes feltételt, és ebben az esetben megtagadja a hozzáférést.

## <a name="enable-or-disable-cae-preview"></a>CAE engedélyezése vagy letiltása (előzetes verzió)

1. Jelentkezzen be a **Azure Portal** feltételes hozzáférésű rendszergazdaként, biztonsági rendszergazdaként vagy globális rendszergazdaként
1. Lépjen a **Azure Active Directory**  >  **folyamatos hozzáférés**  >  **kiértékelése lapra.**
1. Válassza az **Előzetes verzió engedélyezése lehetőséget.**

Ezen az oldalon korlátozhatja az előzetes verzióban érintett felhasználókat és csoportokat.

![A CAE előzetes verzió engedélyezése a Azure Portal](./media/concept-continuous-access-evaluation/enable-cae-preview.png)

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="supported-location-policies"></a>Támogatott hely szabályzatok

A CAE-hez csak az IP-alapú nevestű helyekre vonatkozó információk adatokat biztosítunk. Nincsenek információk más helybeállításokról, például [az MFA](../authentication/howto-mfa-mfasettings.md#trusted-ips) megbízható IP-jéről vagy az országalapú helyekről. Ha a felhasználó MFA megbízható IP-címről vagy megbízható helyekről származik, amelyek MFA megbízható IP-címeket vagy az ország helyét tartalmazzák, a CAE nem lesz kényszerítve a felhasználó másik helyre való áthelyezése után. Ilyen esetekben egy 1 órás CAE-jogkivonatot ad ki az IP-cím azonnali kényszerítési ellenőrzése nélkül.

> [!IMPORTANT]
> A helyek folyamatos hozzáférés-értékeléshez való konfigurálásakor csak az [IP-alapú](../conditional-access/location-condition.md) feltételes hozzáférés helyének feltételét használja, és konfigurálja az identitásszolgáltató és az erőforrás-szolgáltató által látható összes IP-címet, beleértve az IPv4-et és az **IPv6-ot** is. Ne használja az ország helyére vonatkozó feltételeket vagy az Azure AD Multi-Factor Authentication szolgáltatásbeállítási oldalán elérhető megbízható IP-címek funkciót.

### <a name="ip-address-configuration"></a>IP-címkonfiguráció

Az identitásszolgáltató és az erőforrás-szolgáltatók különböző IP-címeket láthatnak. Ez az eltérés a szervezetben a hálózati proxy implementációja vagy az identitásszolgáltató és az erőforrás-szolgáltató közötti helytelen IPv4/IPv6-konfigurációk miatt fordulhat elő. Például:

- Az identitásszolgáltató egy IP-címet lát az ügyféltől.
- Az erőforrás-szolgáltató egy proxyn keresztüli átadás után az ügyféltől eltérő IP-címet lát.
- Az identitásszolgáltató által látható IP-cím egy engedélyezett IP-címtartomány része a szabályzatban, de az erőforrás-szolgáltató IP-címe nem.

Ha ez a forgatókönyv a végtelen hurkok elkerülése érdekében létezik a környezetben, az Azure AD egy órányi CAE-jogkivonatot ad ki, és nem kényszeríti ki az ügyfél helyének változását. Még ebben az esetben is javul a biztonság a hagyományos egyórás tokenekkel szemben, mivel az ügyfélhely-módosítási események mellett a többi eseményt is kiértékeljük. [](#critical-event-evaluation)

### <a name="office-and-web-account-manager-settings"></a>Office- és Webfiókkezelő beállításai

| Office frissítési csatorna | DisableADALatopWAMOverride | DisableAADWAM |
| --- | --- | --- |
| Semi-Annual Enterprise Channel | Ha engedélyezve van vagy 1, a CAE nem támogatott. | Ha engedélyezve van vagy 1, a CAE nem támogatott. |
| Aktuális csatorna <br> vagy <br> Havi nagyvállalati csatorna | A CAE a beállítástól függetlenül támogatott | A CAE a beállítástól függetlenül támogatott |

Az irodai frissítési csatornák magyarázatát lásd: [A Microsoft 365-frissítési csatornák áttekintése.](/deployoffice/overview-update-channels) Javasoljuk, hogy a szervezetek ne tiltsák le a Webfiókkezelő (WAM).

### <a name="group-membership-and-policy-update-effective-time"></a>Csoporttagság és szabályzatfrissítés hatályos ideje

A csoporttagság és a rendszergazdák által készített szabályzatfrissítések hatékonysága akár egy napig is eltenhet. A szabályzatfrissítések optimalizálása megtörtént, ami két órára csökkenti a késést. Ez azonban még nem fedi le az összes forgatókönyvet. 

Ha vészhelyzet van, és frissítenie kell a szabályzatokat, vagy azonnal alkalmaznia kell a csoporttagságot bizonyos felhasználókra, ezt a [PowerShell-parancsot](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) vagy a "Munkamenet visszavonása" parancsot kell használnia a felhasználói profil oldalán a felhasználói munkamenet visszavonásához, ami lehetővé teszi, hogy a frissített szabályzatok alkalmazása azonnal megkezdődik.

### <a name="coauthoring-in-office-apps"></a>Koauthoring az Office-alkalmazásokban

Ha egyszerre több felhasználó is dolgozik ugyanazon a dokumentumon, előfordulhat, hogy a CAE nem vonja vissza azonnal a felhasználó hozzáférését a dokumentumhoz a felhasználó visszavonási vagy szabályzatváltozási eseményei alapján. Ebben az esetben a felhasználó teljesen elveszíti a hozzáférést a dokumentum bezárása, a Word, az Excel vagy a PowerPoint bezárása után, vagy 10 óra múlva.

Ennek az időnek a csökkentése érdekében a SharePoint-rendszergazda igény szerint csökkentheti a SharePoint Online-ban és a OneDrive Vállalati verzió-ban tárolt dokumentumok társszerzői munkamenetének maximális élettartamát egy hálózatihely-szabályzat sharePoint Online-ban való [konfigurálásával.](/sharepoint/control-access-based-on-network-location) A konfiguráció megváltozása után a koauthoring munkamenetek maximális élettartama 15 percre csökken, és a "Set-SPOTenant –IPAddressWACTokenLifetime" SharePoint Online PowerShell-paranccsal módosítható.

### <a name="enable-after-a-user-is-disabled"></a>Engedélyezés egy felhasználó letiltás után

Ha közvetlenül a letiltás után engedélyezi a felhasználót. A fiók engedélyezése némi késéssel érhető el. Az SPO és a Teams késése 15 perc. Az EXO esetén a késleltetés 35–40 perc.

## <a name="faqs"></a>Gyakori kérdések

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Hogyan működik a CAE a bejelentkezés gyakoriságával?

A bejelentkezés gyakoriságát a CAE-val vagy anélkül is be lehet fogni.

## <a name="next-steps"></a>Következő lépések

[Folyamatos hozzáférés-kiértékelés bejelentése](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
