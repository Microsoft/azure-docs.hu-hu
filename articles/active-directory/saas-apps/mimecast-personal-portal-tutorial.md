---
title: 'Oktatóanyag: Azure Active Directory integráció a Mimecast Personal Portal szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Mimecast személyes portál között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: cc12afb9d4a889d53689421a98b522a8e3264da6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101644612"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mimecast-personal-portal"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Mimecast Personal Portalon

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Mimecast személyes portált Azure Active Directory (Azure AD-val). Ha integrálja a Mimecast személyes portálját az Azure AD-vel, a következőket teheti:

* A Mimecast személyes portálhoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek, hogy Mimecast a személyes portálra az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A személyes Mimecast-portál egyszeri bejelentkezéses (SSO) előfizetésének engedélyezése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Mimecast Personal Portal támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t
 
## <a name="add-mimecast-personal-portal-from-the-gallery"></a>Mimecast személyes portál hozzáadása a katalógusból

A Mimecast személyes portál Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Mimecast személyes portált a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Mimecast személyes portál** kifejezést a keresőmezőbe.
1. Válassza a **Mimecast személyes portál** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-mimecast-personal-portal"></a>Az Azure AD SSO konfigurálása és tesztelése a Mimecast személyes portálján

Konfigurálja és tesztelje az Azure AD SSO-t a Mimecast Personal Portalon egy **B. Simon** nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Mimecast Personal Portalon.

