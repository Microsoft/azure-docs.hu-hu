---
title: A jelszóalapú egyszeri bejelentkezés (SSO) használata az Azure Active Directory
description: A jelszóalapú egyszeri bejelentkezés (SSO) használata az Azure Active Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: iangithinji
ms.openlocfilehash: ffa517f068dbc13f2734630216466373d9014ae6
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374556"
---
# <a name="understand-password-based-single-sign-on"></a>A jelszóalapú egyszeri bejelentkezés

Az [alkalmazáskezelés](view-applications-portal.md) gyorsútmutató-sorozatában megtanulta, hogyan használhatja az Azure AD-t identitásszolgáltatóként (IdP) egy alkalmazáshoz. A rövid útmutatóban SAML-alapú vagy OIDC-alapú SSO-t konfigurál. Egy másik lehetőség a jelszóalapú SSO. Ez a cikk részletesebben ismerteti a jelszóalapú SSO-beállítást. 

Ez a lehetőség minden olyan webhelyhez elérhető, amely HTML bejelentkezési oldalhoz van bejelzve. A jelszóalapú SSO más néven jelszótartó. A jelszóalapú SSO lehetővé teszi az identitás-összevonást nem támogató webalkalmazások felhasználói hozzáférésének és jelszavának kezelését. Akkor is hasznos, ha több felhasználónak kell egyetlen fiókot megosztania, például a cég közösségimédia-alkalmazásfiókja esetében.

A jelszóalapú SSO nagyszerű módja az alkalmazások Azure AD-be való gyors integrálásának, és a következő funkciókat teszi lehetővé:

- Egyszeri bejelentkezés engedélyezése a felhasználók számára a felhasználónevek és jelszavak biztonságos tárolásával és visszajátszásával

- Olyan alkalmazások támogatása, amelyek több bejelentkezési mezőt igényelnek az olyan alkalmazásokhoz, amelyek nem csak felhasználónév- és jelszómezőket igényelnek a bejelentkezéshez

- Testre szabhatja a felhasználók által a [](../user-help/my-apps-portal-end-user-access.md) hitelesítő adatok megadásakor a Saját alkalmazások és jelszómezők címkéit

- Lehetővé teszi, hogy a felhasználók saját felhasználóneveket és jelszavakat adjanak meg a manuálisan begépelt meglévő alkalmazásfiókok számára.

- A felhasználóhoz rendelt felhasználónevek és jelszavak megadásának engedélyezése az üzleti csoport tagjai számára az önkiszolgáló [alkalmazás-hozzáférési funkcióval](./manage-self-service-access.md)

-   Annak engedélyezése, hogy egy rendszergazda megad egy felhasználónevet és jelszót, amelyet egyéni felhasználók vagy csoportok fognak használni, amikor bejelentkeznek az alkalmazásba a Hitelesítő adatok frissítése funkcióval 

## <a name="before-you-begin"></a>Előkészületek

Az Azure AD identitásszolgáltatóként (IdP) való használata és az egyszeri bejelentkezés (SSO) konfigurálása a használt alkalmazástól függően egyszerű vagy összetett lehet. Egyes alkalmazások mindössze néhány művelettel konfigurálhatóak. Mások részletes konfigurálást igényelnek. A tudás gyors kigyomlálása érdekében végig kell mennie az [alkalmazáskezelés gyorsútmutató-sorozatán.](view-applications-portal.md) Ha a hozzáadott alkalmazás egyszerű, akkor valószínűleg nem kell elolvasni ezt a cikket. Ha a hozzáadott alkalmazás egyéni konfigurációt igényel, és jelszóalapú SSO-t kell használnia, akkor ez a cikk Önnek készült.

> [!IMPORTANT] 
> Vannak olyan forgatókönyvek, amelyekben az **Egyszeri** bejelentkezés lehetőség nem lesz az alkalmazás navigációs sávján a **Vállalati alkalmazásokban.** 
>
> Ha az alkalmazást  a Alkalmazásregisztrációk regisztrálta, akkor az egyszeri bejelentkezés funkció alapértelmezés szerint az OIDC OAuth használatára van konfigurálva. Ebben az esetben az **Egyszeri bejelentkezés** lehetőség nem fog ni a Vállalati alkalmazások alatti **navigációs sávon.** Ha egyéni alkalmazást **Alkalmazásregisztrációk** a jegyzékfájlban, a beállításokat a jegyzékfájlban kell konfigurálnia. A jegyzékfájllal kapcsolatos további információkért lásd az Azure Active Directory [jegyzékfájlt.](../develop/reference-app-manifest.md) Az SSO-szabványokkal kapcsolatos további információkért lásd: Hitelesítés és engedélyezés [a Microsoft identitásplatformján.](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform) 
>
> Egyéb forgatókönyvek,  amelyekben az egyszeri bejelentkezés hiányzik a navigációból, például ha egy alkalmazást egy másik bérlő üzemeltet, vagy ha a fiókja nem rendelkezik a szükséges engedélyekkel (globális rendszergazda, felhőalkalmazás-rendszergazda, alkalmazás-rendszergazda vagy a szolgáltatásnév tulajdonosa). Az engedélyek olyan forgatókönyvet is okozhatnak, amelyben megnyithatja az egyszeri **bejelentkezést,** de nem tudja menteni. További információ az Azure AD rendszergazdai szerepköreiről: ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .


