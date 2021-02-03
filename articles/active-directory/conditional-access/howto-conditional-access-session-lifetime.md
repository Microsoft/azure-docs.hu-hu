---
title: A hitelesítési munkamenetek kezelésének konfigurálása – Azure Active Directory
description: Az Azure AD-alapú hitelesítési munkamenet konfigurációjának testreszabása, beleértve a felhasználói bejelentkezés gyakoriságát és a böngésző munkamenetének megőrzését.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/23/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 205136437fa1a2a33b3b337f5cc9f2dde283faef
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491940"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>A hitelesítési munkamenet felügyeletének konfigurálása feltételes hozzáféréssel

Összetett központi telepítések esetén a szervezeteknek szükségük lehet a hitelesítési munkamenetek korlátozására. Egyes forgatókönyvek például a következők lehetnek:

* Erőforrás-hozzáférés nem felügyelt vagy megosztott eszközről
* Bizalmas adatokhoz való hozzáférés külső hálózatról
* Nagy hatású felhasználók
* Kritikus fontosságú üzleti alkalmazások

A feltételes hozzáférés vezérlőkkel olyan házirendeket hozhat létre, amelyek konkrét használati eseteket céloznak meg a szervezeten belül anélkül, hogy ez befolyásolná az összes felhasználót.

Mielőtt megkezdené a szabályzat konfigurálásának részleteit, vizsgáljuk meg az alapértelmezett konfigurációt.

## <a name="user-sign-in-frequency"></a>Felhasználói bejelentkezés gyakorisága

A bejelentkezési gyakoriság határozza meg azt az időtartamot, ameddig a felhasználónak újra be kell jelentkeznie, amikor egy erőforráshoz próbál hozzáférni.

A felhasználói bejelentkezés gyakoriságának Azure Active Directory (Azure AD) alapértelmezett konfigurációja a 90 napos gördülő ablak. A felhasználók a hitelesítő adatokkal való megkérdezése gyakran úgy tűnik, mint egy értelmes dolog, de nem sül el: a hitelesítő adatok megadására betanított felhasználók szándékosan nem tudják megadni őket rosszindulatú hitelesítő adatok megadásához.

Előfordulhat, hogy a felhasználók nem kérik vissza a felhasználót, hogy a valóságban az IT-szabályzatok megszegése visszavonja a munkamenetet. Néhány példa a jelszó módosítására, a nem megfelelő eszközre vagy a fiók letiltására vonatkozik. Explicit módon [visszavonhatja a felhasználói munkameneteket a PowerShell használatával](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0&preserve-view=true). Az Azure AD alapértelmezett konfigurációja a "ne kérdezze meg a felhasználókat, hogy adja meg a hitelesítő adataikat, ha a munkamenetek biztonsági helyzete nem változott".

A bejelentkezési gyakoriság beállítása olyan alkalmazásokkal működik, amelyek a szabványok alapján OAUTH2 vagy OIDC protokollokat implementáltak. A Microsoft natív alkalmazásai a Windows, Mac és Mobile rendszerekhez, beleértve a következő webalkalmazásokat is, amelyek megfelelnek a beállításnak.

- Word, Excel, PowerPoint online
- OneNote online
- Office.com
- Microsoft 365 felügyeleti portál
- Exchange Online
- SharePoint és OneDrive
- Csapat webes ügyfele
- Dynamics CRM Online
- Azure Portal

A bejelentkezési gyakoriság beállítása SAML-alkalmazásokkal is működik, feltéve, hogy nem dobja el a saját cookie-kat, és az Azure AD-re való átirányításuk rendszeresen történik.

### <a name="user-sign-in-frequency-and-multi-factor-authentication"></a>Felhasználói bejelentkezés gyakorisága és multi-Factor Authentication

Korábban csak az Azure AD-hez csatlakoztatott, a hibrid Azure AD-hez és az Azure AD-hez regisztrált eszközökön történt a bejelentkezés gyakorisága. A többtényezős hitelesítés (MFA) az adott eszközökön való újbóli betartatása nem volt egyszerű módszer. Az ügyfelek visszajelzései alapján a bejelentkezési gyakoriság az MFA-ra is érvényes lesz.

