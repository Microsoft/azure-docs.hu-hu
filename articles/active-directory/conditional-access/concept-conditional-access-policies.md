---
title: Feltételes hozzáférési szabályzat létrehozása – Azure Active Directory
description: Milyen lehetőségek állnak rendelkezésre a feltételes hozzáférési szabályzat létrehozásához, és mit jelentenek?
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
ms.openlocfilehash: 35d2bf33b4a22c14abfb61a87a3697b05188ed31
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579093"
---
# <a name="building-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása

Ahogy azt a cikk ismerteti, [Mi a feltételes hozzáférés](overview.md), a feltételes hozzáférési szabályzat a **hozzárendelések** és a **hozzáférés-vezérlések** if-then utasítása. A feltételes hozzáférési szabályzatok együttesen, a döntések meghozatalához és a szervezeti szabályzatok betartatásához biztosítanak jeleket.

Hogyan hozza létre a szervezet ezeket a szabályzatokat? Mi szükséges? Hogyan alkalmazzák?

![Feltételes hozzáférés (jelek + döntések + kényszerítés = szabályzatok)](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

Egyszerre több feltételes hozzáférési szabályzat is alkalmazható az egyes felhasználókra. Ebben az esetben minden érvényes szabályzatnak teljesülnie kell. Ha például egy házirendhez többtényezős hitelesítés (MFA) szükséges, és egy másiknak megfelelő eszközre van szüksége, akkor az MFA-t kell elvégeznie, és egy megfelelő eszközt kell használnia. Minden hozzárendelés logikailag **ANDed**. Ha egynél több hozzárendelés van konfigurálva, minden hozzárendelésnek meg kell felelnie a szabályzat elindításának.

Az összes házirend kikényszerítve két fázisban:

- 1. fázis: a munkamenet részleteinek összegyűjtése 
   - Összegyűjtheti a munkamenet részleteit, például a hálózati hely és az eszköz identitását, amely a szabályzat kiértékeléséhez szükséges lesz. 
   - A házirend kiértékelésének 1. fázisa az engedélyezett házirendek és házirendek esetében a [csak jelentési módban](concept-conditional-access-report-only.md)történik.
- 2. fázis: kényszerítés 
   - A nem teljesített követelmények azonosításához használja az 1. fázisban összegyűjtött munkamenet részleteit. 
   - Ha van olyan házirend, amely a hozzáférés blokkolására van konfigurálva, a tiltás engedélyezése vezérlővel a kényszerítés le lesz tiltva, és a felhasználó blokkolva lesz. 
   - A rendszer felszólítja a felhasználót, hogy hajtsa végre az 1. fázisban az alábbi sorrendben nem teljesített további engedélyezési ellenőrzési követelményeket, amíg a szabályzat nem teljesül:  
      - Többtényezős hitelesítés 
      - Jóváhagyott ügyfélalkalmazás/alkalmazás-védelmi szabályzat 
      - Felügyelt eszköz (megfelelő vagy hibrid Azure AD-csatlakozás) 
      - Használati feltételek 
      - Egyéni vezérlők  
   - Ha az összes engedélyezési ellenőrzés teljesült, alkalmazza a munkamenet-vezérlőket (alkalmazás kényszerített, Microsoft Cloud App Security és jogkivonat élettartama) 
   - A házirend kiértékelésének 2. fázisa az összes engedélyezett házirend esetében bekövetkezik. 

## <a name="assignments"></a>Hozzárendelések

A hozzárendelések rész a feltételes hozzáférési szabályzatot határozza meg.

### <a name="users-and-groups"></a>Felhasználók és csoportok

A [felhasználók és csoportok](concept-conditional-access-users-groups.md) hozzárendelik a szabályzatot, vagy kizárják azokat. Ez a hozzárendelés magában foglalhatja az összes felhasználót, a felhasználók adott csoportjait, a címtárbeli szerepköröket vagy a külső vendég felhasználókat. 

### <a name="cloud-apps-or-actions"></a>Felhőalkalmazások vagy műveletek

A [felhőalapú alkalmazások vagy műveletek](concept-conditional-access-cloud-apps.md) magukban foglalhatják vagy kizárhatják a szabályzat hatálya alá eső felhőalapú alkalmazásokat vagy felhasználói műveleteket.

### <a name="conditions"></a>Feltételek

Egy házirend több [feltételt](concept-conditional-access-conditions.md)is tartalmazhat.

#### <a name="sign-in-risk"></a>Bejelentkezési kockázat

Az [Azure ad Identity Protection](../identity-protection/overview-identity-protection.md)-t használó szervezetek esetében az ott létrehozott kockázati észlelések befolyásolhatják a feltételes hozzáférési szabályzatokat.

#### <a name="device-platforms"></a>Eszközplatformok

Azok a szervezetek, amelyeken több eszköz operációs rendszere van, különböző platformokon szeretnének kikényszeríteni bizonyos szabályzatokat. 

Az eszköz platformjának kiszámításához használt információk nem ellenőrzött forrásokból, például felhasználói ügynök sztringből származnak.

#### <a name="locations"></a>Helyek

A tartózkodási hely adatai az IP térinformatikai adataival rendelkeznek. A rendszergazdák meghatározhatják a helyszíneket, és dönthetnek úgy, hogy a szervezet hálózati helyeihez hasonlóan megbízhatóként jelölik meg azokat.

#### <a name="client-apps"></a>Ügyfélalkalmazások

Alapértelmezés szerint a feltételes hozzáférési szabályzatok a modern hitelesítést támogató böngészőalapú alkalmazásokra, mobil alkalmazásokra és asztali ügyfelekre vonatkoznak. 

Ez a hozzárendelési feltétel lehetővé teszi, hogy a feltételes hozzáférési szabályzatok meghatározott ügyfélalkalmazások számára célozzanak, amelyek nem a modern hitelesítést használják. Ezek az alkalmazások közé tartoznak az Exchange ActiveSync-ügyfelek, a régebbi Office-alkalmazások, amelyek nem használnak modern hitelesítést, valamint levelezési protokollokat, például az IMAP, a MAPI, a POP és az SMTP protokollt.

#### <a name="device-state"></a>Eszköz állapota

Ez a vezérlő a hibrid Azure AD-hez csatlakoztatott vagy az Intune-ban megfelelőnek jelölt eszközök kizárására szolgál. Ezt a kizárást megteheti a nem felügyelt eszközök blokkolásához. 

## <a name="access-controls"></a>Hozzáférés-vezérlés

A feltételes hozzáférési házirend hozzáférés-vezérlési része szabályozza a szabályzatok betartatásának módját.

### <a name="grant"></a>Engedély

A [Grant](concept-conditional-access-grant.md) lehetővé teszi a rendszergazdák számára a szabályzatok betartatását, ahol letilthatják vagy megadhatják a hozzáférést.

#### <a name="block-access"></a>Hozzáférés letiltása

A hozzáférés letiltása csak azt eredményezi, hogy letiltja a hozzáférést a megadott hozzárendelések alatt. A blokk vezérlőelem hatékony, és a megfelelő ismeretekkel kell rendelkeznie.

#### <a name="grant-access"></a>Hozzáférés biztosítása

Az engedélyezési vezérlő egy vagy több vezérlő kényszerítését is elindíthatja. 

- Többtényezős hitelesítés megkövetelése (Azure AD Multi-Factor Authentication)
- Eszköz megfelelőként való megjelölésének megkövetelése (Intune)
- Hibrid Azure AD-hez csatlakoztatott eszköz megkövetelése
- Jóváhagyott ügyfélalkalmazás megkövetelése
- Alkalmazásvédelmi szabályzat megkövetelése
- Jelszó módosításának megkövetelése
- Használati feltételek megkövetelése

A rendszergazdák dönthetnek úgy, hogy az alábbi beállítások használatával megkövetelhetik az előző vezérlők vagy az összes kijelölt vezérlő egyikét. A többszörös vezérlők alapértelmezett értéke az összes megkövetelése.

- Az összes kijelölt vezérlő megkövetelése (vezérlés és vezérlés)
- A kiválasztott vezérlők (vezérlés vagy vezérlés) egyikének megkövetelése

### <a name="session"></a>Munkamenet

A [munkamenet-vezérlőelemek](concept-conditional-access-session.md) korlátozhatják a felhasználói élményt 

- Alkalmazás által kényszerített korlátozások használata
   - Jelenleg csak az Exchange Online és a SharePoint Online szolgáltatással működik.
      - Az eszköz adatainak átadásával lehetővé teszi a teljes vagy korlátozott hozzáférést biztosító élmény vezérlését.
- feltételes hozzáférést biztosító alkalmazás-vezérlő használata
   - A Microsoft Cloud App Security jeleket használ a következőkhöz hasonló módon: 
      - Bizalmas dokumentumok letöltésének, kivágásának, másolásának és nyomtatásának letiltása.
      - A kockázatos munkamenet viselkedésének figyelése.
      - Bizalmas fájlok címkézésének megkövetelése.
- Bejelentkezési gyakoriság
   - Lehetőség a modern hitelesítés alapértelmezett bejelentkezési gyakoriságának megváltoztatására.
- Állandó böngésző-munkamenet
   - Lehetővé teszi a felhasználók számára, hogy a böngészőablak bezárása és újbóli megnyitása után is bejelentkezve maradjanak.

## <a name="simple-policies"></a>Egyszerű házirendek

A feltételes hozzáférési szabályzatnak legalább a következőket kell tartalmaznia:

- A házirend **neve** .
- **Hozzárendelések**
   - **Felhasználók és/vagy csoportok** , amelyekre alkalmazni kívánja a szabályzatot.
   - **Felhőalapú alkalmazások vagy műveletek** , amelyekre alkalmazni kell a szabályzatot.
- **Hozzáférés-vezérlés**
   - Vezérlők **engedélyezése** vagy **letiltása**

![Üres feltételes hozzáférési szabályzat](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

Az [általános feltételes hozzáférési szabályzatok](concept-conditional-access-policy-common.md) a legtöbb szervezet számára hasznosnak bizonyuló szabályzatokat tartalmaznak.

## <a name="next-steps"></a>Következő lépések

[Feltételes hozzáférési szabályzat létrehozása](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json#create-a-conditional-access-policy)

[Bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával](troubleshoot-conditional-access-what-if.md)

[Felhőalapú Azure AD Multi-Factor Authentication-telepítés megtervezése](../authentication/howto-mfa-getstarted.md)

[Az eszközök megfelelőségének kezelése az Intune-nal](/intune/device-compliance-get-started)

[Microsoft Cloud App Security és feltételes hozzáférés](/cloud-app-security/proxy-intro-aad)