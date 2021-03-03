---
title: 'Oktatóanyag: Azure Active Directory integráció az Adobe Experience Managerrel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az Adobe Experience Manager között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2021
ms.author: jeedes
ms.openlocfilehash: 1a0340b9d8971446113862465c67143674bd5668
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653354"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Oktatóanyag: Azure Active Directory integráció az Adobe Experience Managerrel

Ez az oktatóanyag azt ismerteti, hogyan integrálható az Adobe Experience Manager Azure Active Directory (Azure AD) szolgáltatással. Az Adobe Experience Manager és az Azure AD integrálásával a következőket teheti:

* Az Adobe Experience Managerhez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az Adobe Experience Managerbe az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az Adobe Experience Manager egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az Adobe Experience Manager támogatja **az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

* Az Adobe Experience Manager **csak időben támogatja a** felhasználók üzembe helyezését

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Az Adobe Experience Manager hozzáadása a gyűjteményből

Az Adobe Experience Manager Azure AD-integrációjának konfigurálásához hozzá kell adnia az Adobe Experience Managert a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe az **Adobe Experience Manager** kifejezést.
1. Válassza az **Adobe Experience Manager** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-adobe-experience-manager"></a>Az Azure AD SSO konfigurálása és tesztelése az Adobe Experience Managerhez

Konfigurálja és tesztelje az Azure AD SSO-t az Adobe Experience Managerrel egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az Adobe Experience Managerben.

