---
title: 'Oktatóanyag: Azure Active Directory integráció a JIRA SAML SSO-val a Microsofttal (V 5.2) | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és JIRA SAML egyszeri bejelentkezés között a Microsoft (V 5.2) használatával.
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
ms.openlocfilehash: 7b85cc064babf44b14e80abc02669573b4730da2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736887"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>Oktatóanyag: Azure Active Directory integráció a JIRA SAML SSO-val a Microsofttal (V 5.2)

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a JIRA SAML SSO-t a Microsoft (V 5.2) használatával a Azure Active Directory (Azure AD) segítségével. Amikor az Azure AD-vel integrálja a JIRA SAML SSO-t a Microsoft (V 5.2) használatával, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a JIRA SAML SSO-hoz a Microsofttól (V 5.2).
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az SAML SSO JIRA (V 5.2) a Microsoft Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="description"></a>Leírás

Az egyszeri bejelentkezés engedélyezéséhez használja a Microsoft Azure Active Directory-fiókját a Atlassian JIRA-kiszolgálóval. Így az összes céges felhasználó használhatja az Azure AD hitelesítő adatait a JIRA alkalmazásba való bejelentkezéshez. Ez a beépülő modul SAML 2,0-et használ a összevonáshoz.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a JIRA SAML SSO használatával a Microsoftnál (V 5.2) a következő elemek szükségesek:

- Azure AD-előfizetés
- A JIRA Core és a 5,2 szoftvernek telepítve és konfigurálva kell lennie a Windows 64 bites verzióján
- A JIRA-kiszolgáló HTTPS-kompatibilis
- Vegye figyelembe, hogy a JIRA beépülő modul támogatott verziói az alábbi szakaszban vannak felsorolva.
- A JIRA-kiszolgáló elérhető az interneten, különösen az Azure AD bejelentkezési oldalán a hitelesítéshez, és képesnek kell lennie fogadni a jogkivonatot az Azure AD-től.
- A rendszergazdai hitelesítő adatok beállítása a JIRA
- A websudo le van tiltva a JIRA
- A JIRA-kiszolgáló alkalmazásban létrehozott felhasználó tesztelése

> [!NOTE]
> Az oktatóanyag lépéseinek teszteléséhez nem ajánlott a JIRA éles környezetének használata. Először tesztelje az integrációt az alkalmazás fejlesztési vagy előkészítési környezetében, majd használja az éles környezetet.

Az oktatóanyag lépéseinek teszteléséhez kövesse az alábbi javaslatokat:

