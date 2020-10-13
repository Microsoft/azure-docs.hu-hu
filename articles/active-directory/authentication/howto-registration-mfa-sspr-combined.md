---
title: A kombinált biztonsági információk regisztrációjának engedélyezése – Azure Active Directory
description: Ismerje meg, hogyan egyszerűsítheti a végfelhasználói élményt a kombinált Azure AD-Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítási regisztrációval.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: e22e4cdbfaf90eb9bba30fc26dc3495f767216f1
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91968339"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>A kombinált biztonsági információk regisztrációjának engedélyezése a Azure Active Directoryban

A kombinált regisztráció előtt a felhasználók az Azure Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítás (SSPR) hitelesítési módszereit külön-külön regisztrálták. Az emberek zavarosak voltak, hogy hasonló módszerekkel használták az Azure Multi-Factor Authentication és a SSPR, de mindkét szolgáltatáshoz regisztrálniuk kellett őket. A közös regisztráció révén a felhasználók egyszer regisztrálhatnak, és igénybe vehetik az Azure Multi-Factor Authentication és a SSPR előnyeit.

> [!NOTE]
> Az Azure AD-bérlők augusztus 2020 15-én kezdődően automatikusan engedélyezve lesznek a kombinált regisztrációhoz.

Annak érdekében, hogy az új felület engedélyezése előtt megértse a funkcionalitást és a hatásokat, tekintse meg a [kombinált biztonsági információk regisztrációjának fogalmait](concept-registration-mfa-sspr-combined.md).

![Összetett biztonsági információk regisztrációjának továbbfejlesztett felhasználói felülete](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>A kombinált regisztráció engedélyezése

A kombinált regisztráció engedélyezéséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be a Azure Portal felhasználói rendszergazdaként vagy globális rendszergazdaként.
2. Nyissa meg **Azure Active Directory**  >  **felhasználói beállítások**  >  **kezelése felhasználói szolgáltatás előzetes**verziójának beállításait.
3. **A felhasználók a biztonsági információk összevont felhasználói felületét használhatják**, így a **kiválasztott** felhasználók vagy az **összes** felhasználó számára engedélyezhető.

   ![Az összevont biztonsági adatok felhasználói felületének engedélyezése](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info.png)

> [!NOTE]
> A kombinált regisztráció engedélyezése után azok a felhasználók, akik az új felhasználói felületen regisztrálják vagy megerősítik a telefonszámot vagy a mobil alkalmazást, használhatják ezeket az Azure Multi-Factor Authentication-és SSPR, ha ezek a módszerek engedélyezve vannak az Azure-Multi-Factor Authentication és a SSPR-házirendekben.
>
> Ha ezt követően letiltja ezt a felhasználói élményt, a többtényezős hitelesítés végrehajtásához az előző SSPR regisztrációs oldalra kell lépnie, `https://aka.ms/ssprsetup` mielőtt hozzá tudnak férni a laphoz.

Ha az Internet Explorerben konfigurálta a *hely-hozzárendelési listát* , a következő helyeknek ugyanabban a zónában kell lenniük:

* *[https://login.microsoftonline.com](https://login.microsoftonline.com)*
* *[https://mysignins.microsoft.com](https://mysignins.microsoft.com)*
* *[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)*

## <a name="conditional-access-policies-for-combined-registration"></a>Feltételes hozzáférési szabályzatok a kombinált regisztrációhoz

Az Azure Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítás felhasználói műveleteinek biztonságossá tételéhez használhatja a feltételes hozzáférési házirendben a felhasználói műveleteket. Ez a funkció olyan szervezeteknél engedélyezhető, amelyeknek a felhasználóknak regisztrálniuk kell az Azure Multi-Factor Authentication és a SSPR egy központi helyről, például egy megbízható hálózati helyről az HR bevezetése során.

> [!NOTE]
> Ez a szabályzat csak akkor érvényes, ha egy felhasználó egy kombinált regisztrációs oldalhoz fér hozzá. Ez a szabályzat nem kényszeríti az MFA-regisztrációt, ha egy felhasználó más alkalmazásokhoz fér hozzá.
>
> Az Azure Identity Protection használatával létrehozhat egy MFA regisztrációs szabályzatot az [MFA-szabályzat konfigurálásával](../identity-protection/howto-identity-protection-configure-mfa-policy.md).

A megbízható helyek feltételes hozzáférésben való létrehozásával kapcsolatos további információkért lásd: [Mi a hely állapota a feltételes hozzáférés Azure Active Directory?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Szabályzat létrehozása megbízható helyről való regisztráció megköveteléséhez

A következő lépések végrehajtásával hozzon létre egy szabályzatot, amely az összes olyan kijelölt felhasználóra érvényes, amely az egyesített regisztrációs élmény használatával próbálkozik, és blokkolja a hozzáférést, hacsak nem megbízható hálózatként megjelölt helyről csatlakozik:

1. A **Azure Portal**keresse meg **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférés**lehetőséget.
1. Válassza az **+ új házirend**elemet.
1. Adja meg a szabályzat nevét, például: *kombinált biztonsági adatok regisztrálása megbízható hálózatokon*.
1. A **Hozzárendelések** alatt válassza a **Felhasználók és csoportok** lehetőséget. Válassza ki azokat a felhasználókat és csoportokat, amelyekre alkalmazni szeretné a szabályzatot, majd válassza a **kész**lehetőséget.

   > [!WARNING]
   > A felhasználók számára engedélyezni kell a kombinált regisztrációt.

1. A **Cloud apps vagy műveletek**területen válassza a **felhasználói műveletek**lehetőséget. Jelölje be a **biztonsági információk regisztrálása**jelölőnégyzetet, majd kattintson a **kész**gombra.

    ![Feltételes hozzáférési szabályzat létrehozása a biztonsági adatok regisztrálásának vezérléséhez](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. A **feltételek**  >  **helye**területen adja meg a következő beállításokat:
   1. Konfigurálja az **Igen értéket**.
   1. Adjon meg **bármilyen helyet**.
   1. **Az összes megbízható helyszín**kizárása.
1. Válassza a **kész** lehetőséget a *helyszínek* ablakban, majd a *feltételek* ablakban válassza a **kész** lehetőséget.
1. A **hozzáférés-vezérlés**  >  **megadása**területen válassza a **hozzáférés letiltása**, majd a **lehetőséget**.
1. Állítsa a **Házirend engedélyezése** kapcsolót **Be** állásba.
1. A szabályzat véglegesítéséhez válassza a **Létrehozás**lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ha segítségre van szüksége, olvassa el a [kombinált biztonsági információk regisztrációjának hibaelhárítása](howto-registration-mfa-sspr-combined-troubleshoot.md) vagy [a mi az Azure ad feltételes hozzáférés a hely feltételének](../conditional-access/location-condition.md) ismertetése című témakört.

Ha a felhasználók engedélyezve vannak a kombinált regisztrációhoz, engedélyezheti az önkiszolgáló [jelszó-visszaállítást](tutorial-enable-sspr.md) , és [engedélyezheti az Azure multi-Factor Authentication](tutorial-enable-azure-mfa.md).

Ha szükséges, Ismerje meg, hogyan [kényszerítheti a felhasználókat a hitelesítési módszerek ismételt regisztrálására](howto-mfa-userdevicesettings.md#manage-user-authentication-options).
