---
title: Problémák a Microsoft-alkalmazásba való bejelentkezéskor | Microsoft Docs
description: Az első féltől származó Microsoft-alkalmazásokhoz az Azure AD használatával való bejelentkezéskor előforduló gyakori problémák elhárítása (például Microsoft 365).
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/10/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79c8d6d072379853d6eca561d372f61dbb8acc8a
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99260011"
---
# <a name="problems-signing-in-to-a-microsoft-application"></a>Problémák a Microsoft-alkalmazásba való bejelentkezéskor

A Microsoft-alkalmazások (például az Exchange, a SharePoint, a Yammer stb.) a harmadik féltől származó SaaS-alkalmazásokkal vagy más, az Azure AD-vel integrált egyszeri bejelentkezéshez használt alkalmazásokkal vannak társítva és kezelve.

A felhasználók három fő módon érhetik el a Microsoft által közzétett alkalmazásokat.

-   A Microsoft 365 vagy más fizetős csomag alkalmazásaiban a felhasználók közvetlenül a felhasználói fiókjához vagy a csoport alapú licenc-hozzárendelési képességgel rendelkező csoporton keresztül kapnak hozzáférést a **licenc-hozzárendelésen** keresztül.

-   Azon alkalmazások esetében, amelyeket a Microsoft vagy harmadik fél szabadon tesz közzé mindenki számára, a felhasználók a **felhasználói engedélyeken** keresztül kaphatnak hozzáférést. Ez azt jelenti, hogy az alkalmazásba bejelentkeznek az Azure AD munkahelyi vagy iskolai fiókjával, és lehetővé teszik, hogy a fiókja korlátozott mennyiségű adathalmazhoz hozzáférjen.

-   Az olyan alkalmazások esetében, amelyeket a Microsoft vagy harmadik fél szabadon tesz közzé mindenki számára, a felhasználók **rendszergazdai engedélyen** keresztül is kaphatnak hozzáférést. Ez azt jelenti, hogy a rendszergazda azt állapította meg, hogy az alkalmazást mindenki használhatja a szervezeten belül, így egy globális rendszergazdai fiókkal jelentkezik be az alkalmazásba, és hozzáférést biztosít mindenki számára a szervezeten belül.

