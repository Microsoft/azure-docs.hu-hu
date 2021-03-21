---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a JIRA SAML SSO-val a Microsofttól | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Microsoft Azure Active Directory és a JIRA SAML SSO között.
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
ms.openlocfilehash: 75b8230a1027bbf3ff3d73fb35ce65107c2db7e9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98730672"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jira-saml-sso-by-microsoft"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a JIRA SAML SSO-val a Microsoft által

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a JIRA SAML SSO-t a Microsofttal a Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a JIRA SAML SSO-t a Microsofttal, a következőket teheti:

* A Microsoft által az SAML SSO JIRA hozzáférését biztosító Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Microsoft SAML SSO-JIRA az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="description"></a>Description

Az egyszeri bejelentkezés engedélyezéséhez használja a Microsoft Azure Active Directory-fiókját a Atlassian JIRA-kiszolgálóval. Így az összes céges felhasználó használhatja az Azure AD hitelesítő adatait a JIRA alkalmazásba való bejelentkezéshez. Ez a beépülő modul SAML 2,0-et használ a összevonáshoz.

## <a name="prerequisites"></a>Előfeltételek

Ha az Azure AD-integrációt a JIRA SAML SSO használatával szeretné konfigurálni a Microsoftnál, a következő elemek szükségesek:

- Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
- A JIRA Core és a 6,4 szoftvert a 8.14.0 vagy a JIRA Service Desk 3,0 4.11.1 kell telepíteni és konfigurálni a Windows 64 bites verziójában
- A JIRA-kiszolgáló HTTPS-kompatibilis
- Vegye figyelembe, hogy a JIRA beépülő modul támogatott verziói az alábbi szakaszban vannak felsorolva.
- A JIRA-kiszolgáló elérhető az interneten, különösen az Azure AD bejelentkezési oldalán a hitelesítéshez, és képesnek kell lennie fogadni a jogkivonatot az Azure AD-től.
- A rendszergazdai hitelesítő adatok beállítása a JIRA
- A websudo le van tiltva a JIRA
- A JIRA-kiszolgáló alkalmazásban létrehozott felhasználó tesztelése

> [!NOTE]
> Az oktatóanyag lépéseinek teszteléséhez nem ajánlott a JIRA éles környezetének használata. Először tesztelje az integrációt az alkalmazás fejlesztési vagy előkészítési környezetében, majd használja az éles környezetet.

Első lépésként a következő elemeket kell megadnia:

* Ne használja éles környezetét, ha szükséges.
* A Microsoft egyszeri bejelentkezés (SSO) engedélyezett előfizetése JIRA SAML SSO-t.

> [!NOTE]
> Ez az integráció az Azure AD USA kormányzati felhőalapú környezetének használatával is elérhető. Ezt az alkalmazást az Azure AD US government Cloud Application Galleryben találja, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben.

## <a name="supported-versions-of-jira"></a>A JIRA támogatott verziói

* JIRA Core és Software: 6,4 – 8.14.0
* JIRA Service Desk 3.0.0 – 4.11.1
* A JIRA a 5,2-es frissítést is támogatja. További részletekért kattintson [Microsoft Azure Active Directory egyszeri bejelentkezés a JIRA 5,2](jira52microsoft-tutorial.md)

> [!NOTE]
> Vegye figyelembe, hogy a JIRA beépülő modul a 16,04-es és a Linux-alapú Ubuntu-verzión is működik.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Microsoft SAML SSO JIRA támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-jira-saml-sso-by-microsoft-from-the-gallery"></a>JIRA SAML SSO hozzáadása a Microsofttól a katalógusból

A JIRA SAML SSO Microsoft Azure AD-ba való integrálásának konfigurálásához a Microsoft által a katalógusból a felügyelt SaaS-alkalmazások listájához hozzá kell adnia a JIRA SAML SSO-t.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **JIRA SAML SSO** -t a Microsoft által a keresőmezőbe.
1. Válassza ki a **JIRA SAML SSO** -t a Microsoft által az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-jira-saml-sso-by-microsoft"></a>Az Azure AD SSO konfigurálása és tesztelése a JIRA SAML egyszeri bejelentkezéshez a Microsofttól

Konfigurálja és tesztelje az Azure AD SSO-t a JIRA SAML SSO-val a Microsoft egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a JIRA SAML SSO által a Microsoft által.

