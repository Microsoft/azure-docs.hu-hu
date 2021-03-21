---
title: 'Oktatóanyag: Azure Active Directory integráció az SAML-hitelesítéssel a RStudio használatával | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és RStudio közötti összekapcsolási SAML-hitelesítéshez.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/21/2020
ms.author: jeedes
ms.openlocfilehash: 84e8c7fc1d2655ea0685ac79841a9c467bf766cf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96182391"
---
# <a name="tutorial-azure-active-directory-integration-with-rstudio-connect-saml-authentication"></a>Oktatóanyag: Azure Active Directory integráció az SAML-alapú RStudio-hitelesítéssel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a RStudio-hez kapcsolódó SAML-hitelesítést Azure Active Directory (Azure AD) használatával.
Az RStudio-hez készült SAML-hitelesítés és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá az SAML-hitelesítés RStudio.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek az SAML-hitelesítés (egyszeri bejelentkezés) RStudio az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció RStudio-kapcsolat SAML-hitelesítéssel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* RStudio-kapcsolat SAML-hitelesítéssel. [45 napos ingyenes próbaverzió](https://www.rstudio.com/products/connect/) áll rendelkezésre.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* RStudio az SAML-hitelesítés támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t

* A RStudio csatlakoztatása SAML **-hitelesítés a** felhasználók üzembe helyezését is támogatja

## <a name="adding-rstudio-connect-saml-authentication-from-the-gallery"></a>RStudio-kapcsolat SAML-hitelesítésének hozzáadása a katalógusból

A RStudio-csatlakozás SAML-hitelesítés Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a RStudio-kapcsolat SAML-hitelesítését a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **RSTUDIO-kapcsolat SAML-hitelesítése** kifejezést a keresőmezőbe.
1. Válassza az **SAML-hitelesítés RStudio** az eredmények panelen lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-rstudio-connect-saml-authentication"></a>Azure AD SSO konfigurálása és tesztelése a RStudio-hez kapcsolódó SAML-hitelesítéshez

Konfigurálja és tesztelje az Azure AD SSO-t a RStudio-hez kapcsolódó SAML-hitelesítéssel egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a RStudio-csatlakozás SAML-hitelesítéshez.

Az Azure AD SSO konfigurálásához és teszteléséhez a RStudio-alapú kapcsolat SAML-hitelesítéssel végezze el a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
    * **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
2. A **[RStudio-kapcsolat SAML-hitelesítésének konfigurálása](#configure-rstudio-connect-saml-authentication-sso)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
    * **[RStudio-kapcsolat SAML-hitelesítési teszt felhasználójának létrehozása](#create-rstudio-connect-saml-authentication-test-user)** – ha a RStudio-hez csatlakozik, a felhasználó Azure ad-BRITTA kapcsolt SAML-hitelesítéssel.
3. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **RStudio-összekötő SAML-hitelesítés** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket, és cserélje le a kifejezést `<example.com>` a RStudio-kapcsolat SAML-hitelesítési kiszolgálójának címe és portja helyére:

    ![RStudio az SAML hitelesítési tartomány és az URL-címek egyszeri bejelentkezési adatainak összekapcsolása](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<example.com>/__login__/saml`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<example.com>/__login__/saml/acs`

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![RStudio-kapcsolat SAML-hitelesítés metaadatainak feltöltése](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<example.com>/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Ezeket a rendszer a RStudio-kapcsolat SAML-hitelesítési kiszolgálójának címe alapján határozza meg ( `https://example.com` a fenti példákban). Ha problémája van, forduljon a [RStudio-kapcsolat SAML-hitelesítés támogatási csapatához](mailto:support@rstudio.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

6. A RStudio-összekötő SAML-hitelesítési alkalmazás meghatározott formátumban várja az SAML-jogcímeket, így egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, ahol a **NameIdentifier** a **User. userPrincipalName** leképezéssel van leképezve. Az SAML-RStudio csatlakoztatása azt várja, hogy a **NameIdentifier** a **User. mail** használatával legyen leképezve, ezért az attribútum-hozzárendelést úgy kell módosítania, hogy a **Szerkesztés** ikonra kattint, és módosítja az attribútum-hozzárendelést.

    ![image](common/edit-attribute.png)

7. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezést az SAML-hitelesítés RStudio való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **RStudio-kapcsolat SAML-hitelesítés** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-rstudio-connect-saml-authentication-sso"></a>RStudio-kapcsolat SAML-hitelesítésének konfigurálása SSO

Ha be szeretné állítani az egyszeri bejelentkezést a RStudio-hez készült **SAML-hitelesítéshez**, az **alkalmazás-összevonási metaadatok URL-címét** és a fent használt **kiszolgálónevet** kell használnia. Ez a RStudio-kapcsolat SAML-hitelesítés konfigurációs fájljában történik a következő helyen: `/etc/rstudio-connect.rstudio-connect.gcfg` .

Ez egy példa konfigurációs fájl:

```
[Server]
SenderEmail =

; Important! The user-facing URL of your RStudio Connect SAML Authentication server.
Address = 

[Http]
Listen = :3939

[Authentication]
Provider = saml

[SAML]
Logging = true

; Important! The URL where your IdP hosts the SAML metadata or the path to a local copy of it placed in the RStudio Connect SAML Authentication server.
IdPMetaData = 

IdPAttributeProfile = azure
SSOInitiated = IdPAndSP
```

Tárolja a **kiszolgáló címét** az `Server.Address` értékben, és az **alkalmazás összevonási metaadatainak URL-címét** az `SAML.IdPMetaData` értékben. Vegye figyelembe, hogy ez a minta konfiguráció titkosítatlan HTTP-kapcsolaton keresztül működik, míg az Azure AD titkosított HTTPS-kapcsolat használatát igényli. Használhat [fordított proxyt](https://docs.rstudio.com/connect/admin/proxy/) a RStudio-kapcsolat SAML-hitelesítése előtt, vagy konfigurálhatja a RStudio-csatlakozás SAML-hitelesítését a [https használatára közvetlenül](https://docs.rstudio.com/connect/admin/appendix/configuration/#HTTPS). 

Ha problémája van a konfigurációval, olvassa el a [RStudio-kapcsolat SAML-hitelesítési felügyeleti útmutatóját](https://docs.rstudio.com/connect/admin/authentication/saml/) , vagy küldjön e-mailt a [RStudio támogatási csapatának](mailto:support@rstudio.com) segítségére.

### <a name="create-rstudio-connect-saml-authentication-test-user"></a>RStudio-kapcsolat SAML-hitelesítési teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a RStudio-összekötő SAML-hitelesítésben. Az RStudio csatlakoztatása az SAML-hitelesítés támogatja az igény szerinti üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a RStudio-kapcsolat SAML-hitelesítésében, akkor létrejön egy új, amikor megpróbál hozzáférni a RStudio-csatlakozás SAML-hitelesítéshez.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ekkor a rendszer átirányítja a RStudio-kapcsolat SAML-hitelesítésének bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Nyissa meg közvetlenül az SAML-alapú RStudio-bejelentkezési URL-címet, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie a RStudio-hez készült SAML-hitelesítésbe, amelyhez be kell állítania az egyszeri bejelentkezést 

A Microsoft Access panel használatával bármilyen módban tesztelheti az alkalmazást. Ha a hozzáférési panelen a RStudio-csatlakozás SAML-hitelesítés csempére kattint, akkor a bejelentkezési folyamat elindításához, illetve ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie a RStudio-összekötő SAML-Hitelesítésbe, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Következő lépések

Miután konfigurálta a RStudio-kapcsolat SAML-hitelesítését, kikényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).