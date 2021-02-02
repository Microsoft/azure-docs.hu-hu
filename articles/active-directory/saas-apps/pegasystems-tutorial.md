---
title: 'Oktatóanyag: Azure Active Directory integráció a Pega-rendszerekkel | Microsoft Docs'
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Pega rendszerek között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: jeedes
ms.openlocfilehash: 4cd767736d6349199f4c82b00cb0b35db36cdb44
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430138"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Oktatóanyag: Azure Active Directory integráció Pega rendszerekkel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Pega rendszereket Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a Pega rendszereket, a következőket teheti:

* A Pega rendszerekhez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az Pega rendszerbe az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Pega rendszerek egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Pega rendszerek támogatják az SP által kezdeményezett és a identitásszolgáltató által kezdeményezett egyszeri bejelentkezést.

## <a name="add-pega-systems-from-the-gallery"></a>Pega-rendszerek hozzáadása a gyűjteményből

A Pega rendszerek Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Pega-rendszereket a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Pega Systems** kifejezést a keresőmezőbe.
1. Válassza ki a **Pega rendszereket** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-pega-systems"></a>Azure AD SSO konfigurálása és tesztelése Pega rendszerekhez

Konfigurálja és tesztelje az Azure AD SSO-t Pega-rendszerekkel egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Pega-rendszerekben.