[![Bejelentkezési gyakoriság és MFA](media/howto-conditional-access-session-lifetime/conditional-access-flow-chart-small.png)](media/howto-conditional-access-session-lifetime/conditional-access-flow-chart.png#lightbox)

### <a name="user-sign-in-frequency-and-device-identities"></a>Felhasználói bejelentkezés gyakorisága és az eszközök identitása

Ha van csatlakoztatva az Azure AD-hez, a hibrid Azure AD-hez vagy az Azure AD-hez regisztrált eszközökhöz, amikor a felhasználó feloldja az eszközt, vagy interaktív módon jelentkezik be, akkor ez az esemény is megfelel a bejelentkezési gyakoriságra vonatkozó házirendnek. A következő két példában a felhasználói bejelentkezési gyakoriság értéke 1 óra:

1. példa:

- 00:00-kor a felhasználók bejelentkeznek a Windows 10 Azure AD-hez csatlakoztatott eszközre, és megkezdik a munkát a SharePoint Online-on tárolt dokumentumon.
- A felhasználó egy órán keresztül folytatja az eszközön való munkát ugyanazon a dokumentumon.
- A (z) 01:00-es időpontban a felhasználónak újra be kell jelentkeznie a bejelentkezési gyakoriság követelménye alapján a rendszergazda által konfigurált feltételes hozzáférési házirendben.

2. példa

- 00:00-kor a felhasználók bejelentkeznek a Windows 10 Azure AD-hez csatlakoztatott eszközre, és megkezdik a munkát a SharePoint Online-on tárolt dokumentumon.
- A 00:30-es időpontban a felhasználó felveszi a felhasználót, és az eszköz zárolását megszakítja.
- A (z) 00:45-es verzióban a felhasználó visszatér a helyükről, és feloldja az eszköz zárolását.
- A (z) 01:45-es időpontban a felhasználónak a bejelentkezés gyakorisága alapján újra be kell jelentkeznie a rendszergazda által az utolsó bejelentkezés óta konfigurált feltételes hozzáférési szabályzatban, a 00:45-es verziónál.

## <a name="persistence-of-browsing-sessions"></a>Böngészési munkamenetek megőrzése

Az állandó böngésző-munkamenet lehetővé teszi a felhasználók számára, hogy a böngészőablak bezárása és újbóli megnyitása után is bejelentkezve maradjanak.

Az Azure AD alapértelmezett böngésző-munkamenetének megőrzése lehetővé teszi, hogy a személyes eszközökön lévő felhasználók kiválaszthatják, hogy a "Stay bejelentkezett?" kifejezéssel megmaradnak-e a munkamenetek. sikeres hitelesítés után Rákérdezés. Ha a böngésző adatmegőrzési funkciója be van állítva a AD FS [AD FS az egyes Sign-On beállítások](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
)című cikk útmutatását követve, akkor az adott szabályzatot is betartjuk, és az Azure ad-munkamenet is megmarad. Azt is beállíthatja, hogy a bérlő felhasználói lássák-e a "Stay bejelentkezve?" című részt. az [Azure ad bejelentkezési oldal testreszabása](../fundamentals/customize-branding.md)című cikk útmutatását követve megváltoztathatja a megfelelő beállítást a vállalati védjegyezés panelen Azure Portal.

## <a name="configuring-authentication-session-controls"></a>A hitelesítési munkamenet vezérlőinek konfigurálása

A feltételes hozzáférés prémium szintű Azure AD képesség, és prémium szintű licencre van szükség. Ha többet szeretne megtudni a feltételes hozzáférésről, tekintse meg a [Mi a feltételes hozzáférés Azure Active Directory?](overview.md#license-requirements) című témakört.

> [!WARNING]
> Ha a [konfigurálható jogkivonat élettartama](../develop/active-directory-configurable-token-lifetimes.md) funkciót jelenleg nyilvános előzetes verzióban használja, vegye figyelembe, hogy nem támogatott két különböző szabályzat létrehozása ugyanahhoz a felhasználóhoz vagy alkalmazás-kombinációhoz: egyet ezzel a szolgáltatással, és egy másik, konfigurálható jogkivonat-élettartam funkciót. A Microsoft visszavonta a hitelesítő jogkivonat élettartama funkciót a frissítéshez és a munkamenet-jogkivonat élettartamához a 2021. január 30-án, és lecserélte a feltételes hozzáférés hitelesítési munkamenet-kezelési funkciójával.  
>
> A bejelentkezési gyakoriság engedélyezése előtt győződjön meg arról, hogy az egyéb újrahitelesítési beállítások le vannak tiltva a bérlőben. Ha a "Remember MFA on Trusted Devices" beállítás engedélyezve van, a bejelentkezési gyakoriság használata előtt tiltsa le a szolgáltatást, mivel a két beállítás együttes használata váratlanul kéri a felhasználókat. Ha többet szeretne megtudni az újrahitelesítéssel kapcsolatos kérésekről és a munkamenetek élettartamáról, tekintse meg az [újrahitelesítéssel kapcsolatos tudnivalókat és az Azure AD multi-Factor Authentication munkamenet-élettartamának megismerését](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)ismertető cikket.

### <a name="policy-1-sign-in-frequency-control"></a>1. szabályzat: a bejelentkezés gyakoriságának szabályozása

1. Új szabályzat létrehozása
1. Válassza ki az ügyfél környezetének összes kötelező feltételét, beleértve a felhőalapú alkalmazásokat is.

   > [!NOTE]
   > A legjobb felhasználói élmény érdekében javasoljuk, hogy a kulcs Microsoft Office alkalmazások, például az Exchange Online és a SharePoint Online esetében az egyenlő hitelesítési kérések gyakoriságát adja meg.

1. Nyissa meg a **hozzáférés-vezérlési**  >  **munkamenetet** , és kattintson a **bejelentkezési gyakoriság** lehetőségre.
1. Adja meg az első szövegmezőben a napok és órák szükséges értékét
1. Válassza ki az **órák** vagy **napok** értékét a legördülő listából
1. Szabályzat mentése

![Bejelentkezési gyakoriságra konfigurált feltételes hozzáférési szabályzat](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

Az Azure AD-ben regisztrált Windows-eszközökön jelentkezzen be az eszközre. Ha például az Office-alkalmazások esetében 24 órán keresztül konfigurálta a bejelentkezési gyakoriságot, akkor az Azure AD-ben regisztrált Windows-eszközökön lévő felhasználók a bejelentkezési gyakorisági szabályzatot az eszközre való bejelentkezéssel fogják kielégíteni, és az Office-alkalmazások megnyitásakor nem fognak megjelenni.

### <a name="policy-2-persistent-browser-session"></a>2. szabályzat: állandó böngésző-munkamenet

1. Új szabályzat létrehozása
1. Válassza ki az összes szükséges feltételt.

   > [!NOTE]
   > Vegye figyelembe, hogy ehhez a vezérlőhöz a "minden felhőalapú alkalmazás" lehetőséget kell választania feltételként. A böngésző-munkamenet megőrzését a hitelesítési munkamenet tokenje vezérli. A böngésző-munkamenet összes lapja egyetlen munkamenet-jogkivonattal rendelkezik, ezért mindegyiknek meg kell egyeznie az adatmegőrzési állapottal.

1. Nyissa meg a **hozzáférés-vezérlési**  >  **munkamenetet** , és kattintson az **állandó böngésző munkamenet**
1. Válasszon ki egy értéket a legördülő listából
1. Szabályzat mentése

![Állandó böngészőre konfigurált feltételes hozzáférési szabályzat](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Az Azure AD feltételes hozzáférés állandó böngésző-munkamenet-konfigurációja felülírja a "Stay bejelentkezve?" Ha mindkét szabályzatot konfigurálta, akkor a Azure Portal vállalati védjegyezése ablaktáblán állítsa be ugyanezt a felhasználót.

## <a name="validation"></a>Érvényesítés

A What-If eszköz használatával szimulálhatja a felhasználótól a célalkalmazás és más feltételek bejelentését a szabályzat beállítása alapján. A hitelesítési munkamenetek felügyeleti vezérlői az eszköz eredményében jelennek meg.

![Feltételes hozzáférés What If eszköz eredményei](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Szabályzat érvénybe léptetése

Annak ellenőrzéséhez, hogy a házirend a várt módon működik-e, az ajánlott eljárás az, hogy tesztelje azt az éles üzemben való működés előtt. Ideális esetben egy tesztelési bérlő használatával ellenőrizheti, hogy az új szabályzat a kívánt módon működik-e. További információkért tekintse meg a [feltételes hozzáférés központi telepítésének megtervezése](plan-conditional-access.md)című cikket.

## <a name="next-steps"></a>Következő lépések

* Ha készen áll a környezet feltételes hozzáférési házirendjeinek konfigurálására, tekintse meg a [feltételes hozzáférés központi telepítésének megtervezése](plan-conditional-access.md)című cikket.