---
title: 'Oktatóanyag: Azure Active Directory a hópehely-integrációval | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a hópehely között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: 1af0209265ec4945950120e80a83e19c8ab2eb4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98726256"
---
# <a name="tutorial-azure-active-directory-integration-with-snowflake"></a>Oktatóanyag: Azure Active Directory a hópehely-integrációval

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a hópehely-t Azure Active Directory (Azure AD) használatával. Ha a hópehely-t az Azure AD-vel integrálja, a következőket teheti:

* A hópehely-hozzáféréssel rendelkező Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a hópehely-ba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a hópehely-nal való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Hópehely egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

- A hópehely támogatja **az SP-t és a identitásszolgáltató** KEZDEMÉNYEZett SSO
- A hópehely támogatja [a felhasználók automatikus üzembe](snowflake-provisioning-tutorial.md) helyezését és megszüntetését (ajánlott)

## <a name="adding-snowflake-from-the-gallery"></a>A hópehely hozzáadása a katalógusból

A hópehely Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a galériából a Managed SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **hópehely** kifejezést a keresőmezőbe.
1. Válassza ki a **hópehely** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-snowflake"></a>Azure AD SSO konfigurálása és tesztelése a hópehely-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a hópehely használatával egy **B. Simon** nevű teszt felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a hópehely-beli kapcsolódó felhasználó között.

Ha az Azure AD SSO-t a hópehely-mel szeretné konfigurálni és tesztelni, hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[HÓPEHELY SSO konfigurálása](#configure-snowflake-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy hópehely-teszt felhasználót](#create-snowflake-test-user)** – ha a felhasználó Azure ad-képviseletéhez csatolt B. Simon-beli saját
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **hópehely** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/login`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást SP-ben kezdeményezett módban szeretné konfigurálni:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`
    
    b. A **kijelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/logout`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Ezeknek az értékeknek a megszerzéséhez forduljon a [hópehely ügyfél-támogatási csapathoz](https://support.snowflake.net/s/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

4. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **hópehely beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a "B. Simon" lehetőséget fogja használni az Azure egyszeri bejelentkezés használatára azáltal, hogy hozzáférést biztosít a hópehely-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **hópehely** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-snowflake-sso"></a>A hópehely SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a hópehely-ba biztonsági rendszergazdaként.

1. **Váltson át** a **ACCOUNTADMIN** szerepkörre a lap jobb felső sarkában található **profil** elemre kattintva.

    > [!NOTE]
    > Ez eltér a Felhasználónév alatti jobb felső sarokban kiválasztott környezettől.
    
    ![A hópehely-rendszergazda](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

1. Nyissa meg a **letöltött Base 64-tanúsítványt** a Jegyzettömbben. Másolja az értéket a "-----BEGIN CERTIFICATe-----" és a "-----END CERTIFICATe-----" között, és illessze be az alábbi **tanúsítvány** melletti idézőjelbe. A **ssoUrl** illessze be a **bejelentkezési URL-cím** értékét, amelyet a Azure Portal másolt. Válassza a **minden lekérdezés** elemet, majd kattintson a **Futtatás** gombra.

   ![Hópehely SQL](./media/snowflake-tutorial/tutorial_snowflake_sql.png)

   ```
   use role accountadmin;
   alter account set saml_identity_provider = '{
   "certificate": "<Paste the content of downloaded certificate from Azure portal>",
   "ssoUrl":"<Login URL value which you have copied from the Azure portal>",
   "type":"custom",
   "label":"AzureAD"
   }';
   alter account set sso_login_page = TRUE;
   ```


### <a name="create-snowflake-test-user"></a>Hópehely-teszt felhasználó létrehozása

Ha engedélyezni szeretné, hogy az Azure AD-felhasználók bejelentkezzenek a hópehely-ba, azt a hópehely-ba kell kiépíteni. A hópehely-ben a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a hópehely-ba biztonsági rendszergazdaként.

2. **Váltson át** a **ACCOUNTADMIN** szerepkörre a lap jobb felső sarkában található **profil** elemre kattintva.  

    ![A hópehely-rendszergazda](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

3. Hozza létre a felhasználót az alábbi SQL-lekérdezés futtatásával, így biztosítva a "bejelentkezési név" beállítását az Azure AD-felhasználónévre a munkalapon az alább látható módon.

    ![A hópehely adminsql](./media/snowflake-tutorial/tutorial_snowflake_usersql.png)

    ```
    use role accountadmin;
    CREATE USER britta_simon PASSWORD = '' LOGIN_NAME = 'BrittaSimon@contoso.com' DISPLAY_NAME = 'Britta Simon';
    ```

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja a hópehely bejelentkezési URL-címre, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen közvetlenül a hópehely bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a hópehely-ra, amelyhez be szeretné állítani az egyszeri bejelentkezést 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások között a hópehely csempére kattint, ha az SP módban van konfigurálva, akkor a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie arra a hópehely-ba, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

A hópehely konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)