Az Azure AD SSO Pega-rendszerekkel való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Pega-rendszerek egyszeri](#configure-pega-systems-sso)** bejelentkezésének konfigurálása – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy Pega-Rendszerteszt felhasználót](#create-pega-systems-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon Pega-rendszerekkel rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Pega Systems** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Ha az **alapszintű SAML-konfiguráció** párbeszédpanelen szeretné konfigurálni az alkalmazást identitásszolgáltató módban, hajtsa végre az alábbi lépéseket.

    ![Alapszintű SAML-konfiguráció párbeszédpanel](common/idp-intiated.png)

    1. Az **azonosító** mezőbe írjon be egy URL-címet ebben a mintában:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. A **Válasz URL-címe** mezőbe írjon be egy URL-címet ebben a mintában:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Ha az alkalmazást SP-kezdeményezésű módban szeretné konfigurálni, válassza a **további URL-címek beállítása** lehetőséget, és végezze el a következő lépéseket.

    ![Pega rendszerek tartomány és URL-címek egyszeri bejelentkezési adatai](common/both-advanced-urls.png)

    1. A **bejelentkezési URL-cím** mezőbe írja be a bejelentkezési URL-cím értékét.

    1. A **továbbítási állapot** mezőben adja meg az URL-címet ebben a mintában: `https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Az itt megadott értékek helyőrzők. A tényleges azonosítót, a válasz URL-címét, a bejelentkezési URL-címet és a továbbítási állapot URL-címét kell használnia. Az Pega alkalmazásban az azonosító és a válasz URL-értékei az oktatóanyag későbbi részében leírtak szerint olvashatók be. A továbbítási állapot értékének lekéréséhez forduljon a [Pega Systems támogatási csapatához](https://www.pega.com/contact-us). Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

6. A Pega rendszerbeli alkalmazásnak az SAML-kijelentéseknek adott formátumban kell lennie. A megfelelő formátumban való lekéréshez egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok láthatók. Kattintson a **Szerkesztés** ikonra a **felhasználói attribútumok** párbeszédpanel megnyitásához:

    ![Felhasználói attribútumok](common/edit-attribute.png)

7. Az előző képernyőképen látható attribútumok mellett a Pega Systems alkalmazásnak néhány további attribútumot is meg kell adni az SAML-válaszban. A **felhasználói attribútumok** párbeszédpanel **felhasználói jogcímek** szakaszában adja meg az alábbi lépéseket az SAML-jogkivonat attribútumainak hozzáadásához:

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Ezek az értékek a szervezetre jellemzőek. Adja meg a megfelelő értékeket.

    1. Válassza az **új jogcím hozzáadása** elemet a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához:

    ![Válassza az új jogcím hozzáadása lehetőséget](common/new-save-attribute.png)

    ![Felhasználói jogcímek kezelése párbeszédpanel](common/new-attribute-details.png)

    1. A **név** mezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    1. Hagyja üresen a **névtér** mezőt.

    1. A **forrás** mezőben válassza az **attribútum** lehetőséget.

    1. A **forrás attribútum** listáról válassza ki az adott sorhoz megjelenő attribútum értékét.

    1. Kattintson az **OK** gombra.

    1. Kattintson a **Mentés** gombra.

8. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban válassza ki a **letöltési** hivatkozást az **összevonási metaadatok XML-fájlja** mellett, a követelmények szerint, és mentse a tanúsítványt a számítógépen:

    ![Tanúsítvány letöltési hivatkozása](common/metadataxml.png)

9. A **Pega rendszerek beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![A konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Pega rendszerekhez.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Pega rendszerek** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="configure-pega-systems-sso"></a>Pega-rendszerek egyszeri bejelentkezésének konfigurálása

1. Ha az egyszeri bejelentkezést a Pega- **rendszerek** oldalon szeretné konfigurálni, jelentkezzen be a Pega-portálra egy másik böngészőablakban található rendszergazdai fiókkal.

2. Válassza a  >  **sysadmin**  >  **Authentication szolgáltatás** létrehozása lehetőséget:

    ![Hitelesítési szolgáltatás kiválasztása](./media/pegasystems-tutorial/admin.png)
    
3. Hajtsa végre a következő lépéseket a **hitelesítési szolgáltatás létrehozása** képernyőn.

    ![Hitelesítési szolgáltatás létrehozása képernyő](./media/pegasystems-tutorial/admin1.png)

    1. A **típus** listában válassza az **SAML 2,0** elemet.

    1. A **név** mezőbe írja be a tetszőleges nevet (például: **Azure ad SSO**).

    1. A **rövid leírás** mezőbe írja be a leírást.  

    1. Válassza **a létrehozás és Megnyitás** elemet.
    
4. Az **Identity Provider (identitásszolgáltató) adatai** szakaszban válassza a **identitásszolgáltató-Metaadatok importálása** lehetőséget, és keresse meg a Azure Portal letöltött metaadat-fájlt. A metaadatok betöltéséhez kattintson a **Submit (Küldés** ) gombra:

    ![Identitáskezelés (identitásszolgáltató) – információ szakasz](./media/pegasystems-tutorial/admin2.png)
    
    Az Importálás az itt látható módon tölti fel a identitásszolgáltató-adatokat:

    ![Importált identitásszolgáltató-adatértékek](./media/pegasystems-tutorial/idp.png)
    
6. Hajtsa végre a következő lépéseket a **szolgáltatói (SP) beállítások** szakaszban.

    ![Szolgáltatói beállítások](./media/pegasystems-tutorial/sp.png)

    1. Másolja az **entitás-azonosító** értéket, és illessze be a Azure Portal **ALAPszintű SAML-konfiguráció** szakaszának **azonosító** mezőjébe.

    1. Másolja ki az **állítási szolgáltatás (ACS) helyének** értékét, és illessze be a **Válasz URL-cím** mezőbe a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    1. Jelölje be a **kérelmek aláírásának letiltása** jelölőnégyzetet.

7. Kattintson a **Mentés** gombra.

### <a name="create-pega-systems-test-user"></a>Pega rendszerű tesztelési felhasználó létrehozása

Ezután létre kell hoznia egy Britta Simon nevű felhasználót a Pega Systems-ben. A felhasználók létrehozásához működjön együtt a [Pega Systems támogatási csapatával](https://www.pega.com/contact-us) .

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a Pega Systems bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen a Pega Systems bejelentkezési URL-címére közvetlenül, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a Pega rendszerbe, amelyhez be szeretné állítani az egyszeri bejelentkezést. 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások Pega-rendszerek csempére kattint, ha az SP módban van konfigurálva, akkor a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, akkor automatikusan be kell jelentkeznie azokra a Pega-rendszerekre, amelyekhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)használatába.

## <a name="next-steps"></a>Következő lépések

A Pega rendszerek konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).