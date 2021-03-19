---
title: Feltételek a feltételes hozzáférési házirendben – Azure Active Directory
description: Az Azure AD feltételes hozzáférési szabályzatának feltételei
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab840b46503aed1a318e3b39a4e8fe3e4d11735c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579127"
---
# <a name="conditional-access-conditions"></a>Feltételes hozzáférés: feltételek

Egy feltételes hozzáférési szabályzaton belül a rendszergazda olyan feltételektől veheti igénybe a jeleket, mint például a kockázat, az eszköz platformja vagy a hely a szabályzatokkal kapcsolatos döntések javítása érdekében. 

[![Feltételes hozzáférési szabályzat definiálása és feltételek megadása](./media/concept-conditional-access-conditions/conditional-access-conditions.png)](./media/concept-conditional-access-conditions/conditional-access-conditions.png#lightbox)

A részletes és a feltételes hozzáférési szabályzatok létrehozásához több feltétel is felhasználható.

Ha például egy bizalmas alkalmazáshoz fér hozzá, a rendszergazda az Identity Protectionből és a helyről származó bejelentkezési kockázati információkat is felhasználhatja a hozzáférési döntésbe más vezérlők, például a többtényezős hitelesítés mellett.

## <a name="sign-in-risk"></a>Bejelentkezési kockázat

Az [Identity Protection](../identity-protection/overview-identity-protection.md)szolgáltatáshoz hozzáféréssel rendelkező ügyfelek esetében a bejelentkezési kockázat kiértékelése feltételes hozzáférési szabályzat részeként lehetséges. A bejelentkezési kockázat azt jelzi, hogy egy adott hitelesítési kérelem nem jogosult az identitás tulajdonosa számára. A bejelentkezési kockázattal kapcsolatos további információkért tekintse meg a cikkekben, a [kockázatokról](../identity-protection/concept-identity-protection-risks.md#sign-in-risk) és [a kockázatkezelési szabályzatok konfigurálásáról és engedélyezéséről](../identity-protection/howto-identity-protection-configure-risk-policies.md)szóló cikket.

## <a name="user-risk"></a>Felhasználói kockázat 

Az [Identity Protection](../identity-protection/overview-identity-protection.md)szolgáltatáshoz hozzáféréssel rendelkező ügyfelek esetében a felhasználói kockázat a feltételes hozzáférési szabályzat részeként értékelhető ki. A felhasználói kockázat azt jelzi, hogy egy adott identitás vagy fiók biztonsága sérül. A felhasználói kockázatokról további információt a következő cikkekben talál: [a kockázatkezelés és](../identity-protection/concept-identity-protection-risks.md#user-risk) [a kockázatkezelési szabályzatok konfigurálása és engedélyezése](../identity-protection/howto-identity-protection-configure-risk-policies.md).

## <a name="device-platforms"></a>Eszközplatformok

Az eszköz platformját az eszközön futó operációs rendszer jellemzi. Az Azure AD a platformot az eszköz által biztosított információk, például a felhasználói ügynök karakterláncai segítségével azonosítja. Mivel a felhasználói ügynök karakterláncai módosíthatók, ez az információ nem ellenőrzött. Az eszköz platformját Microsoft Intune eszköz megfelelőségi házirendjeivel vagy egy blokk-utasítás részeként kell használni. Az alapértelmezett érték az összes eszköz platformra vonatkozik.

Az Azure AD feltételes hozzáférés a következő eszköz platformokat támogatja:

- Android
- iOS
- Windows Phone
- Windows
- macOS

Ha letiltja az örökölt hitelesítést a **többi ügyfél** feltételének használatával, beállíthatja az eszköz platformjának feltételeit is.

> [!IMPORTANT]
> A Microsoft azt javasolja, hogy a nem támogatott eszköz platformokra vonatkozó feltételes hozzáférési szabályzattal rendelkezzen. Ha például le szeretné tiltani a vállalati erőforrásokhoz való hozzáférést a Linux vagy bármely más nem támogatott ügyfél esetében, akkor olyan házirendet kell konfigurálnia egy eszköz-platformmal, amely bármilyen eszközt tartalmaz, és kizárja a támogatott eszközök platformját, és letiltja a hozzáférést a hozzáférés blokkolásához.

## <a name="locations"></a>Helyek

A hely feltételként való konfigurálásakor a szervezetek dönthetnek úgy, hogy kiveszik vagy kizárják a helyeket. Ezek a nevesített helyszínek tartalmazhatják a nyilvános IPv4-hálózati információkat, az országot vagy a régiót, vagy akár ismeretlen területeket, amelyek nem adott országokra vagy régiókra vannak leképezve. Csak az IP-címtartományok lehet megjelölve megbízható helyként.

Ha **bármilyen helyet** tartalmaz, ez a beállítás az interneten található, nem csak az elnevezett helyeket konfigurált IP-címeket tartalmazza. **Bármely hely** kiválasztásakor a rendszergazdák dönthetnek úgy, hogy kizárják az **összes megbízható** vagy **kiválasztott helyet**.

Előfordulhat például, hogy egyes szervezetek nem igénylik a többtényezős hitelesítést, ha a felhasználók megbízható helyen, például a fizikai központban csatlakoznak a hálózathoz. A rendszergazdák létrehozhatnak egy olyan szabályzatot, amely bármilyen helyet tartalmaz, de kizárja a kijelölt helyeket a saját főhadiszállása hálózatához.

A helyekkel kapcsolatos további információkért tekintse meg a következő témakört: a [feltételes hozzáférés Azure Active Directory a hely feltétele](location-condition.md).

## <a name="client-apps"></a>Ügyfélalkalmazások

Alapértelmezés szerint az összes újonnan létrehozott feltételes hozzáférési szabályzat minden ügyfélalkalmazás-típusra érvényes lesz, még akkor is, ha az ügyfélalkalmazás feltétele nincs konfigurálva. 

> [!NOTE]
> Az ügyfélalkalmazások működésének állapota 2020 augusztusában frissült. Ha van meglévő feltételes hozzáférési szabályzata, akkor változatlan marad. Ha azonban egy meglévő szabályzatra kattint, a rendszer eltávolította a configure kapcsolót, és a házirend hatálya alá eső ügyfélalkalmazások ki vannak választva.

> [!IMPORTANT]
> Az örökölt hitelesítési ügyfelektől érkező bejelentkezések nem támogatják az MFA-t, és nem adják át az eszköz állapotadatok az Azure AD-nek, így a feltételes hozzáférés által biztosított vezérlők le lesznek tiltva, például MFA-vagy megfelelőségi eszközökre van szükségük. Ha olyan fiókkal rendelkezik, amelyeknek örökölt hitelesítést kell használniuk, ki kell zárnia ezeket a fiókokat a szabályzatból, vagy úgy kell beállítania a házirendet, hogy csak a modern hitelesítési ügyfelekre vonatkozzon.

Ha az **Igen** értékre van állítva, akkor a beállítás váltógomb az ellenőrzött elemekre vonatkozik, ha a **nem** értékre van **állítva az összes** ügyfélalkalmazás, beleértve a modern és a régi hitelesítési ügyfeleket is. Ez a váltógomb nem jelenik meg az augusztus 2020. előtt létrehozott szabályzatokban.

- Modern hitelesítési ügyfelek
   - Böngésző
      - Ezek közé tartoznak a webes alkalmazások, amelyek olyan protokollokat használnak, mint például az SAML, a WS-Federation, az OpenID Connect vagy a OAuth-alapú bizalmas ügyfélként regisztrált szolgáltatások.
   - Mobile apps és asztali ügyfelek
      -  Ez a beállítás olyan alkalmazásokat is tartalmaz, mint például az Office asztali és a telefonos alkalmazások.
- Örökölt hitelesítési ügyfelek
   - Exchange ActiveSync-ügyfelek
      - Ez magában foglalja az Exchange ActiveSync (EAS) protokoll használatát.
      - Ha a házirend letiltja az Exchange ActiveSync használatát, az érintett felhasználó egyetlen karanténba helyezett e-mailt fog kapni. Ez az e-mail-cím tartalmazza a blokkolásuk okát, és ha tud, szervizelési utasításokat is tartalmaz.
      - A rendszergazdák a feltételes hozzáférési MS Graph API használatával csak a támogatott platformokra (például iOS, Android és Windows rendszerekre) alkalmazhatnak szabályzatot.
   - Más ügyfelek
      - Ez a beállítás olyan ügyfeleket tartalmaz, amelyek olyan alapszintű/örökölt hitelesítési protokollokat használnak, amelyek nem támogatják a modern hitelesítést.
         - Hitelesített SMTP – a POP és az IMAP-ügyfél által használt e-mail üzenetek küldésére használható.
         - Automatikus észlelés – az Outlook és az EAS-ügyfelek által használt postaládák keresése és kapcsolódás az Exchange Online-ban.
         - Exchange Online PowerShell – az Exchange Online-hoz való kapcsolódáshoz használt távoli PowerShell-lel. Ha letiltja az Exchange Online PowerShell alapszintű hitelesítését, a kapcsolódáshoz az Exchange Online PowerShell-modult kell használnia. Útmutatásért lásd: [Kapcsolódás az Exchange Online powershellhez a multi-Factor Authentication használatával](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
         - Exchange Web Services (EWS) – az Outlook, az Outlook for Mac és a harmadik féltől származó alkalmazások által használt programozási felület.
         - IMAP4 – az IMAP e-mail ügyfélprogramok által használt.
         - MAPI HTTP-n keresztül (MAPI/HTTP) – az Outlook 2010-es és újabb verzióiban használt.
         - Offline címjegyzék (OAB) – az Outlook által letöltött és használt címlista-gyűjtemények másolata.
         - Outlook Anywhere (HTTP-n keresztüli RPC) – az Outlook 2016-es és korábbi verzióiban használatos.
         - Outlook-szolgáltatás – a Windows 10 rendszerhez készült mail és naptár alkalmazás használja.
         - POP3 – a POP e-mail ügyfélprogramok által használt.
         - Jelentéskészítési webszolgáltatások – a Jelentésadatok beolvasására szolgálnak az Exchange Online-ban.

Ezeket a feltételeket általában a felügyelt eszköz megkövetelése, a régi hitelesítés blokkolása és a webalkalmazások blokkolása, de a mobil-vagy asztali alkalmazások engedélyezése esetén használják.

### <a name="supported-browsers"></a>Támogatott böngészők

Ez a beállítás minden böngészővel működik. Ahhoz azonban, hogy kielégítse az eszköz házirendjét, például a megfelelő eszköz követelményeit, a következő operációs rendszerek és böngészők támogatottak:

| Operációs rendszer | Böngészők |
| :-- | :-- |
| Windows 10 | Microsoft Edge, Internet Explorer, Chrome |
| Windows 8/8,1 | Internet Explorer, Chrome |
| Windows 7 | Internet Explorer, Chrome |
| iOS | Microsoft Edge, Intune Managed Browser, Safari |
| Android | Microsoft Edge, Intune Managed Browser, Chrome |
| Windows Phone | Microsoft Edge, Internet Explorer |
| Windows Server 2019 | Microsoft Edge, Internet Explorer, Chrome |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| macOS | Chrome, Safari |

> [!NOTE]
> A 85-ös Edge + használatához a felhasználónak be kell jelentkeznie a böngészőbe, hogy megfelelően átadja az eszköz identitását. Ellenkező esetben a Chrome a fiókok bővítmény nélkül viselkedik. Előfordulhat, hogy ez a bejelentkezés nem történik meg automatikusan egy hibrid Azure AD JOIN forgatókönyvben. 

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Miért jelenik meg a tanúsítvány Rákérdezés a böngészőben

Windows 7 rendszeren az iOS, az Android és a macOS Azure AD az eszközt az Azure AD-vel való regisztráláskor kiépített ügyféltanúsítvány használatával azonosítja.  Amikor a felhasználó először jelentkezik be a böngészőben, a rendszer a felhasználótól kéri a tanúsítvány kiválasztását. A felhasználónak a böngésző használata előtt ki kell választania ezt a tanúsítványt.

#### <a name="chrome-support"></a>Chrome-támogatás

A **Windows 10-es Creators Update (1703-es verzió)** vagy újabb verziók Chrome-támogatásához telepítse a [Windows 10-es fiókok bővítményt](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Erre a bővítményre akkor van szükség, ha egy feltételes hozzáférési házirend eszköz-specifikus adatokat igényel.

A bővítmény Chrome böngészőkbe való automatikus telepítéséhez hozza létre a következő beállításkulcsot:

- Elérési út HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist
- 1. név
- Típus REG_SZ (karakterlánc)
- Adatppnbnpeolgkicgegkbkbjmhlideopiji; HTTPS \: //clients2.Google.com/Service/Update2/CRX

A **Windows 8,1 és 7 rendszerhez** készült Chrome-támogatáshoz hozza létre a következő beállításkulcsot:

- Elérési út HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls
- 1. név
- Típus REG_SZ (karakterlánc)
- {"Pattern": " https://device.login.microsoftonline.com ", "filter": {"kiállító": {"CN": "MS-Organization-Access"}}}

Ezek a böngészők támogatják az eszköz hitelesítését, amely lehetővé teszi az eszköz azonosítását és érvényesítését egy szabályzattal szemben. Az eszköz-ellenőrzés sikertelen, ha a böngésző privát módban fut.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Támogatott mobil alkalmazások és asztali ügyfelek

A szervezetek kiválaszthatják a **mobil alkalmazásokat és az asztali ügyfeleket** ügyfélalkalmazásként.

Ez a beállítás hatással van a következő Mobile apps és asztali ügyfelek által kezdeményezett hozzáférési kísérletekre:

| Ügyfélalkalmazások | Cél szolgáltatás | Platform |
| --- | --- | --- |
| Dynamics CRM-alkalmazás | Dynamics CRM | Windows 10, Windows 8,1, iOS és Android |
| Mail/Calendar/People app, Outlook 2016, Outlook 2013 (modern hitelesítéssel)| Exchange Online | Windows 10 |
| MFA-és tartózkodási hely szabályzata alkalmazások számára. Az eszközökön alapuló házirendek nem támogatottak.| A saját alkalmazások app Service | Android és iOS |
| Microsoft Teams Services – Ez vezérli a Microsoft Teams és az összes ügyfélalkalmazás használatát támogató összes szolgáltatást – Windows Desktop, iOS, Android, WP és Web Client | Microsoft Teams | Windows 10, Windows 8,1, Windows 7, iOS, Android és macOS |
| Office 2016-alkalmazások, Office 2013 (modern hitelesítéssel), [OneDrive-szinkronizálási ügyfél](/onedrive/enable-conditional-access) | SharePoint | Windows 8,1, Windows 7 |
| Office 2016-alkalmazások, univerzális Office-alkalmazások, Office 2013 (modern hitelesítéssel), [OneDrive Sync Client](/onedrive/enable-conditional-access) | SharePoint Online | Windows 10 |
| Office 2016 (Word, Excel, PowerPoint, csak OneNote). | SharePoint | macOS |
| Office 2019| SharePoint | Windows 10, macOS |
| Office Mobile-alkalmazások | SharePoint | Android, iOS |
| Office Yammer-alkalmazás | Yammer | Windows 10, iOS, Android |
| Outlook 2019 | SharePoint | Windows 10, macOS |
| Outlook 2016 (macOS-es iroda) | Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (modern hitelesítéssel), Skype vállalati verzió (modern hitelesítéssel) | Exchange Online | Windows 8,1, Windows 7 |
| Outlook Mobile alkalmazás | Exchange Online | Android, iOS |
| Power BI alkalmazás | Power BI szolgáltatásban | Windows 10, Windows 8,1, Windows 7, Android és iOS |
| Skype Vállalati verzió | Exchange Online| Android, iOS |
| Visual Studio Team Services-alkalmazás | Visual Studio Team Services | Windows 10, Windows 8,1, Windows 7, iOS és Android |

### <a name="exchange-activesync-clients"></a>Exchange ActiveSync-ügyfelek

- A szervezetek csak akkor választhatják ki az Exchange ActiveSync-ügyfeleket, ha szabályzatot rendelnek hozzá felhasználókhoz vagy csoportokhoz. Az **összes felhasználó**, a **vendég és a külső felhasználók**, illetve a **címtárbeli szerepkörök** kiválasztásával az összes felhasználó a szabályzat hatálya alá kerül.
- Exchange ActiveSync-ügyfelekhez rendelt szabályzat létrehozásakor az **Exchange Online** csak a Szabályzathoz rendelt felhőalapú alkalmazás lehet. 
- A szervezetek a szabályzat hatókörét meghatározott platformokra szűkítik az **eszköz platformok** feltételének használatával.

Ha a házirendhez rendelt hozzáférés-vezérlés **jóváhagyott ügyfélalkalmazás használatát igényli**, a rendszer a felhasználót az Outlook Mobile-ügyfél telepítésére és használatára irányítja. Ha a **többtényezős hitelesítés**, **használati feltételek** vagy **Egyéni vezérlők** szükségesek, az érintett felhasználók le vannak tiltva, mert az egyszerű hitelesítés nem támogatja ezeket a vezérlőket.

További információért tekintse át a következő cikkeket:

- [Örökölt hitelesítés letiltása feltételes hozzáféréssel](block-legacy-authentication.md)
- [Jóváhagyott ügyfélalkalmazások feltételes hozzáféréssel való megkövetelése](app-based-conditional-access.md)

### <a name="other-clients"></a>Más ügyfelek

**Más ügyfelek** kiválasztásával megadhat egy olyan feltételt, amely az alapszintű hitelesítést használó alkalmazásokat, például az IMAP, a MAPI, a pop, az SMTP és a régebbi Office-alkalmazásokat használja, amelyek nem használnak modern hitelesítést.

## <a name="device-state-preview"></a>Eszköz állapota (előzetes verzió)

Az eszköz állapotának feltétele a hibrid Azure AD-hez csatlakoztatott eszközök és/vagy a szervezet feltételes hozzáférési szabályzatának Microsoft Intune megfelelőségi szabályzatának megfelelőként megjelölt eszközök kizárására használható.

Például minden olyan *felhasználó* **, aki** a *Microsoft Azure Management* Cloud alkalmazáshoz fér hozzá, beleértve az eszköz **összes ÁLLAPOTÁT** , kivéve az **eszközök hibrid Azure ad-hez csatlakoztatott** eszközét és a **megfelelőként megjelölt eszközt** , valamint a *hozzáférés-vezérlést*. 
   - Ez a példa olyan házirendet hoz létre, amely csak a hibrid Azure AD-hez csatlakoztatott vagy a megfelelőként megjelölt eszközökön engedélyezi a Microsoft Azure felügyeletét.

## <a name="next-steps"></a>Következő lépések

- [Feltételes hozzáférés: Engedélyezés](concept-conditional-access-grant.md)

- [Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)
