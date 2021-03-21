---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az erőforrás-központtal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az erőforrás-központ között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: dbc148fcbcd9c3be86a29df1e08755611a347b07
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586555"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-resource-central"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az erőforrás-központtal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az erőforrás-központot Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja az erőforrás-központot, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér az erőforrás-központhoz.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az erőforrás-központba az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az erőforrás-központi egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az erőforrás-Központ támogatja az **SP** által KEZDEMÉNYEZett SSO-t

* **Az erőforrás-központ csak időben támogatja a** felhasználók kiépítési folyamatát

## <a name="add-resource-central-from-the-gallery"></a>Erőforrás-Központ hozzáadása a gyűjteményből

A Resource Central Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az erőforrás-központot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban a keresőmezőbe írja be a **Resource Central** kifejezést.
1. Válassza az **erőforrás-központ** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-resource-central"></a>Azure AD SSO konfigurálása és tesztelése az erőforrás-központhoz

Konfigurálja és tesztelje az Azure AD SSO-t a Resource Central segítségével egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az erőforrás-központban.

Az Azure AD SSO és az erőforrás-központ konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
    1. **[Hozzon létre egy Resource Central test User](#create-resource-central-test-user)** -t, hogy az a felhasználó Azure ad-beli képviseletéhez kapcsolódó B. Simon-beli partnere legyen.
1. A **[Resource Central SSO konfigurálása](#configure-resource-central-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal az **erőforrás központi** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** mezőben adja meg a következő mezők értékeit:

   1. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<DOMAIN_NAME>/ResourceCentral`

   1. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<DOMAIN_NAME>/ResourceCentral`

   1. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/Acs`

    > [!NOTE]
    > Ezek az értékek nem literál értékek. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-értékekkel. Vegye fel a kapcsolatot az [erőforrás központi ügyfelének támogatási csapatával](mailto:st@aod.vn) az értékek beszerzéséhez.  Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML-aláíró tanúsítványában** keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. Az **erőforrás-központ beállítása** területen másolja ki a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy B. Simon nevű teszt felhasználót hoz létre a Azure Portal.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** `username@companydomain.extension` . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az erőforrás-központ hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **erőforrás-központ** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** panelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** panelen válassza a **felhasználók** listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha egy szerepkört szeretne hozzárendelni a felhasználókhoz, akkor kiválaszthatja a **szerepkör kiválasztása** lehetőséget. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a kiválasztott **alapértelmezett hozzáférési** szerepkör jelenik meg.
1. A **hozzárendelés hozzáadása** panelen kattintson a **hozzárendelés** gombra.

### <a name="create-resource-central-test-user"></a>Erőforrás központi tesztelési felhasználó létrehozása

Ebben a szakaszban egy **B. Simon** nevű felhasználó jön létre az **erőforrás-központban**.

1. Az erőforrás-központ területen válassza a **biztonsági**  >  **személyek**  >  **új** lehetőséget.
  
    :::image type="content" source="./media/resource-central/new-person.png" alt-text="Képernyőkép, amely az erőforrás-központ személyek ablaktábláját mutatja az új gomb kiemelésével.":::

1. A **person details**( **megjelenítendő név**) mezőben adja meg a **B. Simon** felhasználót. Az **SMTP-címek** mezőben adja meg a felhasználó Azure ad-felhasználónevét. Például:  `B.Simon@contoso.com`.

    :::image type="content" source="./media/resource-central/person.png" alt-text="Képernyőkép, amely az erőforrás-központ személy részletei paneljét jeleníti meg.":::

## <a name="configure-resource-central-sso"></a>Erőforrás-központi SSO konfigurálása

Ebben a szakaszban az egyszeri bejelentkezést az **erőforrás központi rendszergazdája** konfigurálja.

1. Az erőforrás központi rendszergazdája területen válassza a **külső hitelesítés** lehetőséget.
1.  A **konfiguráció engedélyezése beállításnál** válassza az **Igen** lehetőséget.

    ![Képernyőfelvétel: az erőforrás-központ külső hitelesítés paneljén a konfiguráció engedélyezése lehetőség van kiválasztva.](./media/resource-central/enable.png)

1. A **hitelesítési protokoll** területen válassza a **egy saml2** lehetőséget. 

   :::image type="content" source="./media/resource-central/protocol.png" alt-text="Képernyőkép, amely az erőforrás-központ hitelesítési protokollhoz kiválasztott egy SAML2 jeleníti meg.":::

1. A **egy saml2 konfigurálása** területen adja meg a következő mezők értékeit:

    1. Az **azonosító (Entity ID)**, a **bejelentkezési URL**-cím, a **kijelentkezési URL-cím** és az **Azure ad-azonosító** mezőben adja meg a megfelelő URL-címeket

       :::image type="content" source="./media/resource-central/auth.png" alt-text="Képernyőkép a egy SAML2 konfigurációs paneljéről az erőforrás-központban.":::

        Másolja az URL-címeket az **erőforrás központi beállítása** ablaktáblán:

        :::image type="content" source="./media/resource-central/setup.png" alt-text="A Resource Central erőforrás-központ beállítása paneljének képernyőképe.":::

   1. A **visszatérési URL-cím** mezőben adja meg a értéket `https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/CallbackHandler` .
  
1. A **tanúsítványhoz** töltse fel a tanúsítványt, és adja meg a jelszavát.

   ![Képernyőkép a tanúsítványról szakasz az erőforrás-központban.](./media/resource-central/cert.png)
   
1. Kattintson a **Mentés** gombra.

1. Lépjen vissza az **Azure Portalra**. Az **SAML-aláíró tanúsítványban** töltse fel a tanúsítványt, és adja meg a jelszavát.

   ![Képernyőkép a Azure Portal importálási tanúsítvány paneljéről.](./media/resource-central/cert2.png).

1. Válassza a **Hozzáadás** lehetőséget.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban teszteli az Azure AD egyszeri bejelentkezési konfigurációját. Az egyszeri bejelentkezés teszteléséhez három lehetőség közül választhat:

* A Azure Portal válassza az **alkalmazás tesztelése** lehetőséget. A hivatkozás átirányítja az erőforrás központi bejelentkezési URL-címére, ahol megkezdheti a bejelentkezést.

* Lépjen közvetlenül az erőforrás központi bejelentkezési URL-címére, és indítsa el a bejelentkezést.

   :::image type="content" source="./media/resource-central/test.png" alt-text="Képernyőkép az erőforrás-központi egyszeri bejelentkezési teszt weblapról.":::

* Használja a saját alkalmazások portált a Microsofttól. A saját alkalmazások portálon válassza ki a **Resource Central** -csempét az erőforrás központi bejelentkezési URL-címére való átirányításhoz. További információ: [Bejelentkezés és alkalmazások indítása a saját alkalmazások portálján](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Következő lépések

Miután az Azure AD-vel egyszeri bejelentkezésre beállította az erőforrás-központot, kikényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).
