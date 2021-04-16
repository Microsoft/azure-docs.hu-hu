---
title: Problémák a Microsoft-alkalmazásokba való | Microsoft Docs
description: Elháríthatja a külső Microsoft-alkalmazásokba az Azure AD használatával (például az azure-beli virtuális Microsoft 365) való bejelentkezés során gyakran Microsoft 365.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/10/2018
ms.author: iangithinji
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c56e356a9bacc6479d3a3a33be905457c26e732e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378176"
---
# <a name="problems-signing-in-to-a-microsoft-application"></a>Problémák a Microsoft-alkalmazásokba való bejelentkezés során

A Microsoft-alkalmazások (például Exchange, SharePoint, Yammer stb.) hozzárendelése és kezelése kissé eltér a harmadik féltől származó SaaS-alkalmazásoktól és az Azure AD-be az egyszeri bejelentkezéshez integrálható egyéb alkalmazásoktól.

A felhasználók három fő módon férhetnek hozzá a Microsoft által közzétett alkalmazásokhoz.

-   A Microsoft 365 vagy más fizetős csomagokban található alkalmazásokhoz  a felhasználók közvetlenül a felhasználói fiókjukhoz vagy a csoportalapú licenc-hozzárendelési képességünket használó csoporton keresztül kapnak hozzáférést a licenc-hozzárendeléssel.

-   A Microsoft vagy egy harmadik fél által bárki számára ingyenesen közzétett alkalmazások esetében a felhasználók a felhasználói hozzájárulással kapnak **hozzáférést.** Ez azt jelenti, hogy bejelentkeznek az alkalmazásba a munkahelyi vagy iskolai Azure AD-fiókjukkal, és lehetővé teszik, hogy a fiókjukban korlátozott mennyiségű adathoz férnek hozzá.

-   A Microsoft vagy külső fél által bárki számára ingyenesen közzétett alkalmazások esetén a felhasználók rendszergazdai jóváhagyással is **hozzáférést kapnak.** Ez azt jelenti, hogy egy rendszergazda megállapította, hogy az alkalmazást a szervezet minden tagja hatja, ezért bejelentkezik az alkalmazásba egy globális rendszergazdai fiókkal, és hozzáférést biztosít a szervezet minden tagja számára.

