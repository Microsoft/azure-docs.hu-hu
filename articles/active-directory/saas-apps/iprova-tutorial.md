---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a iProva | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és iProva között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: 717696053a742abae6756655a15416ac81221144
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459747"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iprova"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a iProva

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a iProva a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az iProva-t az Azure AD-vel, a következőket teheti:

* A iProva-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a iProva az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* iProva egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* a iProva támogatja az **SP** által KEZDEMÉNYEZett SSO-t

* A iProva konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben biztosítja a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-iprova-from-the-gallery"></a>IProva hozzáadása a gyűjteményből

A iProva Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a iProva a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **iProva** kifejezést a keresőmezőbe.
1. Válassza ki a **iProva** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-iprova"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a iProva

Konfigurálja és tesztelje az Azure AD SSO-t a iProva a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a iProva-ben.

Az Azure AD SSO és a iProva konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurációs információk beolvasása a iProva-ből](#retrieve-configuration-information-from-iprova)** – felkészülés a következő lépésekre.
1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Hozzon létre iProva-teszt felhasználót](#create-iprova-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-iProva rendelkezik.
1. **[IPROVA SSO konfigurálása](#configure-iprova-sso)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="retrieve-configuration-information-from-iprova"></a>Konfigurációs információk beolvasása a iProva

Ebben a szakaszban adatokat kér le a iProva-ből az Azure AD egyszeri bejelentkezés konfigurálásához.

1. Nyisson meg egy webböngészőt, és nyissa meg a iProva **egy saml2-információ** lapját az alábbi URL-minta használatával:

    ```https
    https://SUBDOMAIN.iprova.nl/saml2info
    https://SUBDOMAIN.iprova.be/saml2info
    ```

    ![A iProva egy SAML2-információs oldalának megtekintése](media/iprova-tutorial/iprova-saml2-info.png)

1. Hagyja nyitva a böngésző fület, miközben egy másik böngésző lap következő lépéseit folytatja.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **iProva** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. Töltse ki a **bejelentkezési URL-cím** mezőt a **iProva egy saml2 info** lapján a címke **bejelentkezési URL-címe** mögött megjelenő értékkel. Ez a lap még mindig meg van nyitva a másik böngésző lapon.

    b. Töltse ki az **azonosító** mezőt a **iProva egy saml2 információs** oldal címke **EntityID** mögött megjelenő értékkel. Ez a lap még mindig meg van nyitva a másik böngésző lapon.

    c. Töltse ki a **Válasz-URL** mezőt a **iProva egy saml2 info** oldal címke **Válasz URL-címe** mögött megjelenő értékkel. Ez a lap még mindig meg van nyitva a másik böngésző lapon.

1. a iProva alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentiek mellett a iProva alkalmazás néhány további attribútumot vár az SAML-válaszban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Name | Forrás attribútum| Névtér  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

## <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

## <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a iProva.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **iProva** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="create-iprova-test-user"></a>IProva-tesztelési felhasználó létrehozása

1. Jelentkezzen be a iProva a **rendszergazdai** fiók használatával.

2. Nyissa meg a **Ugrás** menüt.

3. Válassza az **alkalmazás-kezelés** lehetőséget.

4. A **felhasználók és felhasználói csoportok** panelen válassza a **felhasználók** lehetőséget.

5. Válassza a **Hozzáadás** lehetőséget.

6. A **Felhasználónév** mezőbe írja be a felhasználóhoz hasonló felhasználónevet `B.Simon@contoso.com` .

7. A **Full Name (teljes név** ) mezőbe írja be a felhasználó teljes nevét, például: **B. Simon**.

8. Válassza a **nincs jelszó (egyszeri bejelentkezés használata)** lehetőséget.

9. Az **e-mail cím** mezőbe írja be a felhasználóhoz hasonló e-mail címet `B.Simon@contoso.com` .

10. Görgessen le a lap végére, és válassza a **Befejezés** gombot.

## <a name="configure-iprova-sso"></a>IProva SSO konfigurálása

1. Jelentkezzen be a iProva a **rendszergazdai** fiók használatával.

2. Nyissa meg a **Ugrás** menüt.

3. Válassza az **alkalmazás-kezelés** lehetőséget.

4. Válassza az **általános** lehetőséget a **Rendszerbeállítások** panelen.

5. Válassza a **Szerkesztés** elemet.

6. Görgessen le a **hozzáférés-vezérléshez**.

    ![iProva hozzáférés-vezérlési beállításai](media/iprova-tutorial/iprova-accesscontrol.png)

7. A beállítással a **felhasználók automatikusan bejelentkeznek a hálózati fiókjaikkal**, és megváltoztathatják az Igen értékre **, az SAML-n keresztül történő hitelesítést**. Ekkor megjelenik a további beállítások.

8. Válassza a **Beállítás** lehetőséget.

9. Kattintson a **Tovább** gombra.

10. a iProva megkérdezi, hogy szeretné-e letölteni az összevonási adatok URL-címről való letöltését vagy fájlból való feltöltését. Válassza ki a **from URL** lehetőséget.

    ![Azure AD-metaadatok letöltése](media/iprova-tutorial/iprova-download-metadata.png)

11. Illessze be az Azure AD egyszeri bejelentkezés konfigurálása szakasz utolsó lépéseként mentett metaadat-URL-címet.

12. Válassza a nyíl alakú gombot a metaadatok Azure AD-ből való letöltéséhez.

13. Ha a letöltés befejeződött, megjelenik a megerősítő üzenet **érvényes összevonási adatfájlja** .

14. Kattintson a **Tovább** gombra.

15. Ugorja át most a **bejelentkezési teszt** lehetőséget, majd kattintson a **tovább** gombra.

16. A **használni kívánt jogcím** legördülő listáról válassza a **windowsaccountname** lehetőséget.

17. Válassza a **Befejezés** gombot.

18. Most térjen vissza az **általános beállítások szerkesztése** képernyőre. Görgessen le a lap aljára, majd kattintson **az OK gombra** a konfiguráció mentéséhez.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a iProva csempére kattint, automatikusan be kell jelentkeznie arra a iProva, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A iProva kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)

- [A iProva és a speciális láthatóság és vezérlők elleni védelem](/cloud-app-security/proxy-intro-aad)