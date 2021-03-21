---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Microsofttól származó, összefolyásánál SAML egyszeri bejelentkezéssel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Microsoft Azure Active Directory és a köztes SAML egyszeri bejelentkezés között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/25/2020
ms.author: jeedes
ms.openlocfilehash: 34365a8bd7a15f502aa89a966adb14807e802cc4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736999"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-confluence-saml-sso-by-microsoft"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Microsoft által a torkolatánál SAML SSO-val

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Microsoft Azure Active Directory (Azure AD) szolgáltatásával a összefolyásánál SAML SSO-t. Ha a Microsoft Azure AD-vel integrálja a összefolyásánál SAML SSO-t, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a Microsofttól az összefolyásánál SAML egyszeri bejelentkezéshez.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Microsoft Azure AD-fiókjával való összefolyásánál az SAML egyszeri bejelentkezésre.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.


## <a name="description"></a>Leírás:

Az egyszeri bejelentkezés engedélyezéséhez használja a Microsoft Azure Active Directory fiókját a Atlassian-összefolyásánál kiszolgáló használatával. Így az összes céges felhasználó használhatja az Azure AD hitelesítő adatait az összefolyásánál alkalmazásba való bejelentkezéshez. Ez a beépülő modul SAML 2,0-et használ a összevonáshoz.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Microsofttól származó, összefolyásánál SAML SSO használatával a következő elemek szükségesek:

- Azure AD-előfizetés
- A Windows 64 bites kiszolgálóra (helyszíni vagy a felhőalapú IaaS-infrastruktúrára) telepített összefolyásánál Server alkalmazás
- A torkolatánál lévő kiszolgáló HTTPS-kompatibilis
- Vegye figyelembe, hogy az összefolyásánál beépülő modul támogatott verziói az alábbi szakaszban vannak felsorolva.
- Az összefolyásánál kiszolgáló elérhető az interneten, különösen az Azure AD bejelentkezési oldalán a hitelesítéshez, és képesnek kell lennie fogadni a jogkivonatot az Azure AD-től.
- A rendszergazdai hitelesítő adatok be vannak állítva a torkolatánál
- A websudo le van tiltva a torkolatánál
- Az összefolyásánál kiszolgáló alkalmazásban létrehozott felhasználó tesztelése

> [!NOTE]
> Az oktatóanyag lépéseinek teszteléséhez nem ajánlott az összefolyásánál éles környezet használata. Először tesztelje az integrációt az alkalmazás fejlesztési vagy előkészítési környezetében, majd használja az éles környezetet.

> [!NOTE]
> Ez az integráció az Azure AD USA kormányzati felhőalapú környezetének használatával is elérhető. Ezt az alkalmazást az Azure AD US government Cloud Application Galleryben találja, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben.

Első lépésként a következő elemeket kell megadnia:

* Ne használja éles környezetét, ha szükséges.
* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A Microsoft egyszeri bejelentkezés (SSO) engedélyezett előfizetése által elérhető SAML egyszeri bejelentkezés.

## <a name="supported-versions-of-confluence"></a>A torkolatánál támogatott verziói

Mostantól a torkolatánál következő verziói támogatottak:

- Összefolyásánál: 5,0 – 5,10
- Torkolatánál: 6.0.1 – 6.15.9
- Torkolatánál: 7.0.1 – 7.9.3

> [!NOTE]
> Vegye figyelembe, hogy az összefolyásánál beépülő modul az Ubuntu 16,04-es verzióján is működik

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az "összefolyásánál" SAML SSO a Microsoft támogatja az **SP** által KEZDEMÉNYEZett SSO

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Összefolyásánál SAML SSO hozzáadása a Microsoft által a katalógusból

Ahhoz, hogy a Microsoft az Azure AD-be integrálni tudja az összefolyásánál SAML SSO-t, hozzá kell adnia a Microsoft által a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a következőt a keresőmezőbe: **TORKOLATi SAML SSO a Microsoft által** .
1. Válassza az **összefolyásánál SAML SSO lehetőséget a Microsoft által** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-confluence-saml-sso-by-microsoft"></a>Az Azure AD SSO konfigurálása és tesztelése a Microsoft által végzett összefolyásánál SAML egyszeri bejelentkezéshez

Konfigurálja és tesztelje az Azure AD SSO-t a Microsoft által a " **B. Simon**" nevű tesztelési SAML SSO használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között, amely a Microsoft által elérhető SAML SSO.

