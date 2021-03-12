---
title: Az Azure AD saját alkalmazásaiból alkalmazásba való bejelentkezéssel kapcsolatos problémák elhárítása
description: Az Azure AD saját alkalmazásaiból alkalmazásba való bejelentkezéssel kapcsolatos problémák elhárítása
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q2
ms.openlocfilehash: a54c7d6cc4ccf1d9f42702be030598ad1edfab24
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225149"
---
# <a name="troubleshoot-problems-signing-in-to-an-application-from-azure-ad-my-apps"></a>Az Azure AD saját alkalmazásaiból alkalmazásba való bejelentkezéssel kapcsolatos problémák elhárítása

A saját alkalmazások egy webalapú portál, amely lehetővé teszi, hogy a felhasználó munkahelyi vagy iskolai fiókkal Azure Active Directory (Azure AD) megtekintse és indítsa el azokat a felhőalapú alkalmazásokat, amelyekhez az Azure AD-rendszergazda hozzáférést kapott. A saját alkalmazások webböngészővel érhetők el a webhelyen [https://myapps.microsoft.com](https://myapps.microsoft.com) .

Ha többet szeretne megtudni az Azure AD-alkalmazás identitás-szolgáltatóként való használatáról, tekintse meg a [Mi az az Azure ad-ben való alkalmazás-kezelési](what-is-application-management.md)témakört. A gyors üzembe helyezéshez tekintse [meg az alkalmazások felügyeletének](view-applications-portal.md)rövid útmutatóját.

Ezek az alkalmazások az Azure AD-portálon a felhasználó nevében konfigurálhatók. Az alkalmazást megfelelően kell konfigurálni, és hozzá kell rendelni ahhoz a felhasználóhoz vagy csoporthoz, amelyhez a felhasználó tartozik, hogy az alkalmazás megjelenjen a saját alkalmazásokban. 

A felhasználók által megjelenített alkalmazások típusa a következő kategóriákba tartozhat:
-   Alkalmazások Microsoft 365
-   Összevonási alapú egyszeri bejelentkezéssel konfigurált Microsoft és harmadik féltől származó alkalmazások
-   Jelszó-alapú SSO-alkalmazások
-   Meglévő SSO-megoldásokkal rendelkező alkalmazások

Íme néhány dolog, amelyből megtudhatja, hogy megjelenjen-e az alkalmazás, vagy sem.
- Győződjön meg arról, hogy az alkalmazás hozzá van adva az Azure AD-hoz, és ellenőrizze, hogy a felhasználó hozzá van-e rendelve. További információ: a gyors üzembe helyezési [sorozat az alkalmazások kezelésében](add-application-portal.md).
- Ha egy alkalmazás nemrég lett hozzáadva, a felhasználó kijelentkezik és újra visszakerül. 
- Ha az alkalmazáshoz licenc szükséges, például az Office, akkor győződjön meg arról, hogy a felhasználó hozzá van rendelve a megfelelő licenchez.
- A licencelési módosítások elvégzéséhez szükséges idő a csoport méretétől és összetettségtől függően változhat.

## <a name="general-issues-to-check-first"></a>Általános problémák az első kereséshez

-   Győződjön meg arról, hogy a webböngésző megfelel a követelményeknek, lásd: [saját alkalmazások támogatott böngészők](../user-help/my-apps-portal-end-user-access.md).
-   Győződjön meg arról, hogy a felhasználó böngészőjében hozzá lett adva az alkalmazás URL-címe a **megbízható helyekhez**.
-   Ellenőrizze, hogy az alkalmazás megfelelően van-e **konfigurálva** .
-   Győződjön meg arról, hogy a felhasználó fiókja **engedélyezve** van a bejelentkezésekhez.
-   Győződjön meg arról, hogy a felhasználó fiókja **nincs zárolva.**
-   Győződjön meg arról, hogy a felhasználó **jelszava nem járt le vagy nem felejtette el.**
-   Győződjön meg arról, hogy **multi-Factor Authentication** nem blokkolja a felhasználói hozzáférést.
-   Győződjön meg arról, hogy a **feltételes hozzáférési szabályzat** vagy az **Identity Protection** -házirend nem blokkolja a felhasználói hozzáférést.
-   Győződjön meg arról, hogy a felhasználó **hitelesítési kapcsolattartási adatai** naprakészek multi-Factor Authentication vagy feltételes hozzáférési szabályzatok érvényesítéséhez.
-   Ügyeljen arra, hogy a böngésző cookie-jait is törölje, és próbálja meg újból bejelentkezni.

## <a name="problems-with-the-users-account"></a>A felhasználói fiókkal kapcsolatos problémák
Az alkalmazásokhoz való hozzáférés a felhasználói fiókkal kapcsolatos probléma miatt tiltható le. Az alábbiakban néhány módon elháríthatja a felhasználókat és a fiókjuk beállításait:
-   [Annak ellenőrzése, hogy létezik-e felhasználói fiók a Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)
-   [Felhasználói fiók állapotának megkeresése](#check-a-users-account-status)
-   [Felhasználó jelszavának alaphelyzetbe állítása](#reset-a-users-password)
-   [Új jelszó önkiszolgáló kérésének engedélyezése](#enable-self-service-password-reset)
-   [A felhasználó többtényezős hitelesítési állapotának keresése](#check-a-users-multi-factor-authentication-status)
-   [A felhasználó hitelesítési kapcsolattartási adatainak megkeresése](#check-a-users-authentication-contact-info)
-   [Felhasználó csoport-tagságának keresése](#check-a-users-group-memberships)
-   [Ellenőrizze, hogy a felhasználó több mint 999 alkalmazás-szerepkör-hozzárendeléssel rendelkezik-e](#check-if-a-user-has-more-than-999-app-role-assignments)
-   [Felhasználóhoz rendelt licencek keresése](#check-a-users-assigned-licenses)
-   [Licenc felhasználóhoz rendelése](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Annak ellenőrzése, hogy létezik-e felhasználói fiók a Azure Active Directory
Az alábbi lépéseket követve ellenőrizheti, hogy van-e felhasználó fiókja:
1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**
2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** lehetőség kiválasztásával.
3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.
4.  A navigációs menüben válassza a **felhasználók és csoportok** lehetőséget.
5.  Válassza **a minden felhasználó** lehetőséget.
6.  **Keresse** meg az Önt érdeklő felhasználót, és **válassza ki a** kijelölni kívánt sort.
7.  Ellenőrizze a felhasználói objektum tulajdonságait, és győződjön meg róla, hogy a vártnak megfelelően van-e megadva, és nincs hiányzó adatmennyiség.

### <a name="check-a-users-account-status"></a>Felhasználói fiók állapotának megkeresése
A felhasználó fiókja állapotának megtekintéséhez kövesse az alábbi lépéseket:
1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**
2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** lehetőség kiválasztásával.
3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.
4.  A navigációs menüben válassza a **felhasználók és csoportok** lehetőséget.
5.  Válassza **a minden felhasználó** lehetőséget.
6.  **Keresse** meg az Önt érdeklő felhasználót, és **válassza ki a** kijelölni kívánt sort.
7.  Válassza a **profil** lehetőséget.
8.  A **Beállítások** területen győződjön meg arról, hogy a **Letiltás tiltása** beállítás **nem** értékre van állítva.

### <a name="reset-a-users-password"></a>Felhasználó jelszavának alaphelyzetbe állítása
A felhasználó jelszavának alaphelyzetbe állításához kövesse az alábbi lépéseket:
1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**
2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** lehetőség kiválasztásával.
3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.
4.  A navigációs menüben válassza a **felhasználók és csoportok** lehetőséget.
5.  Válassza **a minden felhasználó** lehetőséget.
6.  **Keresse** meg az Önt érdeklő felhasználót, és **válassza ki a** kijelölni kívánt sort.
7.  Kattintson a **jelszó alaphelyzetbe állítása** gombra a felhasználó ablaktábla tetején.
8.  Kattintson a **jelszó alaphelyzetbe állítása** gombra a megjelenő **jelszó alaphelyzetbe** állítása panelen.
9.  Másolja ki az **ideiglenes jelszót** , vagy **adjon meg egy új jelszót** a felhasználó számára.
10. Ezt az új jelszót a felhasználónak kell megadnia, hogy a következő bejelentkezéskor módosítania kell a jelszót a Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Új jelszó önkiszolgáló kérésének engedélyezése
Az önkiszolgáló jelszó-visszaállítás engedélyezéséhez kövesse az alábbi telepítési lépéseket:
-   [A Azure Active Directory jelszavának alaphelyzetbe állításának engedélyezése a felhasználók számára](../authentication/tutorial-enable-sspr.md)
-   [Az Active Directory helyszíni jelszavak alaphelyzetbe állításának vagy módosításának engedélyezése a felhasználók számára](../authentication/tutorial-enable-sspr.md)

### <a name="check-a-users-multi-factor-authentication-status"></a>A felhasználó többtényezős hitelesítési állapotának keresése
A felhasználó többtényezős hitelesítési állapotának megtekintéséhez kövesse az alábbi lépéseket:
1. Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**
2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** lehetőség kiválasztásával.
3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.
4. A navigációs menüben válassza a **felhasználók és csoportok** lehetőséget.
5. Válassza **a minden felhasználó** lehetőséget.
6. Kattintson a panel tetején található **multi-Factor Authentication** gombra.
7. Miután a **multi-Factor Authentication felügyeleti portál** betöltődik, győződjön meg arról, hogy a **felhasználók** lapon van.
8. Keresse meg a felhasználót a felhasználók listájában keresés, szűrés vagy rendezés alapján.
9. Válassza ki a felhasználót a felhasználók listájából, és **engedélyezze**, **Tiltsa le** vagy **kényszerítse** ki a többtényezős hitelesítést igény szerint.
   >[!NOTE]
   >Ha egy felhasználó **kényszerített** állapotban van, beállíthatja, hogy ideiglenesen **letiltsa** őket a fiókjába való visszatéréshez. Ha ismét bejelentkeznek, a következő bejelentkezéskor **újra meg** lehet változtatni az állapotukat, hogy újra regisztrálni tudják a kapcsolattartási adatokat. Azt is megteheti, hogy a [felhasználó hitelesítési kapcsolattartási adatainak ellenőrzése](#check-a-users-authentication-contact-info) lapon található lépéseket követve ellenőrizheti vagy beállítja ezeket az adatokat.

### <a name="check-a-users-authentication-contact-info"></a>A felhasználó hitelesítési kapcsolattartási adatainak megkeresése
A többtényezős hitelesítéshez, a feltételes hozzáféréshez, az Identitáskezelés és a jelszó alaphelyzetbe állításához használt hitelesítési kapcsolattartási adatok vizsgálatához kövesse az alábbi lépéseket:
1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**
2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** lehetőség kiválasztásával.
3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.
4.  A navigációs menüben válassza a **felhasználók és csoportok** lehetőséget.
5.  Válassza **a minden felhasználó** lehetőséget.
6.  **Keresse** meg az Önt érdeklő felhasználót, és **válassza ki a** kijelölni kívánt sort.
7.  Válassza a **profil** lehetőséget.
8.  Görgessen le a **hitelesítési kapcsolattartási adatokhoz**.
9.  **Tekintse át** a felhasználó számára regisztrált és a frissítéshez szükséges, igény szerinti frissítést.

### <a name="check-a-users-group-memberships"></a>Felhasználó csoport-tagságának keresése
A felhasználók csoporttagságok vizsgálatához kövesse az alábbi lépéseket:
1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**
2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** lehetőség kiválasztásával.
3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.
4.  A navigációs menüben válassza a **felhasználók és csoportok** lehetőséget.
5.  Válassza **a minden felhasználó** lehetőséget.
6.  **Keresse** meg az Önt érdeklő felhasználót, és **válassza ki a** kijelölni kívánt sort.
7.  Válassza ki a **csoportokat** , és ellenőrizze, hogy a felhasználó melyik csoport tagja.

### <a name="check-if-a-user-has-more-than-999-app-role-assignments"></a>Ellenőrizze, hogy a felhasználó több mint 999 alkalmazás-szerepkör-hozzárendeléssel rendelkezik-e
Ha a felhasználó több mint 999 alkalmazás-szerepkör-hozzárendeléssel rendelkezik, akkor előfordulhat, hogy nem látják az alkalmazások összes alkalmazását.

Ennek az az oka, hogy az alkalmazások jelenleg legfeljebb 999 alkalmazás-szerepkör-hozzárendelést olvasnak, hogy meghatározzák azokat az alkalmazásokat, amelyekhez a felhasználók hozzá vannak rendelve. Ha a felhasználó több mint 999 alkalmazáshoz van hozzárendelve, nem lehet szabályozni, hogy mely alkalmazások jelenjenek meg a saját alkalmazások portálon.

Az alábbi lépéseket követve ellenőrizheti, hogy egy felhasználó több mint 999 alkalmazás-szerepkör-hozzárendeléssel rendelkezik-e:
1. Telepítse a [**Microsoft. Graph**](https://github.com/microsoftgraph/msgraph-sdk-powershell) PowerShell-modult.
2. Futtassa az `Connect-MgGraph -Scopes "User.ReadBasic.All Application.Read.All"` parancsot.
3. A futtatásával `(Get-MgUserAppRoleAssignment -UserId "<user-id>" -PageSize 999).Count` határozza meg, hogy a felhasználó mely alkalmazás-szerepkör-hozzárendeléseket adja meg jelenleg.
4. Ha az eredmény 999, a felhasználónak valószínűleg több, mint 999 alkalmazás-szerepkör-hozzárendelése lesz.

### <a name="check-a-users-assigned-licenses"></a>Felhasználóhoz rendelt licencek keresése
A felhasználóhoz hozzárendelt licencek vizsgálatához kövesse az alábbi lépéseket:
1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**
2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** lehetőség kiválasztásával.
3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.
4.  A navigációs menüben válassza a **felhasználók és csoportok** lehetőséget.
5.  Válassza **a minden felhasználó** lehetőséget.
6.  **Keresse** meg az Önt érdeklő felhasználót, és **válassza ki a** kijelölni kívánt sort.
7.  Válassza a **licencek** lehetőséget, hogy megtekintse, mely licenceket rendeli hozzá jelenleg a felhasználó.

### <a name="assign-a-user-a-license"></a>Licenc felhasználóhoz rendelése 
Ha licencet szeretne hozzárendelni egy felhasználóhoz, kövesse az alábbi lépéseket:
1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**
2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** lehetőség kiválasztásával.
3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.
4.  A navigációs menüben válassza a **felhasználók és csoportok** lehetőséget.
5.  Válassza **a minden felhasználó** lehetőséget.
6.  **Keresse** meg az Önt érdeklő felhasználót, és **válassza ki a** kijelölni kívánt sort.
7.  Válassza a **licencek** lehetőséget, hogy megtekintse, mely licenceket rendeli hozzá jelenleg a felhasználó.
8.  Kattintson a **hozzárendelés** gombra.
9.  Válasszon ki **egy vagy több terméket** az elérhető termékek listájából.
10. Nem **kötelező** kijelölni a **hozzárendelési beállítások** elemet a termékek részletességének kiosztásához. Kattintson az **OK** gombra.
11. Kattintson a **hozzárendelés** gombra a licencek ehhez a felhasználóhoz való hozzárendeléséhez.

## <a name="troubleshooting-deep-links"></a>Mély hivatkozások hibaelhárítása
A részletes hivatkozások vagy a felhasználói hozzáférési URL-címek arra utalnak, hogy a felhasználók a jelszó-SSO-alkalmazásaikat közvetlenül a böngészők URL-sávjában érhetik el. Ha megnyitja ezt a hivatkozást, a felhasználók automatikusan bejelentkeznek az alkalmazásba anélkül, hogy először az alkalmazásaikat kellene bejelentkezniük. A hivatkozás ugyanaz, mint amelyet a felhasználók a Microsoft 365 Application Launcher használatával férnek hozzá ezekhez az alkalmazásokhoz.

### <a name="checking-the-deep-link"></a>A mély hivatkozás ellenőrzése

Az alábbi lépéseket követve ellenőrizheti, hogy a megfelelő mély hivatkozással rendelkezik-e:
1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.
2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** lehetőség kiválasztásával.
3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.
4. Az Azure Active Directory bal oldali navigációs menüjében válassza a **Vállalati alkalmazások** elemet.
5. Válassza a **Minden alkalmazás** lehetőséget az összes alkalmazás listájának megtekintéséhez.
   * Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra.**
6. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.
7. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** lehetőség kiválasztásával.
8. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.
9. Az Azure Active Directory bal oldali navigációs menüjében válassza a **Vállalati alkalmazások** elemet.
10. Válassza a **Minden alkalmazás** lehetőséget az összes alkalmazás listájának megtekintéséhez.
    * Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra.**
11. Válassza ki azt az alkalmazást, amelyre vonatkozóan meg szeretné jelölni a Deep hivatkozást.
12. Keresse meg a címke **felhasználói hozzáférési URL-címét**. A mély hivatkozásnak meg kell egyeznie ezzel az URL-címmel.

## <a name="contact-support"></a>Kapcsolatfelvétel a támogatási szolgáltatással
Ha elérhető, nyisson meg egy támogatási jegyet a következő információkkal:
-   Korrelációs hiba azonosítója
-   UPN (felhasználói e-mail-cím)
-   TenantID
-   Böngésző típusa
-   Az időzóna és az idő/időkeret a hiba bekövetkezésekor
-   Hegedűs nyomkövetései

## <a name="next-steps"></a>Következő lépések
- [Gyorsindítás sorozat az alkalmazás-kezelésben](view-applications-portal.md)
