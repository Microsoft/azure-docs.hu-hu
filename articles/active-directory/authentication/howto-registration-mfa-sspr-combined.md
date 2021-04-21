---
title: Kombinált biztonsági információk regisztrálásának engedélyezése – Azure Active Directory
description: Megtudhatja, hogyan egyszerűsítheti le a végfelhasználói élményt az Azure AD Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítási regisztráció együttes használatával.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 01/27/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6eba5ac4ed61847596e12f56544e6d07dca8075
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829574"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Kombinált biztonsági információk regisztrálásának engedélyezése a Azure Active Directory

A kombinált regisztráció előtt a felhasználók külön regisztrálták az Azure AD Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítás (SSPR) hitelesítési módszereit. Az emberek nem tudják, hogy hasonló módszereket használtak az Azure AD Multi-Factor Authentication és az SSPR esetén, de mindkét funkcióra regisztrálni kellett. Mostantól a kombinált regisztrációval a felhasználók egyszer regisztrálnak, és az Azure AD Multi-Factor Authentication és az SSPR előnyeit is kihasználhatja.

> [!NOTE]
> 2020. augusztus 15-től az összes új Azure AD-bérlő automatikusan engedélyezve lesz a kombinált regisztrációhoz. Az ezen dátum után létrehozott bérlők nem tudják használni az örökölt regisztrációs munkafolyamatokat.

Annak érdekében, hogy biztosan megértse a funkciókat és a hatásokat az új felhasználói élmény engedélyezése előtt, tekintse meg a Kombinált biztonsági információk regisztrálásával [kapcsolatos fogalmakat.](concept-registration-mfa-sspr-combined.md)

