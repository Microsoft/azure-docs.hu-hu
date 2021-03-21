---
title: Jelszó-alapú egyszeri bejelentkezés (SSO) értelmezése Azure Active Directory
description: Jelszó-alapú egyszeri bejelentkezés (SSO) értelmezése Azure Active Directory
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: kenwith
ms.openlocfilehash: bac04bd70469d7b9c4d9485b6a87fd7133967154
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99255305"
---
# <a name="understand-password-based-single-sign-on"></a>Jelszó alapú egyszeri bejelentkezés ismertetése

Az alkalmazások felügyeletének rövid útmutató [sorozatában](view-applications-portal.md) megtudhatta, hogyan használhatja az Azure ad-t az alkalmazás identitás-szolgáltatója (identitásszolgáltató) használatával. A rövid útmutatóban SAML-alapú vagy OIDC-alapú egyszeri bejelentkezést konfigurálhat. Egy másik lehetőség a jelszó-alapú egyszeri bejelentkezés. Ez a cikk részletesebben ismerteti a jelszó-alapú egyszeri bejelentkezés lehetőségét. 

Ez a lehetőség bármely olyan webhelyhez elérhető, amelynek HTML-bejelentkezési lapja van. A jelszó-alapú egyszeri bejelentkezés jelszavas tárolóként is ismert. A jelszó-alapú egyszeri bejelentkezés lehetővé teszi a felhasználói hozzáférés és jelszavak kezelését olyan webalkalmazásokhoz, amelyek nem támogatják az identitás-összevonást. Emellett akkor is hasznos, ha több felhasználónak egyetlen fiókot kell megosztania, például a szervezete közösségi Media app-fiókjaival.

A jelszó-alapú egyszeri bejelentkezés nagyszerű lehetőséget biztosít az alkalmazások Azure AD-ba való integrálásának gyors megkezdésére, és lehetővé teszi a következőket:

- Engedélyezze az egyszeri bejelentkezést a felhasználók számára a felhasználónevek és jelszavak biztonságos tárolása és visszajátszása révén

- Olyan alkalmazások támogatása, amelyeknek több bejelentkezési mezőre van szükségük olyan alkalmazásokhoz, amelyek több, mint a Felhasználónév és a jelszó mezőt igényelnek a bejelentkezéshez

- Testreszabhatja a felhasználónevek és a jelszó mezők címkéit, amelyeket a felhasználók a [saját alkalmazásokban](../user-help/my-apps-portal-end-user-access.md) látnak a hitelesítő adataik megadásakor

- Saját felhasználónevek és jelszavak megadásának engedélyezése a felhasználók számára minden olyan meglévő alkalmazás-fiókhoz, amelyet manuálisan írunk be.

- A felhasználóhoz rendelt felhasználónevek és jelszavak megadásának engedélyezése az üzleti csoport tagjai számára az [önkiszolgáló alkalmazás-hozzáférési](./manage-self-service-access.md) szolgáltatás használatával

-   Annak engedélyezése, hogy a rendszergazda megadhatja a felhasználók vagy csoportok által használt felhasználónevet és jelszót, amikor bejelentkeznek az alkalmazásba a hitelesítő adatok frissítése funkcióval 

## <a name="before-you-begin"></a>Előkészületek

Az Azure AD használata az identitás-szolgáltatóként (identitásszolgáltató) és az egyszeri bejelentkezés (SSO) konfigurálása a használt alkalmazástól függően egyszerű vagy összetett lehet. Néhány alkalmazás csak néhány művelettel konfigurálható. Másoknak részletes konfigurációra van szükségük. Az ismeretek gyors bejárásához járjon végig az alkalmazás-felügyeleti útmutató [sorozatán](view-applications-portal.md) . Ha a hozzáadott alkalmazás egyszerű, akkor valószínűleg nem kell elolvasnia ezt a cikket. Ha a felvenni kívánt alkalmazásnak egyéni konfigurációra van szüksége, és jelszó-alapú egyszeri bejelentkezést kell használnia, akkor ez a cikk Önnek szól.

> [!IMPORTANT] 
> Vannak olyan helyzetek, amikor az **egyszeri bejelentkezési** lehetőség nem lesz a **vállalati alkalmazásokban** lévő alkalmazás navigációjában. 
>
> Ha az alkalmazás a **Alkalmazásregisztrációk** használatával lett regisztrálva, akkor az egyszeri bejelentkezés funkció alapértelmezés szerint a OIDC OAuth használatára van konfigurálva. Ebben az esetben az **egyszeri bejelentkezési** lehetőség nem jelenik meg a **vállalati alkalmazások** navigációs sávján. Ha a **Alkalmazásregisztrációk** használatával adja hozzá az egyéni alkalmazást, a jegyzékfájlban konfigurálhatja a beállításokat. További információ a jegyzékfájlról: [Azure Active Directory app manifest](../develop/reference-app-manifest.md). Az SSO-szabványokkal kapcsolatos további tudnivalókért tekintse meg a [hitelesítés és engedélyezés a Microsoft Identity platform használatával](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform)című témakört. 
>
> Egyéb forgatókönyvek, amelyekben az **egyszeri bejelentkezés** hiányzik a navigálásból, ha egy alkalmazás egy másik bérlőn fut, vagy ha a fiókja nem rendelkezik a szükséges engedélyekkel (globális rendszergazda, Felhőbeli alkalmazás rendszergazdája, alkalmazás-rendszergazda vagy az egyszerű szolgáltatásnév tulajdonosa). Az engedélyek olyan eseteket is okozhatnak, ahol megnyithatja az **egyszeri bejelentkezést** , de nem fogja tudni menteni. További információ az Azure AD rendszergazdai szerepköreiről: ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .


## <a name="basic-configuration"></a>Alapszintű konfiguráció

A rövid útmutató [sorozatában](view-applications-portal.md)megtudhatta, hogyan adhat hozzá egy alkalmazást a bérlőhöz, ami lehetővé teszi, hogy az Azure ad tudja, hogy az alkalmazás identitás-szolgáltatója (identitásszolgáltató) használja. Néhány alkalmazás már előre konfigurálva van, és az Azure AD-katalógusban jelenik meg. A többi alkalmazás nem szerepel a katalógusban, és létre kell hoznia egy általános alkalmazást, és manuálisan kell konfigurálnia. Az alkalmazástól függően előfordulhat, hogy a jelszó-alapú egyszeri bejelentkezés beállítás nem érhető el. Ha nem jelenik meg a jelszó-alapú beállítási lista az alkalmazás egyszeri bejelentkezési oldalán, akkor nem érhető el.

> [!IMPORTANT]
> A jelszó-alapú egyszeri bejelentkezéshez a saját alkalmazások böngésző bővítmény szükséges. További információt a [saját alkalmazások telepítésének megtervezése](my-apps-deployment-plan.md)című témakörben talál.

A jelszó-alapú egyszeri bejelentkezés konfigurációs lapja egyszerű. Csak az alkalmazás által használt bejelentkezési oldal URL-címét tartalmazza. A karakterláncnak a Felhasználónév beviteli mezőjét tartalmazó oldalnak kell lennie.

Az URL-cím megadása után válassza a **Mentés** lehetőséget. Az Azure AD elemzi a bejelentkezési oldal HTML-jét a Felhasználónév és jelszó beviteli mezőiben. Ha a kísérlet sikeres, elkészült.
 
A következő lépés a [felhasználók vagy csoportok társítása az alkalmazáshoz](./assign-user-or-group-access-portal.md). A felhasználók és csoportok hozzárendelése után megadhatja a felhasználó számára az alkalmazásba való bejelentkezéskor használandó hitelesítő adatokat. Válassza a **felhasználók és csoportok** lehetőséget, jelölje be a felhasználó vagy csoport sorához tartozó jelölőnégyzetet, majd válassza a **hitelesítő adatok frissítése** lehetőséget. Végül adja meg a felhasználóhoz vagy a csoporthoz használni kívánt felhasználónevet és jelszót. Ha ezt nem teszi meg, a rendszer megkéri a felhasználókat, hogy indítsák el magukat a hitelesítő adatokat.
 

## <a name="manual-configuration"></a>Manuális konfigurálás

Ha az Azure AD elemzési kísérlete sikertelen, manuálisan is konfigurálhatja a bejelentkezést.

1. A **\<application name> konfiguráció** területen válassza **a \<application name> jelszó egyszeri bejelentkezés beállításainak megadása** lehetőséget a **bejelentkezési** lap megjelenítéséhez. 

2. Jelölje be **a bejelentkezési mezők manuális észlelése** jelölőnégyzetet. A bejelentkezési mezők manuális észlelését ismertető további utasítások jelennek meg.

   ![Jelszó alapú egyszeri bejelentkezés manuális konfigurálása](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Jelölje be **a bejelentkezési mezők rögzítése** jelölőnégyzetet. Egy új lapon megnyílik egy rögzítési állapot lap, amely azt mutatja, hogy az üzenet **metaadatainak rögzítése jelenleg folyamatban van**.

4. Ha a **saját alkalmazások bővítmény szükséges** mező megjelenik egy új lapon, válassza a **Telepítés most** lehetőséget a **saját alkalmazások biztonságos bejelentkezési bővítményének** telepítéséhez. (A böngésző kiterjesztéséhez a Microsoft Edge, a Chrome vagy a Firefox szükséges.) Ezután telepítse, indítsa el és engedélyezze a bővítményt, és frissítse a rögzítés állapota lapot.

   Ekkor megnyílik egy másik lap, amely megjeleníti a megadott URL-címet.
5. A megadott URL-címmel rendelkező lapon ugorjon végig a bejelentkezési folyamaton. Adja meg a Felhasználónév és a jelszó mezőket, majd próbáljon meg bejelentkezni. (Nem kell megadnia a megfelelő jelszót.)

   A rendszer felszólítja a rögzített bejelentkezési mezők mentésére.
6. Válassza az **OK** lehetőséget. A böngésző bővítménye frissíti a rögzítés állapota lapot, és az üzenet **metaadatai frissültek az alkalmazáshoz**. A böngésző lap bezárul.

7. Az Azure AD **configure bejelentkezési** oldalán kattintson az **OK gombra, és sikerült bejelentkezni az alkalmazásba**.

8. Válassza az **OK** lehetőséget.

## <a name="next-steps"></a>Következő lépések

- [Felhasználók vagy csoportok társítása az alkalmazáshoz](./assign-user-or-group-access-portal.md)
- [A felhasználói fiókok automatikus üzembe helyezésének konfigurálása](../app-provisioning/configure-automatic-user-provisioning-portal.md)
