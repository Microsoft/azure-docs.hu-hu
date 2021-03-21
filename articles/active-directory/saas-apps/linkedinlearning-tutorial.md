---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a LinkedIn learning szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a LinkedIn learning között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: e5c6bf41e1a3bf92c9141c0d3b54dd58ead2bf3c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98727300"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-learning"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a LinkedIn learninggel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a LinkedIn learninget Azure Active Directory (Azure AD) használatával. Ha integrálja a LinkedIn learninget az Azure AD-val, a következőket teheti:

* A LinkedIn learninghez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a LinkedIn Learningbe az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A LinkedIn learning egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A LinkedIn learning támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t
* A LinkedIn learning **csak időben támogatja a** felhasználók üzembe helyezését


## <a name="adding-linkedin-learning-from-the-gallery"></a>LinkedIn learning hozzáadása a katalógusból

A LinkedIn learning Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a LinkedIn learning szolgáltatást a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **LinkedIn learning** kifejezést a keresőmezőbe.
1. Válassza az eredmények panel **LinkedIn learning** elemét, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-linkedin-learning"></a>Azure AD SSO konfigurálása és tesztelése a LinkedIn learninghez

Konfigurálja és tesztelje az Azure AD SSO-t a LinkedIn learning segítségével egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a LinkedIn learningben.

Az Azure AD SSO konfigurálásához és a LinkedIn Learningtel való teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[LinkedIn learning SSO konfigurálása](#configure-linkedin-learning-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy LinkedIn learning test User](#create-linkedin-learning-test-user)** -t, hogy a LinkedIn learningben a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-ügyféllel rendelkezzen.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **LinkedIn learning** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

     a. Az **azonosító** szövegmezőbe írja be a LinkedIn portálról másolt **entitás azonosítóját** . 

    b. A **Válasz URL-címe** szövegmezőbe írja be a LinkedIn portálról másolt, az **ACS-beli URL-címet** .

    c. Ha az alkalmazást SP-ben **kezdeményezett** módban szeretné konfigurálni, akkor kattintson a **további URL-címek beállítása** lehetőségre az **alapszintű SAML-konfiguráció**  szakaszban, ahol meg fogja adni a bejelentkezési URL-címet. A bejelentkezési URL-cím létrehozásához másolja a Replace **Consumer Service (ACS) URL-címét** , és cserélje le a/SAML/-t a/login/. Ha elkészült, a bejelentkezési URL-címnek a következő mintát kell tartalmaznia:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![A LinkedIn learning domain és az URL-címek egyszeri bejelentkezési adatai](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Ezek az értékek nem valós érték. Ezeket az értékeket a tényleges azonosító és válasz URL-címmel fogja frissíteni, amelyet később az oktatóanyag a **LinkedIn learning SSO konfigurálása** című szakaszában talál.

1. A LinkedIn learning-alkalmazás megadott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, ahol a **NameIdentifier** a **User. userPrincipalName** leképezéssel van leképezve. A LinkedIn learning-alkalmazás azt várja, hogy a **NameIdentifier** a **User. mail** használatával legyen leképezve, ezért az attribútum-hozzárendelést úgy kell módosítania, hogy rákattint a **Szerkesztés** ikonra, és megváltoztatja az attribútumok leképezését.

    ![image](common/edit-attribute.png)

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **LinkedIn learning beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést a LinkedIn learning elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **LinkedIn learning** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-linkedin-learning-sso"></a>A LinkedIn learning SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a LinkedIn learning-bérlőre rendszergazdaként.

2. A **Account Centerben** kattintson a **globális beállítások** elemre a **Beállítások** területen. Emellett válassza a **tanulás – alapértelmezett** lehetőséget a legördülő listából.

    ![A képernyőképen a globális beállítások láthatók, ahol kiválaszthatja az alapértelmezett lehetőséget.](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

3. Kattintson **vagy kattintson ide az egyes mezők betöltéséhez és másolásához az űrlapról** , majd másolja az **entitás-azonosító** és az **állítási fogyasztói szolgáltatás (ACS) URL-címét** , és ILLESSZE be az **alapszintű SAML-konfiguráció** szakaszba a Azure Portal.

    ![Képernyőfelvétel: egyetlen Sign-On, ahol megadhatja a leírt értékeket.](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

4. Ugrás a **LinkedIn admin Settings** szakaszra. Töltse fel a Azure Portal letöltött XML-fájlt az **XML-fájl feltöltése** lehetőségre kattintva.

    ![Képernyőfelvétel: a LinkedIn Service Provider S S O beállításainak konfigurálása, ahol feltölthető egy X M L-fájl.](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

5. Az egyszeri bejelentkezés engedélyezéséhez kattintson **a be** gombra. Az SSO-állapot változásai **nem** csatlakoztatott **kapcsolattal**

    ![Képernyőfelvétel: egyetlen Sign-On, ahol engedélyezheti a felhasználók hitelesítését S S O-vel.](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-learning-test-user"></a>LinkedIn learning test-felhasználó létrehozása

A LinkedIn learning-alkalmazás az igény szerinti felhasználói üzembe helyezést támogatja, és a hitelesítéssel rendelkező felhasználók automatikusan jönnek létre az alkalmazásban. A LinkedIn learning-portál rendszergazdai beállítások lapján a kapcsoló **automatikusan hozzárendeli a licenceket** az aktív időben történő üzembe helyezéshez, és ez a felhasználóhoz is hozzárendel egy licencet.

   ![Azure AD-tesztkörnyezet létrehozása](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a LinkedIn learning bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen közvetlenül a LinkedIn learning bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a LinkedIn Learningbe, amelyhez be szeretné állítani az egyszeri bejelentkezést 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások LinkedIn learning csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie arra a LinkedIn learning szolgáltatásba, amelyhez be kívánja állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

A LinkedIn learning konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-aad).