Az Azure AD SSO és a JIRA SAML SSO Microsoft általi konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[JIRA SAML SSO konfigurálása a Microsoft SSO](#configure-jira-saml-sso-by-microsoft-sso)** -vel – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[JIRA SAML SSO létrehozása a Microsoft test User használatával](#create-jira-saml-sso-by-microsoft-test-user)** – ha a Microsoft a felhasználó Azure ad-képviseletéhez kapcsolt SAML SSO-val rendelkezik, a Microsoft a JIRA SAML egyszeri bejelentkezésre jogosult.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **JIRA SAML SSO a Microsoft** Application Integration lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Az **azonosító** mezőbe írjon be egy URL-címet a következő minta használatával: `https://<domain:port>/`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és Sign-On URL-címmel. A port nem kötelező, ha az egy elnevezett URL-cím. Ezek az értékek a JIRA beépülő modul konfigurálása során érkeznek, amelyet az oktatóanyag későbbi részében ismertetünk.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

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

Ebben a szakaszban lehetővé teszi, hogy a B. Simon az Azure egyszeri bejelentkezés használatára engedélyezze a hozzáférést a Microsoft által biztosított SAML SSO JIRA.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza ki a **JIRA SAML SSO elemet a Microsoftnál**.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-jira-saml-sso-by-microsoft-sso"></a>A JIRA SAML SSO konfigurálása a Microsoft SSO-val

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a JIRA-példányba.

2. Mutasson a fogaskerékre, és kattintson a **bővítmények** elemre.

    ![A képernyőképen a beállítások menüből kiválasztott bővítmények láthatók.](./media/jiramicrosoft-tutorial/addon1.png)

3. Töltse le a beépülő modult a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=56506). Töltse fel manuálisan a Microsoft által biztosított beépülő modult a **feltöltés** menü használatával. A beépülő modul letöltése a [Microsoft szolgáltatási szerződése](https://www.microsoft.com/servicesagreement/)alá tartozik.

    ![A képernyőképen a Bővítmények kezelése hivatkozásra kattintva láthatja a bővítmények kezelését.](./media/jiramicrosoft-tutorial/addon12.png)

4. A JIRA fordított proxy forgatókönyvének vagy a terheléselosztó forgatókönyvének futtatásához hajtsa végre a következő lépéseket:

    > [!NOTE]
    > Először konfigurálja a kiszolgálót az alábbi utasításokkal, majd telepítse a beépülő modult.

    a. Adja hozzá az alábbi attribútumot az **összekötő** -porton **server.xml** fájl JIRA-kiszolgáló alkalmazásban.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Képernyőfelvétel: a Server dot x m l fájl egy szerkesztőben, az új sorral egészült ki.](./media/jiramicrosoft-tutorial/reverseproxy1.png)

    b. Az **alap URL-cím** módosítása a **rendszerbeállításokban** a proxy/Load Balancer alapján.

    ![A képernyőképen az adminisztrációs beállítások láthatók, ahol megváltoztathatja a Base U R L-t.](./media/jiramicrosoft-tutorial/reverseproxy2.png)

5. Miután telepítette a beépülő modult, megjelenik a felhasználói bővítmények **kezelése** szakasz a **felhasználó által telepített** bővítmények szakaszában. Az új beépülő modul konfigurálásához kattintson a **Konfigurálás** elemre.

    ![A képernyőképen az Azure A D SAML-alapú egyszeri bejelentkezés az JIRA szakaszra, a configure kiválasztott beállítással látható.](./media/jiramicrosoft-tutorial/addon14.png)

