---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása 4me | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory 4me között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: jeedes
ms.openlocfilehash: f2a171460defb3a8befba5a674a2f1aadde636af
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517827"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-4me"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása 4me-sel

Ez az oktatóanyag bemutatja, hogyan integrálhatja a 4me-et a Azure Active Directory (Azure AD) használatával. Ha integrálja a 4me-et az Azure AD-val, a következőt tudja:

* Vezérlés az Azure AD-ben, aki hozzáféréssel rendelkezik a 4me-hez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve lesznek a 4me-be az Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésekhez a következő elemekre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* 4me egyszeri bejelentkezést (SSO) engedélyező előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD SSO-t.

* A 4me támogatja **az SP által** kezdeményezett SSO-t.
* A 4me támogatja **az időben való felhasználóáthelyezést.**

## <a name="add-4me-from-the-gallery"></a>4me hozzáadása a katalógusból

A 4me Azure AD-be való integrálásának konfigurálásához hozzá kell adni a 4me-et a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes fiókkal Microsoft-fiók.
1. A bal oldali navigációs panelen válassza ki **a Azure Active Directory** szolgáltatást.
1. Lépjen a Vállalati **alkalmazások lapra,** majd válassza a **Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az Új **alkalmazás lehetőséget.**
1. A Hozzáadás **a katalógusból szakaszban** írja be a **4me parancsot** a keresőmezőbe.
1. Válassza **a 4me lehetőséget** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.

## <a name="configure-and-test-azure-ad-sso-for-4me"></a>Azure AD SSO konfigurálása és tesztelése 4me-hez

Konfigurálja és tesztelje az Azure AD SSO-t 4me-sel egy **B.Simon nevű tesztfelhasználóval.** Ahhoz, hogy az SSO működjön, kapcsolati kapcsolatot kell létesítenie egy Azure AD-felhasználó és a kapcsolódó felhasználó között a 4me-ben.

