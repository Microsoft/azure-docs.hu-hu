---
title: 'Oktatóanyag: Azure Active Directory integráció az SAML SSO-nal a bitbucket by Solution GmbH használatával | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és SAML SSO között a bitbucket by Solution GmbH használatával.
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
ms.openlocfilehash: aa5e3e88ceb957728799f27482de7477ba6b7b48
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98621247"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Oktatóanyag: Azure Active Directory integráció az SAML SSO-nal a bitbucket by Solution GmbH által

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az SAML SSO-t a bitbucket for Solution GmbH Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a bitbucket-hez készült SAML SSO-t, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a toSAML SSO-hoz a bitbucket by Solution GmbH használatával.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a toSAML SSO-ba a bitbucket által az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.


## <a name="prerequisites"></a>Előfeltételek

Ha az Azure AD-integrációt az SAML SSO-nal szeretné konfigurálni a bitbucket by Solution GmbH esetében, a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* SAML egyszeri bejelentkezés a bitbucket-hez az egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A bitbucket által használt SAML SSO támogatja az **SP és a identitásszolgáltató** által kezdeményezett SSO-t
* Az SAML SSO bitbucket általi feloldása a felhasználói üzembe helyezést **csak időben** támogatja


## <a name="add-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>SAML SSO hozzáadása a bitbucket by Solution GmbH által a katalógusból

Ha az SAML SSO-t az Azure AD-be való bitbucket szeretné konfigurálni, az SAML SSO-t hozzá kell adnia az bitbucket-hez készült Solution GmbH által a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali ablaktáblán válassza a **Azure Active Directory** lehetőséget.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a következőt: **SAML SSO a bitbucket by Solution GmbH** a keresőmezőbe.
1. Válassza ki az **SAML SSO-t a bitbucket által** az eredmények közül, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Az Azure AD SSO konfigurálása és tesztelése az SAML SSO-hoz a bitbucket by Solution GmbH használatával

Konfigurálja és tesztelje az Azure AD SSO-t az SAML SSO-vel a bitbucket by Solution GmbH használatával, egy **B. Simon** nevű teszt felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy összekapcsolt kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között az SAML SSO-ban a bitbucket by Solution GmbH használatával.

Az Azure AD SSO és az SAML SSO bitbucket általi konfigurálásához és teszteléséhez az alábbi lépéseket kell végrehajtania:


1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
    1. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
2. **[Konfigurálja az SAML SSO-t a bitbucket by Solution GmbH SSO](#configure-saml-sso-for-bitbucket-by-resolution-gmbh-sso)** -vel – az alkalmazás oldalának Sign-On beállításainak konfigurálásához.
    1. **[SAML SSO létrehozása a bitbucket által a megoldáshoz használt felhasználó](#create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)** számára – a felhasználó Azure ad-beli Britta kapcsolódó, az SAML SSO-ban található, a bitbucket-hez készült "
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD SSO-t a Azure Portalban.
 
1. A Azure Portal a **bitbucket SAML SSO esetén a megoldási GmbH** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyetlen Sign-On módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon válassza az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikont a beállítások szerkesztéséhez.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni:


    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<server-base-url>/plugins/servlet/samlsso`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:
    
    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Vegye fel a kapcsolatot az [SAML SSO-vel a bitbucket by Solution GmbH ügyfélszolgálati csapatával](https://marketplace.atlassian.com/apps/1217045/saml-single-sign-on-sso-bitbucket?hosting=server&tab=support) az értékek beszerzéséhez. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**  >  **felhasználók**  >  **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszót.
   1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a SAML SSO-hoz a bitbucket by Solution GmbH által.

1. A Azure Portal válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.
1. Az alkalmazások listáról válassza ki az **SAML SSO elemet a bitbucket by Solution GmbH által**.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **Felhasználó hozzáadása** elemet. Ezután a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza ki a felhasználók listájából a **B. Simon** elemet. Ezután válassza a **kijelölés** lehetőséget a képernyő alján.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

## <a name="configure-saml-sso-for-bitbucket-by-resolution-gmbh-sso"></a>SAML SSO konfigurálása a bitbucket által a Solution GmbH SSO-val

1. Jelentkezzen be az SAML SSO-ra a bitbucket által a megoldási GmbH céges webhelyre rendszergazdaként.

2. A fő eszköztár jobb oldalán kattintson a **Beállítások** elemre.

3. Nyissa meg a fiókok szakaszt, és kattintson a menüsor **SAML-SingleSignon** .

    ![A Samlsingle](./media/bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

4. Az **SAML SIngleSignOn beépülő modul konfigurálása lapon** kattintson a **identitásszolgáltató hozzáadása** elemre. 

    ![A identitásszolgáltató hozzáadása](./media/bitbucket-tutorial/tutorial_bitbucket_addidp.png)

5. Az **SAML-azonosító kiválasztása** lapon hajtsa végre a következő lépéseket:

    ![Az identitás szolgáltatója](./media/bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. Válassza a **identitásszolgáltató típust** **Azure ad**-ként.

    b. A **név** szövegmezőbe írja be a nevet.

    c. A **Leírás** szövegmezőbe írja be a leírást.

    d. Kattintson a **Tovább** gombra.

6. Az **identitás-szolgáltató konfigurációja lapon** kattintson a **tovább** gombra.

    ![Az identitás konfigurációja](./media/bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

7.  Az **SAML identitásszolgáltató-Metaadatok importálása** lapon kattintson a **fájl betöltése** gombra, hogy feltöltse a **metaadatokat tartalmazó XML-** fájlt, amelyet a Azure Portal letöltött.

    ![A idpmetadata](./media/bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)

8. Kattintson a **Tovább** gombra.

9. Kattintson a **Beállítások mentése** lehetőségre.

    ![A Mentés](./media/bitbucket-tutorial/tutorial_bitbucket_save.png)


## <a name="create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>SAML SSO létrehozása a bitbucket által a Resolution GmbH test User használatával

Ennek a szakasznak a célja, hogy létrehozzon egy Britta Simon nevű felhasználót az SAML SSO-ban a bitbucket by Solution GmbH használatával. A bitbucket által használt SAML SSO az igény szerinti üzembe helyezést támogatja, és a felhasználókat manuálisan is létrehozhatja. az [SAML SSO-t a bitbucket a Solution GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support) ügyfélszolgálati csapata adja meg.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez a rendszer átirányítja az SAML SSO-ra a bitbucket által az alábbi URL-címen, ahol kezdeményezheti a bejelentkezési folyamatot.  

* Nyissa meg az SAML SSO-t a bitbucket by Resolution GmbH közvetlen bejelentkezési URL-címével, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie az SAML SSO-ba a bitbucket által a megoldási GmbH számára, amelyhez be kell állítania az egyszeri bejelentkezést.

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások bitbucket által használt SAML SSO-ra kattint, ha az SP módban van konfigurálva, akkor a bejelentkezési folyamat elindításához, illetve ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie az SAML SSO-ra a bitbucket by Solution GmbH számára, amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

Miután konfigurálta az SAML SSO-t a bitbucket-hez a megoldási szolgáltatáshoz, megadhatja a munkamenet-vezérlőket, amelyek valós időben védik a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáféréstől. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).