Az Azure AD SSO az Adobe Experience Managerrel való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
2. Az **[Adobe Experience Manager egyszeri bejelentkezésének konfigurálása](#configure-adobe-experience-manager-sso)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy Adobe Experience Manager-teszt felhasználót](#create-adobe-experience-manager-test-user)** – hogy a Britta Simon partnere legyen az Adobe Experience Managerben, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal az **Adobe Experience Manager** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az AEM-kiszolgálón definiált egyedi értéket is.

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > A válasz URL-cím értéke nem valódi. A válasz URL-címének frissítése a tényleges válasz URL-címével. Az érték beszerzéséhez lépjen kapcsolatba az [Adobe Experience Manager ügyfél-támogatási csapatával](https://helpx.adobe.com/support/experience-manager.html) az érték beszerzéséhez. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az Adobe Experience Manager-kiszolgáló URL-címét.

6. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

7. Az **Adobe Experience Manager beállítása** szakaszban adja meg a megfelelő URL (eke) t a követelménynek megfelelően.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát az Adobe Experience Manager elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **Adobe Experience Manager** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-adobe-experience-manager-sso"></a>Az Adobe Experience Manager egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban nyissa meg az **Adobe Experience Manager** felügyeleti portált.

2. Válassza a **Beállítások**  >  **biztonsági**  >  **felhasználók** lehetőséget.

    ![Képernyőkép, amely a felhasználók csempét jeleníti meg az Adobe Experience Managerben.](./media/adobe-experience-manager-tutorial/user-1.png)

3. Válassza a **rendszergazda** vagy bármely más megfelelő felhasználó lehetőséget.

    ![Képernyőkép, amely kiemeli a Adminisrator felhasználót.](./media/adobe-experience-manager-tutorial/tutorial-admin-6.png)

4. Válassza a **Fiókbeállítások**  >  **TrustStore kezelése** lehetőséget.

    ![Képernyőkép, amely a TrustStore kezelése menüpontot jeleníti meg.](./media/adobe-experience-manager-tutorial/manage-trust.png)

5. A **tanúsítvány hozzáadása a CER-fájlból** területen kattintson a **tanúsítványfájl kiválasztása** elemre. Tallózással keresse meg és válassza ki a tanúsítványt, amelyet már letöltött a Azure Portalból.

    ![Képernyőfelvétel: a tanúsítványfájl kiválasztása gomb kiemelése.](./media/adobe-experience-manager-tutorial/user-2.png)

6. A rendszer hozzáadja a tanúsítványt a TrustStore. Jegyezze fel a tanúsítvány aliasát.

    ![Képernyőkép, amely azt mutatja, hogy a tanúsítvány hozzá van adva a TrustStore.](./media/adobe-experience-manager-tutorial/tutorial-admin-7.png)

7. A **felhasználók** lapon válassza a **hitelesítés – szolgáltatás** elemet.

    ![A sreenshot, amely kiemeli a hitelesítési szolgáltatást a képernyőn.](./media/adobe-experience-manager-tutorial/tutorial-admin-8.png)

8. Válassza a **Fiókbeállítások**  >  **Létrehozás/felügyelet tároló** beállítása lehetőséget. Hozzon létre egy tárolót a jelszó megadásával.

    ![Képernyőkép a tárolók kezeléséről.](./media/adobe-experience-manager-tutorial/tutorial-admin-9.png)

9. Lépjen vissza a felügyeleti képernyőre. Ezután válassza a **Beállítások**  >  **műveleti**  >  **webkonzol** lehetőséget.

    ![Képernyőfelvétel: a webkonzol kiemelése a beállítások szakasz műveletek területén.](./media/adobe-experience-manager-tutorial/tutorial-admin-1.png)

    Ekkor megnyílik a konfigurációs oldal.

    ![Az egyszeri bejelentkezéses mentés gomb konfigurálása](./media/adobe-experience-manager-tutorial/tutorial-admin-2.png)

10. Az **Adobe gránit SAML 2,0 hitelesítési kezelőjének** keresése. Ezután válassza a **Hozzáadás** ikont.

    ![Képernyőfelvétel: az Adobe gránit SAML 2,0 Authentication kezelője.](./media/adobe-experience-manager-tutorial/tutorial-admin-3.png)

11. Ezen a lapon végezze el a következő műveleteket.

    ![Egyetlen Sign-On mentés gomb konfigurálása](./media/adobe-experience-manager-tutorial/tutorial-admin-4.png)

    a. Az **elérési út** mezőbe írja be a értéket **/** .

    b. A **identitásszolgáltató URL-címe** mezőbe írja be a Azure Portalból másolt **bejelentkezési URL** -értéket.

    c. Az **identitásszolgáltató-tanúsítvány aliasa** mezőben adja meg a TrustStore-ben hozzáadott **tanúsítvány-alias** értéket.

    d. A **biztonsági megadott entitás azonosítója** mezőben adja meg a Azure Portal konfigurált egyedi **Azure ad-azonosító** értékét.

    e. Az **állítás fogyasztói szolgáltatás URL-címe** mezőbe írja be a Azure Portalban konfigurált **Válasz URL-címet** .

    f. A **Kulcstároló jelszava** mezőben adja meg a tárolóban beállított **jelszót** .

    : A **felhasználói ATTRIBÚTUM azonosítója** mezőben adja meg az adott esetben szükséges **azonosítót** vagy más felhasználói azonosítót.

    h. Válassza az **CRX-felhasználók létrehozása** lehetőséget.

    i. A **kijelentkezési URL-cím** mezőbe írja be a Azure Portalból kapott egyedi **kijelentkezési URL-** értéket.

    j. Kattintson a **Mentés** gombra.

### <a name="create-adobe-experience-manager-test-user"></a>Az Adobe Experience Manager tesztelési felhasználójának létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre az Adobe Experience Managerben. Ha a CRX- **felhasználók automatikus létrehozása** lehetőséget választotta, a rendszer automatikusan létrehozza a felhasználókat a sikeres hitelesítés után.

Ha manuálisan szeretné létrehozni a felhasználókat, működjön együtt az [Adobe Experience Manager támogatási csapatával](https://helpx.adobe.com/support/experience-manager.html) , és vegye fel a felhasználókat az Adobe Experience Manager platformba.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja az Adobe Experience Manager bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen közvetlenül az Adobe Experience Manager bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie az Adobe Experience Managerbe, amelyhez be szeretné állítani az egyszeri bejelentkezést 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások Adobe Experience Manager csempére kattint, ha az SP módban van konfigurálva, akkor a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie az Adobe Experience Managerbe, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

Az Adobe Experience Manager konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).