6. Hajtsa végre a következő lépéseket a konfiguráció lapon:

    ![A képernyőképen a JIRA konfigurációs oldalának Microsoft Azure Active Directory egyszeri bejelentkezés látható.](./media/jiramicrosoft-tutorial/addon54.png)

    > [!TIP]
    > Győződjön meg arról, hogy az alkalmazáshoz csak egy tanúsítvány van hozzárendelve, így nincs hiba a metaadatok feloldásakor. Ha több tanúsítvány is van, a metaadatok feloldásakor a rendszergazda hibaüzenetet kap.

    a. A **metaadatok URL-címe** szövegmezőbe illessze be az **alkalmazás-összevonási metaadatok URL-címét** , amelyet a Azure Portal másolt, majd kattintson a **feloldás** gombra. Beolvassa a identitásszolgáltató metaadat-URL-címét, és feltölti az összes mező információit.

    b. Másolja ki az **azonosítót, a válasz URL-címét és a bejelentkezési URL-** értékeket, majd illessze be őket az **azonosító, a válasz URL** -cím mezőbe, és JELENTKEZZen be az URL-címek szövegmezőbe az **SAML SSO JIRA a Microsoft tartomány és URL** -címek szakaszban Azure Portal

    c. A **bejelentkezési gomb neve** mezőbe írja be annak a gombnak a nevét, amelyet a szervezet a felhasználók számára látni kíván a bejelentkezési képernyőn.
    
    d. A **bejelentkezési gomb leírása** mezőbe írja be annak a gombnak a leírását, amelyet a szervezet a felhasználók számára látni kíván a bejelentkezési képernyőn.

    e. Az **SAML felhasználói azonosító helyein** válassza **a felhasználói azonosító elemet a tulajdonos utasítás NameIdentifier elemében** , vagy a **felhasználói azonosító egy attribútum elemben**.  Ennek az AZONOSÍTÓnak a JIRA felhasználói AZONOSÍTÓnak kell lennie. Ha a felhasználói azonosító nem egyezik, a rendszer nem engedélyezi a felhasználók számára a bejelentkezést.

    > [!Note]
    > Az SAML alapértelmezett felhasználói AZONOSÍTÓjának helye a név azonosítója. Ezt módosíthatja egy attribútum lehetőségre, és megadhatja a megfelelő attribútum nevét.

    f. Ha a **felhasználói azonosító** elemet választja, akkor az attribútum **neve** szövegmezőbe írja be annak az attribútumnak a nevét, ahol a rendszer a felhasználói azonosítót várta.

    : Ha az összevont tartományt (például az ADFS-t stb.) használja az Azure AD-vel, kattintson a **Kezdőlap tartomány felderítésének engedélyezése** lehetőségre, és konfigurálja a **tartománynevet**.

    h. A **tartománynév** mezőben adja meg a tartománynevet az ADFS-alapú bejelentkezés esetén.

    i. Jelölje be az egyszeri Kijelentkezés **engedélyezése** lehetőséget, ha ki szeretne jelentkezni az Azure ad-ből, amikor egy felhasználó kijelentkezik a JIRA.
    
    j. Ha csak az Azure AD-beli hitelesítő adatokkal szeretne bejelentkezni, engedélyezze az **Azure-bejelentkezés kényszerítése** jelölőnégyzetet.
    
    > [!Note]
    > Ha engedélyezni szeretné az alapértelmezett bejelentkezési űrlapot a rendszergazdai bejelentkezéshez a bejelentkezési oldalon, akkor adja hozzá a lekérdezési paramétert a böngésző URL-címéhez.
    > `https://<domain:port>/login.jsp?force_azure_login=false`

    k. A beállítások mentéséhez kattintson a **Save (Mentés** ) gombra.

    > [!NOTE]
    > A telepítéssel és a hibaelhárítással kapcsolatos további információkért tekintse meg az [MS JIRA SSO-összekötő rendszergazdai útmutatóját](./ms-confluence-jira-plugin-adminguide.md). A segítségére is van egy [gyakori](./ms-confluence-jira-plugin-adminguide.md) kérdés.

### <a name="create-jira-saml-sso-by-microsoft-test-user"></a>JIRA SAML SSO létrehozása a Microsoft test User használatával

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a JIRA helyszíni kiszolgálóra, a Microsoft JIRA SAML SSO-ba kell kiépíteni őket. A Microsoft SAML SSO JIRA esetében a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a JIRA a helyszíni kiszolgálóra rendszergazdaként.

2. Mutasson a fogaskerékre, és kattintson a **felhasználó-felügyelet** elemre.

    ![A képernyőfelvételen a beállítások menüből kiválasztott felhasználói felügyelet látható.](./media/jiramicrosoft-tutorial/user1.png)

3. A rendszer átirányítja a rendszergazdai hozzáférés lapra a **jelszó** megadásához, majd kattintson a **megerősítés** gombra.

    ![Képernyőfelvétel: rendszergazdai hozzáférés lap, ahol megadhatja a hitelesítő adatait.](./media/jiramicrosoft-tutorial/user2.png)

4. A **felhasználói kezelés** lap szakaszban kattintson a **felhasználó létrehozása** elemre.

    ![Képernyőfelvétel: a felhasználó-kezelés lap, amelyen létrehozhat felhasználókat.](./media/jiramicrosoft-tutorial/user3.png) 

5. Az **"új felhasználó létrehozása"** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![A képernyőképen az új felhasználó létrehozása párbeszédpanel jelenik meg, amelyen megadhatja az ebben a lépésben szereplő információkat.](./media/jiramicrosoft-tutorial/user4.png) 

    a. Az **e-mail cím** szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet B.simon@contoso.com .

    b. A **teljes név** szövegmezőbe írja be a felhasználó teljes nevét, például: B. Simon.

    c. A **Felhasználónév** szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet B.simon@contoso.com .

    d. A **jelszó** szövegmezőbe írja be a felhasználó jelszavát.

    e. Kattintson a **felhasználó létrehozása** gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a JIRA SAML SSO-ra a Microsoft bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* A Microsoft bejelentkezési URL-címével közvetlenül a JIRA SAML SSO-t, a bejelentkezési folyamatot pedig innen indíthatja el.

* Használhatja a Microsoft saját alkalmazásait. Ha rákattint a saját alkalmazások JIRA SAML SSO-ra a Microsoft csempén, ez a Microsoft bejelentkezési URL-címén átirányítja a JIRA SAML SSO-ra. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

Miután konfigurálta a JIRA SAML SSO-t a Microsoftnál, kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)