![Kombinált biztonsági információk regisztrációja – továbbfejlesztett élmény](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>A kombinált regisztráció engedélyezése

A kombinált regisztráció engedélyezéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a Azure Portal felhasználói rendszergazdaként vagy globális rendszergazdaként.
2. A felhasználói **Azure Active Directory**  >  **felhasználói funkciók**  >  **előnézeti beállításainak kezelése lapra.**
3. A **Users can use the combined security information registration experience**(A felhasználók használhatják **a** kombinált biztonsági információk regisztrációs funkcióját) alatt válassza a Kiválasztott felhasználók csoportjának vagy a Minden **felhasználónak beállítást.**

   ![A kombinált biztonsági információk felhasználói élményének engedélyezése](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info.png)

> [!NOTE]
> A kombinált regisztráció engedélyezése után azok a felhasználók, akik az új felhasználói élményben regisztrálják vagy megerősítik telefonszámukat vagy mobilalkalmazásukat, az Azure AD Multi-Factor Authenticationre és az SSPR-re is felhasználhatja őket, ha ezek a módszerek engedélyezve vannak az Azure AD Multi-Factor Authentication- és SSPR-szabályzatban.
>
> Ha ezután letiltja ezt a felhasználói élményt, a korábbi SSPR-regisztrációs oldalra belépő felhasználóknak többtényezős hitelesítést kell végezniük, mielőtt `https://aka.ms/ssprsetup` hozzáférhetnek az oldalhoz.

Ha a helyek  között zóna-hozzárendelési listát konfigurálta a Internet Explorer, a következő helyeknek ugyanabban a zónában kell lennie:

* *[https://login.microsoftonline.com](https://login.microsoftonline.com)*
* *[https://mysignins.microsoft.com](https://mysignins.microsoft.com)*
* *[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)*

## <a name="conditional-access-policies-for-combined-registration"></a>Feltételes hozzáférési szabályzatok kombinált regisztrációhoz

Annak érdekében, hogy a felhasználók mikor és hogyan regisztrálnak az Azure AD Multi-Factor Authenticationre és az önkiszolgáló jelszó-visszaállításra, használhatja a feltételes hozzáférési szabályzatban elérhető felhasználói műveleteket. Ez a funkció olyan szervezeteknél engedélyezhető, amelyek azt szeretnék, hogy a felhasználók regisztrálhatnak az Azure AD Multi-Factor Authenticationre és az SSPR-re egy központi helyről, például egy megbízható hálózati helyről a HR-regisztráció során.

> [!NOTE]
> Ez a szabályzat csak akkor érvényes, ha egy felhasználó egy kombinált regisztrációs laphoz fér hozzá. Ez a szabályzat nem kényszeríti ki az MFA-regisztrációt, ha egy felhasználó más alkalmazásokhoz fér hozzá.
>
> MFA regisztrációs szabályzatot az Azure Identity Protection – [MFA-szabályzat konfigurálása](../identity-protection/howto-identity-protection-configure-mfa-policy.md)használatával hozhat létre.

További információ a megbízható helyek feltételes hozzáférésben való létrehozásáról: Mi a feltételes hozzáférés [helyének Azure Active Directory feltétele?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Szabályzat létrehozása egy megbízható helyről való regisztráció megkövetelhez

A következő lépésekkel hozzon létre egy szabályzatot, amely az összes olyan felhasználóra vonatkozik, akik a kombinált regisztrációs folyamattal próbálnak regisztrálni, és letiltja a hozzáférést, kivéve, ha megbízható hálózatként megjelölt helyről csatlakoznak:

1. A **Azure Portal** lépjen **a** Azure Active Directory  >  **feltételes**  >  **hozzáférésének eléréséhez.**
1. Válassza **az + Új szabályzat lehetőséget.**
1. Adjon nevet a szabályzatnak, például Kombinált biztonsági információk *regisztrációja megbízható hálózatokon.*
1. A **Hozzárendelések** alatt válassza a **Felhasználók és csoportok** lehetőséget. Válassza ki azokat a felhasználókat és csoportokat, amelyekre alkalmazni szeretné ezt a szabályzatot, majd válassza a Kész **lehetőséget.**

   > [!WARNING]
   > A felhasználóknak engedélyezve kell lennie a kombinált regisztrációhoz.

1. A **Felhőalkalmazások vagy műveletek alatt válassza** a Felhasználói műveletek **lehetőséget.** Jelölje be **a Biztonsági adatok regisztrálása jelölőnégyzetet,** majd válassza a **Kész lehetőséget.**

    ![Feltételes hozzáférési szabályzat létrehozása a biztonsági adatok regisztrációja szabályozása érdekében](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. A **Feltételek helye**  >  **alatt** adja meg a következő beállításokat:
   1. Konfigurálja **az Igen et.**
   1. Bármilyen **helyet foglalhat bele.**
   1. Zárja ki **az Összes megbízható helyet.**
1. A **Locations (Helyek)** ablakban  válassza a *Done* (Kész) gombra, majd a Conditions (Feltételek) ablakban a *Done (Kész)* gombra.
1. A **Hozzáférés-vezérlés megadása**  >  **alatt** válassza a Hozzáférés **blokkolása** lehetőséget, majd válassza **a Lehetőséget.**
1. Állítsa a **Házirend engedélyezése** kapcsolót **Be** állásba.
1. A szabályzat végleges létrehozásához válassza a **Létrehozás lehetőséget.**

## <a name="next-steps"></a>Következő lépések

Ha segítségre van szüksége, tekintse meg a kombinált biztonsági adatok [regisztrációjának](howto-registration-mfa-sspr-combined-troubleshoot.md) hibaelhárításával kapcsolatos témakört, vagy tekintse meg a Mi az Azure AD feltételes hozzáférés [helyének feltétele?](../conditional-access/location-condition.md)

Ha a felhasználók számára engedélyezte a [](tutorial-enable-sspr.md) kombinált regisztrációt, engedélyezheti az új jelszó önkiszolgáló beállítását, és engedélyezheti az [Azure AD Multi-Factor Authenticationt.](tutorial-enable-azure-mfa.md)

Ha szükséges, ismerje meg, hogyan kényszeríti a felhasználókat a hitelesítési [módszerek újra regisztrálásra.](howto-mfa-userdevicesettings.md#manage-user-authentication-options)