- Ne használja éles környezetét, ha szükséges.
- Ha nem rendelkezik Azure AD-próbaverzióval, egy hónapos próbaverziót is beszerezhet itt: [próbaverziós ajánlat](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-jira"></a>A JIRA támogatott verziói

* JIRA Core és szoftver: 5,2
* A JIRA a 6,0-7,12 is támogatja. További részletekért kattintson a [Microsoft SAML SSO JIRA](jiramicrosoft-tutorial.md)

> [!NOTE]
> Vegye figyelembe, hogy a JIRA beépülő modul az Ubuntu 16,04-es verzióján is működik.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* JIRA SAML SSO a Microsofttól (V 5.2) támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>JIRA SAML SSO hozzáadása a Microsofttól (V 5.2) a katalógusból

Ahhoz, hogy a JIRA SAML SSO-t a Microsoft (V 5.2) használatával konfigurálja az Azure AD-be, hozzá kell adnia a JIRA SAML SSO-t a Microsoft (V 5.2) szolgáltatásból a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **JIRA SAML SSO-t a Microsoft (v 5.2)** mezőbe a keresőmezőbe.
1. Válassza ki a **JIRA SAML SSO-t a Microsofttól (v 5.2)** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-jira-saml-sso-by-microsoft-v52"></a>Azure AD SSO konfigurálása és tesztelése a JIRA SAML SSO-hoz a Microsoft számára (V 5.2)

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálhatja és tesztelheti a JIRA SAML SSO-val a Microsoft (V 5.2) alapján a **Britta Simon** nevű teszt felhasználója alapján. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy összekapcsolt kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a JIRA SAML SSO által a Microsoft (V 5.2) használatával.

Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a JIRA SAML SSO-vel a Microsofttól (V 5.2) hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
    1. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
2. **[JIRA SAML SSO konfigurálása a Microsoft (v 5.2) egyszeri bejelentkezéssel](#configure-jira-saml-sso-by-microsoft-v52-sso)** – az alkalmazás oldalának Sign-On beállításainak konfigurálása.
    1. **[Hozzon létre JIRA SAML SSO-t a Microsoft (v 5.2) használatával](#create-jira-saml-sso-by-microsoft-v52-test-user)** , hogy a Microsoft (v 5.2) a felhasználó Azure ad-képviseletéhez kapcsolódó Britta Simon partnere legyen.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

1. A Azure Portal a **JIRA SAML SSO Microsoft (v 5.2)** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** ceruza ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Az **azonosító** mezőbe írjon be egy URL-címet a következő minta használatával: `https://<domain:port>/`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és Sign-On URL-címmel. A port nem kötelező, ha az egy elnevezett URL-cím. Ezek az értékek a JIRA beépülő modul konfigurálása során érkeznek, amelyet az oktatóanyag későbbi részében ismertetünk.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

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

Ebben a szakaszban lehetővé teszi, hogy a B. Simon az Azure egyszeri bejelentkezés használatára engedélyezze a hozzáférést a Microsoft (V 5.2) által biztosított SAML SSO JIRA.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **JIRA SAML SSO a Microsofttól (v 5.2)** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-jira-saml-sso-by-microsoft-v52-sso"></a>JIRA SAML SSO konfigurálása a Microsoft (V 5.2) egyszeri bejelentkezéssel

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a JIRA-példányba.

2. Mutasson a fogaskerékre, és kattintson a **bővítmények** elemre.

    ![A képernyőképen a beállítások menüből kiválasztott bővítmények láthatók.](./media/jira52microsoft-tutorial/addon1.png)

3. A Bővítmények lap beállítások területén kattintson a **Bővítmények kezelése** elemre.

    ![A képernyőképen a Bővítmények lapon kiválasztott bővítmények kezelése látható.](./media/jira52microsoft-tutorial/addon7.png)

4. Töltse le a beépülő modult a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=56521). Töltse fel manuálisan a Microsoft által biztosított beépülő modult a **feltöltés** menü használatával. A beépülő modul letöltése a [Microsoft szolgáltatási szerződése](https://www.microsoft.com/servicesagreement/)alá tartozik.

    ![A képernyőképen a Bővítmények kezelése hivatkozásra kattintva láthatja a bővítmények kezelését.](./media/jira52microsoft-tutorial/addon12.png)

5. A beépülő modul telepítése után a **felhasználó által telepített** bővítmények szakaszban jelenik meg. Az új beépülő modul konfigurálásához kattintson a **Konfigurálás** elemre.

    ![A képernyőképen az Azure A D SAML-alapú egyszeri bejelentkezés az JIRA szakaszra, a configure kiválasztott beállítással látható.](./media/jira52microsoft-tutorial/addon13.png)

6. Hajtsa végre a következő lépéseket a konfiguráció lapon:

    ![A képernyőképen a Microsoft JIRA S S O-összekötő konfigurációs lapja látható.](./media/jira52microsoft-tutorial/addon52.png)

    > [!TIP]
    > Győződjön meg arról, hogy az alkalmazáshoz csak egy tanúsítvány van hozzárendelve, így nincs hiba a metaadatok feloldásakor. Ha több tanúsítvány is van, a metaadatok feloldásakor a rendszergazda hibaüzenetet kap.

    a. A **metaadatok URL-címe** szövegmezőbe illessze be az **alkalmazás-összevonási metaadatok URL-címét** , amelyet a Azure Portal másolt, majd kattintson a **feloldás** gombra. Beolvassa a identitásszolgáltató metaadat-URL-címét, és feltölti az összes mező információit.

    b. Másolja ki az **azonosítót, a válasz URL-címét és a bejelentkezési URL** -értékeket, majd illessze be őket az azonosító, a válasz URL-címére, **majd jelentkezzen be az URL-** címek szövegmezőbe a Azure Portal **alapszintű SAML konfigurációs**

    c. A **bejelentkezési gomb neve** mezőbe írja be annak a gombnak a nevét, amelyet a szervezet a felhasználók számára látni kíván a bejelentkezési képernyőn.

    d. Az **SAML felhasználói azonosító helyein** válassza **a felhasználói azonosító elemet a tulajdonos utasítás NameIdentifier elemében** , vagy a **felhasználói azonosító egy attribútum elemben**.  Ennek az AZONOSÍTÓnak a JIRA felhasználói AZONOSÍTÓnak kell lennie. Ha a felhasználói azonosító nem egyezik, a rendszer nem engedélyezi a felhasználók számára a bejelentkezést.

    > [!Note]
    > Az SAML alapértelmezett felhasználói AZONOSÍTÓjának helye a név azonosítója. Ezt módosíthatja egy attribútum lehetőségre, és megadhatja a megfelelő attribútum nevét.

    e. Ha a **felhasználói azonosító** elemet választja, akkor az attribútum **neve** szövegmezőbe írja be annak az attribútumnak a nevét, ahol a rendszer a felhasználói azonosítót várta. 

    f. Ha az összevont tartományt (például az ADFS-t stb.) használja az Azure AD-vel, kattintson a **Kezdőlap tartomány felderítésének engedélyezése** lehetőségre, és konfigurálja a **tartománynevet**.

    : A **tartománynév** mezőben adja meg a tartománynevet az ADFS-alapú bejelentkezés esetén.

    h. Jelölje be az egyszeri Kijelentkezés **engedélyezése** lehetőséget, ha ki szeretne jelentkezni az Azure ad-ből, amikor egy felhasználó kijelentkezik a JIRA. 

    i. A beállítások mentéséhez kattintson a **Save (Mentés** ) gombra.

    > [!NOTE]
    > A telepítéssel és a hibaelhárítással kapcsolatos további információkért tekintse meg az [MS JIRA SSO-összekötő rendszergazdai útmutatóját](./ms-confluence-jira-plugin-adminguide.md) , valamint a segítségére vonatkozó [gyakori kérdéseket](./ms-confluence-jira-plugin-adminguide.md) is.

### <a name="create-jira-saml-sso-by-microsoft-v52-test-user"></a>JIRA SAML SSO létrehozása a Microsofttól (V 5.2) tesztelési felhasználó

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a helyszíni JIRA, a helyszíni JIRA kell kiépíteni őket.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a JIRA a helyszíni kiszolgálóra rendszergazdaként.

2. Mutasson a fogaskerékre, és kattintson a **felhasználó-felügyelet** elemre.

    ![A képernyőfelvételen a beállítások menüből kiválasztott felhasználói felügyelet látható.](./media/jira52microsoft-tutorial/user1.png)

3. A rendszer átirányítja a rendszergazdai hozzáférés lapra a **jelszó** megadásához, majd kattintson a **megerősítés** gombra.

    ![Képernyőfelvétel: rendszergazdai hozzáférés lap, ahol megadhatja a hitelesítő adatait.](./media/jira52microsoft-tutorial/user2.png)

4. A **felhasználói kezelés** lap szakaszban kattintson a **felhasználó létrehozása** elemre.

    ![Képernyőfelvétel: a felhasználó-kezelés lap, amelyen létrehozhat felhasználókat.](./media/jira52microsoft-tutorial/user3.png) 

5. Az **"új felhasználó létrehozása"** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![A képernyőképen az új felhasználó létrehozása párbeszédpanel jelenik meg, amelyen megadhatja az ebben a lépésben szereplő információkat.](./media/jira52microsoft-tutorial/user4.png)

    a. Az **e-mail cím** szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet Brittasimon@contoso.com .

    b. A **teljes név** szövegmezőbe írja be a felhasználó teljes nevét, például a Britta Simon nevet.

    c. A **Felhasználónév** szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet Brittasimon@contoso.com .

    d. A **jelszó** szövegmezőbe írja be a felhasználó jelszavát.

    e. Kattintson a **felhasználó létrehozása** gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a JIRA SAML SSO-ra a Microsoft (V 5.2) bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Nyissa meg a JIRA SAML SSO-t a Microsoft (V 5.2) bejelentkezési URL-címével, és indítsa el innen a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Ha a JIRA SAML SSO-t a Microsoft (V 5.2) csempén a saját alkalmazások területen kattint, a rendszer átirányítja a JIRA SAML SSO-t a Microsoft (V 5.2) bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

Miután konfigurálta a JIRA SAML SSO-t a Microsoft (5.2-es verzió) használatával, kikényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-aad).