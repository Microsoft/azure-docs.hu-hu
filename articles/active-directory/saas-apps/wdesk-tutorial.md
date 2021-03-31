---
title: 'Oktatóanyag: Azure Active Directory integráció a Wdesk-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Wdesk között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: jeedes
ms.openlocfilehash: d85d7ef37536b54ecfc1b65d19eafd1d499ca050
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104603270"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Wdesk

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Wdesk a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Wdesk-t az Azure AD-vel, a következőket teheti:

* A Wdesk-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Wdesk az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Wdesk egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Wdesk az **SP** és a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést támogatja.

## <a name="add-wdesk-from-the-gallery"></a>Wdesk hozzáadása a gyűjteményből

A Wdesk Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Wdesk a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Wdesk** kifejezést a keresőmezőbe.
1. Válassza ki a **Wdesk** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-wdesk"></a>Azure AD SSO konfigurálása és tesztelése a Wdesk-hez

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Wdesk-mel konfigurálja és teszteli a **Britta Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Wdesk kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD SSO és a Wdesk konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[WDESK SSO konfigurálása](#configure-wdesk-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Wdesk-teszt felhasználót](#create-wdesk-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Wdesk rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **Wdesk** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és Sign-On URL-címmel. Ezeket az értékeket a WDesk-portálról szerezheti be az egyszeri bejelentkezés konfigurálásakor.

4. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **Wdesk beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Wdesk.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Wdesk** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-wdesk-sso"></a>Wdesk SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Wdesk biztonsági rendszergazdaként.

1. A bal alsó sarokban kattintson a **rendszergazda** elemre, és válassza a **fiók rendszergazdája** elemet:
 
    ![Képernyőfelvétel: a rendszergazda menüből kiválasztott fiók rendszergazdája.](./media/wdesk-tutorial/account.png)

1. A Wdesk-rendszergazda területen navigáljon a **Biztonság**, majd az **SAML**  >  **SAML-beállítások** elemre:

    ![A képernyőfelvételen az SAML lapon kiválasztott SAML-beállítások láthatók.](./media/wdesk-tutorial/settings.png)

1. Az **SAML felhasználói azonosító beállításai** területen győződjön meg arról, **hogy az SAML felhasználói azonosító Wdesk Felhasználónév**.

    ![A képernyőfelvételen az SAML felhasználói I D beállításai láthatók, ahol kiválaszthatja az SAML-felhasználó I D-t, az ügyfélszolgálati felhasználónevet.](./media/wdesk-tutorial/wdesk-username.png)

4. Az **általános beállítások** területen jelölje be az **SAML egyszeri bejelentkezés engedélyezése**:

    ![Képernyőfelvétel: az SAML-beállítások szerkesztése, ahol kiválaszthatja az SAML egyszeri bejelentkezés engedélyezése lehetőséget.](./media/wdesk-tutorial/user-settings.png)

5. A **szolgáltató részletei** területen hajtsa végre a következő lépéseket:

    ![A képernyőképen a szolgáltató adatai láthatók, ahol megadhatja a leírt értékeket.](./media/wdesk-tutorial/service-provider.png)

    1. Másolja a **bejelentkezési URL-címet** , és illessze be a **bejelentkezési url** szövegmezőbe Azure Portal.

    1. Másolja a **metaadatok URL-címét** , és illessze be az **azonosító** szövegmezőbe Azure Portal.

    1. Másolja a **felhasználói URL-címet** , és illessze be a **Válasz URL-** szövegmezőbe Azure Portal.

    1. A módosítások mentéséhez kattintson a Azure Portal **Mentés** gombra.      

1. A **identitásszolgáltató beállításainak konfigurálása** elemre kattintva nyissa meg a **identitásszolgáltató-beállítások szerkesztése** párbeszédpanelt. Kattintson a **fájl kiválasztása** lehetőségre a Azure Portalból mentett **Metadata.xml** fájl megkereséséhez, majd töltse fel.
    
    ![A képernyőképen a Szerkesztés I d P beállításai láthatók, ahol feltöltheti a metaadatokat.](./media/wdesk-tutorial/metadata.png)
  
1. Kattintson a **módosítások mentése** gombra.

    ![Képernyőfelvétel: a módosítások mentése gomb.](./media/wdesk-tutorial/save.png)

### <a name="create-wdesk-test-user"></a>Wdesk-tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a Wdesk, a Wdesk kell kiépíteni őket. A Wdesk-ben a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a Wdesk biztonsági rendszergazdaként.

2. Navigáljon a **rendszergazdai**  >  **fiók rendszergazdájához**.

     ![Képernyőfelvétel: a rendszergazda menüből kiválasztott fiók rendszergazdája.](./media/wdesk-tutorial/account.png)

3. Kattintson  a tagok **elemre**.

4. Most kattintson a **tag hozzáadása** elemre a **tag hozzáadása** párbeszédpanel megnyitásához. 
   
    ![Képernyőfelvétel: a tagok lap, amelyen kiválaszthatja a tag hozzáadása lehetőséget.](./media/wdesk-tutorial/create-user-1.png)  

5. A **felhasználó** szövegmezőben adja meg a felhasználóhoz hasonló felhasználónevet, b.simon@contoso.com majd kattintson a **Folytatás** gombra.

    ![Képernyőfelvétel: a tag hozzáadása párbeszédpanel, amelyen megadhat egy felhasználót.](./media/wdesk-tutorial/create-user-3.png)

6.  Adja meg a részleteket az alábbi ábrán látható módon:
  
    ![Képernyőfelvétel: a tag hozzáadása párbeszédpanel, amelyen a felhasználók alapszintű információkat adhat hozzá.](./media/wdesk-tutorial/create-user-4.png)
 
    a. Az **e-mail** szövege mezőben adja meg a felhasználóhoz hasonló e-mail címet b.simon@contoso.com .

    b. Az **Utónév** szövegmezőbe írja be a (z) " **B**" felhasználó utónevét.

    c. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét, például **Simon** nevet.

7. Kattintson a **tag mentése** gombra.  

    ![A képernyőképen az üdvözlő e-mail küldése a tag mentése gombra kattintva látható.](./media/wdesk-tutorial/create-user-5.png)

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a Wdesk bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen közvetlenül a Wdesk bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a Wdesk, amelyhez be szeretné állítani az egyszeri bejelentkezést. 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások Wdesk csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat kezdeményezéséhez, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie arra a Wdesk, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)használatába.

## <a name="next-steps"></a>Következő lépések

A Wdesk konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
