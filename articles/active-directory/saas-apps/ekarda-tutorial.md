---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a ekarda | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és ekarda között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: b057d07e10676291f42a9a070e32cb17df672651
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735154"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ekarda"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a ekarda

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a ekarda a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az ekarda-t az Azure AD-vel, a következőket teheti:

* A ekarda-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók Azure AD-fiókjaik használatával automatikusan bejelentkezzenek a ekarda.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Ekarda-előfizetés engedélyezve az egyszeri bejelentkezéshez (SSO).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* a ekarda támogatja az SP által kezdeményezett és a IDENTITÁSSZOLGÁLTATÓ által kezdeményezett egyszeri bejelentkezést.
* a ekarda támogatja az igény szerinti felhasználói üzembe helyezést.

## <a name="add-ekarda-from-the-gallery"></a>Ekarda hozzáadása a gyűjteményből

A ekarda Azure AD-be való integrálásának konfigurálásához adja hozzá a ekarda a katalógusból a felügyelt SaaS-alkalmazások listájához:

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiók használatával.

1. A bal oldali panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Az új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **ekarda** kifejezést a keresőmezőbe.
1. Válassza ki a **ekarda** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-ekarda"></a>Azure AD SSO konfigurálása és tesztelése a ekarda-hez

Konfigurálja és tesztelje az Azure AD SSO-t a ekarda egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy összekapcsolt kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a ekarda-ben.

