---
title: 'Oktatóanyag: Azure Active Directory integráció az SAP Business Bydesignnal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az SAP Business Bydesignnal között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: 6db863f43deb6eb2787cda60650a267a62076aad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101654340"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Oktatóanyag: Azure Active Directory-integráció az SAP Business ByDesign megoldással

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az SAP Business Bydesignnal Azure Active Directory (Azure AD) használatával. Ha integrálja az SAP Business Bydesignnal az Azure AD-vel, a következőket teheti:

* Az SAP Business Bydesignnal hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az SAP Business Bydesignnal az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* SAP Business Bydesignnal egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az SAP Business Bydesignnal támogatja az **SP** által kezdeményezett egyszeri bejelentkezést

## <a name="add-sap-business-bydesign-from-the-gallery"></a>SAP Business Bydesignnal hozzáadása a katalógusból

Az SAP Business Bydesignnal az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az SAP Business Bydesignnal a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be az **SAP Business bydesignnal** kifejezést a keresőmezőbe.
1. Válassza az **SAP Business bydesignnal** az eredmények panelen lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso"></a>Az Azure AD SSO konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t az SAP Business Bydesignnal egy **B. Simon** nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az SAP Business Bydesignnal.

Az Azure AD SSO az SAP Business Bydesignnal való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[SAP Business BYDESIGNNAL SSO konfigurálása](#configure-sap-business-bydesign-sso)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy SAP Business bydesignnal-teszt felhasználót](#create-sap-business-bydesign-test-user)** – hogy a Britta Simon partnere legyen a SAP Business bydesignnal, amely a felhasználó Azure ad-képviseletéhez van társítva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **SAP Business bydesignnal** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<servername>.sapbydesign.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Ezekhez az értékekhez forduljon az [SAP Business Bydesignnal ügyfél-támogatási csapatához](https://www.sap.com/products/cloud-analytics.support.html) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az SAP Business Bydesignnal alkalmazás meghatározott formátumban várja az SAML-kijelentéseket. Konfigurálja a következő jogcímeket ehhez az alkalmazáshoz. Az attribútumok értékeit az alkalmazás-integráció lapon, a **felhasználói attribútumok** szakaszban kezelheti. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** gombra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image1](common/edit-attribute.png)

6. A **név azonosító értékének** szerkesztéséhez kattintson a **Szerkesztés** ikonra.

    ![image2](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. A **felhasználói jogcímek kezelése** szakaszban hajtsa végre a következő lépéseket:

    ![image3](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Válassza a **transzformáció** **forrásként** lehetőséget.

    b. Az **átalakítás** legördülő listában válassza a **ExtractMailPrefix ()** elemet.

    > [!NOTE]
    > Alapértelmezés szerint a ByD a NameID formátumot használja a felhasználói leképezéshez **meghatározatlan** formában. A ByD leképezi az SAML-NameID a ByD felhasználói aliason. Emellett a ByD támogatja a name ID formátumot ( **emailAddress**). Ebben az esetben a ByD leképezi a SAM-NameID a ByD alkalmazott kapcsolattartási ByD felhasználói e-mail címét. További részletekért tekintse meg [ezt az SAP-blogot](https://blogs.sap.com/2017/05/24/single-sign-on-sso-with-sap-business-bydesign/).

8. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

9. Az **SAP Business Bydesignnal beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény szerint.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát az SAP Business Bydesignnal való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **SAP Business bydesignnal** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.

1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-sap-business-bydesign-sso"></a>Az SAP Business Bydesignnal SSO konfigurálása

1. Jelentkezzen be az SAP Business Bydesignnal-portálra rendszergazdai jogosultságokkal.

2. Navigáljon az **alkalmazás-és felhasználói kezelés általános feladathoz** , és kattintson az **identitás-szolgáltató** fülre.

3. Kattintson az **új identitás-szolgáltató** elemre, és válassza ki a metaadatokat tartalmazó XML-fájlt, amelyet a Azure Portal letöltött. A metaadatok importálásával a rendszer automatikusan feltölti a szükséges aláírási tanúsítványt és titkosítási tanúsítványt.

    ![Egyetlen Sign-On1 konfigurálása](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Ha a **szolgáltatói szolgáltatás URL-címét** bele szeretné foglalni az SAML-kérelembe, válassza a include Request **Consumer Service URL-cím** elemet.

5. Kattintson az **egyszeri bejelentkezés aktiválása** elemre.

6. Mentse a módosításokat.

7. Kattintson a **saját rendszerek** fülre.

    ![Egyetlen Sign-On2 konfigurálása](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. Az **Azure ad bejelentkezési URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-cím** értéket, amelyet a Azure Portal másolt.

    ![Egyetlen Sign-On3 konfigurálása](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Adja meg, hogy az alkalmazott manuálisan is kiválaszthatja-e a felhasználói AZONOSÍTÓval és jelszóval való bejelentkezést, vagy az SSO-t a **manuális identitás-szolgáltató kijelölésével**

10. Az **egyszeri bejelentkezési URL-cím** szakaszban válassza ki azt az URL-címet, amelyet az alkalmazottnak a rendszerbe való bejelentkezéshez használnia kell.
    Az alkalmazott legördülő listára eljuttatott URL-címben a következő lehetőségek közül választhat:

    **Nem egyszeri bejelentkezéses URL-cím**

    A rendszeren csak a normál rendszerurl-cím küldhető el az alkalmazottnak. Az alkalmazott nem tud bejelentkezik az SSO használatával, és ehelyett jelszót vagy tanúsítványt kell használnia.

    **EGYSZERI BEJELENTKEZÉS URL-CÍME**

    A rendszeren csak az egyszeri bejelentkezési URL-cím küldhető el az alkalmazottnak. Az alkalmazott az SSO használatával tud bejelentkezik. A hitelesítési kérést a rendszer átirányítja a identitásszolgáltató.

    **Automatikus kijelölés**

    Ha az egyszeri bejelentkezés nem aktív, a rendszer a normál rendszerurl-címet elküldi az alkalmazottnak. Ha az egyszeri bejelentkezés aktív, a rendszer ellenőrzi, hogy az alkalmazott rendelkezik-e jelszóval. Ha a jelszó elérhető, a rendszer az egyszeri bejelentkezési URL-címet és a nem SSO URL-címet is elküldi az alkalmazottnak. Ha azonban az alkalmazottnak nincs jelszava, csak az SSO URL-címet kell elküldeni az alkalmazottnak.

11. Mentse a módosításokat.

### <a name="create-sap-business-bydesign-test-user"></a>SAP Business Bydesignnal-tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre az SAP Business Bydesignnal-ben. Az SAP Business [Bydesignnal ügyfél-támogatási csapatával](https://www.sap.com/products/cloud-analytics.support.html) vegye fel a felhasználókat az SAP Business bydesignnal platformon. 

> [!NOTE]
> Győződjön meg arról, hogy a NameID értékének egyeznie kell az SAP Business Bydesignnal platform username mezőjével.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

1. Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja az SAP Business Bydesignnal bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

2. Lépjen közvetlenül az SAP Business Bydesignnal bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

3. Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások SAP Business Bydesignnal csempére kattint, a rendszer átirányítja az SAP Business Bydesignnal bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

* Az SAP üzleti Bydesignnal konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáféréstől. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).