## <a name="basic-configuration"></a>Alapszintű konfiguráció

A rövid [útmutató sorozatában](view-applications-portal.md)megtanulta, hogyan adhat hozzá alkalmazást a bérlőhöz, így az Azure AD tudhatja, hogy az alkalmazás identitásszolgáltatójaként (IdP) használja. Egyes alkalmazások már előre konfigurálva vannak, és az Azure AD-katalógusban fognak mutatni. Más alkalmazások nincsenek a katalógusban, és létre kell hoznia egy általános alkalmazást, és manuálisan kell konfigurálnia. Az alkalmazástól függően előfordulhat, hogy a jelszóalapú SSO lehetőség nem érhető el. Ha nem látja a Jelszóalapú beállításlistát az alkalmazás egyszeri bejelentkezési oldalán, akkor az nem érhető el.

> [!IMPORTANT]
> A Saját alkalmazások böngészőbővítmény szükséges a jelszóalapú SSO-hez. További információ: [Plan a Saját alkalmazások deployment](my-apps-deployment-plan.md).

A jelszóalapú SSO konfigurációs oldala egyszerű. Csak az alkalmazás által használt bejelentkezési oldal URL-címét tartalmazza. Ennek a sztringnek kell lennie annak az oldalnak, amely tartalmazza a felhasználónév beviteli mezőjét.

Az URL-cím megadása után válassza a **Mentés lehetőséget.** Az Azure AD a bejelentkezési oldal HTML-kódot elemez a felhasználónév és a jelszó beviteli mezőihez. Ha a kísérlet sikeres, akkor végzett.
 
A következő lépés a [Felhasználók vagy csoportok hozzárendelése az alkalmazáshoz.](./assign-user-or-group-access-portal.md) A felhasználók és csoportok hozzárendelése után meg kell adnia a felhasználók hitelesítő adatait, amikor bejelentkeznek az alkalmazásba. Válassza **a Felhasználók és csoportok** lehetőséget, jelölje be a felhasználó vagy csoport sorának jelölőnégyzetét, majd válassza a Hitelesítő adatok frissítése **lehetőséget.** Végül adja meg a felhasználóhoz vagy csoporthoz használni kívánt felhasználónevet és jelszót. Ha ezt nem teszi meg, a rendszer az indításkor kérni fogja a felhasználókat, hogy adjanak meg hitelesítő adatokat.
 

## <a name="manual-configuration"></a>Manuális konfigurálás

Ha az Azure AD elemzési kísérlete sikertelen, manuálisan konfigurálhatja a bejelentkezést.

1. A **\<application name> Konfiguráció alatt** válassza a Jelszó egyszeri bejelentkezési **\<application name> beállításainak** konfigurálása lehetőséget a **Bejelentkezési beállítások konfigurálása lap megjelenítéséhez.** 

2. Válassza **a Bejelentkezési mezők manuális észlelése lehetőséget.** További utasítások jelennek meg, amelyek ismertetik a bejelentkezési mezők manuális észlelését.

   ![Jelszóalapú egyszeri bejelentkezés manuális konfigurálása](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Válassza **a Bejelentkezési mezők rögzítése lehetőséget.** Egy új lapon megnyílik egy rögzítési állapotlap, amely azt mutatja, hogy az üzenetek **metaadatainak rögzítése folyamatban van.**

4. Ha a **Saját alkalmazások bővítmény** szükséges mező egy új lapon  jelenik meg, válassza a Telepítés most lehetőséget a Saját alkalmazások **biztonságos** bejelentkezési bővítmény böngészőbővítmény telepítéséhez. (A böngészőbővítményhez Microsoft Edge, Chrome vagy Firefox szükséges.) Ezután telepítse, indítsa el és engedélyezze a bővítményt, majd frissítse a rögzítési állapot oldalát.

   A böngészőbővítmény ekkor megnyit egy újabb lapot, amely megjeleníti a megadott URL-címet.
5. A megadott URL-címet mutató lapon végig kell mennie a bejelentkezési folyamaton. Töltse ki a felhasználónév és a jelszó mezőket, és próbáljon meg bejelentkezni. (Nem kell megadnia a megfelelő jelszót.)

   A rendszer arra kéri, hogy mentse a rögzített bejelentkezési mezőket.
6. Válassza az **OK** lehetőséget. A böngészőbővítmény frissíti a rögzítési állapot oldalát a következő üzenettel: **Metaadatok frissítve az alkalmazáshoz.** A böngészőlap bezárul.

7. Az Azure AD **bejelentkezési konfigurálása lapon** válassza az OK, sikerült bejelentkezni az **alkalmazásba lehetőséget.**

8. Válassza az **OK** lehetőséget.

## <a name="next-steps"></a>Következő lépések

- [Felhasználók vagy csoportok hozzárendelése az alkalmazáshoz](./assign-user-or-group-access-portal.md)
- [Felhasználói fiókok automatikus építésének konfigurálása](../app-provisioning/configure-automatic-user-provisioning-portal.md)
