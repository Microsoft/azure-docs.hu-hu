---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Lenses.io | Microsoft Docs'
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Lenses.io között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/02/2020
ms.author: jeedes
ms.openlocfilehash: 181d58baf128c4848a538e776aea0e43213994dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92458608"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-the-lensesio-dataops-portal"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Lenses.io DataOps-portálon

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a [Lenses.IO](https://lenses.io/) DataOps portált Azure Active Directory (Azure ad) használatával. A Lenses.io Azure AD-vel való integrálását követően a következőket teheti:

* A Lenses.io-portálhoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a lencsékre az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Ha többet szeretne megtudni a szolgáltatott szoftver (SaaS) alkalmazás Azure AD-integrációval kapcsolatban, tekintse meg a [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure ad-vel](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A lencsék portál egy példánya. Számos [üzembe helyezési lehetőség](https://lenses.io/product/deployment/)közül választhat.
* Az egyszeri bejelentkezést (SSO) támogató Lenses.io- [licenc](https://lenses.io/product/pricing/) .

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését tesztkörnyezetben végezheti el.

* A Lenses.io támogatja a szolgáltató (SP) által kezdeményezett egyszeri bejelentkezést.

* A munkamenet-vezérlést a Lenses.io konfigurálása után kényszerítheti ki. A munkamenet-vezérlő valós időben védi a szervezete bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-lensesio-from-the-gallery"></a>Lenses.io hozzáadása a gyűjteményből

A Lenses.io Azure AD-be való integrálásának konfigurálásához adja hozzá a Lenses.io a felügyelt SaaS-alkalmazások listájához:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Válassza az **Új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban adja meg a **Lenses.IO** kifejezést a keresőmezőbe.
1. Az eredmények panelen válassza a **Lenses.IO** lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-lensesio"></a>Azure AD SSO konfigurálása és tesztelése a Lenses.io-hez

Hozzon létre egy *B. Simon* nevű teszt felhasználót az Azure ad SSO konfigurálásához és teszteléséhez a Lenses.IO-portálon. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Lenses.io-ben.

Végezze el a következő lépéseket:

1. [Konfigurálja az Azure ad SSO](#configure-azure-ad-sso) -t, hogy a felhasználók használhatják ezt a funkciót.
    1. [Hozzon létre egy Azure ad-teszt felhasználót és csoportot](#create-an-azure-ad-test-user-and-group) az Azure ad SSO teszteléséhez B. Simon használatával.
    1. [Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user) , hogy B. Simon engedélyezze az Azure ad SSO használatát.
1. [Konfigurálja a LENSES.IO SSO](#configure-lensesio-sso) -t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
    1. [Hozzon létre Lenses.IO-tesztelési engedélyeket](#create-lensesio-test-group-permissions) annak szabályozására, hogy mi B. Simon fér hozzá a Lenses.IO (Authorization) szolgáltatáshoz.
1. Ellenőrizze az [SSO](#test-sso) -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO-t a Azure Portalban:

1. A [Azure Portal](https://portal.azure.com/) **Lenses.IO** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, majd válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés az SAML-vel** lapon válassza a Szerkesztés/toll ikont az **ALAPszintű SAML-konfigurációhoz** a beállítások szerkesztéséhez.

   ![Képernyőfelvétel: az alapszintű SAML-konfiguráció szerkesztésének ikonja.](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg az értékeket a következő szövegbeviteli mezőkben:

    a. **Bejelentkezési URL-cím**: adjon meg egy URL-címet, amely a következő mintával rendelkezik: `https://<CUSTOMER_LENSES_BASE_URL>` . Például: `https://lenses.my.company.com`.

    b. **Azonosító (entitás azonosítója)**: adjon meg egy URL-címet, amely a következő mintával rendelkezik: `https://<CUSTOMER_LENSES_BASE_URL>` . Például: `https://lenses.my.company.com`.

    c. **Válasz URL-címe**: adjon meg egy URL-címet, amely a következő mintával rendelkezik: `https://<CUSTOMER_LENSES_BASE_URL>/api/v2/auth/saml/callback?client_name=SAML2Client` . Például: `https://lenses.my.company.com/api/v2/auth/saml/callback?client_name=SAML2Client`.

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse azokat a tényleges bejelentkezési URL-címmel, a válasz URL-címével és a lencsék-portál példányának alap URL-címének azonosítójával. További információért tekintse meg a [LENSES.IO SSO dokumentációját](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0) .

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon lépjen az **SAML-aláíró tanúsítvány** szakaszra. Keresse meg az **összevonási metaadatok XML-fájlját**, majd válassza a **Letöltés** lehetőséget a számítógép tanúsítványának letöltéséhez és mentéséhez.

    ![A tanúsítvány letöltési hivatkozását megjelenítő képernyőkép.](common/metadataxml.png)

1. A **Lenses.IO beállítása** szakaszban a letöltött XML-fájl segítségével konfigurálja a lencséket az Azure SSO-ra.

### <a name="create-an-azure-ad-test-user-and-group"></a>Azure AD-teszt felhasználó és csoport létrehozása

A Azure Portal egy B. Simon nevű teszt felhasználót hoz létre. Ezután létre fog hozni egy tesztelési csoportot, amely a B. Simon elérését vezérli a lencsékben.

Azt is megtudhatja, hogyan használja a lencsék csoporttagság-leképezést a [lencsék SSO-dokumentációjában](https://docs.lenses.io/install_setup/configuration/security.html#id3).

**A teszt felhasználó létrehozása:**

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. A képernyő felső részén válassza az **új felhasználó** lehetőséget.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A név mezőbe írja be a **B. Simon** **nevet** .  
   1. A **Felhasználónév** mezőbe írja be a (z username@companydomain.extension ) nevet. Például: B.Simon@contoso.com.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet. Jegyezze fel a jelszót, amely a **jelszó** mezőben jelenik meg.
   1. Válassza a **Létrehozás** lehetőséget.

**A csoport létrehozása:**

1. Lépjen **Azure Active Directory**, majd válassza a **csoportok** lehetőséget.
1. A képernyő felső részén válassza az **új csoport** lehetőséget.
1. A **csoport tulajdonságainál** kövesse az alábbi lépéseket:
   1. A **csoport típusa** mezőben válassza a **Biztonság** elemet.
   1. A csoportnév mezőbe írja be a **LensesUsers** **nevet** .
   1. Válassza a **Létrehozás** lehetőséget.
1. Válassza ki a **LensesUsers** csoportot, és másolja az **objektumazonosítót** (például f8b5c1ec-45de-4abd-af5c-e874091fb5f7). Ezt az azonosítót lencsékben fogja használni a csoport felhasználóinak a [megfelelő engedélyekkel](https://docs.lenses.io/install_setup/configuration/security.html#id3)való leképezéséhez.  

**A csoport társítása a teszt felhasználóhoz:**

1. Lépjen **Azure Active Directory**, majd válassza a **felhasználók** lehetőséget.
1. Válassza ki a teszt felhasználót: **B. Simon**.
1. Válassza a **csoportok** lehetőséget.
1. A képernyő felső részén válassza a **tagság hozzáadása** elemet.
1. Keresse meg és válassza ki a **LensesUsers**.
1. Kattintson a **Kiválasztás** elemre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Lenses.io.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Lenses.IO** lehetőséget.
1. Az alkalmazás – Áttekintés lap **kezelés** területén válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozást megjelenítő képernyőkép.](common/users-groups-blade.png)

1. Válassza a **Felhasználó hozzáadása** elemet.

   ![A felhasználó hozzáadása hivatkozást megjelenítő képernyőkép.](common/add-assign-user.png)

1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza ki az **B. Simon** elemet a felhasználók listából. Ezután kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-kijelentésben bármelyik szerepkör értékét várta, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a listáról a felhasználó számára. Ezután kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-lensesio-sso"></a>Lenses.io SSO konfigurálása

Ha az SSO-t a **Lenses.IO** -portálon szeretné konfigurálni, telepítse a letöltött **összevonási metaadatokat tartalmazó XML-fájlt** a lencsék példányán, és [konfigurálja az egyszeri bejelentkezés engedélyezéséhez szükséges objektívek](https://docs.lenses.io/install_setup/configuration/security.html#configure-lenses)

### <a name="create-lensesio-test-group-permissions"></a>Lenses.io-tesztelési csoport engedélyeinek létrehozása

1. Ha egy csoportot szeretne létrehozni a lencsékben, használja a **LensesUsers** csoport **objektumazonosítóát** . Ez a felhasználó- [létrehozási szakaszban](#create-an-azure-ad-test-user-and-group)másolt azonosító.
1. Rendelje hozzá a kívánt engedélyeket a B. Simon számára.

További információ: [Azure-objektívek csoport leképezése](https://docs.lenses.io/install_setup/configuration/security.html#azure-groups).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD SSO konfigurációját a hozzáférési panel használatával tesztelheti.

Amikor kiválasztja a Lenses.io csempét a hozzáférési panelen, automatikusan be kell jelentkeznie a Lenses.io-portálra. További információkért lásd: [Bevezetés a Hozzáférési panel használatába](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Egyszeri bejelentkezés beállítása a Lenses.io-példányban](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)

- [Az SaaS-alkalmazások Azure AD-vel való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure AD-vel?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés az Azure AD-ben?](../conditional-access/overview.md)

- [A Lenses.io kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)

- [A Lenses.io és a speciális láthatóság és vezérlők elleni védelem](/cloud-app-security/proxy-intro-aad)