A probléma elhárításához kezdje [](#general-problem-areas-with-application-access-to-consider) az Alkalmazás-hozzáféréssel kapcsolatos általános problématerületekkel, majd olvassa el a Részletes útmutató: A Microsoft-alkalmazások hozzáférésének hibaelhárításához szükséges lépések témakört.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Az alkalmazás-hozzáféréssel és a megfontolni szükséges általános problématerületekkel

Az alábbi lista azokat az általános problémákat sorolja fel, amelyekről további információkat kaphat, ha van elképzelése a kezdés helyével, de javasoljuk, hogy olvassa el a gyors kezdéshez szükséges útmutatót: Útmutató: A Microsoft-alkalmazások elérésével kapcsolatos hibák elhárításának lépései.

-   [A felhasználói fiókkal kapcsolatos problémák](#problems-with-the-users-account)

-   [Csoportokkal kapcsolatos problémák](#problems-with-groups)

-   [Feltételes hozzáférési szabályzatokkal kapcsolatos problémák](#problems-with-conditional-access-policies)

-   [Problémák az alkalmazás hozzájárulásával](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>A Microsoft-alkalmazások elérésével kapcsolatos hibák elhárításának lépései

Az alábbiakban néhány olyan gyakori problémát találjanak, amelyek akkor lépnek fel, amikor a felhasználók nem tudnak bejelentkezni egy Microsoft-alkalmazásba.

- Általános problémák, amelyek először ellenőrizve vannak

  * Győződjön meg arról, hogy a felhasználó a megfelelő **URL-címre** jelentkezik be, nem pedig egy helyi alkalmazás URL-címére.

  * Győződjön meg arról, hogy a felhasználó fiókja **nincs kizárva.**

  * Győződjön meg **arról, hogy a felhasználó fiókja létezik a** Azure Active Directory. [Ellenőrizze, hogy létezik-e felhasználói fiók a Azure Active Directory](#problems-with-the-users-account)

  * Győződjön meg arról, hogy  a felhasználó fiókja engedélyezve van a bejelentkezéshez. [Felhasználó fiókállapotának ellenőrzése](#problems-with-the-users-account)

  * Győződjön meg arról, hogy a felhasználó **jelszava nem járt le vagy felejti el.** [Felhasználó jelszavának alaphelyzetbe állítása vagy](#reset-a-users-password) [Önkiszolgáló jelszó-visszaállítás engedélyezése](../authentication/tutorial-enable-sspr.md)

  * Győződjön meg arról, hogy a **Multi-Factor Authentication** nem blokkolja a felhasználói hozzáférést. [Ellenőrizze egy felhasználó többtényezős](#check-a-users-multi-factor-authentication-status) hitelesítési állapotát vagy a felhasználó hitelesítési kapcsolattartási [adatait](#check-a-users-authentication-contact-info)

  * Győződjön meg arról, hogy a **feltételes hozzáférési szabályzat** vagy az Identity **Protection-szabályzat** nem blokkolja a felhasználói hozzáférést. [Ellenőrizzen egy adott feltételes hozzáférési szabályzatot](#problems-with-conditional-access-policies) vagy [egy](#check-a-specific-applications-conditional-access-policy) adott alkalmazás feltételes hozzáférési szabályzatát, vagy tiltsa [le az adott feltételes hozzáférési szabályzatot](#disable-a-specific-conditional-access-policy)

  * Győződjön meg arról,  hogy a felhasználó hitelesítési kapcsolattartási adatai naprakészek, hogy a többtényezős hitelesítés vagy a feltételes hozzáférési szabályzatok kényszerítve legyen. [Ellenőrizze egy felhasználó többtényezős](#check-a-users-multi-factor-authentication-status) hitelesítési állapotát vagy a felhasználó hitelesítési kapcsolattartási [adatait](#check-a-users-authentication-contact-info)

- A **licencet** igénylő **Microsoft-alkalmazások** (például az Office365) esetében az alábbi konkrét problémákat kell ellenőrizni, miután kizárta a fenti általános problémákat:

  * Győződjön meg arról, hogy a felhasználó vagy licenc **van hozzárendelve.** [Egy felhasználóhoz hozzárendelt licencek vagy](#check-a-users-assigned-licenses) [csoporthoz rendelt licencek ellenőrzése](#check-a-groups-assigned-licenses)

  * Ha a licenc egy **statikus csoporthoz** van **rendelve,** győződjön meg arról, hogy a felhasználó tagja ennek **a** csoportnak. [Felhasználó csoporttagságának ellenőrzése](#check-a-users-group-memberships)

  * Ha a licenc dinamikus csoporthoz van **rendelve,** győződjön meg arról, hogy **a** **dinamikuscsoport-szabály helyesen van beállítva.** [Dinamikus csoport tagsági feltételeinek ellenőrzése](#check-a-dynamic-groups-membership-criteria)

  * Ha a licenc dinamikus csoporthoz van rendelve,  győződjön meg arról, hogy a dinamikus csoport befejezte **a** tagságának feldolgozását, és hogy a felhasználó **tag** (ez némi időt is időt is vegyen ki). [Felhasználó csoporttagságának ellenőrzése](#check-a-users-group-memberships)

  *  A licenc hozzárendelése után győződjön meg arról, hogy a licenc **nem járt le.**

  *  Győződjön meg arról, hogy a licenc **arra az alkalmazásra érvényes,** amelyhez hozzáfér.

- A **-licencet** nem igénylő **Microsoft-alkalmazások** esetében az alábbi dolgokat is ellenőrizni kell:

  * Ha az alkalmazás felhasználói szintű engedélyeket **kér** (például "Hozzáférés **a** felhasználó postaládájához"), győződjön meg arról, hogy a felhasználó bejelentkezett az alkalmazásba, és végrehajtott egy felhasználói szintű hozzájárulási műveletet, hogy az alkalmazás hozzáfért az adataihoz.

  * Ha az alkalmazás rendszergazdai szintű engedélyeket **kér** (például "Minden felhasználó postaládájának elérése"), győződjön  meg arról, hogy a globális rendszergazda rendszergazdai szintű hozzájárulási műveletet hajtott végre a szervezet összes felhasználója nevében.

## <a name="problems-with-the-users-account"></a>A felhasználói fiókkal kapcsolatos problémák

Az alkalmazás-hozzáférés az alkalmazáshoz rendelt felhasználóval való probléma miatt blokkolható. A következő módokon háríthatja el és oldhatja meg a felhasználókkal és a fiókbeállításokkal kapcsolatos problémákat:

-   [Ellenőrizze, hogy létezik-e felhasználói fiók a Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Felhasználó fiókállapotának ellenőrzése](#check-a-users-account-status)

-   [Felhasználó jelszavának alaphelyzetbe állítása](#reset-a-users-password)

-   [Új jelszó önkiszolgáló kérésének engedélyezése](#enable-self-service-password-reset)

-   [A felhasználó többtényezős hitelesítési állapotának ellenőrzése](#check-a-users-multi-factor-authentication-status)

-   [Felhasználó hitelesítési kapcsolattartási adatainak ellenőrzése](#check-a-users-authentication-contact-info)

-   [Felhasználó csoporttagságának ellenőrzése](#check-a-users-group-memberships)

-   [Felhasználóhoz hozzárendelt licencek ellenőrzése](#check-a-users-assigned-licenses)

-   [Licenc hozzárendelése egy felhasználóhoz](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Ellenőrizze, hogy létezik-e felhasználói fiók a Azure Active Directory

A következő lépésekkel ellenőrizheti, hogy jelen van-e egy felhasználó fiókja:

1.  Nyissa meg [**Azure Portal,**](https://portal.azure.com/) és jelentkezzen be globális **rendszergazdaként.**

2.  Nyissa meg **Azure Active Directory bővítményt**  a bal oldali fő navigációs menü tetején található Minden szolgáltatás elemre kattintva.

3.  Írja be **a "Azure Active Directory"** elemet a szűrő keresőmezőbe, és válassza ki **Azure Active Directory** elemet.

4.  A **navigációs menüben kattintson a** Felhasználók és csoportok elemre.

5.  kattintson a **Minden felhasználó elemre.**

6.  **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a kiválasztni kívánt** sorra.

7.  Ellenőrizze a felhasználói objektum tulajdonságait, hogy a vártnak megfelelőek-e, és hogy nem hiányoznak-e adatok.

### <a name="check-a-users-account-status"></a>Felhasználó fiókállapotának ellenőrzése

Egy felhasználó fiókállapotának ellenőrzéshez kövesse az alábbi lépéseket:

1.  Nyissa meg [**Azure Portal,**](https://portal.azure.com/) és jelentkezzen be globális **rendszergazdaként.**

2.  Nyissa meg **Azure Active Directory bővítményt**  a bal oldali fő navigációs menü tetején található Minden szolgáltatás elemre kattintva.

3.  Írja be **a "Azure Active Directory"** elemet a szűrő keresőmezőbe, és válassza ki **Azure Active Directory** elemet.

4.  A **navigációs menüben kattintson a** Felhasználók és csoportok elemre.

5.  Kattintson a **Minden felhasználó elemre.**

6.  **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a sorra a** kiválasztásához.

7.  Kattintson a **Profil elemre.**

8.  A **Beállítások alatt** győződjön meg arról, hogy a Bejelentkezés **blokkolása** beállítás nem. 

### <a name="reset-a-users-password"></a>Felhasználó jelszavának alaphelyzetbe állítása

Egy felhasználó jelszavának alaphelyzetbe állításához kövesse az alábbi lépéseket:

1.  Nyissa meg [**Azure Portal,**](https://portal.azure.com/) és jelentkezzen be globális **rendszergazdaként.**

2.  Nyissa meg **Azure Active Directory bővítményt**  a bal oldali fő navigációs menü tetején található Minden szolgáltatás elemre kattintva.

3.  Írja be **a "Azure Active Directory"** elemet a szűrő keresőmezőbe, és válassza ki **Azure Active Directory** elemet.

4.  A **navigációs menüben kattintson a** Felhasználók és csoportok elemre.

5.  kattintson **a Minden felhasználó elemre.**

6.  **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a kiválasztni kívánt** sorra.

7.  kattintson **a Jelszó alaphelyzetbe állítása** gombra a felhasználó panel tetején.

8.  kattintson **a megjelenő Jelszó** alaphelyzetbe állítása gombra a **Jelszó** alaphelyzetbe állítása panelen.

9.  Másolja ki **az ideiglenes jelszót,** **vagy adjon meg egy új jelszót a** felhasználónak.

10. Ezt az új jelszót a felhasználónak kell megadnia, mert a következő bejelentkezés során módosítania kell a Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Új jelszó önkiszolgáló kérésének engedélyezése

Az új jelszó önkiszolgáló beállításának engedélyezéséhez kövesse az alábbi üzembe helyezési lépéseket:

-   [Új jelszó beállításának engedélyezése a Azure Active Directory számára](../authentication/tutorial-enable-sspr.md)

-   [A helyszíni jelszavak visszaállításának vagy Active Directory engedélyezése a felhasználóknak](../authentication/tutorial-enable-sspr.md)

### <a name="check-a-users-multi-factor-authentication-status"></a>A felhasználó többtényezős hitelesítési állapotának ellenőrzése

A felhasználó többtényezős hitelesítési állapotának ellenőrzéséhez kövesse az alábbi lépéseket:

1. Nyissa meg [**Azure Portal,**](https://portal.azure.com/) és jelentkezzen be globális **rendszergazdaként.**

2. Nyissa meg **Azure Active Directory bővítményt**  a bal oldali fő navigációs menü tetején található Minden szolgáltatás elemre kattintva.

3. Írja be **a "Azure Active Directory"** elemet a szűrő keresőmezőbe, és válassza ki **Azure Active Directory** elemet.

4. A **navigációs menüben kattintson a** Felhasználók és csoportok elemre.

5. kattintson a **Minden felhasználó elemre.**

6. kattintson a **Multi-Factor Authentication** gombra a panel tetején.

7. A **Multi-Factor Authentication felügyeleti portál betöltése után** győződjön meg arról, hogy a Felhasználók **lapon** van.

8. Keresse meg a felhasználót a felhasználók listájában kereséssel, szűréssel vagy rendezéssel.

9. Válassza ki a felhasználót a felhasználók listájából, és szükség szerint **engedélyezze,** **tiltsa** le vagy kényszerítje **a** többtényezős hitelesítést.

   * **Megjegyzés:** Ha egy felhasználó Kényszerítve állapotban **van,**  ideiglenesen Letiltva állapotra állíthatja, hogy vissza tudja állítani a fiókjába. Miután visszatértek, az állapotukat ismét  Engedélyezve állapotra módosíthatja, hogy megkövetelni tudja számukra a kapcsolattartási adataik újra regisztrálását a következő bejelentkezés során. Másik lehetőségként a Felhasználó hitelesítési [](#check-a-users-authentication-contact-info) kapcsolattartási adatainak ellenőrzése lépéseit követve ellenőrizheti vagy beállíthatja számukra ezeket az adatokat.

### <a name="check-a-users-authentication-contact-info"></a>Felhasználó hitelesítési kapcsolattartási adatainak ellenőrzése

A felhasználók többtényezős hitelesítéshez, feltételes hozzáféréshez, identitásvédelemhez és jelszó-visszaállításhoz használt hitelesítési kapcsolattartási adatainak ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg [**Azure Portal,**](https://portal.azure.com/) és jelentkezzen be globális **rendszergazdaként.**

2.  Nyissa meg **Azure Active Directory bővítményt**  a bal oldali fő navigációs menü tetején található Minden szolgáltatás elemre kattintva.

3.  Írja be **a "Azure Active Directory"** elemet a szűrő keresőmezőbe, és válassza ki **Azure Active Directory** elemet.

4.  A **navigációs menüben kattintson a** Felhasználók és csoportok elemre.

5.  Kattintson a **Minden felhasználó elemre.**

6.  **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a kiválasztni kívánt** sorra.

7.  Kattintson a **Profil elemre.**

8.  Görgessen le **a Hitelesítési kapcsolattartási adatokhoz.**

9.  **Tekintse** át a felhasználóhoz regisztrált adatokat, és szükség szerint frissítsen.

### <a name="check-a-users-group-memberships"></a>Felhasználó csoporttagságának ellenőrzése

A felhasználó csoporttagságának ellenőrzéshez kövesse az alábbi lépéseket:

1.  Nyissa meg [**Azure Portal,**](https://portal.azure.com/) és jelentkezzen be globális **rendszergazdaként.**

2.  Nyissa meg **Azure Active Directory bővítményt**  a bal oldali fő navigációs menü tetején található Minden szolgáltatás elemre kattintva.

3.  Írja be **a "Azure Active Directory"** elemet a szűrő keresőmezőbe, és válassza ki **Azure Active Directory** elemet.

4.  A **navigációs menüben kattintson a** Felhasználók és csoportok elemre.

5.  Kattintson a **Minden felhasználó elemre.**

6.  **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a sorra a** kiválasztásához.

7.  Kattintson **a Csoportok** elemre, és tekintse meg, hogy a felhasználó mely csoportoknak tagja.

### <a name="check-a-users-assigned-licenses"></a>Felhasználóhoz hozzárendelt licencek ellenőrzése

A felhasználóhoz hozzárendelt licencek ellenőrzéshez kövesse az alábbi lépéseket:

1.  Nyissa meg [**Azure Portal,**](https://portal.azure.com/) és jelentkezzen be globális **rendszergazdaként.**

2.  Nyissa meg **Azure Active Directory bővítményt**  a bal oldali fő navigációs menü tetején található Minden szolgáltatás elemre kattintva.

3.  Írja be **a "Azure Active Directory"** elemet a szűrő keresőmezőbe, és válassza ki **Azure Active Directory** elemet.

4.  A **navigációs menüben kattintson a** Felhasználók és csoportok elemre.

5.  kattintson a **Minden felhasználó elemre.**

6.  **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a kiválasztni kívánt** sorra.

7.  Kattintson **a Licencek elemre,** hogy lássa, a felhasználó jelenleg milyen licenceket rendelt hozzá.

### <a name="assign-a-user-a-license"></a>Licenc hozzárendelése felhasználóhoz 

Ha egy felhasználóhoz szeretne licencet rendelni, kövesse az alábbi lépéseket:

1.  Nyissa meg [**Azure Portal,**](https://portal.azure.com/) és jelentkezzen be globális **rendszergazdaként.**

2.  Nyissa meg **Azure Active Directory bővítményt**  a bal oldali fő navigációs menü tetején található Minden szolgáltatás elemre kattintva.

3.  Írja be **a "Azure Active Directory"** elemet a szűrő keresőmezőbe, és válassza ki **Azure Active Directory** elemet.

4.  A **navigációs menüben kattintson a** Felhasználók és csoportok elemre.

5.  kattintson a **Minden felhasználó elemre.**

6.  **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a kiválasztni kívánt** sorra.

7.  Kattintson **a Licencek elemre,** hogy lássa, a felhasználó jelenleg milyen licenceket rendelt hozzá.

8.  kattintson a **Hozzárendelés gombra.**

9.  Válasszon **ki egy vagy több terméket** az elérhető termékek listájából.

10. **Nem kötelező:** kattintson **a hozzárendelési beállítások** elemre a termékek részletes hozzárendelése érdekében. Ha **ez befejeződött,** kattintson az OK gombra.

11. Kattintson a **Hozzárendelés gombra** a licencek felhasználóhoz való hozzárendeléshez.

## <a name="problems-with-groups"></a>Csoportokkal kapcsolatos problémák

Az alkalmazás-hozzáférés az alkalmazáshoz rendelt csoporttal való probléma miatt blokkolható. A következő módokon háríthatja el és oldhatja meg a csoportokkal és csoporttagságokkal kapcsolatos problémákat:

-   [Csoporttagság ellenőrzése](#check-a-groups-membership)

-   [Dinamikus csoport tagsági feltételeinek ellenőrzése](#check-a-dynamic-groups-membership-criteria)

-   [Csoport hozzárendelt licencének ellenőrzése](#check-a-groups-assigned-licenses)

-   [Csoport licencének újrafeldolgozása](#reprocess-a-groups-licenses)

-   [Licenc hozzárendelése csoporthoz](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Csoporttagság ellenőrzése

Egy csoport tagságának ellenőrzéshez kövesse az alábbi lépéseket:

1.  Nyissa meg [**Azure Portal,**](https://portal.azure.com/) és jelentkezzen be globális **rendszergazdaként.**

2.  Nyissa meg **Azure Active Directory bővítményt**  a bal oldali fő navigációs menü tetején található Minden szolgáltatás elemre kattintva.

3.  Írja be **a "Azure Active Directory"** elemet a szűrő keresőmezőbe, és válassza ki **Azure Active Directory** elemet.

4.  A **navigációs menüben kattintson a** Felhasználók és csoportok elemre.

5.  Kattintson a **Minden csoport elemre.**

6.  **Keresse** meg az Önt érdeklő csoportot, és **kattintson a kiválasztni kívánt** sorra.

7.  kattintson **a Tagok** elemre a csoporthoz rendelt felhasználók listájának áttekintésére.

### <a name="check-a-dynamic-groups-membership-criteria"></a>Dinamikus csoport tagsági feltételeinek ellenőrzése 

A dinamikus csoport tagsági feltételeinek ellenőrzéshez kövesse az alábbi lépéseket:

1.  Nyissa meg [**Azure Portal,**](https://portal.azure.com/) és jelentkezzen be globális **rendszergazdaként.**

2.  Nyissa meg **Azure Active Directory bővítményt**  a bal oldali fő navigációs menü tetején található Minden szolgáltatás elemre kattintva.

3.  Írja be **a "Azure Active Directory"** elemet a szűrő keresőmezőbe, és válassza ki **Azure Active Directory** elemet.

4.  A **navigációs menüben kattintson a** Felhasználók és csoportok elemre.

5.  kattintson a **Minden csoport elemre.**

6.  **Keresse** meg a kívánt csoportot, és **kattintson a kiválasztni kívánt** sorra.

7.  kattintson **a Dinamikus tagsági szabályok elemre.**

8.  Tekintse át **a csoporthoz** **definiált** egyszerű vagy speciális szabályt, és győződjön meg arról, hogy a csoport tagként kívánt felhasználója megfelel ezeknek a feltételeknek.

### <a name="check-a-groups-assigned-licenses"></a>Csoport hozzárendelt licencének ellenőrzése

Egy csoport hozzárendelt licencének ellenőrzéshez kövesse az alábbi lépéseket:

1.  Nyissa meg [**Azure Portal,**](https://portal.azure.com/) és jelentkezzen be globális **rendszergazdaként.**

2.  Nyissa meg **Azure Active Directory bővítményt**  a bal oldali fő navigációs menü tetején található Minden szolgáltatás elemre kattintva.

3.  Írja be **a "Azure Active Directory"** elemet a szűrő keresőmezőbe, és válassza ki **Azure Active Directory** elemet.

4.  A **navigációs menüben kattintson a** Felhasználók és csoportok elemre.

5.  kattintson a **Minden csoport elemre.**

6.  **Keresse** meg a kívánt csoportot, és **kattintson a kiválasztni kívánt** sorra.

7.  Kattintson **a Licencek elemre,** hogy lássa, a csoport jelenleg mely licenceket rendelte hozzá.

### <a name="reprocess-a-groups-licenses"></a>Csoport licencének újrafeldolgozása

Egy csoport hozzárendelt licencének újrafeldolgozáshoz kövesse az alábbi lépéseket:

1. Nyissa meg [**Azure Portal,**](https://portal.azure.com/) és jelentkezzen be globális **rendszergazdaként.**

2. Nyissa meg **Azure Active Directory bővítményt**  a bal oldali fő navigációs menü tetején található Minden szolgáltatás elemre kattintva.

3. Írja be **a "Azure Active Directory"** elemet a szűrő keresőmezőbe, és válassza ki **Azure Active Directory** elemet.

4. A **navigációs menüben kattintson a** Felhasználók és csoportok elemre.

5. kattintson a **Minden csoport elemre.**

6. **Keresse** meg a kívánt csoportot, és **kattintson a kiválasztni kívánt** sorra.

7. Kattintson **a Licencek elemre,** hogy lássa, a csoport jelenleg mely licenceket rendelte hozzá.

8. kattintson **az Újrafeldolgozás gombra** annak biztosításához, hogy a csoport tagjaihoz rendelt licencek naprakészek. Ez a csoport méretétől és összetettségétől függően hosszú ideig is eltelhet.

   >[!NOTE]
   >A gyorsabb érdekében érdemes lehet ideiglenesen hozzárendelni egy licencet közvetlenül a felhasználóhoz. [Rendeljen egy felhasználóhoz egy licencet.](#problems-with-application-consent)
   >
   >

### <a name="assign-a-group-a-license"></a>Licenc hozzárendelése csoporthoz

Ha licenceket szeretne hozzárendelni egy csoporthoz, kövesse az alábbi lépéseket:

1. Nyissa meg [**Azure Portal,**](https://portal.azure.com/) és jelentkezzen be globális **rendszergazdaként.**

2. Nyissa meg **Azure Active Directory bővítményt**  a bal oldali fő navigációs menü tetején található Minden szolgáltatás elemre kattintva.

3. Írja be **a "Azure Active Directory"** elemet a szűrő keresőmezőbe, és válassza ki **Azure Active Directory** elemet.

4. A **navigációs menüben kattintson a** Felhasználók és csoportok elemre.

5. kattintson a **Minden csoport elemre.**

6. **Keresse** meg a kívánt csoportot, és **kattintson a kiválasztni kívánt** sorra.

7. Kattintson **a Licencek elemre,** hogy lássa, a csoport jelenleg mely licenceket rendelte hozzá.

8. kattintson a **Hozzárendelés gombra.**

9. Válasszon **ki egy vagy több terméket** az elérhető termékek listájából.

10. **Nem kötelező:** kattintson **a hozzárendelési beállítások** elemre a termékek részletes hozzárendelése érdekében. Ha **ez befejeződött,** kattintson az OK gombra.

11. Kattintson a **Hozzárendelés gombra** a licencek ehhez a csoporthoz való hozzárendeléshez. Ez a csoport méretétől és összetettségétől függően hosszú ideig is eltelhet.

    >[!NOTE]
    >A gyorsabb érdekében érdemes lehet ideiglenesen hozzárendelni egy licencet közvetlenül a felhasználóhoz. [Rendeljen egy felhasználóhoz egy licencet.](#problems-with-application-consent)
    > 
    >

## <a name="problems-with-conditional-access-policies"></a>Feltételes hozzáférési szabályzatokkal kapcsolatos problémák

### <a name="check-a-specific-conditional-access-policy"></a>Adott feltételes hozzáférési szabályzat ellenőrzése

Egyetlen feltételes hozzáférési szabályzat ellenőrzése vagy ellenőrzése:

1. Nyissa meg [**Azure Portal,**](https://portal.azure.com/) és jelentkezzen be globális **rendszergazdaként.**

2. Nyissa meg **Azure Active Directory bővítményt**  a bal oldali fő navigációs menü tetején található Minden szolgáltatás elemre kattintva.

3. Írja be **a "Azure Active Directory"** elemet a szűrő keresőmezőbe, és válassza ki **Azure Active Directory** elemet.

4. A **navigációs menüben kattintson** a Vállalati alkalmazások elemre.

5. kattintson **a Feltételes hozzáférés navigációs** elemre.

6. kattintson arra a szabályzatra, amelyet meg szeretne vizsgálni.

7. Ellenőrizze, hogy nincsenek-e olyan konkrét feltételek, hozzárendelések vagy egyéb beállítások, amelyek blokkolhatják a felhasználói hozzáférést.

   >[!NOTE]
   >Előfordulhat, hogy ideiglenesen le szeretné tiltani ezt a szabályzatot, hogy az ne befolyásolja a bejelentkezéseket. Ehhez állítsa a  Szabályzat engedélyezése kapcsolót Nem beállításra, **majd** kattintson a **Mentés gombra.**
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Adott alkalmazás feltételes hozzáférési szabályzatának ellenőrzése

Egyetlen alkalmazás jelenleg konfigurált feltételes hozzáférési szabályzatának ellenőrzése vagy ellenőrzése:

1.  Nyissa meg [**Azure Portal,**](https://portal.azure.com/) és jelentkezzen be globális **rendszergazdaként.**

2.  Nyissa meg **Azure Active Directory bővítményt**  a bal oldali fő navigációs menü tetején található Minden szolgáltatás elemre kattintva.

3.  Írja be **a "Azure Active Directory"** elemet a szűrő keresőmezőbe, és válassza ki **Azure Active Directory** elemet.

4.  A **navigációs menüben kattintson** a Vállalati alkalmazások elemre.

5.  kattintson a **Minden alkalmazás elemre.**

6.  Keresse meg azt az alkalmazást, amely érdekli, vagy ha a felhasználó az alkalmazás megjelenített neve vagy az alkalmazásazonosító alapján próbál bejelentkezni.

     >[!NOTE]
     >Ha nem látja a keresett alkalmazást, kattintson  a Szűrő gombra, és bontsa ki a lista hatókörét a **Minden alkalmazás elemre.** Ha több oszlopot szeretne látni, kattintson az **Oszlopok** gombra az alkalmazások további részleteinek hozzáadásához.
     >
     >

7.  kattintson **a Feltételes hozzáférés navigációs** elemre.

8.  kattintson arra a szabályzatra, amelyet meg szeretne vizsgálni.

9.  Ellenőrizze, hogy nincsenek-e olyan konkrét feltételek, hozzárendelések vagy egyéb beállítások, amelyek blokkolhatják a felhasználói hozzáférést.

     >[!NOTE]
     >Előfordulhat, hogy ideiglenesen le szeretné tiltani ezt a szabályzatot, hogy az ne befolyásolja a bejelentkezéseket. Ehhez állítsa a  Szabályzat engedélyezése kapcsolót Nem beállításra, **majd** kattintson a **Mentés gombra.**
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Adott feltételes hozzáférési szabályzat letiltása

Egyetlen feltételes hozzáférési szabályzat ellenőrzése vagy ellenőrzése:

1.  Nyissa meg [**Azure Portal,**](https://portal.azure.com/) és jelentkezzen be globális **rendszergazdaként.**

2.  Nyissa meg **Azure Active Directory bővítményt**  a bal oldali fő navigációs menü tetején található Minden szolgáltatás elemre kattintva.

3.  Írja be **a "Azure Active Directory"** elemet a szűrő keresőmezőbe, és válassza ki **Azure Active Directory** elemet.

4.  A **navigációs menüben kattintson** a Vállalati alkalmazások elemre.

5.  kattintson **a Feltételes hozzáférés navigációs** elemre.

6.  kattintson arra a szabályzatra, amelyet meg szeretne vizsgálni.

7.  Tiltsa le  a szabályzatot a Szabályzat engedélyezése kapcsoló Nem beállításával, **majd** kattintson a **Mentés gombra.**

## <a name="problems-with-application-consent"></a>Alkalmazás-jóváhagyással kapcsolatos problémák

Az alkalmazás-hozzáférés letiltható, mert nem történt meg a megfelelő engedély-jóváhagyási művelet. A következő módokon háríthatja el és oldhatja meg az alkalmazások hozzájárulásával kapcsolatos problémákat:

-   [Felhasználói szintű hozzájárulási művelet végrehajtása](#perform-a-user-level-consent-operation)

-   [Rendszergazdai szintű hozzájárulási művelet végrehajtása bármely alkalmazáshoz](#perform-administrator-level-consent-operation-for-any-application)

-   [Rendszergazdai szintű jóváhagyás végrehajtása egybérlős alkalmazáshoz](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Rendszergazdai szintű jóváhagyás végrehajtása több-bérlős alkalmazáshoz](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Felhasználói szintű hozzájárulási művelet végrehajtása

-   Minden olyan Open ID Connect-kompatibilis alkalmazás esetén, amely engedélyeket kér, az alkalmazás bejelentkezési képernyőjára lépve felhasználói szintű jóváhagyást ad az alkalmazásnak a bejelentkezett felhasználóhoz.

-   Ha ezt programozott módon szeretné megtenni, tekintse meg az egyéni felhasználói hozzájárulás [kérését.](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent)

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Rendszergazdai szintű hozzájárulási művelet végrehajtása bármely alkalmazáshoz

-   Csak **a V1** alkalmazásmodellel fejlesztett alkalmazások esetében kényszerítheti ennek a rendszergazdai szintű hozzájárulásnak a bekövetkezését, ha hozzáadja a "**?prompt=admin \_ consent"** paramétert az alkalmazás bejelentkezési URL-címének végéhez.

-   A **V2** alkalmazásmodellel fejlesztett bármely alkalmazás esetében kényszerítheti ennek a rendszergazdai szintű jóváhagyásnak a végrehajtását a Using the admin consent endpoint (Rendszergazdai hozzájárulási végpont használata) című témakör Request **the permissions from a directory admin (Engedélyek** kérése címtár-rendszergazdai rendszergazdától) szakaszában található [utasítások szerint.](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Rendszergazdai szintű jóváhagyás végrehajtása egybérlős alkalmazáshoz

-   Az  engedélyeket kérelmező egybérlős alkalmazások (például azok, amelyek a szervezetnél fejlesztik vagy a saját tulajdonában vannak) az összes felhasználó  nevében elvégezhet egy rendszergazdai szintű hozzájárulási műveletet, ha globális rendszergazdaként jelentkezik be, és az Application Registry –  **Minden alkalmazás &gt; &gt; &gt;** – Alkalmazás – Szükséges engedélyek panel tetején található Engedélyek megadása gombra kattint.

-   A V1 vagy **V2** alkalmazásmodell használatával fejlesztett bármely alkalmazás esetében kényszerítheti ennek a rendszergazdai szintű jóváhagyásnak a végrehajtását a Using the admin consent endpoint (Rendszergazdai hozzájárulási végpont használata) című témakör Request **the permissions from a directory admin (Engedélyek** kérése címtár-rendszergazdai rendszergazdától) szakaszában található utasítások [szerint.](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Rendszergazdai szintű jóváhagyás végrehajtása több-bérlős alkalmazáshoz

-   Az **engedélyeket kérelmező több-bérlős** alkalmazásokhoz (például egy harmadik féltől vagy a Microsofttól származó alkalmazásokhoz) rendszergazdai szintű hozzájárulási **műveletet hajthat** végre. Jelentkezzen be globális rendszergazdaként,  és kattintson az Engedélyek megadása gombra a Vállalati alkalmazások – Minden alkalmazás – Alkalmazás kiválasztása **&gt; &gt; – &gt; Engedélyek** panelen (hamarosan elérhető).

-   Ezt a rendszergazdai szintű jóváhagyást a Rendszergazdai  jóváhagyás végpont használata című témakör Engedélyek kérése címtár-rendszergazdától szakaszában található utasítások szerint is [kényszerítheti.](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)

## <a name="next-steps"></a>Következő lépések
[A rendszergazdai hozzájárulási végpont használata](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)