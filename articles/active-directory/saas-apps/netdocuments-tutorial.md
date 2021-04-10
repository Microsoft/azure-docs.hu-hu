---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a NetDocuments | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és NetDocuments között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: jeedes
ms.openlocfilehash: 48ba2810c0aaf304042580cdf6579df54fd9ccd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645672"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netdocuments"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a NetDocuments

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a NetDocuments a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az NetDocuments-t az Azure AD-vel, a következőket teheti:

* A NetDocuments-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a NetDocuments az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* NetDocuments egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A NetDocuments támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-netdocuments-from-the-gallery"></a>NetDocuments hozzáadása a gyűjteményből

A NetDocuments Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a NetDocuments a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **NetDocuments** kifejezést a keresőmezőbe.
1. Válassza ki a **NetDocuments** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-netdocuments"></a>Azure AD SSO konfigurálása és tesztelése a NetDocuments-hez

Konfigurálja és tesztelje az Azure AD SSO-t a NetDocuments a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a NetDocuments-ben.

Az Azure AD SSO és a NetDocuments konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[NETDOCUMENTS SSO konfigurálása](#configure-netdocuments-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre NetDocuments-teszt felhasználót](#create-netdocuments-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-NetDocuments rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **NetDocuments** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az alábbi URL-minták egyikét:

    |Bejelentkezési URL-cím|
    |-----------|
    |`https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://eu.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://de.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://au.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címek egyikét:

    |Azonosító|
    |-----------|
    |`http://netdocuments.com/VAULT`|
    |`http://netdocuments.com/EU`|
    |`http://netdocuments.com/AU`|
    |`http://netdocuments.com/DE`|
    |

    c. A **Válasz URL-címe** szövegmezőbe írja be az alábbi URL-minták egyikét:

    |Válasz URL-cím|
    |-----------|
    |`https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://eu.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://de.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://au.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és a válasz URL-címével. A tárház-azonosító egy olyan érték, amely a NetDocuments-tárházhoz tartozó, a **CA-** tól kezdődően 8 karakterből áll. További információért tekintse meg a [NetDocuments összevont identitás támogatási dokumentumát](https://support.netdocuments.com/hc/en-us/articles/205220410-Federated-Identity-Login) . Ha nehézségekbe ütközik a fenti információk használatával, vegye fel a kapcsolatot a NetDocuments ügyfélszolgálati [csapatával](https://support.netdocuments.com/hc/) is. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A NetDocuments alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, ahol a **NameIdentifier** a **User. userPrincipalName** leképezéssel van leképezve. A NetDocuments alkalmazás a **ObjectId** -mel vagy bármely más, a szervezete **NameIdentifier**-ként érvényes jogcímet rendeli hozzá a **NameIdentifier** , ezért az attribútum-hozzárendelést úgy kell módosítania, hogy a **Szerkesztés** ikonra kattint, és megváltoztatja az attribútumok leképezését.

    ![image](common/edit-attribute.png)

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **alkalmazás-összevonási metaadatok URL-címét** , és másolja az URL-címet.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

1. A **NetDocuments beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a NetDocuments.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **NetDocuments** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a &quot;default Access&quot; szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name=&quot;configure-netdocuments-sso&quot;></a>NetDocuments SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a NetDocuments vállalati webhelyre rendszergazdaként.

2. A jobb felső sarokban válassza ki a nevét>**rendszergazdaként**.

3. Válassza a **Security Center** lehetőséget.
   
    ![Adattár](./media/netdocuments-tutorial/security-center.png &quot;Security Center")

4. Válassza a **speciális hitelesítés** lehetőséget.
    
    ![Speciális hitelesítési beállítások konfigurálása](./media/netdocuments-tutorial/advance-authentication.png "Speciális hitelesítési beállítások konfigurálása")

5.  Az **összevont azonosító** lapon hajtsa végre a következő lépéseket:   
   
    [![Összevont identitás](./media/netdocuments-tutorial/federated-id.png "Federated Identity")](./media/netdocuments-tutorial/federated-id.png#lightbox)
   
    a. Az **Összevont identitás-kiszolgáló típusa** beállításnál válassza a **Windows Azure Active Directory** lehetőséget.
    
    b.  Válassza a **fájl kiválasztása** lehetőséget, hogy feltöltse a letöltött metaadat-fájlt, amelyet a Azure Portal letöltött.
    
    c.  Válassza a **Mentés** lehetőséget.

### <a name="create-netdocuments-test-user"></a>NetDocuments-tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a NetDocuments, a NetDocuments kell kiépíteni őket. NetDocuments esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **NetDocuments** vállalati webhelyre rendszergazdaként.

2. A jobb felső sarokban válassza ki a nevét>**rendszergazdaként**.
   
    ![Felügyelet](./media/netdocuments-tutorial/user-admin.png "Rendszergazda")

3. Válassza a **Felhasználók és csoportok** elemet.
   
    ![Felhasználók és csoportok](./media/netdocuments-tutorial/users-groups.png "Adattár")

4. Az **E-mail cím** szövegmezőbe írja be a kiépíteni kívánt érvényes Azure Active Directory fiók e-mail-címét, majd kattintson a **felhasználó hozzáadása** elemre.
   
    ![E-mail-cím](./media/netdocuments-tutorial/user-mail.png "E-mail-cím")
   
    > [!NOTE]
    > A Azure Active Directory fiók tulajdonosa egy e-mailt fog kapni, amely tartalmaz egy hivatkozást, amely megerősíti a fiókot, mielőtt az aktívvá válna. A NetDocuments által biztosított egyéb NetDocuments-létrehozási eszközöket vagy API-kat használhatja Azure Active Directory felhasználói fiókok kiépítéséhez.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a NetDocuments bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a NetDocuments bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások NetDocuments csempére kattint, automatikusan be kell jelentkeznie arra a NetDocuments, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

A NetDocuments konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).