---
title: Feltételes hozzáférés – Kombinált biztonsági információk – Azure Active Directory
description: Egyéni feltételes hozzáférési szabályzat létrehozása a biztonsági adatok regisztrációhoz
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 04/20/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05aca853e2eba98d224131c98751b4e2f4200024
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765646"
---
# <a name="conditional-access-securing-security-info-registration"></a>Feltételes hozzáférés: A biztonsági információk regisztrálásának védelme

A feltételes hozzáférési szabályzat felhasználói műveletei lehetővé teszi annak biztosítását, hogy a felhasználók mikor és hogyan regisztrálnak az Azure AD Multi-Factor Authenticationre és az önkiszolgáló jelszó-visszaállításra. Ez a funkció olyan szervezetek számára érhető el, amelyek engedélyezték a [kombinált regisztrációt.](../authentication/concept-registration-mfa-sspr-combined.md) Ez a funkció lehetővé teszi a szervezetek számára, hogy a feltételes hozzáférési szabályzatok alkalmazásához hasonló módon kezeljenek regisztrációs folyamatot, és a feltételes hozzáférés minden funkcióját használják a felhasználói élmény biztonságának biztosítása érdekében. 

Előfordulhat, hogy egyes szervezetek korábban megbízható hálózati helyet vagy eszközmegmegelést használtak a regisztrációs élmény biztonságossá tereként. Az Azure [AD-ideiglenes hozzáférési kód](../authentication/howto-authentication-temporary-access-pass.md) lehetővé teszi a rendszergazdák számára, hogy időkorrekta hitelesítő adatokat használjanak a felhasználók számára, amelyek lehetővé teszik számukra, hogy bármilyen eszközről vagy helyről regisztrálnak. ideiglenes hozzáférési kód hitelesítő adatok megfelelnek a többtényezős hitelesítés feltételes hozzáférési követelményeinek.

## <a name="create-a-policy-to-secure-registration"></a>Szabályzat létrehozása a regisztráció biztonságossá való létrehozására

Az alábbi szabályzat a kiválasztott felhasználókra vonatkozik, akik a kombinált regisztrációs folyamat használatával próbálnak regisztrálni. A szabályzat megköveteli a felhasználóktól, hogy többtényezős hitelesítést végezzenek, vagy ideiglenes hozzáférési kód hitelesítő adatokat használjanak.

1. A **Azure Portal** nyissa meg a **Azure Active Directory**  >  **feltételes**  >  **hozzáférést.**
1. Válassza az **Új szabályzat lehetőséget.**
1. A Név alatt adja meg a szabályzat nevét. Például: **Kombinált biztonsági adatok regisztrálása a TAP-val.**
1. A **Hozzárendelések alatt** válassza a **Felhasználók és csoportok** lehetőséget, majd válassza ki azokat a felhasználókat és csoportokat, amelyekre alkalmazni szeretné ezt a szabályzatot.
   1. A **Include (Tartalmazza)** alatt válassza a **Minden felhasználó lehetőséget.**

      > [!WARNING]
      > A kombinált regisztrációhoz engedélyezni kell [a felhasználókat.](../authentication/howto-registration-mfa-sspr-combined.md)

   1. Az **Exclude (Kizárás) alatt.**
      1. Válassza a **Minden vendég- és külső felhasználó lehetőséget.**
      
         > [!NOTE]
         > ideiglenes hozzáférési kód nem működik a vendégfelhasználóknál.

      1. Válassza **a Felhasználók és csoportok lehetőséget,** és válassza ki a szervezet vészelérési vagy vészhelyzeti fiókjait. 
1. A **Felhőalkalmazások vagy műveletek alatt válassza** a Felhasználói **műveletek** lehetőséget, jelölje be a Biztonsági **adatok regisztrálása jelölőnégyzetet.**
1. A **Hozzáférés-vezérlés alatt adja meg az**  >  **et.**
   1. Válassza a **Hozzáférés biztosítása** lehetőséget.
   1. Válassza **a Többtényezős hitelesítés megkövetelve lehetőséget.**
   1. Kattintson a **Kiválasztás** elemre.
1. Állítsa a **Házirend engedélyezése** kapcsolót **Be** állásba.
1. Ezután kattintson a **Létrehozás** elemre.

A rendszergazdáknak most ki kell ideiglenes hozzáférési kód hitelesítő adatokat az új felhasználóknak, hogy eleget tehessenek a többtényezős hitelesítésre vonatkozó követelményeknek a regisztráláshoz. A feladat elvégzésének lépései a Create a ideiglenes hozzáférési kód in the Azure AD Portal (Erőforráscsoport létrehozása az [Azure AD portálon) című szakaszban találhatók.](../authentication/howto-authentication-temporary-access-pass.md#create-a-temporary-access-pass)

A szervezetek dönthetnek úgy, hogy a Többtényezős hitelesítés megkövetelve beállítás mellett vagy helyett más engedély-vezérlést is megkövetelnek a 6b. lépésben.  Ha több vezérlőt választ ki, a megfelelő választógombot  választva követelheti meg a kiválasztott vezérlők mindegyikét vagy valamelyikét a módosításhoz. 

### <a name="guest-user-registration"></a>Vendégfelhasználó regisztrálása

Azon [vendégfelhasználók](../external-identities/what-is-b2b.md) esetén, akiknek többtényezős hitelesítésre kell regisztrálni a [](concept-conditional-access-conditions.md#locations) címtárban, a következő útmutató segítségével letilthatja a megbízható hálózati helyeken kívülről való regisztrációt.

1. A **Azure Portal** lépjen **a** Azure Active Directory  >  **feltételes**  >  **hozzáférésének eléréséhez.**
1. Válassza az **Új szabályzat lehetőséget.**
1. A Név alatt adja meg a szabályzat nevét. Például: **Összevont biztonsági információk regisztrációja megbízható hálózatokon.**
1. A **Hozzárendelések alatt** válassza a **Felhasználók és csoportok** lehetőséget, majd válassza ki azokat a felhasználókat és csoportokat, amelyekre alkalmazni szeretné ezt a szabályzatot.
   1. A **Include (Tartalmazza)** alatt válassza a **All guest and external users (Minden vendég- és külső felhasználó) lehetőséget.**
1. A **Felhőalkalmazások vagy műveletek alatt válassza** a Felhasználói **műveletek** lehetőséget, jelölje be a Biztonsági **adatok regisztrálása jelölőnégyzetet.**
1. A Feltételek  >  **helyei alatt.**
   1. Konfigurálja **az Igen et.**
   1. Bármilyen **helyet foglalhat bele.**
   1. Zárja ki **az Összes megbízható helyet.**
   1. A **Helyek panelen** válassza a Kész lehetőséget.
   1. A **Feltételek panelen** válassza a Kész lehetőséget.
1. A **Hozzáférés-vezérlés alatt adja** meg a  >  **et.**
   1. Válassza a **Hozzáférés blokkolása lehetőséget.**
   1. Ezután kattintson a **Kiválasztás** elemre.
1. Állítsa a **Házirend engedélyezése** kapcsolót **Be** állásba.
1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

[Feltételes hozzáférés – gyakori szabályzatok](concept-conditional-access-policy-common.md)

[Hatás meghatározása a feltételes hozzáférés csak jelentési módjával](howto-conditional-access-insights-reporting.md)

[Bejelentkezési viselkedés szimulálása a Feltételes hozzáférés What If eszközzel](troubleshoot-conditional-access-what-if.md)

[A hitelesítési adatok újbóli firkálásának megkövetele a felhasználóktól](../authentication/concept-sspr-howitworks.md#reconfirm-authentication-information)