Az Azure AD SSO 4me környezetben való konfiguráláshoz és teszteléshez töltse ki a következő építőelemeket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy lehetővé tegye a felhasználók számára a funkció használatát.
    1. **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének tesztelése a B.Simon használatával.
    1. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. **[Konfigurálja a 4me SSO-t](#configure-4me-sso)** – az egyszeri bejelentkezési beállítások alkalmazásoldalon való konfigurálása.
    1. **[4me tesztfelhasználó létrehozása](#create-4me-test-user)** – a B.Simon megfelelője a 4me-ben, amely a felhasználó Azure AD-reprezentációjával van összekapcsolva.
1. **[Az SSO tesztelése](#test-sso)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A Azure Portal **4me** alkalmazásintegrációs oldalán keresse meg a **Kezelés** szakaszt, és válassza az **egyszeri bejelentkezés lehetőséget.**
1. Az Egyszeri **bejelentkezési módszer** kiválasztása lapon válassza az **SAML lehetőséget.**
1. Az Egyszeri **bejelentkezés beállítása SAML-sel** lapon kattintson az SamL-alapkonfiguráció ceruza ikonjára a beállítások szerkesztéséhez. 

   ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

1. Az **SAML-alapkonfiguráció szakaszban** adja meg a következő mezők értékeit:

    a. A **Bejelentkezési URL-cím** szövegmezőbe írjon be egy URL-címet az alábbi minták egyikével:

    | Környezet| URL-cím|
    |---|---|
    | Termelés | `https://<SUBDOMAIN>.4me.com`|
    | QA| `https://<SUBDOMAIN>.4me.qa`|
    | | |

    b. Az **Azonosító (Entitásazonosító) szövegmezőbe** írjon be egy URL-címet az alábbi minták egyikével:

    | Környezet| URL-cím|
    |---|---|
    | Termelés | `https://<SUBDOMAIN>.4me.com`|
    | QA| `https://<SUBDOMAIN>.4me.qa`|
    | | |

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a bejelentkezési URL-cím és az azonosító tényleges értékével. Ezeket az [értékeket a 4me-es ügyféltámogatási](mailto:support@4me.com) csapattól kapják meg. Az **SAML** alapkonfiguráció szakaszában látható mintákra is hivatkozhat a Azure Portal.

1. A 4me alkalmazás egy adott formátumban várja az SAML helyességi feltételeket, amelyhez hozzá kell adni egyéni attribútumleképezéseket az SAML-jogkivonat attribútumainak konfigurációjában. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a 4me alkalmazás arra számít, hogy néhány további attribútumot kell visszaadódni az SAML-válaszban, amelyek alább láthatók. Ezek az attribútumok is előre ki vannak töltve, de a követelményeknek megfelelően áttekintheti őket.

    | Name | Forrásattribútum|
    | ---------------| --------------- |
    | first_name | user.givenname |
    | last_name | user.surname |

1. Az **SAML aláíró tanúsítvány szakaszban** kattintson a **Szerkesztés** gombra az SAML aláíró **tanúsítványa párbeszédpanel megnyitásához.**

    ![SAML aláíró tanúsítvány szerkesztése](common/edit-certificate.png)

1. Az **SAML aláíró tanúsítvány szakaszban** másolja ki az **UJJLENYOMATot,** és mentse a számítógépre.

    ![Ujjlenyomat értékének másolása](common/copy-thumbprint.png)

1. A **Beállítás 4me szakaszban** másolja ki a követelménynek megfelelő URL-címet/URL-címeket.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű tesztfelhasználót fog létrehozni a Azure Portal területen.

1. A bal oldali panelen válassza Azure Portal lehetőséget, **Azure Active Directory** **a Felhasználók** lehetőséget, majd válassza a Minden **felhasználó lehetőséget.**
1. Válassza **az Új felhasználó** lehetőséget a képernyő tetején.
1. A Felhasználó **tulajdonságai** között kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév **mezőbe írja** be a következőt: username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket. 
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezheti a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát. Ehhez hozzáférést biztosít a 4me-hez.

1. A Azure Portal válassza a **Vállalati alkalmazások,** majd a **Minden alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza a **4me lehetőséget.**
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza **a Felhasználó hozzáadása** lehetőséget, majd a Hozzárendelés hozzáadása **párbeszédpanelen válassza a** Felhasználók és **csoportok** lehetőséget.
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a **B.Simon**  lehetőséget a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja **azt a Szerepkör kiválasztása** legördülő menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az "Alapértelmezett hozzáférés" szerepkör van kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

## <a name="configure-4me-sso"></a>4me SSO konfigurálása

1. Egy másik webböngészőablakban jelentkezzen be rendszergazdaként a 4me-be.

1. A bal felső sarokban kattintson a **Beállítások emblémára,** majd a bal oldali sávon kattintson az **Egyszeri bejelentkezés elemre.**

    ![4me-beállítások](./media/4me-tutorial/settings.png)

1. Az Egyszeri **bejelentkezés lapon** hajtsa végre a következő lépéseket:

    ![4me singleasignon](./media/4me-tutorial/single-sign-on.png)

    a. Jelölje be az **Engedélyezett** beállítást.

    b. A Távoli **kijelentkezés URL-címe** szövegmezőbe illessze be a Kijelentkezés **URL-címe** értéket, amelyet kimásott a Azure Portal.

    c. Az **SAML szakasz** **SAML SSO URL-címe** szövegmezőbe illessze be a bejelentkezési **URL-cím** értékét, amelyet kimásott a Azure Portal.

    d. A **Tanúsítvány** ujjlenyomata szövegmezőbe illessze be az **UJJLENYOMAT** értéket kettősponttal elválasztva, kettősponttal, kettőspontos sorrendben (AA:COLON:CC:DD:EE:FF:GG:HH:II), amelyet a Azure Portal.

    e. Kattintson a **Mentés** gombra.

### <a name="create-4me-test-user"></a>4me tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a 4me fájlban. A 4me támogatja az alapértelmezés szerint engedélyezett, időben elérhető felhasználókiépítést. Ebben a szakaszban nem található műveletelem. Ha a felhasználó még nem létezik a 4me-ben, a hitelesítés után létrejön egy új.

> [!Note]
> Ha manuálisan kell létrehoznia egy felhasználót, forduljon a [4me támogatási csapatához.](mailto:support@4me.com)

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az alábbi beállításokkal teszteli az Azure AD egyszeri bejelentkezési konfigurációját. 

* Kattintson az **Alkalmazás tesztelése elemre a** Azure Portal. Ez átirányítja a 4me bejelentkezési URL-címre, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a 4me bejelentkezési URL-címre, és onnan indítsa el a bejelentkezési folyamatot.

* Használhatja a Microsoft Saját alkalmazások. Ha a 4me csempére kattint a Saját alkalmazások, a rendszer a 4me bejelentkezési URL-címre irányítja át. További információ a [Saját alkalmazások: Bevezetés a](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)Saját alkalmazások.

## <a name="next-steps"></a>Következő lépések

A 4me konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Ismerje meg, hogyan kényszeríthető ki a munkamenet-vezérlés a Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)