A probléma elhárításához kezdje az [általános problémás területekkel, amelyekkel megtekintheti az alkalmazáshoz való hozzáférést](#general-problem-areas-with-application-access-to-consider) , és elolvashatja a bemutató: lépések a Microsoft-alkalmazásokhoz való hozzáférés hibaelhárítására szolgáló lépéseket a részletek beszerzéséhez.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Általános problémás területek az alkalmazáshoz való hozzáférés megfontolni

Az alábbi lista az általános problémás területeket sorolja fel, amelyeket kihasználhat, ha rendelkezik egy ötlettel a kezdéshez, de javasoljuk, hogy a gyors kezdéshez olvassa el a bemutatót: útmutató: a Microsoft-alkalmazások hozzáférésének hibaelhárítási lépései.

-   [A felhasználói fiókkal kapcsolatos problémák](#problems-with-the-users-account)

-   [Problémák a csoportokkal](#problems-with-groups)

-   [A feltételes hozzáférési szabályzatokkal kapcsolatos problémák](#problems-with-conditional-access-policies)

-   [Alkalmazás-beleegyezett problémák](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>A Microsoft-alkalmazásokhoz való hozzáférés hibaelhárításának lépései

A következő gyakori problémákkal találkozhat, amikor a felhasználók nem tudnak bejelentkezni egy Microsoft-alkalmazásba.

- Általános problémák az első kereséshez

  * Győződjön meg arról, hogy a felhasználó bejelentkezik a **megfelelő URL-címre** , nem pedig egy helyi alkalmazás URL-címére.

  * Győződjön meg arról, hogy a felhasználó fiókja **nincs zárolva.**

  * Győződjön meg arról, hogy a **felhasználói fiók létezik** a Azure Active Directoryban. [Annak ellenőrzése, hogy létezik-e felhasználói fiók a Azure Active Directory](#problems-with-the-users-account)

  * Győződjön meg arról, hogy a felhasználó fiókja **engedélyezve** van a bejelentkezésekhez. [Felhasználói fiók állapotának megkeresése](#problems-with-the-users-account)

  * Győződjön meg arról, hogy a felhasználó **jelszava nem járt le vagy nem felejtette el.** [A felhasználó jelszavának](#reset-a-users-password) alaphelyzetbe állítása vagy az önkiszolgáló [jelszó-visszaállítás engedélyezése](../authentication/tutorial-enable-sspr.md)

  * Győződjön meg arról, hogy **multi-Factor Authentication** nem blokkolja a felhasználói hozzáférést. [A felhasználó többtényezős hitelesítési állapotának megtekintése](#check-a-users-multi-factor-authentication-status) vagy [a felhasználó hitelesítési kapcsolattartási adatainak megkeresése](#check-a-users-authentication-contact-info)

  * Győződjön meg arról, hogy a **feltételes hozzáférési szabályzat** vagy az **Identity Protection** -házirend nem blokkolja a felhasználói hozzáférést. [Egy adott feltételes hozzáférési szabályzat ellenőrzésével](#problems-with-conditional-access-policies) vagy egy [adott alkalmazás feltételes hozzáférési házirendjének](#check-a-specific-applications-conditional-access-policy) ellenőrzésével vagy [egy adott feltételes hozzáférési szabályzat letiltásával](#disable-a-specific-conditional-access-policy)

  * Győződjön meg arról, hogy a felhasználó **hitelesítési kapcsolattartási adatai** naprakészek multi-Factor Authentication vagy feltételes hozzáférési szabályzatok érvényesítéséhez. [A felhasználó többtényezős hitelesítési állapotának megtekintése](#check-a-users-multi-factor-authentication-status) vagy [a felhasználó hitelesítési kapcsolattartási adatainak megkeresése](#check-a-users-authentication-contact-info)

- Az olyan **Microsoft** - **alkalmazásokhoz, amelyek licencet igényelnek** (például Office 365), az alábbiakban néhány konkrét problémát ismertetünk, ha kizárta a fenti általános problémákat:

  * Győződjön meg arról, hogy a felhasználó vagy **licenc van hozzárendelve.** [Felhasználóhoz rendelt licencek](#check-a-users-assigned-licenses) megtekintése vagy [a csoport hozzárendelt licencének bejelölése](#check-a-groups-assigned-licenses)

  * Ha a licenc **egy** **statikus csoporthoz** van rendelve, győződjön meg arról, hogy a **felhasználó tagja** ennek a csoportnak. [Felhasználó csoport-tagságának keresése](#check-a-users-group-memberships)

  * Ha a licenc **egy** **dinamikus csoporthoz** van rendelve, győződjön meg arról, hogy a **dinamikus csoport szabálya helyesen van beállítva**. [Dinamikus csoport tagsági feltételeinek bejelölése](#check-a-dynamic-groups-membership-criteria)

  * Ha a licenc **egy** **dinamikus csoporthoz** van rendelve, győződjön meg arról, hogy a dinamikus csoport **befejezte a tagság feldolgozását** , és hogy a **felhasználó tagja** (ez eltarthat egy ideig). [Felhasználó csoport-tagságának keresése](#check-a-users-group-memberships)

  *  Ha ellenőrzi, hogy a licenc hozzá van-e rendelve, győződjön meg arról, hogy a licenc **nem járt le**.

  *  Győződjön meg arról, hogy a licenc az elérni **kívánt alkalmazáshoz szükséges** .

- A  **licenccel nem rendelkező Microsoft-alkalmazásokhoz** néhány további dolgot is meg kell néznie:

  * Ha az alkalmazás **felhasználói szintű engedélyeket** kér (például "hozzáférés a felhasználó postaládájához"), győződjön meg arról, hogy a felhasználó bejelentkezett az alkalmazásba, és elvégezte a **felhasználói szintű** belefoglalási műveletet, amely lehetővé teszi az alkalmazás számára az adatelérést.

  * Ha az alkalmazás **rendszergazdai szintű engedélyeket** kér (például "az összes felhasználó postaládájának elérése"), győződjön meg arról, hogy a globális rendszergazda **rendszergazdai szintű belefoglalási műveletet hajtott végre a szervezet összes felhasználója nevében** .

## <a name="problems-with-the-users-account"></a>A felhasználói fiókkal kapcsolatos problémák

Az alkalmazáshoz való hozzáférés az alkalmazáshoz hozzárendelt felhasználóval kapcsolatos probléma miatt blokkolható. Az alábbiakban néhány módon elháríthatja a felhasználókat és a fiókjuk beállításait:

-   [Annak ellenőrzése, hogy létezik-e felhasználói fiók a Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Felhasználói fiók állapotának megkeresése](#check-a-users-account-status)

-   [Felhasználó jelszavának alaphelyzetbe állítása](#reset-a-users-password)

-   [Új jelszó önkiszolgáló kérésének engedélyezése](#enable-self-service-password-reset)

-   [A felhasználó többtényezős hitelesítési állapotának keresése](#check-a-users-multi-factor-authentication-status)

-   [A felhasználó hitelesítési kapcsolattartási adatainak megkeresése](#check-a-users-authentication-contact-info)

-   [Felhasználó csoport-tagságának keresése](#check-a-users-group-memberships)

-   [Felhasználóhoz rendelt licencek keresése](#check-a-users-assigned-licenses)

-   [Licenc felhasználóhoz rendelése](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Annak ellenőrzése, hogy létezik-e felhasználói fiók a Azure Active Directory

Az alábbi lépéseket követve ellenőrizheti, hogy van-e felhasználó fiókja:

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5.  kattintson **a minden felhasználó** elemre.

6.  **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7.  Ellenőrizze a felhasználói objektum tulajdonságait, és győződjön meg róla, hogy a vártnak megfelelően van-e megadva, és nincs hiányzó adatmennyiség.

### <a name="check-a-users-account-status"></a>Felhasználói fiók állapotának megkeresése

A felhasználó fiókja állapotának megtekintéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5.  kattintson **a minden felhasználó** elemre.

6.  **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7.  kattintson a **profil** elemre.

8.  A **Beállítások** területen győződjön meg arról, hogy a **Letiltás tiltása** beállítás **nem** értékre van állítva.

### <a name="reset-a-users-password"></a>Felhasználó jelszavának alaphelyzetbe állítása

A felhasználó jelszavának alaphelyzetbe állításához kövesse az alábbi lépéseket:

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5.  kattintson **a minden felhasználó** elemre.

6.  **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7.  kattintson a **jelszó alaphelyzetbe állítása** gombra a felhasználó ablaktábla tetején.

8.  kattintson a **jelszó alaphelyzetbe állítása** gombra a megjelenő **jelszó visszaállítása** panelen.

9.  Másolja ki az **ideiglenes jelszót** , vagy **adjon meg egy új jelszót** a felhasználó számára.

10. Ezt az új jelszót a felhasználónak kell megadnia, hogy a következő bejelentkezéskor módosítania kell a jelszót a Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Új jelszó önkiszolgáló kérésének engedélyezése

Az önkiszolgáló jelszó-visszaállítás engedélyezéséhez kövesse az alábbi telepítési lépéseket:

-   [A Azure Active Directory jelszavának alaphelyzetbe állításának engedélyezése a felhasználók számára](../authentication/tutorial-enable-sspr.md)

-   [Az Active Directory helyszíni jelszavak alaphelyzetbe állításának vagy módosításának engedélyezése a felhasználók számára](../authentication/tutorial-enable-sspr.md)

### <a name="check-a-users-multi-factor-authentication-status"></a>A felhasználó többtényezős hitelesítési állapotának keresése

A felhasználó többtényezős hitelesítési állapotának megtekintéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5. kattintson **a minden felhasználó** elemre.

6. kattintson a panel tetején található **multi-Factor Authentication** gombra.

7. Miután a **multi-Factor Authentication felügyeleti portál** betöltődik, győződjön meg arról, hogy a **felhasználók** lapon van.

8. Keresse meg a felhasználót a felhasználók listájában keresés, szűrés vagy rendezés alapján.

9. Válassza ki a felhasználót a felhasználók listájából, és **engedélyezze**, **Tiltsa le** vagy **kényszerítse** ki a többtényezős hitelesítést igény szerint.

   * **Megjegyzés**: Ha egy felhasználó **kényszerített** állapotban van, beállíthatja, hogy ideiglenesen **letiltsa** őket, hogy visszalépjenek a fiókjába. Ha ismét bejelentkeznek, a következő bejelentkezéskor **újra meg** lehet változtatni az állapotukat, hogy újra regisztrálni tudják a kapcsolattartási adatokat. Azt is megteheti, hogy a [felhasználó hitelesítési kapcsolattartási adatainak ellenőrzése](#check-a-users-authentication-contact-info) lapon található lépéseket követve ellenőrizheti vagy beállítja ezeket az adatokat.

### <a name="check-a-users-authentication-contact-info"></a>A felhasználó hitelesítési kapcsolattartási adatainak megkeresése

A többtényezős hitelesítéshez, a feltételes hozzáféréshez, az Identitáskezelés és a jelszó alaphelyzetbe állításához használt hitelesítési kapcsolattartási adatok vizsgálatához kövesse az alábbi lépéseket:

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5.  kattintson **a minden felhasználó** elemre.

6.  **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7.  kattintson a **profil** elemre.

8.  Görgessen le a **hitelesítési kapcsolattartási adatokhoz**.

9.  **Tekintse át** a felhasználó számára regisztrált és a frissítéshez szükséges, igény szerinti frissítést.

### <a name="check-a-users-group-memberships"></a>Felhasználó csoport-tagságának keresése

A felhasználók csoporttagságok vizsgálatához kövesse az alábbi lépéseket:

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5.  kattintson **a minden felhasználó** elemre.

6.  **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7.  a **csoportok** elemre kattintva megtekintheti, hogy a felhasználó mely csoportok tagjai.

### <a name="check-a-users-assigned-licenses"></a>Felhasználóhoz rendelt licencek keresése

A felhasználóhoz hozzárendelt licencek vizsgálatához kövesse az alábbi lépéseket:

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5.  kattintson **a minden felhasználó** elemre.

6.  **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7.  a **licencek** elemre kattintva megtekintheti, hogy a felhasználó mely licenceket rendeli hozzá jelenleg.

### <a name="assign-a-user-a-license"></a>Licenc felhasználóhoz rendelése 

Ha licencet szeretne hozzárendelni egy felhasználóhoz, kövesse az alábbi lépéseket:

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5.  kattintson **a minden felhasználó** elemre.

6.  **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7.  a **licencek** elemre kattintva megtekintheti, hogy a felhasználó mely licenceket rendeli hozzá jelenleg.

8.  kattintson a **hozzárendelés** gombra.

9.  Válasszon ki **egy vagy több terméket** az elérhető termékek listájából.

10. A termékek részletes hozzárendeléséhez kattintson a **hozzárendelési beállítások** **elemre** . Ha a művelet befejeződött, kattintson **az OK gombra** .

11. Kattintson a **hozzárendelés** gombra a licencek ehhez a felhasználóhoz való hozzárendeléséhez.

## <a name="problems-with-groups"></a>Problémák a csoportokkal

Az alkalmazáshoz való hozzáférés az alkalmazáshoz rendelt csoporttal kapcsolatos probléma miatt blokkolható. A következő módokon végezheti el a csoportok és csoporttagságok problémáinak elhárítását és megoldását:

-   [Csoport tagságának keresése](#check-a-groups-membership)

-   [Dinamikus csoport tagsági feltételeinek bejelölése](#check-a-dynamic-groups-membership-criteria)

-   [Csoport hozzárendelt licencének keresése](#check-a-groups-assigned-licenses)

-   [Csoport licencének újrafeldolgozása](#reprocess-a-groups-licenses)

-   [Licenc-csoport társítása](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Csoport tagságának keresése

A csoport tagságának vizsgálatához kövesse az alábbi lépéseket:

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5.  kattintson **a minden csoport** elemre.

6.  **Keresse** meg az Önt érdeklő csoportot, és **kattintson a** kijelölni kívánt sorra.

7.  kattintson a **tagok** elemre a csoporthoz rendelt felhasználók listájának áttekintéséhez.

### <a name="check-a-dynamic-groups-membership-criteria"></a>Dinamikus csoport tagsági feltételeinek bejelölése 

A dinamikus csoport tagsági feltételeinek vizsgálatához kövesse az alábbi lépéseket:

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5.  kattintson **a minden csoport** elemre.

6.  **Keresse** meg az Önt érdeklő csoportot, és **kattintson a** kijelölni kívánt sorra.

7.  kattintson a **dinamikus tagsági szabályok** elemre.

8.  Tekintse át az ehhez a csoporthoz definiált **egyszerű** vagy **speciális** szabályt, és győződjön meg arról, hogy a csoport tagjának kell lennie a feltételnek.

### <a name="check-a-groups-assigned-licenses"></a>Csoport hozzárendelt licencének keresése

A csoporthoz hozzárendelt licencek vizsgálatához kövesse az alábbi lépéseket:

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5.  kattintson **a minden csoport** elemre.

6.  **Keresse** meg az Önt érdeklő csoportot, és **kattintson a** kijelölni kívánt sorra.

7.  a **licencek** elemre kattintva megtekintheti, hogy mely licencek vannak hozzárendelve a csoporthoz.

### <a name="reprocess-a-groups-licenses"></a>Csoport licencének újrafeldolgozása

A csoporthoz hozzárendelt licencek újrafeldolgozásához kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5. kattintson **a minden csoport** elemre.

6. **Keresse** meg az Önt érdeklő csoportot, és **kattintson a** kijelölni kívánt sorra.

7. a **licencek** elemre kattintva megtekintheti, hogy mely licencek vannak hozzárendelve a csoporthoz.

8. az **újrafeldolgozás** gombra kattintva győződjön meg arról, hogy a csoport tagjaihoz rendelt licencek naprakészek. Ez hosszú időt is igénybe vehet, a csoport méretétől és bonyolultságától függően.

   >[!NOTE]
   >Ennek gyorsabb elvégzéséhez érdemes lehet ideiglenesen hozzárendelni egy licencet a felhasználóhoz. [Rendeljen hozzá egy licencet a felhasználóhoz](#problems-with-application-consent).
   >
   >

### <a name="assign-a-group-a-license"></a>Licenc-csoport társítása

A licencek csoporthoz rendeléséhez kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5. kattintson **a minden csoport** elemre.

6. **Keresse** meg az Önt érdeklő csoportot, és **kattintson a** kijelölni kívánt sorra.

7. a **licencek** elemre kattintva megtekintheti, hogy mely licencek vannak hozzárendelve a csoporthoz.

8. kattintson a **hozzárendelés** gombra.

9. Válasszon ki **egy vagy több terméket** az elérhető termékek listájából.

10. A termékek részletes hozzárendeléséhez kattintson a **hozzárendelési beállítások** **elemre** . Ha a művelet befejeződött, kattintson **az OK gombra** .

11. Kattintson a **hozzárendelés** gombra a licencek ehhez a csoporthoz való hozzárendeléséhez. Ez hosszú időt is igénybe vehet, a csoport méretétől és bonyolultságától függően.

    >[!NOTE]
    >Ennek gyorsabb elvégzéséhez érdemes lehet ideiglenesen hozzárendelni egy licencet a felhasználóhoz. [Rendeljen hozzá egy licencet a felhasználóhoz](#problems-with-application-consent).
    > 
    >

## <a name="problems-with-conditional-access-policies"></a>A feltételes hozzáférési szabályzatokkal kapcsolatos problémák

### <a name="check-a-specific-conditional-access-policy"></a>Konkrét feltételes hozzáférési szabályzat keresése

Egyetlen feltételes hozzáférési szabályzat ellenőrzése vagy ellenőrzése:

1. Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. a navigációs menüben kattintson a **vállalati alkalmazások** elemre.

5. kattintson a **feltételes hozzáférés** navigációs elemre.

6. kattintson a vizsgálni kívánt szabályzatra.

7. Ellenőrizze, hogy nincsenek-e olyan konkrét feltételek, hozzárendelések vagy egyéb beállítások, amelyek blokkolják a felhasználói hozzáférést.

   >[!NOTE]
   >Előfordulhat, hogy ideiglenesen le szeretné tiltani ezt a házirendet annak biztosítása érdekében, hogy ne befolyásolja a bejelentkezéseket. Ehhez állítsa a **házirend engedélyezése** kapcsolót **nem** értékre, majd kattintson a **Mentés** gombra.
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Egy adott alkalmazás feltételes hozzáférési házirendjének megkeresése

Egyetlen alkalmazás jelenleg konfigurált feltételes hozzáférési házirendjének ellenőrzése vagy ellenőrzése:

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  a navigációs menüben kattintson a **vállalati alkalmazások** elemre.

5.  kattintson **a minden alkalmazás** elemre.

6.  Keresse meg az Önt érdeklő alkalmazást, vagy a felhasználó megpróbál bejelentkezni az alkalmazás megjelenítendő neve vagy az alkalmazás azonosítója szerint.

     >[!NOTE]
     >Ha nem látja a keresett alkalmazást, kattintson a **szűrő** gombra, és bontsa ki a lista hatókörét **minden alkalmazásra**. Ha további oszlopokat szeretne látni, kattintson az **oszlopok** gombra az alkalmazások további részleteinek hozzáadásához.
     >
     >

7.  kattintson a **feltételes hozzáférés** navigációs elemre.

8.  kattintson a vizsgálni kívánt szabályzatra.

9.  Ellenőrizze, hogy nincsenek-e konkrét feltételek, hozzárendelések vagy más olyan beállítások, amelyek blokkolják a felhasználói hozzáférést.

     >[!NOTE]
     >Előfordulhat, hogy ideiglenesen le szeretné tiltani ezt a házirendet annak biztosítása érdekében, hogy ne befolyásolja a bejelentkezéseket. Ehhez állítsa a **házirend engedélyezése** kapcsolót **nem** értékre, majd kattintson a **Mentés** gombra.
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Adott feltételes hozzáférési szabályzat letiltása

Egyetlen feltételes hozzáférési szabályzat ellenőrzése vagy ellenőrzése:

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  a navigációs menüben kattintson a **vállalati alkalmazások** elemre.

5.  kattintson a **feltételes hozzáférés** navigációs elemre.

6.  kattintson a vizsgálni kívánt szabályzatra.

7.  A házirend letiltásához állítsa be a **házirend engedélyezése** beállítást **nem** értékre, majd kattintson a **Save (Mentés** ) gombra.

## <a name="problems-with-application-consent"></a>Alkalmazás-beleegyezett problémák

Az alkalmazás hozzáférése blokkolható, mert nem történt meg a megfelelő engedélyek beleegyezett művelete. Az alábbi módokon lehet elhárítani az alkalmazás-beleegyezett problémák megoldását:

-   [Felhasználói szintű beleegyezett művelet végrehajtása](#perform-a-user-level-consent-operation)

-   [Rendszergazdai szintű beleegyező művelet végrehajtása bármely alkalmazáshoz](#perform-administrator-level-consent-operation-for-any-application)

-   [Rendszergazdai szintű engedély végrehajtása egyetlen bérlős alkalmazáshoz](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Rendszergazdai szintű engedély végrehajtása több-bérlős alkalmazáshoz](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Felhasználói szintű beleegyezett művelet végrehajtása

-   Minden olyan nyitott AZONOSÍTÓval rendelkező, kapcsolattal rendelkező alkalmazás, amely engedélyeket kér, az alkalmazás bejelentkezési képernyőjén való navigálás felhasználói szintű beleegyezést biztosít az alkalmazásnak a bejelentkezett felhasználó számára.

-   Ha ezt programozott módon szeretné elvégezni, tekintse meg az [egyéni felhasználói engedély kérése](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent)című részt.

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Rendszergazdai szintű beleegyező művelet végrehajtása bármely alkalmazáshoz

-   **Csak a v1-es alkalmazási modellel fejlesztett alkalmazások** esetében kényszerítheti ezt a rendszergazdai szintű hozzájárulást, ha az alkalmazás bejelentkezési URL-címének végéhez hozzáadja a "**? prompt = rendszergazdai \_ jóváhagyás" kifejezést**.

-   A **v2-alkalmazási modellel fejlesztett bármely alkalmazás** esetében kényszerítheti ezt a rendszergazdai szintű beleegyező jogosultságot, ha az **engedélyek kérése a címtár-rendszergazda** számára című részben található utasításokat követve a [rendszergazdai engedélyezési végpontot használja](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Rendszergazdai szintű engedély végrehajtása egyetlen bérlős alkalmazáshoz

-   Az olyan **egybérlős alkalmazások** esetében, amelyek engedélyeket kérnek (például a szervezeten belül fejlesztenek vagy azok tulajdonosai), az összes felhasználó nevében elvégezheti a **rendszergazdai szintű jóváhagyást** , ha globális rendszergazdaként jelentkezik be, majd az **engedélyek megadása** gombra kattint az **alkalmazás beállításjegyzékének tetején – &gt; minden alkalmazás – &gt; válassza ki az alkalmazáshoz &gt; szükséges engedélyek** ablaktáblát.

-   A **v1 vagy v2 alkalmazás modelljével fejlesztett alkalmazások** esetében kényszerítheti ezt a rendszergazdai szintű beleegyező jogosultságot, ha az **engedélyek kérése a címtár-rendszergazda** számára című részben található utasításokat követve a [rendszergazdai engedélyezési végpontot használja](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Rendszergazdai szintű engedély végrehajtása több-bérlős alkalmazáshoz

-   Olyan **több-bérlős alkalmazások** esetében, amelyek engedélyeket kérnek (például egy harmadik féltől származó alkalmazás, vagy a Microsoft, fejleszt), **rendszergazdai szintű beleegyező** műveletet hajthat végre. Jelentkezzen be globális rendszergazdaként, és kattintson az **engedélyek megadása** gombra a **vállalati alkalmazások – minden alkalmazás területen, és &gt; &gt; válassza ki az alkalmazás- &gt; engedélyek** panelt (hamarosan elérhető).

-   Ezt a rendszergazdai szintű beleegyező jogosultságot arra is kényszerítheti, hogy a rendszergazdai [engedélyezési végpont használatával](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)adja meg az **engedélyek kérése egy címtár-rendszergazda** szakasz utasításait.

## <a name="next-steps"></a>Következő lépések
[A rendszergazdai engedélyezési végpont használata](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)