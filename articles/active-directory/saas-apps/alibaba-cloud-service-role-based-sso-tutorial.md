---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az Alibaba Cloud Service-szel (szerepköralapú SSO) | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az Alibaba Cloud Service (szerepköralapú SSO) között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/08/2020
ms.author: jeedes
ms.openlocfilehash: 6e4cb3c372b696b203d2441c74a6cafff175af47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736134"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-alibaba-cloud-service-role-based-sso"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az Alibaba Cloud Service-szel (szerepköralapú SSO)

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az Alibaba Cloud Service-t (szerepköralapú SSO) Azure Active Directory (Azure AD-val). Az Alibaba Cloud Service (szerepköralapú SSO) Azure AD-vel való integrálásakor a következőket teheti:

* Az Alibaba Cloud Service-hez (szerepköralapú SSO) hozzáférő Azure AD-beli vezérlés.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezzenek az Alibaba Cloud Service-be (szerepköralapú SSO) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az Alibaba Cloud Service (szerepköralapú SSO) egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az Alibaba Cloud Service (szerepköralapú SSO) támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Az Alibaba Cloud Service (szerepköralapú SSO) hozzáadása a katalógusból

Az Alibaba Cloud Service (szerepköralapú SSO) Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az Alibaba Cloud Service-t (szerepköralapú SSO) a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe az **Alibaba Cloud Service (szerepköralapú SSO)** kifejezést.
1. Válassza az **Alibaba Cloud Service (SZEREPKÖRALAPÚ SSO)** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.
5. Az **Alibaba Cloud Service (SZEREPKÖRALAPÚ SSO)** lapon kattintson a **Tulajdonságok** elemre a bal oldali navigációs ablaktáblán, és másolja az **objektumazonosítót** , és mentse a számítógépre a későbbi használat érdekében.

    ![Tulajdonságok konfiguráció](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)


## <a name="configure-and-test-azure-ad-sso-for-alibaba-cloud-service-role-based-sso"></a>Az Azure AD SSO konfigurálása és tesztelése az Alibaba Cloud Service-hez (szerepköralapú SSO)

Konfigurálja és tesztelje az Azure AD SSO-t az Alibaba Cloud Service-szel (szerepköralapú SSO) egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az Alibaba Cloud Service-ben (szerepköralapú SSO).

Az Azure AD SSO és az Alibaba Cloud Service (szerepköralapú SSO) konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
    1. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
