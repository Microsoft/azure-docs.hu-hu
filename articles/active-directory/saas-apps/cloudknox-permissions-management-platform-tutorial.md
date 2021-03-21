---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a CloudKnox permissions Management platformmal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a CloudKnox engedélyek kezelési platformja között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 8c1b45d78d4fb61d239fef178cbcb9fa6efd9539
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101645685"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudknox-permissions-management-platform"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a CloudKnox engedélyek kezelése platformmal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a CloudKnox-engedélyek kezelési platformját Azure Active Directory (Azure AD) használatával. Ha integrálja az CloudKnox-engedélyek kezelési platformját az Azure AD-vel, a következőket teheti:

* Az Azure AD-ben a CloudKnox engedélyek kezelési platformhoz hozzáférő vezérlőelem.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a CloudKnox az engedélyek kezelése platformra az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* CloudKnox engedélyek Management platform egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A CloudKnox engedélyek kezelési platformja támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-cloudknox-permissions-management-platform-from-the-gallery"></a>CloudKnox-engedélyek kezelési platformjának hozzáadása a katalógusból

A CloudKnox engedélyek kezelési platform Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a CloudKnox engedélyek kezelési platformot a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **CloudKnox engedélyek kezelése platformot** a keresőmezőbe.
1. Válassza ki a **CloudKnox engedélyek kezelése platformot** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-cloudknox-permissions-management-platform"></a>Az Azure AD SSO konfigurálása és tesztelése a CloudKnox engedélyek kezelési platformján

Konfigurálja és tesztelje az Azure AD SSO-t a CloudKnox permissions Management platformmal egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a CloudKnox engedélyek kezelése platformon.

Az Azure AD SSO és a CloudKnox engedélyek kezelési platformjának konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[CloudKnox engedélyek kezelési platformjának egyszeri bejelentkezésének konfigurálása](#configure-cloudknox-permissions-management-platform-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[CloudKnox-engedélyek kezelése platform tesztelési felhasználó](#create-cloudknox-permissions-management-platform-test-user)** – a felhasználó Azure ad-képviseletéhez kapcsolódó "B. Simon" CloudKnox engedélyek kezelési platformjának megfelelő jogosultsággal rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **CloudKnox engedélyek kezelése platform** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon adja meg a következő mezők értékeit:

    A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://app.cloudknox.io/saml/<ID>`

    > [!NOTE]
    > A válasz URL-cím értéke nem valódi. Frissítse az értéket a tényleges válasz URL-címével. Az érték beszerzéséhez lépjen kapcsolatba a [CloudKnox permissions Management platform ügyfél-támogatási csapatával](mailto:support@cloudknox.io) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A CloudKnox permissions Management platform alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentiek mellett a CloudKnox permissions Management platform alkalmazás néhány további attribútumot vár az SAML-válaszban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.
    
    | Name |  Forrás attribútum|
    | --------------- | --------- |
    | First_Name | User. givenName |
    | Csoportok | User. groups |
    | Last_Name | felhasználó. vezetéknév |
    | Email_Address | User. mail |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **CloudKnox engedélyek kezelése platform beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát a CloudKnox engedélyek kezelési platformhoz való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **CloudKnox engedélyek kezelése platform** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-cloudknox-permissions-management-platform-sso"></a>A CloudKnox engedélyek kezelési platformjának egyszeri bejelentkezésének konfigurálása

Az egyszeri bejelentkezés a **CloudKnox engedélyek kezelése platformon** való konfigurálásához el kell küldenie a letöltött **összevonás-metaadatokat tartalmazó XML-fájlt** és a megfelelő másolt url-címeket a Azure Portalról a [CloudKnox permissions Management platform támogatási csapatához](mailto:support@cloudknox.io). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-cloudknox-permissions-management-platform-test-user"></a>CloudKnox engedélyekkel rendelkező felügyeleti platform tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a CloudKnox permissions Management platformon. A [CloudKnox permissions Management platform támogatási csapatával](mailto:support@cloudknox.io) a felhasználókat a CloudKnox permissions Management platform platformon veheti fel. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

* Kattintson az alkalmazás tesztelése Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a CloudKnox-engedélyek felügyeleti platformra, amelyhez be szeretné állítani az egyszeri bejelentkezést

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások CloudKnox engedélyek kezelése platform csempére kattint, automatikusan be kell jelentkeznie a CloudKnox-engedélyek kezelési platformra, amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

A CloudKnox-engedélyek kezelési platformjának konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).