Az Azure AD SSO és a ekarda konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. [Konfigurálja az Azure ad SSO](#configure-azure-ad-sso) -t, hogy a felhasználók használhatják ezt a funkciót.

    1. [Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user) az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. [Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user) , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. [Konfigurálja a EKARDA SSO](#configure-ekarda-sso) -t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
    * [Hozzon létre egy ekarda-tesztelési felhasználót](#create-an-ekarda-test-user) , amely a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon ekarda rendelkezik.
1. Ellenőrizze az [SSO](#test-sso) -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az Azure AD SSO engedélyezéséhez kövesse az Azure Portal alábbi lépéseit:

1. Jelentkezzen be az Azure Portalra.
1. A **ekarda** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeres Sign-On beállítása az SAML-vel** lapon válassza a ceruza ikont az **alapszintű SAML-konfigurációs** beállítások szerkesztéséhez.

   ![Képernyőfelvétel: egyszeres Sign-On beállítása SAML-oldallal, a ceruza ikon kiemelve.](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha a **szolgáltatói metaadatokat tartalmazó fájlt** látja, kövesse az alábbi lépéseket:
    1. Válassza a **metaadat-fájl feltöltése** lehetőséget.
    1. Válassza a mappa ikont a metaadat-fájl kiválasztásához, majd válassza a **feltöltés** lehetőséget.
    1. A metaadat-fájl feltöltése után az **azonosító** és a **Válasz URL-címe** automatikusan megjelenik a ekarda szakasz szövegmezőben.

    > [!Note]
    > Ha az **azonosító** és a **Válasz URL-címe** nem jelenik meg automatikusan, adja meg manuálisan az értékeket az igényeinek megfelelően.

1. Ha nem látja a szolgáltatói **metaadatokat tartalmazó fájlt** az **ALAPszintű SAML-konfiguráció** szakaszban, és az alkalmazást identitásszolgáltató módban szeretné konfigurálni, adja meg az alábbi mezők értékeit:

    1. Az **azonosító** szövegmezőbe írja be azt az URL-címet, amely a következő mintát követi: `https://my.ekarda.com/users/saml_metadata/<COMPANY_ID>`
    1. A **Válasz URL-címe** szövegmezőbe írja be azt az URL-címet, amely a következő mintát követi: `https://my.ekarda.com/users/saml_acs/<COMPANY_ID>`

1. Ha az alkalmazást SP-kezdeményezésű módban szeretné konfigurálni, válassza a **további URL-címek beállítása** lehetőséget, és tegye a következőket:

    A **bejelentkezési URL** szövegmezőbe írja be azt az URL-címet, amely a következő mintát követi:  `https://my.ekarda.com/users/saml_sso/<COMPANY_ID>`

    > [!NOTE]
    > Az előző két lépés értékei nem valósak. Frissítse őket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címek értékével. Az értékek lekéréséhez forduljon a ekarda ügyfélszolgálati [csapatához](mailto:contact@ekarda.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban válassza a **Letöltés** lehetőséget a **tanúsítvány (Base64)** mentéséhez a számítógépen.

    ![Képernyőfelvétel az egyszeri Sign-On beállítása az SAML-vel lapon – a Base64-tanúsítványra Kiemelt letöltési hivatkozással.](common/certificatebase64.png)

1. A **Ekarda beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Képernyőfelvétel az egyszeri Sign-On beállítása az SAML-vel lapon – az URL-címek ekarda beállítása című rész.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban a Azure Portal használatával hozzon létre egy B. Simon nevű teszt felhasználót.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**  >  **felhasználók**  >  **minden felhasználó** lehetőséget.

1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Adja meg például a következőt: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, és jegyezze fel a **jelszó** mezőben megjelenő értéket.
   1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a ekarda.

1. A Azure Portal válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **ekarda** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

1. A **felhasználók és csoportok** párbeszédpanelen válassza ki a felhasználók listájából a **B. Simon** elemet. Ezután válassza a **kijelölés** lehetőséget a képernyő alján.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

## <a name="configure-ekarda-sso"></a>Ekarda SSO konfigurálása

1. A ekarda belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **Ekarda beállítása** elemre, majd a ekarda alkalmazásra irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a ekarda való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-6-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a ekarda, egy másik böngészőablakban jelentkezzen be a ekarda vállalati webhelyre rendszergazdaként.

1. Válassza   >  **a rendszergazda saját fiók** lehetőséget.

    ![Képernyőkép a ekarda-hely felhasználói felületéről a fiókom kiemelve a felügyeleti menüben.](./media/ekarda-tutorial/ekarda.png)

1. A lap alján keresse meg az SAML- **Beállítások** szakaszt. Ez a szakasz az SAML-integráció konfigurálását ismerteti.
1. Az **SAML-beállítások** szakaszban kövesse az alábbi lépéseket:

    ![Képernyőkép a ekarda SAML-beállítások oldalról, ahol az SAML-konfigurációs mezők ki vannak emelve.](./media/ekarda-tutorial/ekarda1.png)

    1. Válassza ki a **szolgáltatói metaadatok** hivatkozást, és mentse fájlba a számítógépén.
    1. Jelölje be az **SAML engedélyezése** jelölőnégyzetet.
    1. Illessze be a korábban a Azure Portalból másolt **Azure ad-azonosító** értékét a **IDENTITÁSSZOLGÁLTATÓ-entitás azonosítója** szövegmezőbe.
    1. A **identitásszolgáltató bejelentkezési URL-címe** szövegmezőbe illessze be a korábban a Azure Portalból másolt **bejelentkezési URL-** értéket.
    1. A **identitásszolgáltató kijelentkezési URL-címe** szövegmezőbe illessze be a korábban a Azure Portalból másolt **kijelentkezési URL-** értéket.
    1. A Jegyzettömb segítségével nyissa meg a Azure Portal letöltött **tanúsítvány-(Base64-)** fájlt. Illessze be a tartalmat a **identitásszolgáltató x509-tanúsítvány** szövegmezőbe.
    1. Jelölje be az **slo engedélyezése** jelölőnégyzetet a **Beállítások** szakaszban.
    1. Válassza a **Frissítés** lehetőséget.

### <a name="create-an-ekarda-test-user"></a>Ekarda-teszt felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a ekarda-ben. a ekarda támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nem hajtható végre művelet. Ha egy B. Simon nevű felhasználó már nem létezik a ekarda-ben, akkor a hitelesítés után létrejön egy újat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a ekarda bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.

* Lépjen közvetlenül a ekarda bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a ekarda, amelyhez be szeretné állítani az egyszeri bejelentkezést

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások ekarda csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat kezdeményezéséhez, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie arra a ekarda, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A ekarda konfigurálása után kényszerítheti a munkamenet-vezérlést. Ez a figyelmeztetés védelmet nyújt a szervezete bizalmas adatainak valós idejű kiszűrése és beszivárgása ellen. A munkamenet-vezérlő kiterjeszthető feltételes hozzáférést biztosító alkalmazás-vezérlőról. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).