Az Azure AD SSO konfigurálásához és teszteléséhez a Microsoft által végzett összefolyásánál SAML SSO használatával hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[összefolyásánál SAML SSO konfigurálása a Microsoft SSO](#configure-confluence-saml-sso-by-microsoft-sso)** -vel – az alkalmazás oldalának egyszeri bejelentkezési beállításainak konfigurálása.
    1. **[Összefolyásánál SAML SSO-t hozhat létre a Microsoft test User](#create-confluence-saml-sso-by-microsoft-test-user)** -vel, hogy a B. Simon párja legyen a Microsoftnál, amely a felhasználó Azure ad-képviseletéhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal, a Microsoft Application Integration **SAML SSO-nal való összefolyásánál** , keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Az **azonosító** mezőbe írjon be egy URL-címet a következő minta használatával: `https://<domain:port>/`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és Sign-On URL-címmel. A port nem kötelező, ha az egy elnevezett URL-cím. Ezek az értékek az összefolyásánál beépülő modul konfigurálása során érkeznek, amelyet az oktatóanyag későbbi részében ismertetünk.

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

Ebben a szakaszban lehetővé teszi, hogy a B. Simon az Azure egyszeri bejelentkezés használatára engedélyezze a hozzáférést a Microsoft által biztosított összefolyásánál SAML SSO-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **összefolyásánál SAML SSO a Microsoft által** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-confluence-saml-sso-by-microsoft-sso"></a>A összefolyásánál SAML SSO konfigurálása a Microsoft SSO-val

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként az összefolyásánál-példányba.

1. Mutasson a fogaskerékre, és kattintson a **bővítmények** elemre.

    ![A legördülő menüben a "fogaskerék" ikont és a "bővítmények" elemet bemutató képernyőkép.](./media/confluencemicrosoft-tutorial/addon1.png)

1. Töltse le a beépülő modult a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=56503). Töltse fel manuálisan a Microsoft által biztosított beépülő modult a **feltöltés** menü használatával. A beépülő modul letöltése a [Microsoft szolgáltatási szerződése](https://www.microsoft.com/servicesagreement/)alá tartozik.

    ![Képernyőfelvétel: a "bővítmények kezelése" lap, amelyen a "bővítmény feltöltése" művelet van kiválasztva.](./media/confluencemicrosoft-tutorial/addon12.png)

1. Az összefolyásánál fordított proxy vagy a terheléselosztó forgatókönyvének futtatásához hajtsa végre a következő lépéseket:

    > [!NOTE]
    > Először konfigurálja a kiszolgálót az alábbi utasításokkal, majd telepítse a beépülő modult.

    a. Adja hozzá az alábbi attribútumot az **összekötő** -porton **server.xml** fájl JIRA-kiszolgáló alkalmazásban.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Képernyőkép, amely az "összekötő" porthoz hozzáadott attribútummal rendelkező "server.xml" fájlt mutatja.](./media/confluencemicrosoft-tutorial/reverseproxy1.png)

    b. Az **alap URL-cím** módosítása a **rendszerbeállításokban** a proxy/Load Balancer alapján.

    ![Képernyőkép, amely a "kiindulási URL-címmel" jelölt "adminisztráció-beállítások" lapot mutatja.](./media/confluencemicrosoft-tutorial/reverseproxy2.png)

1. Miután telepítette a beépülő modult, megjelenik a felhasználói bővítmények **kezelése** szakasz a **felhasználó által telepített** bővítmények szakaszában. Az új beépülő modul konfigurálásához kattintson a **Konfigurálás** elemre.

    ![Képernyőfelvétel: a "felhasználó által telepített" szakasz, a "configure" (Konfigurálás) gomb kiemelve.](./media/confluencemicrosoft-tutorial/addon15.png)

1. Hajtsa végre a következő lépéseket a konfiguráció lapon:

    ![Képernyőkép, amely az egyszeri bejelentkezési konfiguráció oldalát jeleníti meg.](./media/confluencemicrosoft-tutorial/addon54.png)

    > [!TIP]
    > Győződjön meg arról, hogy az alkalmazáshoz csak egy tanúsítvány van hozzárendelve, így nincs hiba a metaadatok feloldásakor. Ha több tanúsítvány is van, a rendszergazda hibaüzenetet kap a metaadatok feloldásakor.

    1. A **metaadatok URL-címe** szövegmezőbe illessze be az **alkalmazás-összevonási metaadatok URL-címét** , amelyet a Azure Portal másolt, majd kattintson a **feloldás** gombra. Beolvassa a identitásszolgáltató metaadat-URL-címét, és feltölti az összes mező információit.

    1. Másolja ki az **azonosítót, a válasz URL-címét és a bejelentkezési URL** -értékeket, majd illessze be őket az azonosító, a válasz URL-címére, **majd jelentkezzen be az URL-** címek szövegmezőbe a Azure Portal **alapszintű SAML konfigurációs**

    1. A **bejelentkezési gomb neve** mezőbe írja be annak a gombnak a nevét, amelyet a szervezet a felhasználók számára látni kíván a bejelentkezési képernyőn.

    1. A **bejelentkezési gomb leírása** mezőbe írja be annak a gombnak a leírását, amelyet a szervezet a felhasználók számára látni kíván a bejelentkezési képernyőn.

    1. Az **SAML felhasználói azonosító helyein** válassza ki **a felhasználói azonosító elemet a tulajdonos utasítás NameIdentifier elemében** , vagy a **felhasználói azonosító egy attribútum elemben**.  Ennek az AZONOSÍTÓnak az összefolyásánál felhasználói AZONOSÍTÓnak kell lennie. Ha a felhasználói azonosító nem egyezik, a rendszer nem engedélyezi a felhasználók számára a bejelentkezést. 

       > [!Note]
       > Az SAML alapértelmezett felhasználói AZONOSÍTÓjának helye a név azonosítója. Ezt módosíthatja egy attribútum lehetőségre, és megadhatja a megfelelő attribútum nevét.

    1. Ha a **felhasználói azonosító** elemet választja, akkor az attribútum **neve** szövegmezőbe írja be annak az attribútumnak a nevét, ahol a rendszer a felhasználói azonosítót várta. 

    1. Ha az összevont tartományt (például az ADFS-t stb.) használja az Azure AD-vel, kattintson a **Kezdőlap tartomány felderítésének engedélyezése** lehetőségre, és konfigurálja a **tartománynevet**.

    1. A **tartománynév** mezőben adja meg a tartománynevet az ADFS-alapú bejelentkezés esetén.

    1. Jelölje be az egyszeri Kijelentkezés **engedélyezése** lehetőséget, ha ki szeretne jelentkezni az Azure ad-ből, amikor egy felhasználó kijelentkezik a torkolatánál. 

    1. Ha csak az Azure AD-beli hitelesítő adatokkal szeretne bejelentkezni, engedélyezze az **Azure-bejelentkezés kényszerítése** jelölőnégyzetet.

       > [!Note]
       > Ha engedélyezni szeretné az alapértelmezett bejelentkezési űrlapot a rendszergazdai bejelentkezéshez a bejelentkezési oldalon, akkor adja hozzá a lekérdezési paramétert a böngésző URL-címéhez.
       > `https://<domain:port>/login.action?force_azure_login=false`

    1. A beállítások mentéséhez kattintson a **Save (Mentés** ) gombra.

       > [!NOTE]
       > A telepítéssel és a hibaelhárítással kapcsolatos további információkért tekintse meg az [MS TORKOLATÁNÁL SSO-összekötő felügyeleti útmutatóját](./ms-confluence-jira-plugin-adminguide.md). A segítségére is van egy [gyakori](./ms-confluence-jira-plugin-adminguide.md) kérdés.

### <a name="create-confluence-saml-sso-by-microsoft-test-user"></a>Összefolyásánál SAML SSO létrehozása a Microsoft test User használatával

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a helyszíni kiszolgálóba, a Microsofttól kell kiépíteni a összefolyásánál SAML SSO-t. A Microsoft által biztosított, SAML egyszeri bejelentkezéshez a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a helyi összefolyásánál lévő kiszolgálóra rendszergazdaként.

1. Mutasson a fogaskerékre, és kattintson a **felhasználó-felügyelet** elemre.

    ![Alkalmazott hozzáadása](./media/confluencemicrosoft-tutorial/user1.png)

1. A felhasználók szakaszban kattintson a **felhasználók hozzáadása** fülre. A **felhasználó hozzáadása** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Képernyőkép, amely megjeleníti a "torkolatánál-adminisztráció" elemet a "felhasználók hozzáadása" lapon, és a "felhasználó hozzáadása" adatokat.](./media/confluencemicrosoft-tutorial/user2.png)

    a. A **Felhasználónév** szövegmezőbe írja be a következőhöz hasonló felhasználó e-mail címét: B. Simon.

    b. A **teljes név** szövegmezőbe írja be a felhasználó teljes nevét, például: B. Simon.

    c. Az **e-mail** szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet B.Simon@contoso.com .

    d. A **jelszó** szövegmezőbe írja be a következőt: B. Simon.

    e. Kattintson a **Jelszó megerősítése** gombra a jelszó újbóli megadásához.

    f. Kattintson a **Hozzáadás** gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja a Microsoft bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen a Microsoft bejelentkezési URL-címére közvetlenül a összefolyásánál SAML SSO szolgáltatáshoz, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Ha a My apps szolgáltatásban a Microsoft csempén található összefolyásánál SAML egyszeri bejelentkezésre kattint, a rendszer átirányítja a Microsoft bejelentkezési URL-címén keresztül, hogy átirányítsa a köztes SAML SSO A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

Miután konfigurálta a összefolyásánál SAML egyszeri bejelentkezést a Microsofttal, kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)