Az Azure AD SSO konfigurálásához és teszteléséhez a Mimecast Personal Portalon hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Mimecast személyes portál egyszeri bejelentkezésének konfigurálása](#configure-mimecast-personal-portal-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Mimecast személyes portál tesztelési felhasználó](#create-mimecast-personal-portal-test-user)** – a Mimecast személyes portálon található, a felhasználó Azure ad-képviseletéhez kapcsolódó, B. Simon-nek megfelelő jogosultsággal rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **Mimecast személyes portál** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást identitásszolgáltató kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:

    | Region  |  Érték | 
    | --------------- | --------------- |
    | Európa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Egyesült Államok   | `https://us-api.mimecast.com/sso/<accountcode>`|
    | Dél-afrikai Köztársaság    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Ausztrália       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Offshore szoftverfejlesztési        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    > [!NOTE]
    > A `accountcode` Mimecast Personal Portalon található értéket a **Fiókbeállítások**  >    >  **fiók kódja** alatt találja. Fűzze hozzá az `accountcode` azonosítót az azonosítóhoz.

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet:

    | Region  |  Érték |
    | --------------- | --------------- |
    | Európa          | `https://eu-api.mimecast.com/login/saml`|
    | Egyesült Államok   | `https://us-api.mimecast.com/login/saml`|
    | Dél-afrikai Köztársaság    | `https://za-api.mimecast.com/login/saml`|
    | Ausztrália       | `https://au-api.mimecast.com/login/saml`|
    | Offshore szoftverfejlesztési        | `https://jer-api.mimecast.com/login/saml`|

1. Ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL** szövegmezőbe írja be az URL-címet:

    | Region  |  Érték |
    | --------------- | --------------- |
    | Európa          | `https://eu-api.mimecast.com/login/saml`|
    | Egyesült Államok   | `https://us-api.mimecast.com/login/saml`|
    | Dél-afrikai Köztársaság    | `https://za-api.mimecast.com/login/saml`|
    | Ausztrália       | `https://au-api.mimecast.com/login/saml`|
    | Offshore szoftverfejlesztési        | `https://jer-api.mimecast.com/login/saml`|

1. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Mimecast személyes portálhoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Mimecast személyes portál** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-mimecast-personal-portal-sso"></a>Mimecast személyes portál egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Mimecast felügyeleti konzolba.

1. Navigáljon a **felügyeleti**  >  **szolgáltatások**  >  **alkalmazásaihoz**.

    ![Képernyőfelvétel: a Mimecast ablak megjelenítése a kiválasztott alkalmazásokkal.](./media/mimecast-personal-portal-tutorial/services.png)

1. Kattintson a **hitelesítési profilok** fülre.
    
    ![A képernyőképen a kiválasztott hitelesítési profilok alkalmazás lap látható.](./media/mimecast-personal-portal-tutorial/authentication-profiles.png)

1. Kattintson az **új hitelesítési profil** fülre.

    ![A képernyőképen a kiválasztott új hitelesítési profil látható.](./media/mimecast-personal-portal-tutorial/new-authenticatio-profile.png)

1. Adjon meg egy érvényes leírást a **Leírás** szövegmezőben, és válassza az **SAML-hitelesítés kikényszerítés a Mimecast személyes portálon** jelölőnégyzetet.

    ![A képernyőképen a kiválasztott új hitelesítési profil látható.](./media/mimecast-personal-portal-tutorial/selecting-personal-portal.png)

1. A **Mimecast Personal Portal SAML-konfigurációja** lapon hajtsa végre a következő lépéseket:

    ![A képernyőképen látható, hogy hol válassza ki az SAML-hitelesítés kikényszerített felügyeleti konzolját.](./media/mimecast-personal-portal-tutorial/sso-settings.png)

    a. A **szolgáltatónál** válassza a legördülő listából a **Azure Active Directory** elemet.

    b. A **metaadatok URL-címe** szövegmezőbe illessze be az **alkalmazás-összevonási metaadatok URL-címét** , amelyet a Azure Portal másolt.

    c. Kattintson az **Importálás** gombra. A metaadatok URL-címének importálása után a mezők automatikusan fel lesznek töltve, nem kell végrehajtania semmilyen műveletet ezeken a mezőkön.

    d. Ügyeljen arra, hogy szüntesse meg a **jelszóval védett környezet használatának** törlését és **az integrált hitelesítési környezet használata** jelölőnégyzeteket.

    e. Kattintson a **Mentés** gombra.

### <a name="create-mimecast-personal-portal-test-user"></a>Mimecast személyes portál tesztelési felhasználó létrehozása

1. Egy másik böngészőablakban jelentkezzen be a Mimecast felügyeleti konzolba.

1. Navigáljon az **adminisztrációs**  >  **könyvtárak**  >  **belső könyvtáraihoz**.

    ![A képernyőfelvételen a Mimecast személyes portál SAML-konfigurációja látható, ahol megadhatja a leírt értékeket.](./media/mimecast-personal-portal-tutorial/internal-directories.png)

1. Válassza ki a tartományt, ha a tartomány alább szerepel, máskülönben hozzon létre egy új tartományt az **új tartományra** kattintva.

    ![A képernyőképen a Mimecast ablak látható, amelyen belső könyvtárak vannak kiválasztva.](./media/mimecast-personal-portal-tutorial/domain-name.png)

1. Kattintson az **új címek** fülre.

    ![Képernyőfelvétel: a kiválasztott tartomány.](./media/mimecast-personal-portal-tutorial/new-address.png)

1. Adja meg a szükséges felhasználói adatokat a következő oldalon:

    ![Képernyőfelvétel: az a lap, amelyen megadhatja a leírt értékeket.](./media/mimecast-personal-portal-tutorial/user-information.png)

    a. Az **E-mail cím** szövegmezőbe írja be a felhasználó e-mail címét, például: `B.Simon@yourdomainname.com` .

    b. A **globális név** szövegmezőbe írja be a felhasználó **teljes nevét** .

    c. **A jelszó és** **Jelszó megerősítése** szövegmezőbe írja be a felhasználó jelszavát.

    d. Válassza **a kényszerített módosítás bejelentkezéskor** jelölőnégyzetet.

    e. Kattintson a **Mentés** gombra.

    f. Ha szerepköröket szeretne hozzárendelni a felhasználóhoz, kattintson a **szerepkör szerkesztése** lehetőségre, és rendelje hozzá a szükséges szerepkört a felhasználóhoz a szervezete követelményeinek megfelelően.

    ![A képernyőfelvételen megtekintheti a szerepkör-szerkesztés lehetőséget.](./media/mimecast-personal-portal-tutorial/assign-role.png)


## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 
Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a Mimecast személyes portál bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen a Mimecast személyes portál bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a Mimecast személyes portálra, amelyhez be szeretné állítani az egyszeri bejelentkezést 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások Mimecast személyes portál csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie a Mimecast személyes portálra, amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A Mimecast személyes portál konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app)