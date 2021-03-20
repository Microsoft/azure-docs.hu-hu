---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a tartalommal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a tartalomhoz.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: jeedes
ms.openlocfilehash: b664a7e4aac571359fb1494cf69d5bbe3e841b4d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92455315"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-contentful"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a tartalommal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a tartalmakat Azure Active Directory (Azure AD) használatával. Az Azure AD-val való tartalomkezelés integrálásával a következőket teheti:

* A tartalomhoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Azure AD-fiókjával a tartalomba.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Egyszeri bejelentkezési (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A tartalomszolgáltató támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t
* A tartalom-előállítási **szolgáltatás csak időben támogatja a** felhasználók üzembe helyezését
* A tartalom konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben biztosítja a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték. Egyetlen bérlőn csak egy példány konfigurálható.

## <a name="adding-contentful-from-the-gallery"></a>Tartalom kibővítése a katalógusból

A tartalom Azure AD-ba való integrálásának konfigurálásához hozzá kell adnia egy tartalmat a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban a keresőmezőbe írja be a **tartalom** kifejezést.
1. Válassza ki a **tartalmat** az eredmények között, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-contentful"></a>Azure AD-alapú egyszeri bejelentkezés konfigurálása és tesztelése a tartalomhoz

Konfigurálja és tesztelje az Azure AD SSO-t a tartalommal rendelkező, **B. Simon** nevű felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a tartalommal.

Az Azure AD SSO a tartalommal való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az egyszeri bejelentkezés **[beállításainak konfigurálása az](#configure-contentful-sso)** alkalmazás oldalán.
    * **[Tartalombeli tesztelési felhasználó létrehozása](#create-contentful-test-user)** – ha a felhasználó Azure ad-beli képviseletéhez kapcsolódó, B. Simon-beli partnere van.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **tartalommal** rendelkező alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    - A **Válasz URL-címe** szövegmezőben másolja az ACS (állítási fogyasztói szolgáltatás) URL-címét a tartalommal ellátható egyszeri bejelentkezés beállítása oldaláról. A következőképpen fog kinézni: `https://be.contentful.com/sso/<organization_id>/consume`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    - A **bejelentkezési URL** szövegmezőben másolja ki ugyanazt az ACS-t (az állítási fogyasztói szolgáltatást) tartalmazó URL-címet. A következőképpen fog kinézni: `https://be.contentful.com/sso/<organization_id>/login`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges válasz URL-címével, és Sign-On URL-címet úgy, hogy átmásolja az ACS-(állítási fogyasztói szolgáltatás) URL-címét az SSO Setup oldaláról a tartalomból

1. A tartalommal rendelkező alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a tartalomszolgáltató alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.
    
    | Name |  Forrás attribútum|
    | --------------- | --------- |
    | e-mail | User. userPrincipalName |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **tartalom-előállítók beállítása** szakaszban másolja a bejelentkezési URL-címet a tartalommal rendelkező egyszeri bejelentkezés konfigurálásához.

    ![Konfigurációs URL-címek másolása](media/contentful-tutorial/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést a tartalomhoz való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **tartalom** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza ki az **B. Simon** elemet a felhasználók listából, majd kattintson a lap alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a lap alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-contentful-sso"></a>Tartalommal rendelkező egyszeri bejelentkezés konfigurálása

A következő lépésekkel konfigurálhatja az egyszeri bejelentkezést a **tartalommal** rendelkező oldalon.

1. A [tartalom](https://app.contentful.com)lapon navigáljon a **szervezeti beállítások** SSO beállítása lapjára.
1. Kattintson az **SSO beállítása** lehetőségre.
1. Másolja és illessze be a bejelentkezési URL-címet az Azure AD-ben a **tartalom beállítása** szakaszban.
1. Másolja és illessze be a tanúsítványt az Azure AD-ből letöltött Base64 tanúsítványfájl-fájlból.
1. Állítson be egy SSO-nevet az SP által kezdeményezett bejelentkezéshez.
1. Kattintson az **egyszeri bejelentkezés engedélyezése** lehetőségre.

Ha ez nem működik, érje el a felderített [támogatási csapatot](mailto:support@contentful.com).

### <a name="create-contentful-test-user"></a>Tartalom-ellenőrző felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a tartalommal. A tartalommal rendelkező felhasználó-kiépítés a felhasználók számára alapértelmezés szerint engedélyezett. Ez a szakasz nem tartalmaz műveleti elemeket. Ha a felhasználó még nem létezik a tartalomban, akkor a hitelesítés után létrejön egy újat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a tartalom csempére kattint, akkor automatikusan be kell jelentkeznie arra a tartalomba, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [Próbálja ki a tartalmat az Azure AD-vel](https://aad.portal.azure.com/)