2. **[Role-Based egyetlen Sign-On konfigurálása az Alibaba Cloud Service-ben](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. Az **[Alibaba Cloud Service (SZEREPKÖRALAPÚ SSO) egyszeri bejelentkezésének konfigurálása](#configure-alibaba-cloud-service-role-based-sso-sso)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Alibaba Cloud Service-t (SZEREPKÖRALAPÚ SSO](#create-alibaba-cloud-service-role-based-sso-test-user)** -t), hogy az a felhasználó Azure ad-beli képviseletéhez kapcsolódó, az Alibaba Cloud Service-ben (szerepköralapú SSO) található Britta Simon-ügyféllel rendelkezzen.
3. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal az **Alibaba Cloud Service (SZEREPKÖRALAPÚ SSO)** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha **szolgáltatói metaadatokat tartalmazó fájllal** rendelkezik, hajtsa végre a következő lépéseket:

    a. Kattintson a **metaadat-fájl feltöltése** elemre.

    b. Kattintson a **mappa emblémára** a metaadat-fájl kiválasztásához, majd kattintson a **feltöltés** elemre.


    >[!NOTE]
    >1. Az Alibaba Cloud International webhelyén töltse le a szolgáltatói metaadatokat [erről](https://signin.alibabacloud.com/saml-role/sp-metadata.xml) a hivatkozásról.
    > 1. Az Alibaba Cloud Service (CN) helyhez töltse le a szolgáltatói metaadatokat [erről](https://signin.aliyun.com/saml-role/sp-metadata.xml) a hivatkozásról.

    c. A metaadat-fájl feltöltése után az **azonosító** és a **Válasz URL-** értékei automatikusan fel lesznek töltve az Alibaba Cloud Service (szerepköralapú SSO) szakasz szövegmezőbe:

    > [!Note]
    > Ha az **azonosító** és a **Válasz URL-címe** nem kap automatikus kitöltést, akkor a követelménynek megfelelően adja meg manuálisan az értékeket.

1. Az Alibaba Cloud Service (szerepköralapú SSO) megköveteli a szerepkörök konfigurálását az Azure AD-ben. A szerepkör-jogcím előre konfigurálva van, így nem kell konfigurálnia, de az Azure AD-ben is létre kell hoznia őket a jelen [cikk](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)használatával.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az **Alibaba Cloud Service (SZEREPKÖRALAPÚ SSO) beállítása** szakaszban másolja ki a megfelelő URL-címet (ka) t a követelmény alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít az Alibaba Cloud Service-hez (szerepköralapú SSO).

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **Alibaba Cloud Service (SZEREPKÖRALAPÚ SSO)** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** lapon válassza a U2 lehetőséget a felhasználók listából, majd kattintson a **kiválasztás** gombra. Ezután kattintson a **hozzárendelés** elemre.

    ![Az Azure AD-teszt Felhasználó1 kiosztása](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

1. Tekintse meg a hozzárendelt szerepkört és az Alibaba Cloud Service (szerepköralapú SSO) tesztelését.

    ![Az Azure AD-teszt Felhasználó2 kiosztása](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >Miután hozzárendelte a felhasználót (U2), a létrehozott szerepkör automatikusan csatolva lesz a felhasználóhoz. Ha több szerepkört hozott létre, szükség szerint csatolja a megfelelő szerepkört a felhasználóhoz. Ha a szerepköralapú egyszeri bejelentkezést több Alibaba Felhőbeli fiókkal szeretné megvalósítani az Azure AD-ből, ismételje meg a fenti lépéseket.

## <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Role-Based egyetlen Sign-On konfigurálása az Alibaba Cloud Service-ben

1. Jelentkezzen be az Alibaba Cloud [RAM-konzolra](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9) a Account1 használatával.

2. A bal oldali navigációs ablaktáblán válassza az **egyszeri bejelentkezés** lehetőséget.

3. A **szerepköralapú egyszeri bejelentkezés** lapon kattintson a **identitásszolgáltató létrehozása** elemre.

4. A megjelenő lapon írja be `AAD` a identitásszolgáltató neve mezőbe a Leírás mezőt, és kattintson **a** **feltöltés** gombra a letöltött összevonási metaadat-fájl feltöltéséhez, majd kattintson **az OK** gombra.

5. A identitásszolgáltató sikeres létrehozása után kattintson a **RAM-szerepkör létrehozása** lehetőségre.

6. A **RAM-szerepkör neve** mezőbe írja be `AADrole` `AAD` a következőt: válasszon a **identitásszolgáltató kiválasztása** legördülő listából, majd kattintson az OK gombra.

    >[!NOTE]
    >Szükség esetén engedélyt adhat a szerepkörnek. A identitásszolgáltató és a hozzá tartozó szerepkör létrehozása után azt javasoljuk, hogy mentse a identitásszolgáltató és a szerepkör ARNs a későbbi használatra. A ARNs a identitásszolgáltató-információ lapon és a szerepkör adatai lapon szerezheti be.

7. Társítsa az Alibaba Cloud RAM szerepkört (AADrole) az Azure ad-felhasználóval (U2): Ha a RAM-szerepkört az Azure AD-felhasználóhoz társítja, létre kell hoznia egy szerepkört az Azure AD-ben a következő lépések végrehajtásával:

    a. Jelentkezzen be a [Microsoft Graph Explorerben](https://developer.microsoft.com/graph/graph-explorer).

    b. A szerepkör létrehozásához szükséges engedélyek beszerzéséhez kattintson az **engedélyek módosítása** elemre.

    ![Gráf config1](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. Válassza ki az alábbi engedélyeket a listából, majd kattintson az **engedélyek módosítása** lehetőségre az alábbi ábrán látható módon.

    ![Gráf config2](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >Az engedélyek megadása után jelentkezzen be újra a Graph Explorerben.

    d. A Graph Explorer lapon válassza az első legördülő lista **beolvasása** lehetőséget a második  legördülő listából. Ezután írja be `https://graph.microsoft.com/beta/servicePrincipals` a mezőt a legördülő lista melletti mezőbe, és kattintson a **lekérdezés futtatása** elemre.

    ![Gráf config3](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >Ha több könyvtárat használ, megadhatja a `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` lekérdezés mezőjét.

    e. A **Válasz előnézete** szakaszban bontsa ki a appRoles tulajdonságot a "szolgáltatásnév" értékről a későbbi használat érdekében.

    ![Gráf config4](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >A appRoles tulajdonság megkereséséhez írja `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` be a lekérdezést a mezőbe. Vegye figyelembe, hogy az az `objectID` objektum-azonosító, amelyet az Azure ad **tulajdonságlapján** másolt.

    f. Lépjen vissza a Graph Explorer programba, módosítsa a metódust a **beolvasás** **javításba**, illessze be a következő tartalmat a **kérelem törzse** szakaszba, és kattintson a **lekérdezés futtatása** gombra:
    ```
    { 
      "appRoles": [
        { 
          "allowedMemberTypes":[
            "User"
          ],
          "description": "msiam_access",
          "displayName": "msiam_access",
          "id": "41be2db8-48d9-4277-8e86-f6d22d35****",
          "isEnabled": true,
          "origin": "Application",
          "value": null
        },
        { "allowedMemberTypes": [
            "User"
        ],
        "description": "Admin,AzureADProd",
        "displayName": "Admin,AzureADProd",
        "id": "68adae10-8b6b-47e6-9142-6476078cdbce",
        "isEnabled": true,
        "origin": "ServicePrincipal",
        "value": "acs:ram::187125022722****:role/aadrole,acs:ram::187125022722****:saml-provider/AAD"
        }
      ]
    }
    ```
    > [!NOTE]
    > A a `value` identitásszolgáltató és a RAM-konzolon létrehozott szerepkör ARNs. Itt több szerepkört is hozzáadhat, ha szükséges. Az Azure AD a következő szerepkörök értékét küldi el az SAML-válaszban szereplő jogcím-értékként. A javítási művelet részeként azonban csak új szerepköröket adhat hozzá `msiam_access` . A létrehozási folyamat zökkenőmentes kihasználása érdekében javasoljuk, hogy használjon azonosító-generátort, például GUID-generátort, hogy valós időben hozza létre az azonosítókat.

    : Miután a "szolgáltatásnév" a szükséges szerepkörrel rendelkezik, csatolja a szerepkört az Azure AD-felhasználóhoz (U2) az oktatóanyag **Azure ad-teszt felhasználójának hozzárendelése** című szakaszának lépéseit követve.

### <a name="configure-alibaba-cloud-service-role-based-sso-sso"></a>Az Alibaba Cloud Service (szerepköralapú SSO) egyszeri bejelentkezésének konfigurálása

Az **Alibaba Cloud Service (SZEREPKÖRALAPÚ SSO)** oldalon történő egyszeri bejelentkezés konfigurálásához el kell küldenie a letöltött **összevonási METAADATOKat tartalmazó XML** -fájlt és a megfelelő másolt url-címeket a Azure Portalról az [ALIBABA Cloud Service (szerepköralapú SSO) támogatási csapatnak](https://www.aliyun.com/service/). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Az Alibaba Cloud Service (szerepköralapú SSO) tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre az Alibaba Cloud Service-ben (szerepköralapú SSO). Az [Alibaba Cloud Service (SZEREPKÖRALAPÚ SSO) támogatási csapatának](https://www.aliyun.com/service/) használata a felhasználók hozzáadásához az Alibaba Cloud Service (szerepköralapú SSO) platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Az előző konfigurációk befejezése után tesztelje az Alibaba Cloud Service-t (szerepköralapú SSO) a következő lépések végrehajtásával:

1. A Azure Portal lépjen az **Alibaba Cloud Service (SZEREPKÖRALAPÚ SSO)** lapra, válassza az **egyszeri bejelentkezés** lehetőséget, majd kattintson a **teszt** gombra.

    ![Config1 tesztelése](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. Kattintson a **Bejelentkezés az aktuális felhasználóként** elemre.

    ![Config2 tesztelése](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. A fiók kiválasztása lapon válassza a U2 lehetőséget.

    ![Config3 tesztelése](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. A következő oldal jelenik meg, amely azt jelzi, hogy a szerepköralapú egyszeri bejelentkezés sikeres.

    ![Config4 tesztelése](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="next-steps"></a>Következő lépések

Az Alibaba Cloud Service (szerepköralapú SSO) konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).
