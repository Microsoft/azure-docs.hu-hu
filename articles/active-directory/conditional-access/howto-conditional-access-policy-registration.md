---
title: Feltételes hozzáférés – kombinált biztonsági információk – Azure Active Directory
description: Egyéni feltételes hozzáférési szabályzat létrehozása a biztonsági adatok regisztrálásához
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 03/24/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 711d4bdf2be2ad3158c12e4690a70fb83fe7a846
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559502"
---
# <a name="conditional-access-securing-security-info-registration"></a>Feltételes hozzáférés: A biztonsági információk regisztrálásának védelme

Az Azure AD-Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítás felhasználói műveleteinek biztonságossá tétele a feltételes hozzáférési szabályzatokban. Ez a funkció olyan szervezetek számára érhető el, akik engedélyezték a [kombinált regisztrációt](../authentication/concept-registration-mfa-sspr-combined.md). Ez a funkció lehetővé teszi a szervezetek számára, hogy a feltételes hozzáférési szabályzat bármely alkalmazásához hasonlóan kezeljék a regisztrációs folyamatot, és a feltételes hozzáférés teljes erejét használják a felhasználói élmény biztosításához. 

Előfordulhat, hogy a múltban egyes szervezetek megbízható hálózati helyet vagy eszköz megfelelőségét használták a regisztrációs élmény biztonságossá tételéhez. Az Azure AD-ben az [ideiglenes hozzáférés-továbbítás](../authentication/howto-authentication-temporary-access-pass.md) hozzáadásával a rendszergazdák időkorlátos hitelesítő adatokat állíthatnak be a felhasználók számára, amelyek lehetővé teszik, hogy bármely eszközről vagy helyről regisztráljanak. Az ideiglenes hozzáférési hitelesítő adatok megfelelnek a többtényezős hitelesítés feltételes hozzáférési követelményeinek.

## <a name="create-a-policy-to-secure-registration"></a>Szabályzat létrehozása a regisztráció biztonságossá tételéhez

A következő szabályzat a kiválasztott felhasználókra vonatkozik, akik a közös regisztrációs élmény használatával próbálnak regisztrálni. A szabályzat megköveteli, hogy a felhasználók többtényezős hitelesítést végezzenek, vagy ideiglenes hozzáférési hitelesítő adatokat használjanak.

1. A **Azure Portal** keresse meg **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférés** lehetőséget.
1. Válassza az **új szabályzat** lehetőséget.
1. A név mezőben adja meg a szabályzat nevét. Például a **biztonsági adatok összevont regisztrálása a** következővel: koppint.
1. A **hozzárendelések** területen válassza a **felhasználók és csoportok** lehetőséget, majd válassza ki azokat a felhasználókat és csoportokat, amelyekre alkalmazni szeretné a szabályzatot.
   1. A **Belefoglalás** területen válassza a **minden felhasználó** lehetőséget.

      > [!WARNING]
      > A felhasználók számára engedélyezni kell a [kombinált regisztrációt](../authentication/howto-registration-mfa-sspr-combined.md).

   1. A **kizárás** területen.
      1. Válassza **az összes vendég és külső felhasználó** lehetőséget.
      
         > [!NOTE]
         > Az ideiglenes hozzáférési pass nem működik a vendég felhasználói számára.

      1. Válassza a **felhasználók és csoportok** lehetőséget, és válassza ki a szervezet vészhelyzeti hozzáférését vagy az adatbontási fiókokat. 
1. A **Cloud apps vagy műveletek** területen válassza a **felhasználói műveletek** lehetőséget, és jelölje be a **biztonsági információk regisztrálása** jelölőnégyzetet.
1. A **hozzáférés-vezérlés**  >  **megadása** területen.
   1. Válassza a **Hozzáférés biztosítása** lehetőséget.
   1. Válassza a **többtényezős hitelesítés megkövetelése** lehetőséget.
   1. Kattintson a **Kiválasztás** elemre.
1. Állítsa a **Házirend engedélyezése** kapcsolót **Be** állásba.
1. Ezután kattintson a **Létrehozás** elemre.

A rendszergazdáknak mostantól az ideiglenes hozzáférési hitelesítő adatokat kell kiadniuk az új felhasználóknak, hogy megfeleljenek a regisztrációhoz szükséges multi-Factor Authentication követelményeinek. A feladat végrehajtásának lépései a következő szakaszban találhatók: [ideiglenes hozzáférési pass létrehozása az Azure ad-portálon](../authentication/howto-authentication-temporary-access-pass.md#create-a-temporary-access-pass-in-the-azure-ad-portal).

A szervezetek dönthetnek úgy, hogy más engedélyezési szabályozást is igényelnek a 6b. lépésben a **többtényezős hitelesítés megkövetelése** mellett vagy a helyett. Ha több vezérlőt választ ki, jelölje be a megfelelő választógombot úgy, hogy a módosítás végrehajtásakor az **összes** kijelölt vezérlőelemet vagy az **egyiket** megkövetelje.

### <a name="guest-user-registration"></a>Vendég felhasználói regisztráció

A többtényezős hitelesítéshez a címtárban regisztrálni kívánt [vendég felhasználók](../external-identities/what-is-b2b.md) számára dönthet úgy, hogy letiltja a regisztrációt a [megbízható hálózati helyekről](concept-conditional-access-conditions.md#locations) a következő útmutató segítségével.

1. A **Azure Portal** keresse meg **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférés** lehetőséget.
1. Válassza az **új szabályzat** lehetőséget.
1. A név mezőben adja meg a szabályzat nevét. Például **a megbízható hálózatokon található kombinált biztonsági adatok regisztrálása**.
1. A **hozzárendelések** területen válassza a **felhasználók és csoportok** lehetőséget, majd válassza ki azokat a felhasználókat és csoportokat, amelyekre alkalmazni szeretné a szabályzatot.
   1. A **Belefoglalás** területen válassza **az összes vendég és külső felhasználó** lehetőséget.
1. A **Cloud apps vagy műveletek** területen válassza a **felhasználói műveletek** lehetőséget, és jelölje be a **biztonsági információk regisztrálása** jelölőnégyzetet.
1. A **feltételek**  >  **helye** területen.
   1. Konfigurálja az **Igen értéket**.
   1. Adjon meg **bármilyen helyet**.
   1. **Az összes megbízható helyszín** kizárása.
   1. Válassza a **kész** lehetőséget a helyszínek panelen.
   1. Válassza a **kész** lehetőséget a feltételek panelen.
1. A **hozzáférés-vezérlés**  >  **megadása** területen.
   1. Válassza a **hozzáférés letiltása** lehetőséget.
   1. Ezután kattintson a **Kiválasztás** elemre.
1. Állítsa a **Házirend engedélyezése** kapcsolót **Be** állásba.
1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

[Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

[A hatás meghatározása a feltételes hozzáférésről szóló jelentés módban](howto-conditional-access-insights-reporting.md)

[Bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával](troubleshoot-conditional-access-what-if.md)
